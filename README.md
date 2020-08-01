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

        await signIn();
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

        await signIn({
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

## Criando hook useAuth
Ao invés de termos
```tsx
  const { user, signIn } = useContext(AuthContext);
```
Poderíamos ter
```tsx
  const { user, signIn } = useAuth();
```

Então, vamos criar o `useAuth` em `src/context/AuthContext.tsx`
```tsx
function useAuth(): AuthContextData {
  const context = useContext(AuthContext);

  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }

  return context;
}

export { AuthProvider, useAuth };
```

E agora em `src/pages/SignIn/index.tsx` vamos importar somente `useAuth` e verificamos se ainda está funcionando com o console.log do user
```tsx
import { useAuth } from '../../context/AuthContext';
//...
  const { user, signIn } = useAuth();
  console.log(user)
```
Removemos o user, pois não precisaremos utilizar nessa página por enquanto
```tsx
  const { signIn } = useAuth();
```

## Logout da aplicação
Em `src/context/AuthContext.tsx` adicionamos o método de signOut
```tsx
interface AuthContextData {
  user: object;
  signIn(credentials: SignInCredentials): Promise<void>;
  signOut(): void;
}
//...
  const signOut = useCallback(() => {
    localStorage.removeItem('@GoBarber:token');
    localStorage.removeItem('@GoBarber:user');

    setData({} as AuthState);
  }, []);

  return (
    <AuthContext.Provider value={{ user: data.user, signIn, signOut }}>
```

Mudamos o nome da pasta de `context` para `hooks` e arrumar a importação
```tsx
import { useAuth } from '../../hooks/AuthContext';
```

# Mensagens de toast

## Estrutura de toasts
Quando der um erro na aplicação, vamos disparar um toast. Vamos criar um componente `src/components/ToastContainer/indext.tsx`
```tsx
import React from 'react';
import { FiAlertCircle, FiXCircle } from 'react-icons/fi';

import { Container, Toast } from './styles';

const ToastContainer: React.FC = () => {
  return (
    <Container>
      <Toast>
        <FiAlertCircle size={20} />

        <div>
          <strong>Aconteceu um erro</strong>
          <p>Não foi possível fazer login na aplicação</p>
        </div>

        <button type="button">
          <FiXCircle size={18} />
        </button>
      </Toast>
    </Container>
  );
};

export default ToastContainer;
```
E importamos ele no `App.tsx`
```tsx
import ToastContainer from './components/ToastContainer';
//...
      <ToastContainer />
```

E agora, vamos estilizar o ToastContainer e também adicionar novas props para cada type de toast (success, error, default) ou quando não tiver description.

Em `src/components/ToastContainer/styles.ts`
```ts
import styled, { css } from 'styled-components';

interface ToastProps {
  type?: 'success' | 'error' | 'info';
  hasDescription: boolean;
}

const toastTypeVariation = {
  info: css`
    background: #ebf8ff;
    color: #3172b7;
  `,
  success: css`
    background: #e6fffa;
    color: #2e656a;
  `,
  error: css`
    background: #fddede;
    color: #c53030;
  `,
};

export const Container = styled.div`
  position: absolute;
  right: 0;
  top: 0;
  padding: 30px;
  overflow: hidden;
`;

export const Toast = styled.div<ToastProps>`
  width: 360px;

  position: relative;
  padding: 16px 30px 16px 16px;
  border-radius: 10px;
  box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.2);

  display: flex;

  & + div {
    margin-top: 8px;
  }

  ${(props) => toastTypeVariation[props.type || 'info']}

  > svg {
    margin: 4px 12px 0 0;
  }

  div {
    flex: 1;

    p {
      margin-top: 4px;
      font-size: 14px;
      opacity: 0.8;
      line-height: 20px;
    }
  }

  button {
    position: absolute;
    right: 16px;
    top: 19px;
    opacity: 0.6;
    border: 0;
    background: transparent;
    color: inherit;
  }

  ${(props) =>
    !props.hasDescription &&
    css`
      align-items: center;

      svg {
        margin-top: 0;
      }
    `}
`;
```

Em `src/components/ToastContainer/index.tsx`
```tsx
//...
  return (
    <Container>
      <Toast hasDescription>
        ...
      </Toast>

      <Toast type="success" hasDescription={false}>
        ...
      </Toast>

      <Toast type="error" hasDescription>
        ...
      </Toast>
    </Container>
  );
};

export default ToastContainer;
```

## Criando hook de toast
Para que os toasts apareçam dinamicamente na nossa aplicação `src/hooks/toast.tsx`
```tsx
import React, { createContext, useCallback, useContext } from 'react';

interface ToastContextData {
  addToast(): void;
  removeToast(): void;
}

const ToastContext = createContext<ToastContextData>({} as ToastContextData);

const ToastProvider: React.FC = ({ children }) => {
  const addToast = useCallback(() => {
    console.log('add toast');
  }, []);

  const removeToast = useCallback(() => {
    console.log('add toast');
  }, []);

  return (
    <ToastContext.Provider value={{ addToast, removeToast }}>
      {children}
    </ToastContext.Provider>
  );
};

function useToast(): ToastContextData {
  const context = useContext(ToastContext);

  if (!context) {
    throw new Error('useToast must be used within a ToastProvider');
  }

  return context;
}

export { ToastProvider, useToast };
```

Mas para não poluir o `App.tsx`, vamos criar um `src/hooks/index.tsx` para fazer a importação de todos os Providers num lugar só
```tsx
import React from 'react';

import { AuthProvider } from './auth';
import { ToastProvider } from './toast';

const AppProvider: React.FC = ({ children }) => {
  return (
    <AuthProvider>
      <ToastProvider>{children}</ToastProvider>
    </AuthProvider>
  );
};

export default AppProvider;
```

E então fazemos a importação em `App.tsx` e movemos o ToastContainer para dentro do contexto (hook) de Toast
```tsx
//...
      <AppProvider>
        {/* <SignUp /> */}
        <SignIn />
      </AppProvider>
```

Em `src/hooks/toast.tsx`
```tsx
//...
  return (
    <ToastContext.Provider value={{ addToast, removeToast }}>
      {children}
      <ToastContainer />
    </ToastContext.Provider>
  );
```

Agora podemos importar em SignIn o useToast `src/pages/SignIn/index.tsx`
```tsx
import { useToast } from '../../hooks/toast';
//...
  const { addToast } = useToast();
//...
      } catch (err) {
        if (err instanceof Yup.ValidationError) {
          const errors = getValidationErrors(err);
          formRef.current?.setErrors(errors);
        }

        addToast();
      }
    },
    [signIn, addToast],
  ```

## Adicionando e removendo Toasts
Usamos o estado para armazenar os toasts. Em `src/hooks/toast.tsx`, vamos importar `useState` e criar as constantes de `messages` e `setMessages` para armazenar o array de toasts.
```tsx
import React, { createContext, useCallback, useContext, useState } from 'react';
//...
interface ToastMessage {
  id: string;
  type?: 'success' | 'error' | 'info'
  title: string;
  description?: string;
}
//...
  const [messages, setMessages] = useState<ToastMessage[]>([]);
```

Vamos instalar o pacote para criar os ids dos toasts
```bash
yarn add uuidv4
```

No método `addToast`, primeiramente criamos um `id`. O parâmetro recebido `message` tem todas as propriedades da interface de `ToastMessage` menos o `id`, por isso, usamos o `Omit`. Em `src/hooks/toast.tsx`
```tsx
  const [messages, setMessages] = useState<ToastMessage[]>([]);
  const addToast = useCallback(({ type, title, description }: Omit<ToastMessage, 'id'>) => {
    const id = uuid();

    const toast = {
      id,
      type,
      title,
      description
    }

    setMessages(state => [ ...state, toast ])
  }, []);
//...
      <ToastContainer messages={messages} />
```

Em `src/components/ToastContainer/index.tsx`
```tsx
import { ToastMessage } from '../../hooks/toast';
import { Container, Toast } from './styles';

interface ToastContainerProps {
  messages: ToastMessage[]
}

const ToastContainer: React.FC<ToastContainerProps> = ({ messages }) => {
  return (
    <Container>
      {messages.map(message => (
        <Toast key={message.id} type={message.type} hasDescription={!!message.description}>
          <FiAlertCircle size={20} />

          <div>
            <strong>{message.title}</strong>
            {message.description && <p>{message.description}</p>}
          </div>

          <button type="button">
            <FiXCircle size={18} />
          </button>
        </Toast>
      ))}
    </Container>
  );
};
```

E para remover um toast, em `src/hooks/toast.tsx`
```tsx
interface ToastContextData {
  addToast(message: Omit<ToastMessage, 'id'>): void;
  removeToast(id: string): void;
}

  const removeToast = useCallback((id: string) => {
    setMessages(state => state.filter(message => message.id !== id))
  }, []);
  ```

  E adicionamos no botão de remover toast lá de `src/components/ToastContainer/index.tsx`
  ```tsx
import { ToastMessage, useToast } from '../../hooks/toast';
//...
  const { removeToast } = useToast();
//...
          <button type="button" onClick={() => removeToast(message.id)}>
```

E vamos também fazer com que o Toast feche sozinho depois de um tempo, lembrando que precisamos limpar o timeout se o usuário fechar o toast antes. Então, vamos isolar o Toast do ToastContainer criando uma pasta `src/components/ToastContainer/Toast/` e passando o componente para lá. Também adicionamos outros icons para quando for type success ou info.
```tsx
import React, { useEffect } from 'react';
import { FiAlertCircle, FiCheck, FiInfo, FiXCircle } from 'react-icons/fi';

import { ToastMessage, useToast } from '../../../hooks/toast';

import { Container } from './styles'

interface ToastProps {
  message: ToastMessage;
}

const icons = {
  info: <FiInfo size={24} />,
  error: <FiAlertCircle size={24} />,
  success: <FiCheck size={24} />,
}

const Toast: React.FC<ToastProps> = ({ message }) => {
  const { removeToast } = useToast();

  useEffect(() => {
    const timer = setTimeout(() => {
      removeToast(message.id)
    }, 3000)

    return () => clearTimeout(timer);
  }, [removeToast, message.id]);

  return (
    <Container type={message.type} hasDescription={!!message.description}>
      {icons[message.type || 'info']}

      <div>
        <strong>{message.title}</strong>
        {message.description && <p>{message.description}</p>}
      </div>

      <button type="button" onClick={() => removeToast(message.id)}>
        <FiXCircle size={18} />
      </button>
    </Container>
  )
}

export default Toast;
```
Com seu estilo
```ts
import styled, { css } from 'styled-components';

interface ToastProps {
  type?: 'success' | 'error' | 'info';
  hasDescription: boolean;
}

const toastTypeVariation = {
  info: css`
    background: #ebf8ff;
    color: #3172b7;
  `,
  success: css`
    background: #e6fffa;
    color: #2e656a;
  `,
  error: css`
    background: #fddede;
    color: #c53030;
  `,
};


export const Container = styled.div<ToastProps>`
  width: 360px;

  position: relative;
  padding: 16px 30px 16px 16px;
  border-radius: 10px;
  box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.2);

  display: flex;

  & + div {
    margin-top: 8px;
  }

  ${(props) => toastTypeVariation[props.type || 'info']}

  > svg {
    margin: 4px 12px 0 0;
  }

  div {
    flex: 1;

    p {
      margin-top: 4px;
      font-size: 14px;
      opacity: 0.8;
      line-height: 20px;
    }
  }

  button {
    position: absolute;
    right: 16px;
    top: 19px;
    opacity: 0.6;
    border: 0;
    background: transparent;
    color: inherit;
  }

  ${(props) =>
    !props.hasDescription &&
    css`
      align-items: center;

      svg {
        margin-top: 0;
      }
    `}
