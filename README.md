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
```

Esse resumo cobre desde a instalação até boas práticas e links úteis. Quer que eu prepare também uma versão mais **enxuta** (tipo um *cheat sheet*) para consulta rápida?
