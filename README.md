# CloudOps API — CI/CD Pipeline Challenge

API REST simples em Python (FastAPI), construída como base para um pipeline de CI/CD completo com GitHub Actions: testes automatizados, análise de segurança estática (SAST) com Semgrep, build de imagem Docker e publicação automática no Docker Hub — seguindo o fluxo de branching GitFlow.

## Sobre o projeto

Este repositório simula o cenário de uma startup lançando sua primeira API, com o objetivo de implementar um pipeline de entrega profissional, seguro e automatizado, do commit até a imagem publicada.

**Endpoints disponíveis:**

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/` | Retorna uma mensagem de saudação e status da API |
| `GET` | `/health` | Retorna o status de saúde da aplicação |

## Stack utilizada

- **Python 3.12**
- **FastAPI** — framework web
- **Uvicorn** — servidor ASGI
- **Pytest** — testes unitários
- **Docker** — containerização (multi-stage build)
- **GitHub Actions** — automação de CI/CD
- **Semgrep** — análise de segurança estática (SAST)

## Como executar localmente

### 1. Clonar o repositório

```bash
git clone https://github.com/mthbrito/cicd-pipeline-challenge.git
cd cicd-pipeline-challenge
```

### 2. Instalar as dependências

```bash
pip install -r requirements.txt
```

> No Windows, se o comando `pip` não for reconhecido, use `python -m pip install -r requirements.txt`.

### 3. Rodar a aplicação

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

A API estará disponível em `http://localhost:8000`, com documentação automática (Swagger) em `http://localhost:8000/docs`.

### 4. Rodar os testes

```bash
pytest tests/ -v
```

## Executando com Docker

### Build da imagem

```bash
docker build -t cicd-pipeline-challenge .
```

### Rodar o container

```bash
docker run -p 8000:8000 cicd-pipeline-challenge
```

A imagem utiliza **multi-stage build**, separando a etapa de instalação de dependências da imagem final de runtime, resultando em uma imagem mais enxuta e otimizada.

## Estrutura do repositório

```
├── .github/
│   └── workflows/
│       ├── ci.yml          # Testes + SAST (Pull Requests)
│       └── cd.yml          # Build + Push Docker (merge em main)
├── app/
│   ├── __init__.py
│   └── main.py              # Código da API
├── tests/
│   └── test_main.py         # Testes unitários
├── Dockerfile
├── requirements.txt
└── README.md
```

## Pipeline de CI/CD

O projeto segue a estratégia de branching **GitFlow**:

- `main` — código de produção (protegida)
- `develop` — integração de features
- `feature/*` — desenvolvimento de novas funcionalidades

### CI (Integração Contínua)

Disparado em **Pull Requests para `develop` e `main`**, executando:

1. **Testes unitários** com `pytest` — o pipeline falha se algum teste não passar
2. **Análise de segurança estática (SAST)** com **Semgrep**, reportando findings nos logs

### CD (Entrega Contínua)

Disparado **apenas no merge para `main`**, executando:

1. Build da imagem Docker da API
2. Publicação automática da imagem no **Docker Hub**, com duas tags:
   - `latest`
   - o **SHA do commit**

### Fluxo visual do pipeline

```
feature/*  --PR-->  develop  --PR-->  main
    |                  |                |
    v                  v                v
   CI              CI (testes+SAST)   CI + CD
(testes+SAST)                      (build+push Docker Hub)
```

## Segurança

- Credenciais do Docker Hub configuradas como **GitHub Secrets**, nunca expostas no código ou nos logs
- Container roda sem dependências desnecessárias graças ao build multi-stage

## Autor

Projeto desenvolvido como parte de um desafio prático de aprendizado em DevOps / CI-CD.