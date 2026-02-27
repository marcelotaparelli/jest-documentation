# jest-documentation

# Jest

## O que é
Jest é um framework de testes em JavaScript desenvolvido pelo Facebook.  
Ele é amplamente utilizado para testar aplicações React, mas também funciona com qualquer projeto JavaScript/TypeScript.

## Principais características
- **Zero configuração inicial**: funciona direto em muitos projetos.
- **Testes rápidos e paralelos**: executa testes em múltiplos workers.
- **Mocks integrados**: permite simular funções, módulos e timers.
- **Snapshots**: captura o estado de componentes e compara automaticamente em execuções futuras.
- **Cobertura de código**: gera relatórios de cobertura nativamente.
- **Integração com Babel e TypeScript**: suporta transpilers e projetos modernos.

## Instalação
```bash
npm install --save-dev jest
```

## Scripts comuns
Adicionar no `package.json`:
```json
"scripts": {
  "test": "jest"
}
```

## Estrutura básica de teste
```javascript
// sum.js
function sum(a, b) {
  return a + b;
}
module.exports = sum;

// sum.test.js
const sum = require('./sum');

test('soma 1 + 2 para ser igual a 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

## Comandos úteis
- `npm test` → executa todos os testes.
- `jest --watch` → executa em modo observador.
- `jest --coverage` → gera relatório de cobertura.
- `jest nomeDoArquivo.test.js` → executa testes específicos.

## Boas práticas
- Nomear arquivos de teste com `.test.js` ou `.spec.js`.
- Manter testes próximos ao código que validam.
- Usar **mocks** para isolar dependências externas.
- Utilizar **snapshots** para componentes visuais ou objetos complexos.
- Garantir que os testes sejam rápidos e independentes.

## Recursos adicionais
- Documentação oficial [(jestjs.io in Bing)](https://www.bing.com/search?q="https%3A%2F%2Fjestjs.io%2Fdocs%2Fgetting-started")
- API de Matchers [(jestjs.io in Bing)](https://www.bing.com/search?q="https%3A%2F%2Fjestjs.io%2Fdocs%2Fusing-matchers")
- Guia de Mocks [(jestjs.io in Bing)](https://www.bing.com/search?q="https%3A%2F%2Fjestjs.io%2Fdocs%2Fmock-functions")

<br><br>

# Testes de Integração 

## Diferencial do Teste de Integração
Ao contrário dos testes unitários, aqui validamos a comunicação entre:
- Rotas (Express/Fastify/NestJS).
- Middlewares (Autenticação, Validação).
- Lógica de Negócio (Services).
- Acesso a Dados (Banco de Dados/Memória).


## Setup Inicial

1. **Instalação das dependências:**
```bash
npm install --save-dev jest supertest

```

2. **Configuração do `package.json`:**

```json
"scripts": {
  "test:int": "jest --config jest-integration.config.js --runInBand"
}

```

*Nota: `--runInBand` é recomendado se os testes compartilharem o mesmo banco de dados para evitar condições de corrida.*

---

## Estrutura do Teste

Um teste de integração eficaz geralmente segue o padrão **AAA** (Arrange, Act, Assert).

```javascript
const request = require('supertest');
const app = require('../src/app'); // Sua instância do Express/app

describe('User Controller Integration Tests', () => {
  
  // Limpeza do banco ou setup antes dos testes
  beforeAll(async () => {
    // Ex: await db.migrate();
  });

  afterAll(async () => {
    // Ex: await db.close();
  });

  describe('POST /users', () => {
    it('deve criar um novo usuário e retornar status 201', async () => {
      // Act (Agir)
      const response = await request(app)
        .post('/users')
        .send({
          name: 'Dev Test',
          email: 'test@example.com'
        });

      // Assert (Afirmar)
      expect(response.status).toBe(201);
      expect(response.body).toHaveProperty('id');
      expect(response.body.name).toBe('Dev Test');
    });

    it('deve retornar 400 se o e-mail já existir', async () => {
      const response = await request(app)
        .post('/users')
        .send({
          name: 'Dev Test',
          email: 'test@example.com' // E-mail já cadastrado no teste anterior
        });

      expect(response.status).toBe(400);
      expect(response.body.message).toBe('Email already in use');
    });
  });
});

