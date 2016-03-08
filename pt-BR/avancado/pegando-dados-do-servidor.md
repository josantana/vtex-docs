# Pegando dados do servidor

Temos duas páginas: a home e a página de produto.

> Mas o que é uma pagina de produto sem produtos?

Vamos aprender a pegar dados da API pra preencher essa página.

### Criando o arquivo de definição do componente

Copie o JSON e coloque no arquivo `storefront/components/ProductPage.json`:

```json
{
  "resourceBindings": [
    {
      "locator": "search@vtex.storefront-sdk",
      "relativePath": "/products",
      "queryParams": {
        "pageSize": 5
      },
      "bindTo": "products@vtex"
    }
  ],
  "assets": [
    "common.js",
    "ProductPage.js"
  ]
}
```

Veja que inserimos uma nova propriedade chamada `resourceBindings`. Os parâmetros que ela recebe são:

- **locator**: identificador do resource que será usado. Neste caso estamos usando um resource do SDK chamado ["search"](https://github.com/vtex-apps/storefront-sdk/blob/master/storefront/resources/search.json), responsável por prover o base-endpoint da chamada.

- **relativePath**: é o complemento necessário para a URL de locator definida. 
  * Os paths também podem conter **variáveis**:  _"/\{\{ account }}/products/\{\{ route.slug }}/"_

Imagine que você tem uma API com a URL `http://minhapi.com.br`, o `relativePath` complementa essa URL tendo como resultado `http://minhaapi.com.br/acount-name-da-loja/nome-do-produto`.

- **bindTo**: É o nome da chave que o resource irá receber na store do Storefront SDK.

- **queryParams**: São os parametros que serão repassados na query string durante a chamada.


O `resourceBindings` liga uma rota a uma chamada a API. Voce pode declarar quantos `bindings` quiser, portanto, diferentes dados podem ser pré-carregados para uma mesma página.

Carregue a página de produto no browser ([http://sualoja.local.myvtex.com:3000/moto-x/p](http://sualoja.local.myvtex.com:3000/moto-x/p)), clique com o botão direito do mouse e veja o código fonte. Você pode ver que os dados do produto estão impressos na página. O SDK pega esses dados automaticamente e os insere dentro da store "ProductStore".

### Criando o componente React da página

Vamos fazer com que o nosso código React acesse essa store e imprima o nome do produto na página.

Copie o seguinte código e coloque no arquivo `src/pages/ProductPage/ProductPage.js`:
```js
import React from 'react';
import { stores } from 'sdk';

class ProductPage extends React.Component {
  render() {
    // Pega o parametro slug da rota
    let slug = this.props.params.slug;

    // Pega o estado atual da ProductStore
    let ProductStore = stores.ProductStore.getState();
    // Pega o produto com o slug da rota
    let product = ProductStore.get(slug);

    let productName = product ? product.name : 'carregando...';

    return (
      <div>
        <h1>Essa é a página do produto: {productName}</h1>
      </div>
    );
  }
}

export default ProductPage;

```

Veja que estamos usando a função `get`. Isso é um método de objetos do Immutable.

> Mas o quê?

---

## Uma rápida explicação sobre o Immutable em 30 segundos

Todas as stores fornecidas pelo SDK usam a biblioteca [Immutable](http://facebook.github.io/immutable-js/). Como o nome já indica, ela permite criar objetos imutáveis. Com isso ganhamos duas vantagens:

1. Comparação de objetos de forma extremamente eficiente (em tempo [O(1)](https://en.wikipedia.org/wiki/Analysis_of_algorithms#Orders_of_growth))
2. Segurança de que nenhuma outra parte do código vai alterar o objeto que você está usando

Imagine o seguinte objeto da ProdutStore:

```js
var products = ProductStore.getState();
// {
//   "camisa-polo": {
//     "name": "Camisa Polo",
//     "slug": "camisa-polo",
//     "brand": {
//       "name": "Lacoste",
//       "slug": "lacoste"
//     }
//     ...
//   },
//   "meia-branca": {
//     "name": "Meia branca",
//     "slug": "meia-branca",
//     ...
//   }
// }
```

Para pegarmos os dados do produto "camisa-polo":

Em um JSON| Em um objeto Immutable
---|---
`products["camisa-polo"]`|`products.get('camisa-polo')`

Para pegarmos o nome da marca da "camisa-polo":

Em um JSON| Em um objeto Immutable
---|---
`products["camisa-polo"].brand.name`|`products.getIn(['camisa-polo', 'brand', 'name'])`

Para este guia, essas informações são o suficiente. Porém, existem outros métodos bastante úteis: você pode ler sobre eles na [documentação do Immutable](http://facebook.github.io/immutable-js/docs/).

---

Finalmente, abra a página no browser e veja o nome na tela:

[http://sualoja.local.myvtex.com:3000/moto-x/p](http://sualoja.local.myvtex.com:3000/moto-x/p)

Conseguimos!

### Criando um link entre as páginas

Vamos criar um link para a home para que possamos testar mais facilmente.

Substitua o código do arquivo `src/components/ProductPage/ProductPage.js` por:

```js
import React from 'react';
import { stores } from 'sdk';
// Importa o component React "Link" fornecido pela biblioteca "React Router"
import { Link } from 'react-router';

class ProductPage extends React.Component {
  render() {
    // Pega o parametro slug da rota
    let slug = this.props.params.slug;

    // Pega o estado atual da ProductStore
    let ProductStore = stores.ProductStore.getState();
    // Pega o produto com o slug da rota
    let product = ProductStore.get(slug);

    let productName = product ? product.name : 'carregando...';

    return (
      <div>
        <h1>Essa é a página do produto: {productName}</h1>
        <Link to="/">Ir para a home</Link>
      </div>
    );
  }
}

export default ProductPage;
```

O componente link gera uma tag `<a>` com o atributo `href` para a URL da rota, porém intercepta o comportamento do browser e faz com que apenas o componente React renderizado na página mude. Veja funcionando no browser!

Também precisamos de um link na home para a página de produto.

Copie e cole o código abaixo no arquivo `src/components/HomePage/HomePage.js`:

```js
import React from 'react';
import './HomePage.less';
import HelloWorld from 'components/HelloWorld/HelloWorld';
import { Link } from 'react-router';

class HomePage extends React.Component {
  render() {
    return (
      <div>
        <HelloWorld />
        <p className="message">Crie, construa, inove!</p>
        <Link to="/moto-x/p">Ver produto Moto X</Link>
      </div>
    );
  }
}

export default HomePage;

```

Para fazer o link para a página de produto, precisamos informar alguns dados para que o componente `Link` consiga montar a URL.

Legal, agora você pode ir de uma página pra outra de forma rápida.

### Ah, não... tem um bug! :anguished:

Entretanto, temos um bug! Siga os passos para reproduzir:

- Abra a página home
- De um refresh no browser
- Navegue até a página de produto
- O página de produto mostra "carregando..." e não mostra a página de produto

> Por que isso acontece?

Quando o usuário carrega a página de produto, o seguinte acontece:

- O servidor coloca os dados diretamente no HTML da página por conta do `resourceBinding`
- O SDK pega esses dados e coloca na "ProductStore"

Porém, quando o usuário carrega a página home, como ela não tem `resourceBinding`, o servidor não coloca os dados da página de produto que iremos abrir e, com isso, a "ProductStore" fica vazia.

O que precisamos fazer é pegar os recursos associados a rota que iremos abrir, ou seja, quando o usuário abrir a página de produto, fazemos um request AJAX para o servidor pedindo os recursos da rota. Quando o AJAX terminar, a "ProductStore" será automaticamente preenchida com os dados do produto.

### Carregando resources de forma assíncronamente

Abra o arquivo `src/components/ProductPage/ProductPage.js` e substitua o conteúdo pelo seguinte código:

```js
import React from 'react';
import { stores, actions } from 'sdk';
import { Link } from 'react-router';

class ProductPage extends React.Component {
  constructor(props){
    super(props);

    this.state = {
      product: stores.ProductStore.getState().get(this.props.params.slug)
    }

    // Escuta as mudanças da "ProductStore", registramos o método "onChange" como callback
    stores.ProductStore.listen(this.onChange);

    // Pega o path atual
    let currentURL = (window.location.pathname + window.location.search);
    // Caso a "ResourceStore" não tenha os resources da pagina carregada
    if (!stores.ResourceStore.getState().get(currentURL)) {
      // Pede os resources da página "product" passando os parâmetros necessários (neste caso o slug)
      // Essa action faz uma chamada AJAX ao servidor do Storefront
      actions.ResourceActions.getRouteResources(currentURL, 'product', this.props.params);
    }
  }

  // O React chamará esse método quando o componente está saindo da tela
  componentWillUnmount() {
    // Para de escutar as mudanças
    stores.ProductStore.unlisten(this.onChange);
  }

  // Esse método será chamado toda vez que a "ProductStore" mudar
  onChange = () => {
    // Muda o estado do componente, isso fará com que ele renderize novamente
    this.setState({
      // Pega o estado atual da store (provavelmente agora ela terá os dados do produto)
      product: stores.ProductStore.getState().get(this.props.params.slug)
    });
  }

  render() {
      // Pega o produto com o slug da rota
    let product = this.state.product;

    let productName = product ? product.name : 'carregando...';

    return (
      <div>
        <h1>Essa é a página do produto: {productName}</h1>
        <Link to="/">Ir para a home</Link>
      </div>
    );
  }
}

export default ProductPage;
```

Bug corrigido! A navegação entre as páginas agora funciona perfeitamente.

Tente exibir outras informações do Produto na página! (Dica: dê um `console.log(product)` para saber quais propriedades estão disponíveis)

---

## Recapitulando

Você completou o "Pegando dados do servidor"! Você aprendeu a usar `resourceBindings` e como obter dados de uma página de forma assíncrona.

---

## Próximos passos

Agora vamos aprender a [salvar dados no servidor](/salvando-dados-no-servidor.md).
