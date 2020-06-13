# Iniciando front-end web - GoBarber
## Configurando Estrutura
Adicionar as devDependencies e as configurações de eslint, prettier e typescript do projeto ReactJS anterior. Deletar os arquivos desnecessários.

## Estilos globais
Adicionar o `styled-components`. Criar o arquivo `src/styles/global.ts`
```ts
import { createGlobalStyle } from 'styled-components';

export default createGlobalStyle`
  * {
    margin: 0;
    padding: 0;
    outline: none;
    box-sizing: border-box;
  }

  body {
    background: #312e38;
    color: #fff;
    -webkit-font-smoothing: antialiased;
  }

  body, input, button {
    font-family: 'Roboto Slab', serif;
    font-size: 16px;
  }

  h1, h2, h3, h4, h5, h6, strong {
    font-weight: 500;
  }

  button {
    cursor: pointer;
  }
`;
```

No arquivo `src/App.tsx`, importar o `GlobalStyle`
```tsx
import React from 'react';

import GlobalStyle from './styles/global';

const App: React.FC = () => {
  return (
    <>
      <h1>Hello World</h1>
      <GlobalStyle />
    </>
  );
};

export default App;
```

## Página de login
Importar pacote de icons
```bash
yarn add react-icons
```
`src/pages/SignIn/index.tsx`
```tsx
import React from 'react';
import { FiLogIn } from 'react-icons/fi';
import logoImg from '../../assets/logo.svg';

import { Container, Content, Background } from './styles';

const SignIn: React.FC = () => (
  <Container>
    <Content>
      <img src={logoImg} alt="GoBarber" />

      <form action="submit">
        <h1>Faça seu logon</h1>
        <input placeholder="E-mail" />
        <input type="password" placeholder="Senha" />
        <button type="submit">Entrar</button>

        <a href="forgot">Esqueci minha senha</a>
      </form>

      <a href="criar">
        <FiLogIn />
        Criar conta
      </a>
    </Content>
    <Background />
  </Container>
);

export default SignIn;
```

Importar pacote polished
```bash
yarn add polished
```
`src/pages/SignIn/styles.ts``
```ts
import styled from 'styled-components';
import { shade } from 'polished';

import SignInBackground from '../../assets/sign-in-background.png';

export const Container = styled.div`
  height: 100vh;
  display: flex;
  align-items: stretch;
`;

export const Content = styled.div`
  display: flex;
  flex-direction: column;
  place-content: center;
  align-items: center;

  width: 100%;
  max-width: 700px;

  form {
    margin: 80px 0;
    width: 340px;
    text-align: center;

    h1 {
      margin-bottom: 24px;
    }

    input {
      background: #232129;
      border-radius: 10px;
      border: 2px solid #232129;
      padding: 16px;
      width: 100%;
      color: #f4ede8;

      &::placeholder {
        color: #666360;
      }

      & + input {
        margin-top: 8px;
      }
    }

    button {
      background: #ff9000;
      height: 56px;
      border-radius: 10px;
      border: 0;
      padding: 0 16px;
      color: #312e38;
      width: 100%;
      font-weight: 500;
      margin-top: 16px;
      transition: background-color 0.2s;

      &:hover {
        background: ${shade(0.2, '#ff9000')};
      }
    }

    a {
      color: #f4ede8;
      display: block;
      margin-top: 24px;
      text-decoration: none;
      transition: color 0.2s;

      &:hover {
        color: ${shade(0.2, '#f4ede8')};
      }
    }
  }

  > a {
    color: #ff9000;
    display: block;
    margin-top: 24px;
    text-decoration: none;
    transition: color 0.2s;

    display: flex;
    align-items: center;

    svg {
      margin-right: 16px;
    }

    &:hover {
      color: ${shade(0.2, '#ff9000')};
    }
  }
`;

export const Background = styled.div`
  flex: 1;
  background: url(${SignInBackground}) no-repeat center;
  background-size: cover;
`;
```

## Isolando componentes
Percebemos a repetição do input e do button em vários lugares da aplicação. Vamos criar a pasta de `components`.

Deixar obrigatório o atributo `name` do input. Em `src/components/Input/index.tsx`
```tsx
import React, { InputHTMLAttributes } from 'react';
import { IconBaseProps } from 'react-icons';

import { Container } from './styles';

interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  name: string;
  icon?: React.ComponentType<IconBaseProps>;
}

