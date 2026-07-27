# Autenticação

Toda requisição exige uma chave de API no header `Authorization`:

```
Authorization: Bearer pt_live_xxxxxxxxxxxxxxxxxxxx
```

## Tipos de chave

| Chave | Uso | Cobrança |
|---|---|---|
| `pt_test_...` | Sandbox — retorna dados fictícios | Não consome créditos |
| `pt_live_...` | Produção | Consome créditos reais |

- A chave identifica a **conta**, o **plano** e o **saldo de créditos**.
- Requisição sem chave válida → `401 invalid_api_key`.

!!! danger "Nunca exponha uma chave `pt_live_...`"
    Não coloque a chave no front-end nem em repositório público. Use no **servidor** ou via **variável de ambiente**. Se vazar, revogue no painel e gere outra.

## Exemplo

```bash
curl "https://api.provatopia.com/v1/questions?limit=1" \
  -H "Authorization: Bearer pt_live_xxxxxxxx"
```
