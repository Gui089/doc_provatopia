# doc_provatopia

Documentação da **API do ProvaTopia** — API para exibir e gerar questões, com cobrança por uso (créditos).

Site: **https://gui089.github.io/doc_provatopia/**

## Rodar localmente

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Abra `http://127.0.0.1:8000`.

## Publicar

O site é publicado no GitHub Pages a partir da branch `gh-pages`:

```bash
mkdocs gh-deploy --force
```

> Esta é uma versão inicial (v0). Valores como domínio, tabela de preços e limites são ilustrativos. Nenhuma chave real está na documentação — apenas placeholders.