```


## Boas Práticas e Dicas

### 1. Separação de Ambiente

Nunca execute testes de integração no banco de dados de produção ou desenvolvimento. Utilize um arquivo `.env.test` ou um banco de dados em memória (como SQLite ou containers Docker).

### 2. Mocking Seletivo

No teste de integração, evite mocar o banco de dados. Porém, é recomendado mocar:

* Envio de e-mails.
* Gateways de pagamento externos.
* APIs de terceiros (usando ferramentas como `nock`).

### 3. Utilitários de Autenticação

Para rotas protegidas, crie um helper para gerar tokens JWT rapidamente:

```javascript
const token = generateTestToken(userPayload);

const response = await request(app)
  .get('/profile')
  .set('Authorization', `Bearer ${token}`);

```


## ✅ Checklist de Cobertura

* [ ] O status code retornado é o correto (200, 201, 400, 404, 500)?
* [ ] O corpo da resposta (JSON) contém as propriedades esperadas?
* [ ] Os dados foram realmente persistidos no banco de dados após o `POST/PUT`?
* [ ] Middlewares de erro estão capturando exceções corretamente?


<br><br>


# Testes End-to-End (E2E)

Testes de ponta a ponta validam fluxos completos do usuário, desde a interface (Frontend) até a persistência final (Backend/Database).

## O que é o Teste E2E?
Diferente dos testes de integração, o E2E testa o sistema "caixa preta". O teste abre um navegador real, interage com a UI e espera que o sistema inteiro se comporte conforme o esperado.


## Setup (Playwright)

O Playwright é recomendado por sua velocidade, suporte a múltiplos browsers e ferramentas de auto-wait.

1. **Instalação:**
```bash
npm init playwright@latest

```

2. **Estrutura de comandos:**

* `npx playwright test`: Executa todos os testes.
* `npx playwright show-report`: Abre o relatório visual de erros.
* `npx playwright codegen`: Abre o gerador de código (você clica no site e ele escreve o teste para você).


## Exemplo de Fluxo de Autenticação

Arquivo: `tests/auth.spec.ts`

```typescript
import { test, expect } from '@playwright/test';

test.describe('Fluxo de Login', () => {
  
  test.beforeEach(async ({ page }) => {
    await page.goto('/login');
  });

  test('deve realizar login com sucesso e redirecionar para o dashboard', async ({ page }) => {
    await page.fill('input[name="email"]', 'usuario@exemplo.com');
    await page.fill('input[name="password"]', 'senha123');
    await page.click('button[type="submit"]');

    await expect(page).toHaveURL(/.*dashboard/);
    
    const welcomeMessage = page.locator('h1');
    await expect(welcomeMessage).toContainText('Bem-vindo');
  });

  test('deve exibir erro com credenciais inválidas', async ({ page }) => {
    await page.fill('input[name="email"]', 'errado@exemplo.com');
    await page.fill('input[name="password"]', 'senhaErrada');
    await page.click('button[type="submit"]');

    const errorAlert = page.locator('.alert-error');
    await expect(errorAlert).toBeVisible();
    await expect(errorAlert).toHaveText('Credenciais inválidas');
  });
});

```


## Estratégias Avançadas

### 1. Page Object Model (POM)

Para evitar repetição de código (como seletores de CSS), utilize o padrão POM. Crie classes que representam suas páginas.

### 2. Bypass de Login (Global Setup)

Para não precisar logar manualmente em cada teste (o que é lento), você pode realizar o login uma vez, salvar o estado do browser (cookies/storage) e reutilizar nos outros testes.

### 3. Evite Seletores Frágeis

Evite usar classes CSS que mudam frequentemente. Prefira `data-testid`:

```html
<button data-testid="submit-button">Enviar</button>

