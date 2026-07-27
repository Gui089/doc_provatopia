# Erros

Todos os erros seguem o mesmo formato JSON:

```json
{
  "error": {
    "type": "billing_error",
    "code": "insufficient_credits",
    "message": "Saldo de créditos insuficiente para gerar 5 questões."
  }
}
```

## Códigos

| HTTP | `code` | Quando |
|---|---|---|
| 400 | `invalid_request` | body/params inválidos |
| 401 | `invalid_api_key` | chave ausente ou incorreta |
| 402 | `insufficient_credits` | saldo insuficiente |
| 404 | `not_found` | recurso inexistente |
| 429 | `rate_limited` | excedeu o rate limit |
| 500 | `internal_error` | falha interna |

## Boas práticas

- Trate `429` respeitando o header `Retry-After`.
- Trate `402` direcionando o usuário para recarregar créditos.
- Nunca exponha a mensagem de erro crua com dados sensíveis ao usuário final.
