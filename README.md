# ServeRest Cypress E2E Automation Challenge

Automação de testes **E2E para frontend e API** utilizando **Cypress + JavaScript**, aplicada à plataforma ServeRest.

- Frontend: https://front.serverest.dev/
- API / Swagger: https://serverest.dev/
- Framework: Cypress
- Linguagem: JavaScript
- CI/CD: GitHub Actions
- Reporter: Mochawesome
- Arquitetura: Page Object Model (POM) + Service Object Pattern + Custom Commands + Fixtures

> Objetivo: demonstrar uma suíte de automação organizada, escalável, legível e preparada para execução local e em pipeline CI.

---

## 1. Cobertura do desafio

### Frontend — 3 cenários E2E

| ID | Cenário | Tipo |
|---|---|---|
| FE-001 | Login com usuário válido | Happy path |
| FE-002 | Login com senha inválida | Cenário negativo |
| FE-003 | Cadastro de novo usuário | Happy path / validação |

### API — 3 cenários

| ID | Cenário | Endpoint | Tipo |
|---|---|---|---|
| API-001 | Criar usuário com dados válidos | `POST /usuarios` | Happy path |
| API-002 | Tentar criar usuário com e-mail já cadastrado | `POST /usuarios` | Cenário negativo |
| API-003 | Realizar login com credenciais inválidas | `POST /login` | Cenário negativo |

A suíte pode ser expandida posteriormente para produtos, carrinhos, autenticação administrativa, contratos de API e testes de regressão.

---

## 2. Estratégia de automação

A estrutura foi pensada para separar responsabilidades:

```text
Test Specs
   |
   +-- Page Objects ------------> Elementos e ações da UI
   |
   +-- Service Objects ----------> Operações de API
   |
   +-- Custom Commands ----------> Ações Cypress reutilizáveis
   |
   +-- Fixtures -----------------> Massa de dados
   |
   +-- Support -------------------> Configuração global
```

### Page Object Model

Os elementos e ações das páginas ficam encapsulados nos Page Objects.

Exemplo conceitual:

```javascript
loginPage.login(email, password);
loginPage.assertLoginSuccess();
```

Isso evita espalhar seletores pelos testes e facilita manutenção quando a interface muda.

### Service Object Pattern

Operações de API são centralizadas em serviços:

```javascript
usuariosService.create(user);
usuariosService.getById(userId);
loginService.login(email, password);
```

Dessa forma, os testes ficam focados no comportamento esperado, enquanto os detalhes HTTP ficam encapsulados.

### Custom Commands

Ações comuns do Cypress podem ser reutilizadas:

```javascript
cy.login(email, password);
cy.createUser(user);
```

### Fixtures

Dados estáveis de teste ficam centralizados:

```text
cypress/
└── fixtures/
    ├── users.json
    └── products.json
```

Dados dinâmicos, como e-mail de usuário, devem ser gerados durante a execução para evitar colisões entre execuções.

---

## 3. Pré-requisitos

Recomendado:

- Node.js LTS
- npm
- Git
- Google Chrome ou outro navegador suportado pelo Cypress
- Conta GitHub para publicação do projeto

Verifique as versões:

```bash
node --version
npm --version
git --version
```

---

## 4. Instalação do projeto

Clone o repositório:

```bash
git clone https://github.com/<SEU-USUARIO>/<SEU-REPOSITORIO>.git
cd <SEU-REPOSITORIO>
```

Instale as dependências:

```bash
npm ci
```

Para uma instalação inicial sem `package-lock.json`:

```bash
npm install
```

Caso o Cypress tenha sido instalado, mas o binary não esteja disponível:

```bash
npx cypress install
```

Para validar a instalação:

```bash
npx cypress verify
```

---

## 5. Dependências esperadas

Exemplo de instalação das principais dependências:

```bash
npm install --save-dev cypress
npm install --save-dev mochawesome
npm install --save-dev mochawesome-merge
npm install --save-dev mochawesome-report-generator
```

As versões devem ser fixadas no `package-lock.json` para garantir reprodutibilidade no CI.

---

## 6. Estrutura recomendada do projeto

