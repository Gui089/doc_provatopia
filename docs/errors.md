# Erros

A maioria dos erros retorna JSON `{ "error": "<mensagem>" }`. O gate premium adiciona `"upgrade": true`.

## Códigos comuns

| HTTP | Quando | Corpo (exemplo) |
|---|---|---|
| 400 | Requisição inválida (body/params) | `{"error":"answers must be an array"}` |
| 401 | Sem JWT / token inválido ou expirado | `{"error":"Missing or invalid Authorization header"}` · `{"error":"Invalid or expired token"}` |
| 402 | Recurso premium ou limite do free | `{"error":"Recurso exclusivo do Premium","upgrade":true}` |
| 403 | Sem permissão (ex.: apagar item de outro) | `{"error":"Você só pode apagar seus próprios comentários"}` |
| 404 | Recurso não encontrado | `{"error":"Questão não encontrada"}` |
| 409 | Conflito (e-mail/caderno duplicado) | `{"error":"Email already in use"}` |
| 429 | Rate limit (login/registro/beta) | `{"error":"Muitas tentativas. Tente novamente em alguns minutos."}` |
| 500 | Falha interna | `{"error":"Failed to fetch questions"}` |
| 502 | IA indisponível (correção de redação) | `{"error":"..."}` |
| 503 | MercadoPago não configurado | `{"error":"Pagamento indisponível..."}` |

## Boas práticas

- Trate `401` renovando o login (token expira em 7 dias).
- Trate `402` com `upgrade: true` oferecendo o Premium.
- Respeite o `Retry-After` nos `429`.