const Input: React.FC<InputProps> = ({ icon: Icon, ...rest }) => (
  <Container>
    {Icon && <Icon size={20} />}
    <input {...rest} />
  </Container>
);

export default Input;
```

Migrar o estilo da página SignIn para o `src/components/Input/styles.ts`
```ts
import styled from 'styled-components';

export const Container = styled.div`
  background: #232129;
  border-radius: 10px;
  border: 2px solid #232129;
  padding: 16px;
  width: 100%;
  color: #666360;

  display: flex;
  align-items: center;

  & + div {
    margin-top: 8px;
  }

  input {
    color: #f4ede8;
    flex: 1;
    background: transparent;
    border: 0;

    &::placeholder {
      color: #666360;
    }
  }

  svg {
    margin-right: 16px;
  }
`;
```

No caso do `button`, como não estamos sobrescrevendo nenhum atributo, então ao invés de usarmos `interface` utlizamos `type`.
```tsx
import React, { ButtonHTMLAttributes } from 'react';

import { Container } from './styles';

type ButtonProps = ButtonHTMLAttributes<HTMLButtonElement>;

const Button: React.FC<ButtonProps> = ({ children, ...rest }) => (
  <Container type="button" {...rest}>
    {children}
  </Container>
);

export default Button;
```

E no estilo do button `src/components/Button/styles.ts`
```ts
import styled from 'styled-components';
import { shade } from 'polished';

export const Container = styled.button`
  background: #ff9000;
  height: 56px;
  border-radius: 10px;
  border: 0;
  padding: 0 16px;
  color: #312e38;
  width: 100%;
  font-weight: 500;
  margin-top: 16px;
  transition: background-color 0.2s;

  &:hover {
    background: ${shade(0.2, '#ff9000')};
  }
`;
```

Adicionar uma rule no `eslintrc.json` para aceitar o spread das props.
```json
{
  "rules": {

    "react/jsx-props-no-spreading": "off",
  },
}
```

Na página `src/pages/SignIn/index.tsx`, fazer a importação dos componentes
```tsx
import React from 'react';
import { FiLogIn, FiMail, FiLock } from 'react-icons/fi';
import logoImg from '../../assets/logo.svg';

import Input from '../../components/Input';
import Button from '../../components/Button';

import { Container, Content, Background } from './styles';

const SignIn: React.FC = () => (
  <Container>
    <Content>
      <img src={logoImg} alt="GoBarber" />

      <form action="submit">
        <h1>Faça seu logon</h1>
        <Input name="email" icon={FiMail} placeholder="E-mail" />
        <Input
          name="password"
          icon={FiLock}
          type="password"
          placeholder="Senha"
        />
        <Button type="submit">Entrar</Button>

        <a href="forgot">Esqueci minha senha</a>
      </form>

      <a href="criar">
        <FiLogIn />
        Criar conta
      </a>
    </Content>
    <Background />
  </Container>
);

export default SignIn;
```

## Página de cadastro
Para a página de `SignUp`, copiamos tudo da página `SignIn` e fizemos as alterações necessárias.


## Utilizando Unform
Para cada input estávamos usando um estado. E toda vez que o estado era alterado, o componente atualiza.
Instalar os pacotes
```bash
yarn add @unform/core @unform/web
```

Importar o `Form` dessa biblioteca
```tsx
import { Form } from '@unform/web';

<Form onSubmit={handleSubmit}>
  <h1>Faça seu cadastro</h1>
  <Input name="name" icon={FiUser} placeholder="Nome" />
  <Input name="email" icon={FiMail} placeholder="E-mail" />
  <Input
    name="password"
    icon={FiLock}
    type="password"
    placeholder="Senha"
  />
  <Button type="submit">Cadastrar</Button>
</Form>
```

E substituir `<form>` por `Form`. Além disso, precisamos informar quais campos do formulário serão monitorados/registrados nesse form quando der o submit. Para isso, fazemos a alteração do componente de `Input`. O `useField` é um hook disponível que recebe como parâmetro o nome do campo, e retorna várias propriedades (fieldName, defaultName, error, registerField). Assim que o componente for exibido em tela, vou chamar a função `registerField`, por isso, precisamos do `useEffect`.

A primeira propriedade que o `registerField` recebe é o `name` que usamos o `fieldName` para não alterar, e o `ref` que vai referenciar o input de maneira direta (sem ter que acessar state) e usamo o `useRef` para isso. Agora conseguimos acessar o `inputRef` diretamente da DOM. O input fica dentro de `Input.current` (padrão do React). O `path` é onde o unform vai buscar o valor dentro dessa referência, que nesse caso é o `'value'`.
```tsx
import React, { InputHTMLAttributes, useEffect, useRef } from 'react';
import { IconBaseProps } from 'react-icons';
import { useField } from '@unform/core';