```text
.
├── .github/
│   └── workflows/
│       └── cypress.yml
│
├── cypress/
│   ├── e2e/
│   │   ├── frontend/
│   │   │   ├── login.cy.js
│   │   │   └── cadastro.cy.js
│   │   │
│   │   └── api/
│   │       ├── usuarios.cy.js
│   │       └── login.cy.js
│   │
│   ├── fixtures/
│   │   └── users.json
│   │
│   ├── pages/
│   │   ├── LoginPage.js
│   │   └── CadastroPage.js
│   │
│   ├── services/
│   │   ├── UsuarioService.js
│   │   └── LoginService.js
│   │
│   └── support/
│       ├── commands.js
│       └── e2e.js
│
├── reports/
├── screenshots/
├── videos/
├── .gitignore
├── cypress.config.js
├── package.json
├── package-lock.json
└── README.md
```

---

## 7. Configuração de ambientes

Recomenda-se centralizar URLs no `cypress.config.js`:

```javascript
const { defineConfig } = require('cypress');

module.exports = defineConfig({
  e2e: {
    baseUrl: 'https://front.serverest.dev',
  },

  env: {
    apiUrl: 'https://serverest.dev',
  },

  reporter: 'mochawesome',

  reporterOptions: {
    reportDir: 'reports',
    overwrite: false,
    html: true,
    json: true
  },

  video: true,
  screenshotOnRunFailure: true
});
```

Nos testes de API:

```javascript
cy.request({
  method: 'POST',
  url: `${Cypress.env('apiUrl')}/usuarios`,
  body: payload
});
```

---

## 8. Executar o Cypress em modo interativo

Abrir o Cypress:

```bash
npx cypress open
```

Selecionar:

```text
E2E Testing
    -> Chrome
        -> selecionar o cenário
```

---

## 9. Executar todos os testes em modo headless

```bash
npx cypress run
```

Executar somente frontend:

```bash
npx cypress run --spec "cypress/e2e/frontend/**/*.cy.js"
```

Executar somente API:

```bash
npx cypress run --spec "cypress/e2e/api/**/*.cy.js"
```

Executar em Chrome:

```bash
npx cypress run --browser chrome
```

Executar um arquivo específico:

```bash
npx cypress run --spec "cypress/e2e/frontend/login.cy.js"
```

---

## 10. Scripts recomendados no package.json

Adicionar:

```json
{
  "scripts": {
    "cy:open": "cypress open",
    "cy:run": "cypress run",
    "test": "cypress run",
    "test:frontend": "cypress run --spec \"cypress/e2e/frontend/**/*.cy.js\"",
    "test:api": "cypress run --spec \"cypress/e2e/api/**/*.cy.js\"",
    "test:chrome": "cypress run --browser chrome",
    "report:merge": "mochawesome-merge \"reports/*.json\" -o reports/combined.json",
    "report:generate": "marge reports/combined.json -f index -o reports/html",
    "report": "npm run report:merge && npm run report:generate"
  }
}
```

Depois disso, os comandos ficam mais simples:

```bash
npm test
```

```bash
npm run test:frontend
```

```bash
npm run test:api
```

```bash
npm run test:chrome
```

---

## 11. Relatórios Mochawesome

Executar a suíte gerando resultados:

```bash
npx cypress run --reporter mochawesome
```

Combinar os arquivos JSON:

```bash
npx mochawesome-merge "reports/*.json" -o reports/combined.json
```

Gerar o HTML:

```bash
npx marge reports/combined.json -f index -o reports/html
```

Ou simplesmente:

```bash
npm run report
```

O relatório ficará em:

```text
reports/html/index.html
```

### Evidências

Em caso de falha, o Cypress pode gerar:

```text
screenshots/
videos/
reports/
```

Esses artefatos são úteis para investigação de falhas no CI.

---

## 12. Exemplo de cenário de frontend

```javascript
describe('Login - Frontend', () => {

  it('FE-001 - Deve realizar login com usuário válido', () => {
    cy.fixture('users').then((users) => {
      cy.login(users.valid.email, users.valid.password);

      cy.url().should('include', '/home');
      cy.contains('Logout').should('be.visible');
    });
  });

});
```

