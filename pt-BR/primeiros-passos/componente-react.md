# Componente React

Vamos criar nosso primeiro componente React no Storefront. Esse componente será responsável pela renderização da home.

Crie a pasta `storefront`. Tudo que estiver dentro dessa pasta será interpretado pelo Storefront (app "vtex.storefront"). Iremos pouco a pouco apresentando o que o Storefront oferece e quais pastas e arquivos devem ser criados.

Crie a pasta `assets` dentro da pasta `storefront`.

Você deve ter algo assim:

```sh
.
├── meta.json
└── storefront/
    └── assets/
```

Em `assets` devem ficar todos os arquivos Javascript, CSS, imagens e fontes. Crie o arquivo `HomePage.jsx` dentro dela com um componente React simples:

### `HomePage.jsx`

```js
import React from 'react';

class HomePage extends React.Component {
  render() {
    return (
      <h1>Hello world!</h1>
    );
  }
}
```

Como ele é um arquivo que contém código JSX, devemos compilá-lo.

Para isso, vamos usar o Babel. Para instalá-lo, use `npm install babel-cli`. Além disso, também precisamos instalar um preset com `npm install babel-preset-react` para que o Babel entenda o código HTML em seu JSX (`<h1>Hello world!</h1>`). Tudo pronto, vamos compilar nosso arquivo:

```sh
babel --presets react ./storefront/assets/**.jsx --out-dir .
```

O Babel irá gerar o arquivo `HomePage.js` com o código compilado na mesma pasta do arquivo JSX.

> Caso você receba como resposta que o babel não foi encontrado, adicione `./node_modules/.bin` ao seu $PATH. Dessa forma conseguimos executar qualquer script local como se estivesse instalado globalmente.

## Recapitulando

Na plataforma VTEX existem apps de serviço, o Storefront é um deles. Um app de serviço exerce suas funções apenas nos arquivos que estão dentro de sua pasta. A pasta `storefront` possui sua estrutura própria.

Na pasta `storefront/assets/` ficam todos os arquivos estáticos de seu app que serão consumidos pelo Storefront.

Criamos um arquivo Javascript que usa novos recursos da linguagem Javascript (ES6) e JSX. Compilamos usando o Babel para Javascript atual (ES5).

---

### Próximos Passos

Criamos nosso primeiro asset, um componente React. Vamos agora criar o [descritor do componente](descritor-de-componente.html).
