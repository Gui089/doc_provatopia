# Preços & Créditos

A API cobra por uso via **créditos**. Cada chave tem um saldo por plano, e cada chamada consome créditos.

## Custo por operação

| Operação | Custo (créditos) | Observação |
|---|---|---|
| `GET /questions` | **1** por requisição | leitura barata |
| `GET /questions/{id}` | **1** | leitura unitária |
| `POST /questions/generate` | **10** por questão gerada | custo de IA |
| `GET /subjects` · `GET /usage` | **0** | metadados grátis |

!!! note "Valores ilustrativos"
    A tabela acima e os planos abaixo são um rascunho inicial e serão confirmados.

## Planos (exemplo)

| Plano | Créditos/mês |
|---|---|
| Free | 100 |
| Pro | 5.000 |
| Scale | 50.000 |

## Como o consumo é reportado

Cada resposta traz o consumo:

```json
{ "credits_used": 50, "credits_remaining": 4930 }
```

E nos headers:

```
X-Credits-Remaining: 4930
```

- Saldo insuficiente → `402 insufficient_credits`.
- Consulte o saldo a qualquer momento em [`GET /usage`](api-reference.md#55-get-usage-saldo-e-consumo).

## Rate limiting

| Grupo | Limite |
|---|---|
| Leitura (`GET /questions*`) | 60 req/min |
| Geração (`POST /questions/generate`) | 10 req/min |

Headers de resposta:

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 57
```

Estouro → `429 rate_limited` + `Retry-After: <segundos>`.
