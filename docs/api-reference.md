# Referência da API

Base URL: `https://api.provatopia.com` · Auth: `Authorization: Bearer <jwt>` (exceto rotas públicas).

**Legenda:** 🔓 pública · 🔒 requer JWT · ⭐ requer **Premium**.

O objeto **`question`** (retornado por vários endpoints) tem: `id`, `topic`, `assunto`, `difficulty`, `support_text`, `question_text`, `options` (JSON), `correct_answer` (ex.: `"C"`), `explanation`, `figure_svg`, `status`, `exam_id`, `exam_number`, `created_at`.

---

## Perfil & Conta

### `GET /auth/me` 🔒
Perfil do usuário logado. **`200`** → `{ id, name, email, plan, xp, stats: { totalQuestions, correctAnswers, quizzesCompleted, averageScore } }`. Erros: `401`, `404 User not found`.

### `PUT /me` 🔒
Atualiza perfil. Body (ao menos um): `name`, `email`, `password` (aplicado só se ≥ 6 chars). **`200`** → `{ id, name, email, plan, xp }`. Erros: `400 Nothing to update`, `409 Email already in use`.

### `GET /me/entitlements` 🔒
Plano, entitlements, uso e assinatura — ver [Planos & Cobrança](plans-billing.md#consultar-entitlements).

---

## Questões

### `GET /questions` 🔒
Lista questões **ainda não respondidas** pelo usuário. Query: `topic`, `difficulty`, `status` (default `approved`), `limit`, `randomize`/`random` (`"true"`). **`200`** → array de `question`.

### `GET /questions/browse` 🔒
Navegação paginada (10/página). Query: `topic`, `assunto`, `difficulty`, `hasFigure` (`true`/`false`), `search`, `status` (`all`|`unanswered`|`wrong`), `page`. **`200`** → `{ page, pageSize: 10, total, totalPages, items: [ { ...question, commentCount, bookmarked } ] }`.

### `GET /questions/:id/stats` 🔒
Estatísticas da questão. **`200`** → `{ total, correct, accuracy, distribution: {A,B,C,D,E}, perceivedDifficulty: { facil, medio, dificil, total, mine } }`.

### `POST /questions/:id/report` 🔒
Reportar problema. Body: `reason` (`errada|ambigua|gabarito|offtopic|outro`), `detail` (≤ 500). **`201`** `{ "ok": true }`. Erro: `400 Motivo inválido`.

### `POST /questions/:id/difficulty-rating` 🔒
Votar dificuldade percebida. Body: `rating` (`facil|medio|dificil`). **`200`** `{ "ok": true, "rating": "medio" }`.
### `DELETE /questions/:id/difficulty-rating` 🔒 → **`204`**.

### `POST /questions/:id/bookmark` 🔒
Salvar em um caderno. Body: `caderno` (opcional, default `"Salvos"`). **`201`** `{ "ok": true, "caderno": "Salvos" }`.
### `DELETE /questions/:id/bookmark` 🔒 — query `caderno` (omitido = remove de todos). **`204`**.

### `GET /questions/:id/comments` 🔒
Comentários (1 nível de resposta). **`200`** → array `{ id, body, createdAt, author, authorId, isMine, parentId, replies: [...] }`.
### `POST /questions/:id/comments` 🔒 — body `body` (1–1000), `parentId` (opcional). **`201`** o comentário.
### `DELETE /comments/:id` 🔒 — **`204`**. Erros: `403` (não é seu), `404`.

### `GET /questions/:id/note` 🔒 → `{ body, updatedAt }` (nota pessoal na questão).
### `PUT /questions/:id/note` 🔒 — body `body` (≤ 4000; vazio deleta). → `{ body, updatedAt }`.

### `GET /questions/:id/related` 🔒
Questões relacionadas (mesmo assunto/tópico). **`200`** → `{ assunto, topic, items: [question] }`.

### `GET /me/graph` 🔒 ⭐
Grafo de conhecimento. **`200`** → `{ topics: [], nodes: [ { id, topic, assunto, total, accuracy } ] }`.

---

## Cadernos & Notas

### `GET /me/cadernos` 🔒 → array `{ id, name, emoji, cover, description, count }`.
### `POST /me/cadernos` 🔒 — body `name` (≤ 80, obrigatório), `emoji` (default 📓), `cover` (`sunset|ocean|forest|grape|coral|mono`), `description`. **`201`**. Erro: `409 Já existe um caderno com esse nome`.
### `PATCH /me/cadernos/:id` 🔒 — body opcionais `name`, `emoji`, `cover`, `description`. **`200`**.
### `DELETE /me/cadernos/:id` 🔒 — **`204`**.
### `POST /me/cadernos/:id/questions` 🔒 — body `question_id`. **`201`** `{ "ok": true, "caderno": "<name>" }`.
### `GET /me/cadernos/:caderno/questions` 🔒 — (`:caderno` = **nome**). → array de `question` + `note`.

### `GET /me/cadernos/:caderno/notes` 🔒 → array `{ id, title, body, assuntos, linkedQuestions, updatedAt }`.
### `POST /me/cadernos/:caderno/notes` 🔒 — body `title` (≤160), `body` (≤50000), `assuntos` (≤20), `questionIds` (≤50). **`201`**.
### `GET /me/notes/:id` 🔒 → `{ id, caderno, title, body, assuntos, questions: [question], updatedAt }`.
### `PATCH /me/notes/:id` 🔒 — `questionIds` substitui o vínculo. **`200`** `{ "ok": true }`.
### `DELETE /me/notes/:id` 🔒 — **`204`**.

---

## Simulados & Quiz

### `POST /quiz/submit` 🔒
Envia respostas de um simulado. Cada envio = 1 simulado (free: **máx. 1/dia**). Body: `answers: [ { question_id, user_answer } ]`.
**`200`** → `{ saved, correct, total, xpEarned, xpTotal }`.
Erros: `400` formato inválido · `402 {"error":"Limite de 1 simulado(s) por dia no plano grátis...","upgrade":true}`.

---

## Revisão espaçada (FSRS)

### `GET /me/wrong-questions` 🔒
Erros que estão "vencidos" para revisão (pior retenção primeiro). **`200`** → array `{ question, lastAttemptAt, wrongStreak, nextReviewAt, retrievability }`.

### `GET /me/review` 🔒
Fila de revisão (todas as respondidas que venceram no FSRS). **`200`** → `{ dueCount, items: [...] }`.
### `POST /me/review/submit` 🔒 — body `answers: [ { question_id, user_answer } ]` (sem limite diário). **`200`** → `{ saved, correct, total, xpEarned, xpTotal, scheduled: [ { question_id, isCorrect, nextReviewAt, retrievability } ] }`.

### `GET /me/quiz-history` 🔒
Sessões agrupadas. **`200`** → array `{ id, subject, date, score, total }`.

### `GET /me/retention` 🔒 ⭐
Análise de retenção. **`200`** → `{ totalAnswered, totalErrors, accuracy, dueCount, byMateria: [...], byAssunto: [...], weakest: [...], curve: [...], curveStabilityDays }`.

---

## Plano de estudo, Metas & Desafio

### `GET /me/study-plan` 🔒 ⭐
Sessão guiada do dia (~10 questões). **`200`** → `{ goal, score, streak, session: { targetCount, estMinutes, questions: [...], items: [ { questionId, bucket: "revisar|reforcar|avancar", reason } ] } }`.
### `POST /me/study-plan/complete` 🔒 ⭐ — sem body. **`200`** → `{ streak, best, completedToday: true, xpAwarded }` (+50 XP, idempotente/dia).

### `GET /me/daily-challenge` 🔒
Questão do dia (igual para todos). **`200`** → `{ date, question, doneToday, myAnswer, isCorrect, streak, best }`.
### `POST /me/daily-challenge/submit` 🔒 — body `user_answer` (letra A–E). **`200`** → `{ alreadyDone, isCorrect, correctAnswer, streak, best, xpAwarded }`.

### `GET /me/goal` 🔒 ⭐ → `{ goal: {...}|null, options: [ { course, university, cutoff } ] }`.
### `PUT /me/goal` 🔒 ⭐ — body `course`, `university` (obrigatórios), `vestibular`, `cutoff`, `examDate`. **`200`** → `{ goal }`.

### `GET /me/score-estimate` 🔒 ⭐
Estimativa de nota (estilo TRI, pela dificuldade da comunidade). **`200`** → `{ answered, areas: [ { area, score, answered } ], overall, cutoffs: [ { course, university, year, cutoff, gap, reached } ], disclaimer }`.

---

## Redação

### `GET /essays/config` 🔒 → `{ vestibulares: [ { key, name, redacao: { max, instrucoes, competencias: [...] } } ] }` (ENEM: 1000 pts, 5 competências).
### `POST /essays` 🔒 ⭐
Corrige uma redação por IA (gpt-4o + RAG). Body: `vestibular` (default `enem`), `tema` (obrigatório), `body` (≥ 50 palavras, ≤ 8000). **`201`** → `{ id, total, max, competencias: [ { id, titulo, score, max, feedback } ], comentarioGeral, pontosFortes, aMelhorar, sugestaoReescrita, tema, createdAt }`. Erros: `400` tema/tamanho · `502` IA indisponível.
### `GET /essays` 🔒 → array `{ id, vestibular, tema, total, max, createdAt }`.
### `GET /essays/:id` 🔒 → redação completa com `competencias` e `feedback`.

---

## Social & Feed

### `GET /posts` 🔒 — query `page` (10/pág). → `{ page, hasMore, items: [ { id, body, topic, createdAt, author, authorId, isMine, likes, comments, likedByMe } ] }`.
### `POST /posts` 🔒 — body `body` (≤ 2000), `topic` (opcional). **`201`**.
### `DELETE /posts/:id` 🔒 — **`204`** (`403` se não for seu).
### `POST /posts/:id/like` 🔒 → `{ likes, likedByMe: true }` · `DELETE /posts/:id/like` 🔒 → `{ likes, likedByMe: false }`.
### `GET /posts/:id/comments` 🔒 → array de comentários · `POST /posts/:id/comments` 🔒 — body `body` (≤ 1000). **`201`**.

### `GET /feed` 🔒 → `{ activity: [ {kind:"comment"|"new_question", ...} ] }` (máx. 20).
### `GET /feed/questions` 🔒 — query `page` (5/pág). → `{ page, hasMore, items: [ { ...question, commentCount } ] }`.

---

## Provas, Tópicos & Ranking

### `GET /exams` 🔒 → array `{ id, name, year, day, area, source, questionCount }` (só publicadas).
### `GET /exams/:id/questions` 🔒 → `{ exam: {...}, questions: [question] }`.
### `GET /topics` 🔒 → array `{ topic, available }` (questões ainda não respondidas por tópico).
### `GET /ranking` 🔒 → `{ top: [ { rank, id, name, xp, totalQuestions, correctAnswers, accuracy, isMe } ], me, totalUsers }` (top 50 por XP).

---

## Cobrança

Ver [Planos & Cobrança](plans-billing.md): `POST /billing/checkout` 🔒 · `POST /billing/webhook` 🔓 (MercadoPago) · `POST /billing/cancel` 🔒.

---

## Beta

### `POST /beta-signup` 🔓 (rate-limited 10/15min)
Inscrição na landing. Body: `name` (2–120), `email`, `exam` (`enem|fuvest|unicamp|uerj|outro`, default `outro`). **`201`** `{ "ok": true }`.