import { Container } from './styles';

interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  name: string;
  icon?: React.ComponentType<IconBaseProps>;
}

const Input: React.FC<InputProps> = ({ name, icon: Icon, ...rest }) => {
  const { fieldName, defaultValue, error, registerField } = useField(name);
  const inputRef = useRef(null);

  useEffect(() => {
    registerField({
      name: fieldName,
      ref: inputRef.current,
      path: 'value',
    });
  }, [fieldName, registerField]);

  return (
    <Container>
      {Icon && <Icon size={20} />}
      <input ref={inputRef} {...rest} />
    </Container>
  );
};

export default Input;
```

Para adicionar um `defaultValue` no input, o `Form` recebe um `initialData` onde é passado um objeto com a nome do input e o valor default para já vir autocompletado
```tsx
  <Form initialData={{ name: 'Cintia' }} onSubmit={handleSubmit}>
```

## Usabilidade do Input
Para pegar o focus e o blur do input, armazenar no state `isFocused`. Fazer a estilização conforme props. E para adicionar uma função do `handleBlur`, usar o hooks `useCallback` para evitar que essa função seja criada todas as vezes que esse componente input foi renderizado.

Em `src/components/Input/index.tsx`
```tsx
const Input: React.FC<InputProps> = ({ name, icon: Icon, ...rest }) => {
  const { fieldName, defaultValue, error, registerField } = useField(name);
  const [isFocused, setIsFocused] = useState(false);
  const [isFilled, setIsFilled] = useState(false);
  const inputRef = useRef<HTMLInputElement>(null);

  const handleInputFocus = useCallback(() => {
    setIsFocused(true);
  }, []);

  const handleInputBlur = useCallback(() => {
    setIsFocused(false);

    setIsFilled(!!inputRef.current?.value);
  }, []);

  useEffect(() => {
    registerField({
      name: fieldName,
      ref: inputRef.current,
      path: 'value',
    });
  }, [fieldName, registerField]);

  return (
    <Container isFilled={isFilled} isFocused={isFocused}>
      {Icon && <Icon size={20} />}
      <input
        onFocus={handleInputFocus}
        onBlur={handleInputBlur}
        defaultValue={defaultValue}
        ref={inputRef}
        {...rest}
      />
    </Container>
  );
};
```

Em `src/components/Input/styles.ts`
```ts
import styled, { css } from 'styled-components';

interface ContainerProps {
  isFocused: boolean;
  isFilled: boolean;
}

export const Container = styled.div<ContainerProps>`
  background: #232129;
  border-radius: 10px;
  padding: 16px;
  width: 100%;

  border: 2px solid #232129;
  color: #666360;

  display: flex;
  align-items: center;

  & + div {
    margin-top: 8px;
  }

  ${(props) =>
    props.isFocused &&
    css`
      color: #ff9000;
      border-color: #ff9000;
    `}

  ${(props) =>
    props.isFilled &&
    css`
      color: #ff9000;
    `}

// ...
```

## Validando cadastro
Instalar `yup` para validação de formulário
```bash
yarn add yup
yarn add @types/yup -D
```

Em `src/pages/SignUp/index.tsx` importar tudo como `Yup` e fazer a validação do `data`. Como é uma função dentro do componente, usamos o `useCallback`. O Yup para no primeiro erro que dá, e por isso, deixamos o `abortEarly: false` para retornar todos os erros encontrados e não somente o primeiro
```tsx
import * as Yup from 'yup';
// ...
  const handleSubmit = useCallback(async (data: object) => {
    try {
      const schema = Yup.object().shape({
        name: Yup.string().required('Nome obrigatório'),
        email: Yup.string()
          .required('E-mail obrigatório')
          .email('Digite um e-mail válido'),
        password: Yup.string().min(6, 'No mínimo 6 caracteres'),
      });

      await schema.validate(data, {
        abortEarly: false;
      });
    } catch (err) {
      console.log(err);
    }
  }, []);
// ...
```

## Exibindo erro no input
A biblioteca `unform` tem uma forma de tratar os erros do input. Primeiramente, vamos criar a referência para o `Form` para então conseguir acessar as propriedades existentes que trata os erros
Em `src/pages/SignUp/index.tsx`
```tsx
  const formRef = useRef(null);

  <Form ref={formRef} onSubmit={handleSubmit}>
```

