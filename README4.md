[↩ Voltar](README3.md)

# Testes no ReactJS
## Ambiente de testes

### Configurando ambiente do Jest

### Criando primeiro teste do zero
Criamos a pastas `__tests__` dentro de `src` e adicionamos as pastas `components`, `hooks` e `pages`. Criamos o primeiro arquivo de testes `SignIn.spec.ts` e vimos que começou a dar vários warnings. É necessário adicionar no `eslint` que estamos usando o `jest`.
```json
{
  "env": {
    "browser": true,
    "es6": true,
    "jest": true
  },
```

Sempre que iremos usar a sintaxe de jsx ou tsx, devemos importar o `React` e deixar a extensão como `tsx` nesse caso. Fazemos a desestruturação do `render` e utilizamos o `debug` que vai fazer como se fosse um `console.log` de todo html contido nesse elemento e dá um sintaxe highlight. Rodamos o teste no terminal.
```sh
yarn test
```

Já dá um erro, pois essa página de `SignIn` usa o `useHistory` que conseguimos pegar graças ao `Router` lá do `react-router-dom`. Então, usaremos um recurso do Jest chamado `mock` que é uma forma de atribuir valores fictícios para funções ou variáveis que serão utilizadas no nosso teste. Como o `useHistory` vem de uma biblioteca, não é tão simples como usar o `jest.spyOn` como fizemos no back-end.

Vamos usar nesse caso o `jest.mock` por fora do `describe` para que sempre funcione para todos os testes do `SignIn`. E colocamos o nome do módulo que queremos mockar. Nesse caso, o `react-router-dom`. E podemos retornar exatamente o que precisamos, que é o `useHistory`, mas como só quero que exista mas que não precise funcionar, só deixamos como um `jest.fn()`. Também falou retornar o `Link` que vai ter um `children` que tem uma tipagem de `ReactNode` que pode ser qualquer conteúdo que um componente React poderia receber.
```tsx
import React from 'react';
import { render } from '@testing-library/react';
import SignIn from '../../pages/SignIn';

jest.mock('react-router-dom', () => {
  return {
    useHistory: jest.fn(),
    Link: ({ children }: { children: React.ReactNode }) => children,
  };
});

describe('SignIn Page', () => {
  it('should be able to sign in', () => {
    const { debug } = render(<SignIn />);

    debug();
  });
});
```
E agora nosso teste rodou mostrando no console todo nosso html da página SignIn.

### Gerando coverage report
No `package.json` vamos configurar o coverage para que não pegue em toda a aplicação. Adicionamos essa parte em que vai testar todos arquivos de pages, components e hooks, mas não iremos testar o index do hooks, pois não é um arquivo necessário.
```json
{
  "jest": {
    "collectCoverageFrom": [
      "src/pages/**/*.tsx",
      "src/components/**/*.tsx",
      "src/hooks/*.tsx",
      "!src/hooks/index.tsx"
    ]
  },
```

Para rodar no terminar e gerar o coverage, paramos o `yarn test` para então rodar
```bash
yarn test --coverage --watchAll false
```

### Teste de login
Queremos encontrar o input de email e senha. Usamos o `getByPlaceholderText` para capturar esses elementos. E para adicionar o texto, usamos o `fireEvent.change`, lembrando que acessamos o valor dos inputs pelo `e.target.value`. Então, precisamos colocar o objeto nesse formato para inserir o valor no input. Já o botão, iremos pegá-lo pelo `getByText` e usaremos o `fireEvent.click`.

Imaginando o comportamento do login, o usuários seria direcionado para a página de `Dashboard` assim que seus dados fossem validados e o signIn fosse realizado. Então, não basta ter o `useHistory` mockado, mas ele tem que retornar uma função `push` que eu possa verificar depois que foi chamada.
```tsx
import React from 'react';
import { render, fireEvent } from '@testing-library/react';
import SignIn from '../../pages/SignIn';

const mockedHistoryPush = jest.fn();

jest.mock('react-router-dom', () => {
  return {
    useHistory: () => ({
      push: mockedHistoryPush,
    }),
    Link: ({ children }: { children: React.ReactNode }) => children,
  };
});

describe('SignIn Page', () => {
  it('should be able to sign in', () => {
    const { getByPlaceholderText, getByText } = render(<SignIn />);

    const emailField = getByPlaceholderText('E-mail');
    const passwordField = getByPlaceholderText('Senha');
    const buttonElement = getByText('Entrar');

    fireEvent.change(emailField, { target: { value: 'johndoe@example.com' } });
    fireEvent.change(passwordField, { target: { value: '123456' } });

    fireEvent.click(buttonElement);

    expect(mockedHistoryPush).toHaveBeenCalledWith('/dashboard');
  });
});
```

Ao rodar o teste, agora falhou a parte de requisição da API.
