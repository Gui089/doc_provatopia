# ProvaTopia API

Documentação da API do **ProvaTopia** — plataforma de estudos para **ENEM e vestibulares**: banco de questões, simulados, cadernos, redação com correção por IA, **revisão espaçada (FSRS)**, XP/ranking e planos premium.

!!! note "Duas camadas"
    - **[API atual](api-reference.md)** — o que existe hoje: autenticação **JWT**, assinatura via **MercadoPago**. É o backend que o app consome.
    - **[Partner API](partner-api.md)** — **planejada**: acesso de terceiros por **API key** (créditos/uso). Ainda **não** implementada — documentada como roadmap.

    Nenhuma chave/segredo real aparece nesta doc — todos os exemplos usam placeholders.

## Base URL

```
https://api.provatopia.com    # produção (a confirmar)
http://localhost:3333          # desenvolvimento (atrás de túnel cloudflared/ngrok)
```

## Autenticação em 30 segundos

A API usa **JWT**. Você cria uma conta, faz login, recebe um `token` e o envia em todas as chamadas protegidas:

```
Authorization: Bearer <seu-jwt>
```

O token expira em **7 dias**. Detalhes em [Autenticação](authentication.md).

## Quickstart

```bash
# 1. Criar conta
curl -X POST https://api.provatopia.com/user \
  -H "Content-Type: application/json" \
  -d '{"name":"Ana","email":"ana@exemplo.com","password":"segredo123"}'

# 2. Login → recebe { token, user }
curl -X POST https://api.provatopia.com/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ana@exemplo.com","password":"segredo123"}'

# 3. Buscar questões (não respondidas ainda)
curl "https://api.provatopia.com/questions?topic=Matemática&limit=5" \
  -H "Authorization: Bearer <seu-jwt>"

# 4. Enviar um simulado
curl -X POST https://api.provatopia.com/quiz/submit \
  -H "Authorization: Bearer <seu-jwt>" \
  -H "Content-Type: application/json" \
  -d '{"answers":[{"question_id":"uuid","user_answer":"C"}]}'
```

## Mapa

- [Autenticação](authentication.md) — registro, login, JWT, rate limits
- [Planos & Cobrança](plans-billing.md) — free vs premium, MercadoPago
- [Referência da API](api-reference.md) — todos os endpoints
- [Partner API (planejado)](partner-api.md) — venda de acesso por key
- [Erros](errors.md) — convenções de erro