`;
```

## Animação dos Toasts
Adicionar `react-spring`
```bash
yarn add react-spring
```
Para controlar quando um elemento entra e sai de uma tela, usamos o `useTransition` que recebe 3 parâmetros, o primeiro são as `messages`, o segundo parâmetro é uma função que vai obter a chave da `message` que é única, e o terceiro parâmetro é um objeto contendo as animações. Em `src/components/ToastContainer/index.tsx`
```tsx
import { useTransition } from 'react-spring';
//...
  const messagesWithTransitions = useTransition(
    messages,
    message => message.id,
    {
      from: { right: '-120%', opacity: 0 },
      enter: { right: '0%', opacity: 1 },
      leave: { right: '-120%', opacity: 0 },
    }
  );
  //...
    <Container>
      {messagesWithTransitions.map(({ item, key, props }) => (
        <Toast
          key={key}
          style={props}
          message={item}
        />
      ))}
    </Container>
```

Em `src/components/ToastContainer/Toast/index.tsx`
```tsx
interface ToastProps {
  message: ToastMessage;
  style: object;
}
//...
const Toast: React.FC<ToastProps> = ({ message, style }) => {
//...
    <Container
      type={message.type}
      hasDescription={!!message.description}
      style={style}
    >
```

Em `src/components/ToastContainer/Toast/styles.ts`
```ts
import { animated } from 'react-spring';
//...
export const Container = styled(animated.div)<ToastProps>`
```

# Rotas da aplicação
## Configurando Rotas
Instalar `react-router-dom`
```bash
yarn add react-router-dom
yarn add -D @types/react-router-dom
```

Criamos um arquivo `src/routes/index.tsx`
```tsx
import React from 'react';
import { Switch, Route } from 'react-router-dom';

