# ProvaTopia API

Bem-vindo à documentação da **API do ProvaTopia** — a API para **exibir** e **gerar questões** de prova, com **cobrança por uso** (modelo de créditos).

!!! note "Rascunho v0"
    Versão inicial da documentação. Valores como domínio, tabela de preços e limites são **ilustrativos** e ainda serão confirmados. Nenhuma chave real aparece aqui — todos os exemplos usam placeholders (`pt_live_...`).

## O que você pode fazer

- **Exibir questões** de um banco existente, com filtros (disciplina, tópico, dificuldade, tipo).
- **Gerar questões** novas por IA, sob demanda.
- Acompanhar seu **consumo e saldo de créditos**.

## Base URL

```
https://api.provatopia.com/v1
```

## Quickstart

1. Pegue sua chave de API no painel (`pt_live_...`).
2. Faça sua primeira chamada — listar questões:

```bash
curl "https://api.provatopia.com/v1/questions?subject=aws-ai&limit=1" \
  -H "Authorization: Bearer pt_live_xxxxxxxx"
```

3. Gere questões por IA:

```bash
curl -X POST https://api.provatopia.com/v1/questions/generate \
  -H "Authorization: Bearer pt_live_xxxxxxxx" \
  -H "Content-Type: application/json" \
  -d '{"topic":"Bedrock e RAG","count":5,"difficulty":"medium"}'
```

## Próximos passos

- [Autenticação](authentication.md) — como usar sua chave de API
- [Preços & Créditos](pricing.md) — quanto custa cada operação
- [Referência da API](api-reference.md) — todos os endpoints
- [Erros](errors.md) — códigos e tratamento
