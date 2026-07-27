# Planos & Cobrança

O ProvaTopia tem dois planos. O acesso pago é uma **assinatura** processada pelo **MercadoPago** (não é cobrança por chamada de API).

## Planos e entitlements

| Recurso | Free | Premium |
|---|---|---|
| Simulados por dia | **1** | ilimitado |
| Desempenho (retenção / curva de esquecimento) | ❌ | ✅ |
| Banco de questões completo | ❌ | ✅ |
| Revisão espaçada (FSRS) | ❌ | ✅ |

Rotas premium respondem `402 {"error":"Recurso exclusivo do Premium","upgrade":true}` para quem não é assinante. São elas: `GET /me/graph`, `POST /essays`, `GET /me/retention`, `GET+POST /me/study-plan*`, `GET+PUT /me/goal`, `GET /me/score-estimate`.

## Preços

Valores em **centavos de BRL** (padrão da API — o front converte na renderização):

| Ciclo | `amount` | Exibição | Equivalente/mês |
|---|---|---|---|
| `monthly` | `2490` | R$ 24,90/mês | R$ 24,90 |
| `annual` | `14900` | R$ 149,00/ano | ~R$ 12,42 |

## Fluxo de assinatura

### `POST /billing/checkout` — JWT

```bash
curl -X POST https://api.provatopia.com/billing/checkout \
  -H "Authorization: Bearer <jwt>" -H "Content-Type: application/json" \
  -d '{"cycle":"annual"}'
```

**`200`** → `{ "checkoutUrl": "https://www.mercadopago.com/..." }` — redirecione o usuário para essa URL.

Erros: `400 {"error":"cycle deve ser 'monthly' ou 'annual'"}` · `503` MercadoPago não configurado no servidor.

### `POST /billing/webhook` — público (callback do MercadoPago)

Endpoint interno que o MercadoPago chama. Em pagamento aprovado, ativa o premium do usuário (casado por `external_reference` = `user_id`). Sempre responde `200` para o provedor não reprocessar. **Não é chamado pelo cliente.**

### `POST /billing/cancel` — JWT

Cancela imediatamente: plano → `free`, assinatura → `cancelled`.

**`200`** → `{ "ok": true, "plan": "free" }`

## Consultar entitlements

### `GET /me/entitlements` — JWT

```json
{
  "plan": "premium",
  "entitlements": { "dailySimulados": null, "desempenho": true, "bancoCompleto": true, "revisaoFSRS": true },
  "usage": { "simuladosToday": 2, "simuladosRemaining": null },
  "subscription": { "status": "active", "cycle": "annual", "periodEnd": "2027-07-24T00:00:00.000Z" },
  "prices": { "monthly": { "amount": 2490 }, "annual": { "amount": 14900 } }
}
```