import SignIn from '../pages/SignIn';
import SignUp from '../pages/SignUp';

const Routes: React.FC = () => (
  <Switch>
    <Route path="/" exact component={SignIn} />
    <Route path="/signup" component={SignUp} />
  </Switch>
);

export default Routes;
```
Alteramos em `src/App.tsx`
```tsx
import { BrowserRouter as Router } from 'react-router-dom';

import Routes from './routes';

const App: React.FC = () => {
  return (
    <Router>
      <AppProvider>
        <Routes />
      </AppProvider>

      <GlobalStyle />
    </Router>
  );
};
```

Em `src/pages/SignIn/index.tsx`
```tsx
import { Link } from 'react-router-dom';
//...
        <Link to="/signup">
          <FiLogIn />
          Criar conta
        </Link>
```

Em `src/pages/SignUp/index.tsx` fazer o mesmo.

Agora vamos fazer uma animação ao carregar as páginas de SignIn e SignUp envolvendo o conteúdo de `<Content>` com outra tag `<AnimationContainer>` e fazendo o keyframe na estilização
```ts
import styled, { keyframes } from 'styled-components';
//...
export const Content = styled.div`
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  width: 100%;
  max-width: 700px;
`;

const appearFromLeft = keyframes`
  from {
    opacity: 0;
    transform: translateX(-50px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
`;

