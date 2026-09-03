# ServeRest Cypress Automation Challenge

Projeto de automação E2E de frontend e API para o teste técnico ServeRest, utilizando Cypress + JavaScript.

## Stack

- Cypress
- JavaScript
- Page Object Model (POM)
- Service Object Pattern
- Custom Commands
- Fixtures
- Mochawesome
- GitHub Actions

## Cobertura

### Frontend

1. **FE-001** — Login com usuário válido.
2. **FE-002** — Login com senha inválida.
3. **FE-003** — Cadastro de novo usuário com e-mail único.

### API

1. **API-001** — Criar usuário com dados válidos (`POST /usuarios`).
2. **API-002** — Rejeitar e-mail duplicado (`POST /usuarios`).
3. **API-003** — Rejeitar login com credenciais inválidas (`POST /login`).

## Pré-requisitos

- Node.js LTS
- npm
- Git
- Chrome (opcional para execução headless/CI)

## Instalação

```bash
npm install
npx cypress verify
```

## Execução

Abrir interface:

```bash
npm run cy:open
```

Todos os testes:

```bash
npm test
```

Frontend:

```bash
npm run test:frontend
```

API:

```bash
npm run test:api
```

Chrome:

```bash
npm run test:chrome
```

## Relatório

A suíte utiliza Mochawesome em JSON e gera um relatório HTML consolidado.

```bash
npm run report
```

O HTML será gerado em:

```text
reports/html/index.html
```

O `mochawesome-merge` é usado para consolidar os JSONs gerados por cada spec antes da geração do HTML.

## Arquitetura

```text
cypress/
├── e2e/
│   ├── frontend/
│   └── api/
├── fixtures/
├── pages/
├── services/
└── support/
```

### Pages

Encapsulam seletores e ações da interface.

### Services

Encapsulam chamadas HTTP e deixam os testes focados no comportamento esperado.

### Fixtures

Centralizam massa de dados estável.

### Custom Commands

Centralizam operações reutilizáveis, como criação de usuário via API e geração de e-mail único.

## CI/CD

O workflow `.github/workflows/cypress.yml` executa automaticamente em push para `main`/`develop`, Pull Requests para `main` e manualmente via `workflow_dispatch`.

O workflow:

1. Faz checkout do código.
2. Executa Cypress em Chrome.
3. Gera o relatório Mochawesome mesmo quando os testes falham.
4. Publica relatórios, screenshots e vídeos como GitHub Actions artifacts.

A GitHub Action oficial do Cypress recomenda `cypress-io/github-action@v7` e fornece instalação/cache de dependências. Consulte a documentação oficial do Cypress para evolução do pipeline.

## Boas práticas aplicadas

- Separação de responsabilidades.
- Testes independentes.
- Massa dinâmica para evitar colisão de e-mail.
- Assertions de status HTTP e regras de negócio.
- Cenários positivos e negativos.
- Sem `cy.wait()` fixo.
- Seletores `data-testid`.
- Secrets fora do código.
- Artefatos preservados em falhas de CI.
- Commits pequenos e sem mensagens genéricas.

## Git

```bash
git init
git add .
git commit -m "chore: initialize cypress automation project"
git branch -M main
git remote add origin https://github.com/<SEU-USUARIO>/<SEU-REPOSITORIO>.git
git push -u origin main
```

Sugestão de commits:

```text
chore: initialize cypress automation project
test: add frontend login scenarios
test: add frontend registration scenario
test: add api user scenarios
test: add api invalid login scenario
feat: add page objects and service objects
feat: add reusable custom commands
ci: add github actions workflow
docs: update project readme
```

## Endpoints

Frontend: https://front.serverest.dev/

API: https://serverest.dev/

Swagger: https://serverest.dev/

## Observação

A aplicação ServeRest é um ambiente público de estudos e os dados persistidos podem sofrer alterações externas. Por isso, os cenários que criam usuários usam e-mails únicos em cada execução.
