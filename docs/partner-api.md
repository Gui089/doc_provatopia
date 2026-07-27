# Partner API (planejado)

!!! warning "Status: PLANEJADO — ainda não implementado"
    Esta página descreve um produto **futuro**: expor o conteúdo do ProvaTopia para **terceiros** via **API key**, com cobrança por uso. **Nada aqui existe no backend atual** — a API de hoje usa [JWT de usuário](authentication.md) e assinatura [MercadoPago](plans-billing.md). Isto é o **norte de design**, não contrato vigente.

## Objetivo

Permitir que outros produtos (cursinhos, apps, plataformas) integrem o **banco de questões** e a **geração/correção por IA** do ProvaTopia, comprando uma **chave de API** e pagando por **créditos de uso** — modelo estilo OpenAI/Anthropic.

## Autenticação (planejada)

Diferente do JWT de usuário: uma **chave de parceiro**, emitida no painel.

```
Authorization: Bearer pt_live_xxxxxxxxxxxxxxxx
```

- `pt_test_...` → sandbox, sem cobrança.
- `pt_live_...` → produção, consome créditos.

## Cobrança (planejada) — créditos

| Operação | Custo (créditos) — *ilustrativo* |
|---|---|
| Servir questões (`GET /partner/questions`) | 1 / requisição |
| Gerar questões por IA (`POST /partner/questions/generate`) | 10 / questão |
| Corrigir redação (`POST /partner/essays/grade`) | por redação |

## Endpoints propostos (rascunho)

### `GET /partner/questions` — servir do banco
Filtros por `topic`, `assunto`, `difficulty`, paginação. Retorna questões do acervo aprovado.

### `POST /partner/questions/generate` — gerar por IA
Body: `topic`, `count`, `difficulty`, `type`. Gera questões novas (reaproveita o pipeline de IA interno). Consome créditos por questão.

### `POST /partner/essays/grade` — correção de redação
Reaproveita o `essayGrader` (gpt-4o + RAG) exposto como serviço.

### `GET /partner/usage` — saldo e consumo
Créditos restantes, consumo do período, plano do parceiro.

## Decisões a fechar antes de construir

- [ ] Emissão/rotação de chaves + escopos (read-only vs generate)
- [ ] Modelo de créditos ↔ preço (R$) e planos de parceiro
- [ ] Rate limits por chave
- [ ] Isolar o acervo exposto (o que pode ir para terceiros vs. conteúdo interno)
- [ ] Geração síncrona vs. assíncrona (webhook) para lotes grandes
- [ ] Termos de uso / atribuição do conteúdo

> Quando decidir construir, esta página vira o spec inicial — os endpoints acima entram no backend sob o prefixo `/partner/*`, separados da API JWT do app.
