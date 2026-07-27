# Referência da API

Base URL: `https://api.provatopia.com/v1` · Autenticação: header `Authorization: Bearer pt_live_...` ([detalhes](authentication.md)).

---

## `GET /questions` — exibir/listar questões

Retorna questões do banco, com filtros e paginação.

**Query params**

| Param | Tipo | Descrição |
|---|---|---|
| `subject` | string | filtra por disciplina (ex.: `aws-ai`) |
| `topic` | string | filtra por tópico |
| `difficulty` | enum | `easy` \| `medium` \| `hard` |
| `type` | enum | `multiple_choice` \| `true_false` \| `open` |
| `language` | string | `pt-BR` (default) \| `en-US` |
| `limit` | int | 1–100 (default 20) |
| `cursor` | string | paginação (opaco) |

**Requisição**

```bash
curl "https://api.provatopia.com/v1/questions?subject=aws-ai&difficulty=medium&limit=2" \
  -H "Authorization: Bearer pt_live_xxx"
```

**Resposta `200`**

```json
{
  "data": [
    {
      "id": "q_9f2a...",
      "subject": "aws-ai",
      "topic": "Amazon Bedrock",
      "difficulty": "medium",
      "type": "multiple_choice",
      "language": "pt-BR",
      "statement": "Qual serviço permite acessar múltiplos foundation models por uma única API?",
      "options": [
        { "key": "A", "text": "Amazon SageMaker" },
        { "key": "B", "text": "Amazon Bedrock" },
        { "key": "C", "text": "Amazon EC2" },
        { "key": "D", "text": "AWS Lambda" }
      ],
      "answer": "B",
      "explanation": "Bedrock oferece FMs de vários provedores via API única.",
      "tags": ["bedrock", "foundation-models"],
      "source": "provatopia"
    }
  ],
  "next_cursor": "eyJvZmZzZXQiOjJ9",
  "has_more": true,
  "credits_used": 1
}
```

---

## `GET /questions/{id}` — questão única

```bash
curl https://api.provatopia.com/v1/questions/q_9f2a... \
  -H "Authorization: Bearer pt_live_xxx"
```

Retorna o mesmo objeto `question` do endpoint anterior. Não encontrada → `404 not_found`.

---

## `POST /questions/generate` — gerar questões por IA

Gera novas questões sob demanda. **Consome créditos por questão gerada** (ver [Preços](pricing.md)).

**Body**

| Campo | Obrigatório | Descrição |
|---|---|---|
| `topic` | sim | assunto das questões |
| `count` | sim | quantas gerar (máx. 20 por chamada) |
| `difficulty` | não | `easy` \| `medium` \| `hard` (default `medium`) |
| `type` | não | default `multiple_choice` |
| `language` | não | default `pt-BR` |
| `with_explanation` | não | inclui explicação (default `true`) |

**Requisição**

```bash
curl -X POST https://api.provatopia.com/v1/questions/generate \
  -H "Authorization: Bearer pt_live_xxx" \
  -H "Content-Type: application/json" \
  -d '{"topic":"Bedrock e RAG","count":5,"difficulty":"medium"}'
```

**Resposta `200`**

```json
{
  "data": [
    {
      "id": "q_gen_1",
      "statement": "...",
      "options": [ { "key": "A", "text": "..." } ],
      "answer": "C",
      "explanation": "..."
    }
  ],
  "count": 5,
  "credits_used": 50,
  "credits_remaining": 4930
}
```

---

## `GET /subjects` — catálogo (grátis)

```json
{
  "data": [
    { "subject": "aws-ai", "label": "AWS AI Practitioner", "topics": ["Bedrock", "SageMaker", "IA Responsável"] }
  ],
  "credits_used": 0
}
```

---

## `GET /usage` — saldo e consumo (grátis)

```json
{
  "plan": "pro",
  "credits_remaining": 4930,
  "credits_included": 5000,
  "period_start": "2026-07-01",
  "period_end": "2026-07-31",
  "credits_used": 70
}
```