export const AnimationContainer = styled.div`
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  animation: ${appearFromLeft} 1s;

  form {
    margin: 80px 0;
    width: 340px;
    text-align: center;

    h1 {
      margin-bottom: 24px;
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
```
E na página de SignUp replicar o mesmo style porém fazendo animation da direita.

## Rotas autenticadas
Criar um componente de rotas que se o usuário estiver logado, ser redirecionado para a página Dashboard, e se caso não esteja logado, vá para página de SignIn. Em `src/routes/Route.tsx`
```tsx
import React from 'react';
import {
  Route as ReactDOMRoute,
  RouteProps as ReactDOMRouteProps,
  Redirect,
} from 'react-router-dom';
import { useAuth } from '../hooks/auth';

interface RouteProps extends ReactDOMRouteProps {
  isPrivate?: boolean;
  component: React.ComponentType;
}

const Route: React.FC<RouteProps> = ({ isPrivate = false, component: Component, ...rest }) => {
  const { user } = useAuth();

  return (
    <ReactDOMRoute
      {...rest}
      render={({ location }) => {
        return isPrivate === !!user ? (
          <Component />
        ) : (
          <Redirect to={{
            pathname: isPrivate ? '/' : '/dashboard',
            state: { from: location }
          }} />
        )
      }}
    />
  );
}

export default Route;
```

Importamos em `src/routes/index.tsx`
```tsx
import Route from './Route';
//...
    <Route path="/dashboard" component={Dashboard} isPrivate />
```

E também criamos a página de Dashboard `src/pages/Dashboard/index.tsx`
```tsx
import React from 'react';

const Dashboard: React.FC = () => {
  return (
    <h1>Hello</h1>
  );
}

export default Dashboard;
```

## Finalizando App
Na tela de cadastro vamos adicionar o `useHistory` e o `useToast` para quando o cadastro for realizado com sucesso, assim como, o toast se der algo errado.
```tsx
import { Link, useHistory } from 'react-router-dom';
import api from '../../services/api';
import { useToast } from '../../hooks/toast';
//...
interface SignUpFormData {
  name: string;
  email: string;
  password: string;
}

const SignUp: React.FC = () => {
  //...
  const history = useHistory();

  const handleSubmit = useCallback(async (data: SignUpFormData) => {
      await api.post('/users', data);

      history.push('/');

      addToast({
        type: 'success',
        title: 'Cadastro realizado!',
        description: 'Você já pode fazer o seu login no GoBarber!'
      });
    } catch (err) {
      if (err instanceof Yup.ValidationError) {
        const errors = getValidationErrors(err);
        formRef.current?.setErrors(errors);

        return;
      }

      addToast({
        type: 'error',
        title: 'Erro no cadastro',
        description: 'Ocorreu um erro ao fazer cadastro. Tente novamente.'
      });
    }
  }, [addToast, history]);
```

E no SignIn faltou adicionarmos o `useHistory` para redirecionar para o Dashboard
```tsx
import { Link, useHistory } from 'react-router-dom';
//...
  const history = useHistory();

        history.push('/dashboard');
```

Continuar em [Recuperação de senha](README2.md)