Ao dar um `console.log(formRef)` podemos ver dentro da propriedade `current` várias funções para manipular meus inputs, como a `setErrors` que permite setar erro para cada input do `Form`. Mas ao tentar acessar essas propriedades, não conseguimos. Então, precisamos importar o `FormHandles` que tem a interface com a tipagem de todos funções dentro de `formRef.current`
```tsx
import { FormHandles } from '@unform/core';

  const formRef = useRef<FormHandles>(null);
```

Adicionar em `rules` de `eslintrc.json`
```json
    "no-unused-expressions": "off",
```

No `err` do `try/catch` existe a propriedade `inner` que mostra cada um dos erros que aconteceu. Como faremos essa validação dos erros várias vezes, vamos isolar essa função numa pasta `utils`

Em `src/utils/getValidationError.ts`, vamos passar o parâmetro de `ValidationError` que vem do `Form` e vamos tratar para retornar cada erro contendo o `path: message` para cada input. A interface ao invés de colocar especificamente `name: string`, `email: string`, etc, colocamos mais generalizado que vai receber `[key: string]: string`
```ts
import { ValidationError } from 'yup';

interface Errors {
  [key: string]: string;
}

export default function getValidationError(err: ValidationError): Errors {
  const validationErrors: Errors = {};

  err.inner.forEach((error) => {
    validationErrors[error.path] = error.message;
  });

  return validationErrors;
}
```

Adicionamos o `{error}` após o input somente para vermos os erros quando enviamos um submit com valores vazios `src/components/Input/index.tsx`
```tsx
    <Container isFilled={isFilled} isFocused={isFocused}>
      {Icon && <Icon size={20} />}
      <input
        onFocus={handleInputFocus}
        onBlur={handleInputBlur}
        defaultValue={defaultValue}
        ref={inputRef}
        {...rest}
      />

      {error}
    </Container>
```

E lembrar de limpar os erros no início `src/pages/SignUp/index.tsx`
```tsx
const handleSubmit = useCallback(async (data: object) => {
    try {
      formRef.current?.setErrors({});
```

## Criando tooltip de erros
Adicionamos mais uma props no componente do Input para alterar o estilo quando tiver um erro.
```tsx
<Container hasError={!!error} isFilled={isFilled} isFocused={isFocused}>
```

E adicionamos no styled-component desse componente essa prop na tipagem e sua estilização
```ts
interface ContainerProps {
  isFocused: boolean;
  isFilled: boolean;
  hasError: boolean;
}
// ...
  ${(props) =>
    props.hasError &&
    css`
      border-color: #c53030;
    `}
// ...
```

E adicionamos um icon no input
```tsx
      {error && (
        <Error>
          <FiAlertCircle color="#c53030" size={20} />
        </Error>
      )}
```

Na estilização
```ts
export const Error = styled.div`
  height: 20px;
  margin-left: 16px;

  svg {
    margin: 0;
  }
`;
```

Criando o compontente Tooltip, temos que importá-lo no styled do Input, assim, é possível passar as propriedades por meio das className. Em `src/components/Tooltip/index.tsx`
```tsx
import React from 'react';

import { Container } from './styles';

interface TooltipProps {
  title: string;
  className?: string;
}

const Tooltip: React.FC<TooltipProps> = ({ title, className, children }) => {
  return (
    <Container className={className}>
      {children}
      <span>{title}</span>
    </Container>
  );
};

export default Tooltip;
```

Em `src/components/Input/styles.ts`
```ts
export const Error = styled(Tooltip)`
  height: 20px;
  margin-left: 16px;

  svg {
    margin: 0;
  }
`;
```

Estilizamos então o Tooltip tanto no que deve ser padrão dele em `src/components/Tooltip/styles.ts`
```ts
import styled from 'styled-components';

export const Container = styled.div`
  position: relative;

  span {
    width: 160px;
    background: #ff9000;
    padding: 8px;
    border-radius: 4px;
    font-size: 14px;
    font-weight: 500;
    opacity: 0;
    transition: opacity 0.4s;
    visibility: hidden;

    position: absolute;
    bottom: calc(100% + 12px);
    left: 50%;
    transform: translateX(-50%);
    color: #312e38;

    &::before {
      content: '';
      border-style: solid;
      border-color: #ff9000 transparent;
      border-width: 6px 6px 0 6px;
      top: 100%;
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
    }
  }

  &:hover span {
    opacity: 1;
    visibility: visible;
  }