### Boas práticas

- Preferir seletores estáveis.
- Evitar `cy.wait(5000)` desnecessário.
- Usar assertions específicas.
- Não depender da ordem de execução dos testes.
- Criar massa de dados independente.
- Evitar duplicação.
- Manter cada teste independente.

---

## 13. Exemplo de cenário negativo de API

```javascript
describe('Login - API', () => {

  it('API-003 - Deve rejeitar login com senha inválida', () => {

    cy.request({
      method: 'POST',
      url: `${Cypress.env('apiUrl')}/login`,
      failOnStatusCode: false,
      body: {
        email: 'usuario.teste@example.com',
        password: 'senha-invalida'
      }
    }).then((response) => {

      expect(response.status).to.eq(401);
      expect(response.body.message)
        .to.eq('Email e/ou senha inválidos');

    });

  });

});
```

O `failOnStatusCode: false` é importante em cenários negativos quando o status HTTP esperado representa uma falha de negócio.

---

## 14. Assertions

As assertions devem validar comportamento e contrato, não somente existência de elementos.

### UI

Evitar:

```javascript
cy.get('.button').should('exist');
```

Preferir:

```javascript
cy.contains('Login realizado com sucesso')
  .should('be.visible');
```

### API

Validar:

```javascript
expect(response.status).to.eq(201);
expect(response.body).to.have.property('message');
expect(response.body.message)
  .to.eq('Cadastro realizado com sucesso');
```

Quando aplicável, também validar:

- status code;
- response body;
- headers;
- schema;
- regras de negócio;
- mensagens de erro;
- campos obrigatórios.

---

## 15. Dados dinâmicos

Para evitar conflito de e-mail durante o cadastro:

```javascript
const uniqueEmail =
  `qa_${Date.now()}@example.com`;
```

Exemplo:

```javascript
const user = {
  nome: 'QA Automation',
  email: `qa_${Date.now()}@example.com`,
  password: 'Password@123',
  administrador: 'false'
};
```

Isso torna os testes mais independentes e reduz falhas causadas por dados persistidos no ambiente.

---

## 16. Git — fluxo recomendado

Inicializar:

```bash
git init
```

Adicionar arquivos:

```bash
git add .
```

Primeiro commit:

```bash
git commit -m "chore: initialize cypress automation project"
```

Adicionar remote:

```bash
git remote add origin https://github.com/<SEU-USUARIO>/<SEU-REPOSITORIO>.git
```

Renomear branch:

```bash
git branch -M main
```

Enviar:

```bash
git push -u origin main
```

---

## 17. Estratégia de commits

Os commits devem ser pequenos, objetivos e representar mudanças coerentes.

Exemplos:

```text
chore: initialize cypress project
test: add frontend login scenarios
test: add frontend user registration scenario
test: add api user creation scenario
test: add api invalid login scenario
feat: add reusable login custom command
feat: add user service object
refactor: organize test data into fixtures
ci: add github actions workflow
docs: add project documentation
```

Evitar:

```text
update
fix
changes
teste
final
final2
```

---

## 18. GitHub Actions — CI/CD

Criar:

```text
.github/workflows/cypress.yml
```

Conteúdo:

```yaml
name: Cypress Tests

on:
  push:
    branches:
      - main
      - develop

  pull_request:
    branches:
      - main

jobs:
  cypress:
    runs-on: ubuntu-24.04

    steps:
      - name: Checkout repository
        uses: actions/checkout@v7

      - name: Run Cypress tests
        uses: cypress-io/github-action@v7
        with:
          browser: chrome
          install: true
          command: npm run test

      - name: Upload test artifacts
        if: always()
        uses: actions/upload-artifact@v6
        with:
          name: cypress-artifacts
          path: |
            reports/
            screenshots/
            videos/
          if-no-files-found: ignore
```

O workflow executa automaticamente os testes em `push` e `pull_request`.

---

## 19. CI — execução recomendada

Fluxo:

