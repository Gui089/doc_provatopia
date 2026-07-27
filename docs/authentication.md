# Autenticação

A API usa **JWT** (JSON Web Token). Fluxo: criar conta → login → usar o `token` como Bearer.

## Registro

`POST /user` — público (rate-limited: 20 req / 15 min por IP)

| Campo | Tipo | Regras |
|---|---|---|
| `name` | string | obrigatório, ≥ 2 chars |
| `email` | string | obrigatório, e-mail válido (lowercased) |
| `password` | string | obrigatório, ≥ 6 chars |
| `plan` | string | opcional, default `"free"` |

```bash
curl -X POST https://api.provatopia.com/user \
  -H "Content-Type: application/json" \
  -d '{"name":"Ana","email":"ana@exemplo.com","password":"segredo123"}'
```

**`201`** → `{ "id": "uuid", "name": "Ana", "email": "ana@exemplo.com", "plan": "free", "xp": 0 }`
(o `password` nunca é retornado)

Erros: `400` nome/e-mail/senha inválidos · `409` e-mail já cadastrado.

## Login

`POST /auth/login` — público (rate-limited: 20 req / 15 min por IP)

```bash
curl -X POST https://api.provatopia.com/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ana@exemplo.com","password":"segredo123"}'
```

**`200`**
```json
{
  "token": "eyJhbGciOi...",
  "user": {
    "id": "uuid", "name": "Ana", "email": "ana@exemplo.com",
    "plan": "free", "xp": 120,
    "stats": { "totalQuestions": 40, "correctAnswers": 31, "quizzesCompleted": 5, "averageScore": 78 }
  }
}
```

Erros: `400 {"error":"Email and password are required"}` · `401` credenciais inválidas.

## Usando o token

Envie o JWT em toda rota protegida:

```
Authorization: Bearer eyJhbGciOi...
```

- Validade: **7 dias**.
- Header ausente/malformado → `401 {"error":"Missing or invalid Authorization header"}`.
- Token inválido/expirado → `401 {"error":"Invalid or expired token"}`.

!!! warning "Rate limiting de credenciais"
    `POST /user` e `POST /auth/login` aceitam **20 tentativas por IP a cada 15 min**; `POST /beta-signup`, **10 / 15 min**. Excedeu → `429 {"error":"Muitas tentativas. Tente novamente em alguns minutos."}`.