`;
```

Quanto no componente Error do Input em `src/components/Input/styles.ts`
```ts
export const Error = styled(Tooltip)`
  height: 20px;
  margin-left: 16px;

  svg {
    margin: 0;
  }

  span {
    background: #c53030;
    color: #fff;

    &::before {
      border-color: #c53030 transparent;
    }
  }
`;
```

## Validação de login
Voltamos para a página de SignIn para então configurar o tooltip de erro, mas também, fazer as importações corretas. Em `src/pages/SignIn/index.tsx`
```tsx
import React, { useRef, useCallback } from 'react';
import { FiLogIn, FiMail, FiLock } from 'react-icons/fi';
import { FormHandles } from '@unform/core';
import { Form } from '@unform/web';
import * as Yup from 'yup';

import getValidationErrors from '../../utils/getValidationError';

import logoImg from '../../assets/logo.svg';

import Input from '../../components/Input';
import Button from '../../components/Button';

import { Container, Content, Background } from './styles';

const SignIn: React.FC = () => {
  const formRef = useRef<FormHandles>(null);

  const handleSubmit = useCallback(async (data: object) => {
    try {
      formRef.current?.setErrors({});

      const schema = Yup.object().shape({
        email: Yup.string()
          .required('E-mail obrigatório')
          .email('Digite um e-mail válido'),
        password: Yup.string().required('Senha obrigatória'),
      });

      await schema.validate(data, {
        abortEarly: false,
      });
    } catch (err) {
      const errors = getValidationErrors(err);
      formRef.current?.setErrors(errors);
    }
  }, []);
  return (
    <Container>
      <Content>
        <img src={logoImg} alt="GoBarber" />

        <Form ref={formRef} onSubmit={handleSubmit}>
          <h1>Faça seu logon</h1>
          <Input name="email" icon={FiMail} placeholder="E-mail" />
          <Input
            name="password"
            icon={FiLock}
            type="password"
            placeholder="Senha"
          />
          <Button type="submit">Entrar</Button>

          <a href="forgot">Esqueci minha senha</a>
        </Form>

        <a href="criar">
          <FiLogIn />
          Criar conta
        </a>
      </Content>
      <Background />
    </Container>
  );
};

export default SignIn;
```

# Autenticação
## Habilitando CORS na API
Voltamos para o projeto de backend
```bash
yarn add cors
yarn add -D @types/cors
```

Em `src/server.ts`
```ts
//...
import cors from 'cors';
//...
app.use(cors());
//...
```

Rodar o banco no docker e o server e conferir com o Insomnia se está funcionando.


## API de Contexto
Contexto é uma variável que vai ficar acessível de modo global ou não. Criamos uma pasta `context` e adicionamos, por exemplo, um contexto de autenticação `src/context/AuthContext.ts`
```ts
import { createContext } from 'react';

interface AuthContextData {
  name: string;
}

const AuthContext = createContext<AuthContextData>({} as AuthContextData);

export default AuthContext;
```

Agora importamos no nosso `App.tsx` e vamos colocar em volta de tudo que queremos que tenha acesso a esse contexto na nossa aplicação e precisamos informar um `value` inicial
```tsx
import AuthContext from './context/AuthContext';
//...
const App: React.FC = () => {
  return (
    <>
      <AuthContext.Provider value={{ name: 'Cintia' }}>
        {/* <SignUp /> */}
        <SignIn />
      </AuthContext.Provider>
      <GlobalStyle />
    </>
  );
};
//...
```

E em `src/pages/SignIn/index.tsx`
```tsx
import React, { useRef, useCallback, useContext } from 'react';
import AuthContext from '../../context/AuthContext';
//...
  const { name } = useContext(AuthContext);
  console.log(name);
//...
```

## Login pelo contexto
Vamos criar um método de autenticação que vai ser armazenado dentro do nosso contexto de autenticação (cadastro, login, logout). Mas ao invés de colocar todos os contextos de autenticação dentro de `App.tsx`, vamos isolar em um componente React mesmo. Alteramos o arquivo `.ts` para `.tsx`. Em `src/context/AuthContext.tsx`
```tsx
import React, { createContext } from 'react';

interface AuthContextData {
  name: string;
}

const AuthContext = createContext<AuthContextData>({} as AuthContextData);

export const AuthProvider: React.FC = ({ children }) => {
  return (
    <AuthContext.Provider value={{ name: 'Cintia' }}>
      {children}
    </AuthContext.Provider>
  );
};
```