```text
Developer
    |
    v
git push
    |
    v
GitHub
    |
    v
GitHub Actions
    |
    +--> npm install
    |
    +--> Cypress
    |
    +--> Frontend tests
    |
    +--> API tests
    |
    +--> Reports
    |
    +--> Screenshots/Videos
    |
    v
PASS / FAIL
```

A integração contínua permite detectar regressões antes do merge.

---

## 20. Artifacts no GitHub Actions

Em caso de falha, o pipeline deve preservar:

```text
reports/
screenshots/
videos/
```

Isso permite investigar o problema diretamente na execução do GitHub Actions.

---

## 21. Cypress Cloud — opcional

Para uma solução mais completa, o projeto pode ser integrado ao Cypress Cloud.

Após configurar o projeto:

```bash
npx cypress run --record --key <CYPRESS_RECORD_KEY>
```

No GitHub Actions, a chave deve ser armazenada como Secret:

```text
Settings
  -> Secrets and variables
      -> Actions
          -> New repository secret
```

Nome:

```text
CYPRESS_RECORD_KEY
```

Nunca colocar a chave diretamente no código ou no README.

---

## 22. Variáveis de ambiente

Para ambientes diferentes, recomenda-se utilizar variáveis:

```bash
CYPRESS_BASE_URL=https://front.serverest.dev
CYPRESS_API_URL=https://serverest.dev
```

No Cypress:

```javascript
Cypress.env('apiUrl');
```

Para execução local:

```bash
npx cypress run
```

Para CI, as variáveis podem ser configuradas no GitHub Actions.

---

## 23. `.gitignore`

Recomendação:

```gitignore
node_modules/
cypress/screenshots/
cypress/videos/
reports/
.env
.DS_Store
```

Não versionar:

- `node_modules`;
- vídeos;
- screenshots gerados;
- relatórios temporários;
- secrets;
- tokens;
- senhas reais.

O `package-lock.json` deve ser versionado.

---

## 24. Checklist de qualidade

### Arquitetura

- [x] Page Object Model
- [x] Service Object Pattern
- [x] Custom Commands
- [x] Fixtures
- [x] Separação UI/API
- [x] Configuração centralizada

### Testes

- [x] Happy path
- [x] Cenários negativos
- [x] Assertions claras
- [x] Massa de dados controlada
- [x] Testes independentes

### Engenharia

- [x] JavaScript
- [x] Cypress
- [x] Git
- [x] GitHub
- [x] GitHub Actions
- [x] Mochawesome
- [x] Screenshots/Videos
- [x] Artifacts no CI

---

## 25. Comandos rápidos

Instalar:

```bash
npm ci
```

Abrir Cypress:

```bash
npm run cy:open
```

Todos os testes:

```bash
npm test
```

Somente frontend:

```bash
npm run test:frontend
```

Somente API:

```bash
npm run test:api
```

Chrome:

```bash
npm run test:chrome
```

Gerar relatório:

```bash
npm run report
```

Verificar Cypress:

```bash
npx cypress verify
```

---

## 26. Definition of Done

Antes de compartilhar o desafio:

```text
[ ] Projeto público no GitHub
[ ] README atualizado
[ ] package-lock.json versionado
[ ] 3 cenários E2E frontend
[ ] 3 cenários API
[ ] Page Objects implementados
[ ] Service Objects implementados
[ ] Custom Commands implementados
[ ] Fixtures implementadas
[ ] Cenários positivos e negativos
[ ] Assertions claras
[ ] Relatório Mochawesome funcionando
[ ] Screenshots/Videos configurados
[ ] GitHub Actions funcionando
[ ] Secrets fora do código
[ ] Commits organizados
[ ] npm test executando com sucesso
[ ] CI executando com sucesso
```

---

## 27. Referências

- Cypress: https://docs.cypress.io/
- Cypress CI: https://docs.cypress.io/app/continuous-integration/github-actions
- Cypress Reporters: https://docs.cypress.io/app/tooling/reporters
- ServeRest / Swagger: https://serverest.dev/
- Frontend: https://front.serverest.dev/

---

## Autor

**QA Automation Engineer**

Projeto desenvolvido como teste técnico de automação utilizando Cypress e JavaScript.
