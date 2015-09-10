# Melhorando o processo de desenvolvimento

Apesar de funcionar bem, essa forma de desenvolvimento pode se tornar tediosa à medida que sua app começa a crescer. Arquivos Javascript e CSS começam a se proliferar e escrever React sem JSX é bem estranho. Além disso, nosso código não está minificado e não estamos usando nenhum pré-processador de CSS, como LESS ou SASS.

Para resolver tudo isso, usamos a ferramenta de build Webpack e uma estrutura de desenvolvimento opinionada que usa ES6 (e até o 7!), eslint, LESS, react-hot-loader e Webpack:

 - ES6 é a mais recente versão do Javascript que foi lançada em 2015, então já estamos escrevendo. Mas não esquecemos do IE8! Usamos o Babel, uma ferramenta que transforma o código ES6 para a versão do Javascript que a maioria dos browsers entende, que é o ES5.

 - eslint é uma ferramenta que padroniza como o código deve ser escrito.

 - LESS é o pré-processador CSS escolhido pelo VTEX Lab, também um dos mais usados pela comunidade front-end.

 - O `react-hot-loader` é uma espécie de livereload 2.0 que, ao invés de recarregar toda a página, atualiza apenas o componente React que foi modificado. Isso faz com que o ciclo de desenvolvimento seja *muito* mais rápido.

 - Por fim, o Webpack agrega todas essas ferramentas e faz todas funcionarem como mágica, além de minificar imagens, SVGs, Javascript e CSS.

## Nova estrutura de pastas

Não se apegue à sua app agora, vamos apagar toda essa estrutura e começá-la do zero com essa nova estrutura de desenvolvimento.

Apague todos os arquivos da pasta de sua app. Abra o terminal na mesma pasta e digite:

```sh
yo vtex:storefront
```

Responda o generator com:
- "my-first-app"
- "alphateam"
- "yes" (ou "y")
- "yes" (ou "y")

Você verá que algumas pastas e arquivos foram criados e as dependências do node foram instaladas. Sua app agora deve parecer com isso:

```
.
├── src/
│   ├── assets/
│   ├── components/
│   ├── editors/
│   ├── pages/
│   └── utils/
├── storefront/
│   ├── assets/
│   ├── components/
│   └── resources/
├── .eslintrc
├── .gitignore
├── .vtexignore
├── meta.json
├── package.json
└── webpack.config.js
```

Nessa nova estrutura, escreveremos a maior parte do nosso código dentro da pasta `src/`. Não mexeremos mais na pasta `storefront/assets/`, já que o Webpack se encarregará de colocar os arquivos processados nela.

### src/assets/

Aqui ficam todos os assets que não são Javascript ou CSS — como imagens e SVGs.

### src/components/

Nessa pasta moram grande parte dos componentes React.

### src/editors/

Aqui ficam os componentes que são usados para criar editores: componentes que formam as interfaces que o administrador da loja irá usar para configurar um componente.

### src/pages/

Os arquivos nesta pasta são componentes React responsáveis por responder por uma rota.

### src/utils/

Componentes React utilitários e outros arquivos Javascript.

### .eslintrc

Arquivo de configuração do eslint.

### .gitignore

Arquivo importante caso use git, ferramenta de versionamento de código.

### .vtexignore

O `.vtexignore` impede que arquivos sejam enviados para a Gallery/Sandbox desnecessariamente. Note que a pasta `src/` está incluida nessa lista, pois esse é o código fonte não processado: o que queremos enviar são apenas os arquivos gerados pelo Webpack, que são arquivos compilados, minificados e otimizados que vão morar dentro da pasta `storefront/assets/`.

### package.json

Arquivo necessário para projetos Javascript que usam o npm, o package manager de Javascript.

### webpack.config.js

Esse arquivo possui todas as configurações do Webpack.

## Buildando o código fonte

Para fazer o build do projeto vamos usar o Webpack.

Abra o terminal na pasta de sua app e rode o webpack (por padrão, ele está dando watch na pasta `src/`):
```sh
webpack
```

Você pode ver que quatro arquivos foram criados na pasta `storefront/assets/`. Todos eles são códigos Javascript minificados com respectivos *source maps* (arquivos que facilitam debugar o código em ferramentas como o Chrome Developer Tools). Perceba que mesmo tendo arquivos LESS no código fonte, nos arquivos gerados existem apenas arquivos Javascript. O Webpack coloca todo o código CSS dentro do Javascript para se alavancar do cache do browser. Ele também se encarrega de inserir o CSS no código HTML, fazendo com que tudo funcione normalmente.

Agora que temos os arquivos dentro da pasta `storefront/assets/`, podemos enviá-los para a sandbox.

Cancele o processo do webpack ou abra outra aba do terminal e digite:

```sh
vtex watch nomedasandbox
```

E confira tudo funcionando no browser.

## Toolbelt ao resgate

Ganhamos a facilidade de escrever em ES6, JSX, LESS, minificar, etc, mas agora temos dois processos rodando, o watch do Toolbelt e o Webpack. Pensando em facilitar ainda mais o desenvolvimento, o Toobelt possui uma opção para quem usa Webpack.

Abra o terminal na pasta de sua app e digite:
```sh
vtex watch nomedasandbox --webpack
```

Dessa forma, o Toolbelt se encarrega de rodar o Webpack e fazer o upload dos arquivos.

## Mais rápido! Mais rápido!

Ainda não estamos rápidos o suficiente, vamos usar um dos grandes diferenciais do combo Webpack+React, o hot-loader.

Pare o Toolbelt que está rodando e ligue-o novamente com a flag `--server`:
```
vtex watch nomedasandbox --server
```

Essa opção liga um servidor local, o Webpack Dev Server, que permite utilizar o hot-loader. Normalmente quando desenvolvemos em servidores locais acessamos URLs como `http://localhost:3000/`, no nosso caso, vamos usar a URL:

[http://basedevmkp.local.myvtex.com:3000/](http://basedevmkp.local.myvtex.com:3000/)

Como mudamos de domínio, de `beta.myvtex.com` para `local.myvtex.com`, devemos configurar os cookies de sandbox e workspace novamente.

Faça uma alteração no componente `HomePage.js` ou em um arquivo CSS e veja as mudanças aplicadas sem dar reload na página.

---

Você completou o "Melhorando o processo de desenvolvimento"! Agora estamos desenvolvendo de forma eficiente e produtiva.

Próximo passo: [Criando uma nova página](3-criando-uma-nova-pagina.md)