E fazemos a importação em `App.tsx` do `AuthProvider`
```tsx
import { AuthProvider } from './context/AuthContext';

const App: React.FC = () => {
  return (
    <>
      <AuthProvider>
        {/* <SignUp /> */}
        <SignIn />
      </AuthProvider>
      <GlobalStyle />
    </>
  );
};
```

E vamos adicionar o método `signIn` dentro de `AuthContext.tsx`
```tsx
import React, { createContext, useCallback } from 'react';

interface AuthContextData {
  name: string;
  signIn(): void;
}

const AuthContext = createContext<AuthContextData>({} as AuthContextData);

const AuthProvider: React.FC = ({ children }) => {
  const signIn = useCallback(() => {
    console.log('sign in');
  }, []);

  return (
    <AuthContext.Provider value={{ name: 'Cintia', signIn }}>
      {children}
    </AuthContext.Provider>
  );
};

export { AuthContext, AuthProvider };
```

Em `src/pages/SignIn/index.tsx`
```tsx
import React, { useRef, useCallback, useContext } from 'react';
//...
import { AuthContext } from '../../context/AuthContext';
//...
  const { signIn } = useContext(AuthContext);

  const handleSubmit = useCallback(
    async (data: object) => {
      try {
        formRef.current?.setErrors({});

        const schema = Yup.object().shape({
          email: Yup.string()
            .required('E-mail obrigatório')
            .email('Digite um e-mail válido'),
          password: Yup.string().required('Senha obrigatória'),
        });

        await schema.validate(data, {
          abortEarly: false,
        });

        signIn();
      } catch (err) {
        const errors = getValidationErrors(err);
        formRef.current?.setErrors(errors);
      }
    },
    [signIn],
  );
```

Instalamos o `axios`
```bash
yarn add axios
```

Vamos ligar o signIn com nossa api criando o arquivo `src/services/api.ts`
```ts
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:3333',
});

export default api;
```

Fazemos então a chamada da api no método de signIn em `src/context/AuthContext.tsx`
```tsx
import api from '../services/api';

interface SignInCredentials {
  email: string;
  password: string;
}
//...
  const signIn = useCallback(async ({ email, password }) => {
    const response = await api.post('sessions', {
      email,
      password,
    });

    console.log(response.data);
  }, []);
```

E vamos fazer a alteração no `src/pages/SignIn/index.tsx`
```tsx
//...
interface SignInFormData {
  email: string;
  password: string;
}
//...
  const handleSubmit = useCallback(
    async (data: SignInFormData) => {
      try {
        formRef.current?.setErrors({});

        const schema = Yup.object().shape({
          email: Yup.string()
            .required('E-mail obrigatório')
            .email('Digite um e-mail válido'),
          password: Yup.string().required('Senha obrigatória'),
        });

        await schema.validate(data, {
          abortEarly: false,
        });

        signIn({
          email: data.email,
          password: data.password,
        });
//...
```

## Mantendo usuário no storage
Vamos usar o localStorage para armazenar a informação do usuário. Para garantirmos um pouco de segurança, vamos deixar o token expirar com o menor tempo possível, para o usuário logar de novo. No app, poderemos fazer um refresh token.
Em `src/context/AuthContext.tsx` vamos salvar no localStorage as informações de token e user
```tsx
//...
    const { token, user } = response.data;

    localStorage.setItem('@GoBarber:token', token);
    localStorage.setItem('@GoBarber:user', JSON.stringify(user));
//...
```
E para passar essa informação para outros lugares, vamos armazenar no state
```tsx
import React, { createContext, useCallback, useState } from 'react';

interface AuthState {
  token: string;
  user: object;
}
//...
interface AuthContextData {
  user: object;
  signIn(credentials: SignInCredentials): Promise<void>;
}
//...
const AuthProvider: React.FC = ({ children }) => {
  const [data, setData] = useState<AuthState>(() => {
    const token = localStorage.getItem('@GoBarber:token');
    const user = localStorage.getItem('@GoBarber:user');

    if (token && user) {
      return { token, user: JSON.parse(user) };
    }

    return {} as AuthState;
  });
//...
  return (
    <AuthContext.Provider value={{ user: data.user, signIn }}>
```

Em `src/pages/SignIn/index.tsx` adicionamos o user no useContext e ao fazermos login, podemos ver no console e no localStorage o token e o user salvos
```tsx
//...
  const { user, signIn } = useContext(AuthContext);
  console.log(user);
```