await page.getByTestId('submit-button').click();

```

## Quando usar E2E vs Integração?

| Critério | Integração | E2E |
| --- | --- | --- |
| **Confiança** | Alta (Lógica OK) | Total (Usuário OK) |
| **Manutenção** | Baixa | Alta (UI muda muito) |
| **Execução** | Segundos | Minutos |

Use E2E apenas para os **Caminhos Felizes (Happy Paths)** e fluxos críticos (Checkout, Cadastro, Login). Deixe os casos de borda e erros de validação para os Testes de Integração.

---

Para testar processos que envolvem o Banco de Dados (BD), o desafio é equilibrar **fidelidade** (o quão próximo do real o teste é) e **velocidade** (o quão rápido o teste roda).

Como você trabalha com **Node.js, NestJS e TypeScript**, aqui estão as principais estratégias divididas por abordagem:

## 1. Banco de Dados em Memória (SQLite / MongoDB Memory Server)

Esta é a opção mais rápida. Em vez de um banco persistente em disco, os dados vivem no cache da aplicação durante o teste.

* **Como funciona:** Você configura o TypeORM, Prisma ou Mongoose para se conectar a um banco `:memory:` durante os testes.
* **Vantagens:** Extremamente veloz; não deixa "lixo" (o banco morre quando o teste acaba).
* **Desvantagens:** **Fidelidade baixa.** O SQLite não suporta todas as funções de um PostgreSQL ou MySQL (como tipos JSONB, Enums específicos ou triggers).
* **Ideal para:** Testes de integração de lógica simples.

## 2. Testcontainers (A Abordagem Moderna)

O **Testcontainers** é uma biblioteca que sobe containers Docker temporários (como uma instância real do Postgres) exclusivamente para o tempo de execução do teste.

* **Como funciona:** O Jest inicia um container do seu banco real, roda as migrações, executa o teste e destrói o container ao final.
* **Vantagens:** **Fidelidade máxima.** Você testa exatamente o que terá em produção.
* **Desvantagens:** Requer Docker rodando e é mais lento que o banco em memória devido ao tempo de subida do container.
* **Ideal para:** Garantir que queries complexas e transações funcionem de verdade.


## 3. Banco de Teste Dedicado (Instância Fixa)

Ter um banco de dados (geralmente local ou em um container fixo) chamado `meu_app_test`.

* **Como funciona:** Você aponta o seu `.env.test` para esse banco. Antes de cada teste, você usa um **Global Setup** para limpar as tabelas (Truncate).
* **Vantagens:** Mais rápido que subir um container do zero a cada execução.
* **Desvantagens:** Se os testes rodarem em paralelo, um pode interferir nos dados do outro (condição de corrida).
* **Ideal para:** Ambientes de CI/CD onde o banco já está provisionado.


## 4. Repository Pattern & Mocks (Sem BD Real)

Aqui você finge que o banco existe, isolando a camada de persistência.

* **Como funciona:** Você usa o Jest para mocar o retorno do seu Repository ou Service.
```typescript
jest.spyOn(userRepository, 'save').mockResolvedValue(mockedUser);

```


* **Vantagens:** Instantâneo. Não depende de infraestrutura.
* **Desvantagens:** **Não testa o banco.** Você está testando se o seu código chama a função, mas não se a query SQL está correta ou se uma constraint de "email único" vai quebrar.
* **Ideal para:** Testes unitários de regras de negócio.


## Comparativo de Decisão

| Opção | Velocidade | Fidelidade | Complexidade de Setup |
| --- | --- | --- | --- |
| **Mocks** | ⚡ Instantâneo |  Nula | Baixa |
| **In-Memory (SQLite)** | Muito Rápido | Média | Média |
| **Banco Fixo (Local)** | Rápido | Alta | Baixa |
| **Testcontainers** | Lento | Máxima | Alta |
