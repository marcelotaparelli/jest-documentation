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


# Testes de Integração 

Este documento serve como guia rápido para a implementação de testes de integração em APIs Node.js, utilizando **Jest** como runner e **Supertest** para simulação de requisições HTTP.

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

---

## ✅ Checklist de Cobertura

* [ ] O status code retornado é o correto (200, 201, 400, 404, 500)?
* [ ] O corpo da resposta (JSON) contém as propriedades esperadas?
* [ ] Os dados foram realmente persistidos no banco de dados após o `POST/PUT`?
* [ ] Middlewares de erro estão capturando exceções corretamente?

```

```
