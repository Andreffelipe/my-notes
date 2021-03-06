# Typescript


<!-- TOC -->

- [Typescript](#typescript)
  - [O que é Typescript](#o-que-é-typescript)
  - [Iniciando com o projeto](#iniciando-com-o-projeto)
  - [Modelando uma classe](#modelando-uma-classe)
    - [Testando para erros](#testando-para-erros)
  - [Configurando o Typescript](#configurando-o-typescript)
    - [Configurando o compilador](#configurando-o-compilador)
  - [Utilizando Typescript](#utilizando-typescript)
    - [Propriedades privadas](#propriedades-privadas)
    - [Evitando JS com erros de compilação](#evitando-js-com-erros-de-compilação)
  - [Watch](#watch)
  - [Obtendo dados do formulário](#obtendo-dados-do-formulário)
  - [Definindo tipos](#definindo-tipos)
    - [Definindo tipos (mais rápido)](#definindo-tipos-mais-rápido)
  - [Casting](#casting)
  - [Listando negociações](#listando-negociações)
    - [Inferição de tipos](#inferição-de-tipos)
    - [Imutabilidade de valores](#imutabilidade-de-valores)
  - [Exibindo o modelo ao usuário](#exibindo-o-modelo-ao-usuário)
    - [Evitando repetição de código](#evitando-repetição-de-código)
      - [Herança](#herança)
    - [Generics](#generics)
    - [Classes Abstratas](#classes-abstratas)
  - [Usando definitions](#usando-definitions)
    - [Typescript Definitions](#typescript-definitions)
  - [Removendo partes inúteis do código](#removendo-partes-inúteis-do-código)

<!-- /TOC -->


## O que é Typescript

É uma linguagem criada pela Microsoft que foi definida como um Superset do ES2015, ou seja, ela contém tudo que o ES2015 contém e adiciona algumas outras características a mais.

Um dos exemplos que você pode ter são:

- Decorators
- Tipagem estática

## Iniciando com o projeto

Nesta pasta você vai encontrar um arquivo [projeto.zip](./projeto.zip) que contém a seguinte estrutura:

- _App_: pasta raiz
  - _css_: Usamos o Bootstrap para ter um visual bacana com custo 0
  - _js_: Nosso Javascript
    - _controllers_
    - _views_
    - _models_
    - _helpers_
  - _lib_: Bibliotecas externas
  - `index.html`: Arquivo inicial

Vamos utilizar este projeto ao longo do curso para poder incluir nossos códigos.

## Modelando uma classe

Para podermos incluir uma nova negociação no nosso banco, vamos primeiro precisar modelá-la em uma classe. Vamos utilizar ES2015, criando o arquivo `models/Negociacao.js`.

Para modelarmos temos algumas regras, primeiro, temos que receber `data`, `quantidade` e `valor` logo quando criamos uma negociação, essas propriedades __não podem ser alteradas depois de criadas__ e __são obrigatórias em todas as negociações__.

A outra regra é que a classe deve saber calcular o seu volume, então precisaremos de um método `volume`:

```js
class Negociacao {
  // Estamos "forçando" uma negociação a receber esses tres parâmetros
  constructor (data, quantidade, valor) {
    this._data = data
    this._quantidade = quantidade
    this._valor = valor
  }

  get data () {
    return this._data
  }

  get quantidade () {
    return this._quantidade
  }

  get valor () {
    return this._valor
  }

  get volume () {
    return this._quantidade * this._valor
  }
}
```

Pontos importantes:

- Veja que estamos usando `_nome` para definir propriedades "privadas" que não podem ser acessadas nem alteradas por outros métodos que não sejam os da própria classe
- Criamos getters para pegar esses valores

### Testando para erros

Incluimos nosso script de negociação no nosso `index.html`:

```html
  <script src="js/models/Negociacao.js"></script>
  <script src="js/app.js"></script>
```

Veja que adicionamos um arquivo `app.js`, este arquivo é o nosso arquivo responsável por permitir que testemos nossas classes. Ele está criado dentro da pasta `js` e tem essa estrutura:

```js
const negociacao = new Negociacao(new Date(), 1, 100)

console.log(negociacao)
```

Será que estamos garantindo que nossas regras estão sendo seguidas?

O primeiro problema é que podemos criar uma classe sem nenhum dos parâmetros do construtor, podemos criar um tratamento de erros desta forma no construtor da classe:

```js
if (!data) throw new Error('data deve ser preenchida')
if (!quantidade) throw new Error('quantidade deve ser preenchida')
if (!valor) throw new Error('valor deve ser preenchida')
```

O outro problema é que o programador ainda pode usar `new Negociacao()` no código, porque só vamos ter erros em _runtime_... Ou seja, vamos saber disso só em tempo de execução.

Da mesma forma podemos alterar as nossas propriedades com `_` sem problemas.

## Configurando o Typescript

O Typescript entra neste ponto. Demoraríamos muito tempo para descobrir que alguma coisa está errada, pois só teríamos erros sendo exibidos em tempo de execução, e não antes disso.

Antes que possamos fazer qualquer coisa, temos que instalar o compilador do typescript. Primeiramente precisamos entrar na nossa pasta do projeto e executar `npm init`. E depois executar `npm i typescript@2.3.2 --save-dev`, estaremos fixando a versão para não termos erros de execução provenientes de versões que possam quebrar (No momento deste texto a versão mais recente é 2.7.2):

```json
{
  "name": "alurabank",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "devDependencies": {
    "typescript": "2.3.2"
  }
}
```

Agora vamos renomear nossa pasta `js` para `ts`, faremos a mesma coisa renomeando o arquivo `app.js` para `app.ts` e também `Negociacao.js` para `Negociacao.ts`. Temos agora a nossa pasta typescript com todos os nossos códigos na linguagem.

### Configurando o compilador

Vamos criar um novo arquivo no projeto chamado `tsconfig.json`, nele vamos colar o texto seguinte:

```json
{
  "compilerOptions": {
    "target": "es6",
    "outDir": "app/js"
  },
  "include": [
    "app/ts/**/*"
  ]
}
```

> Este texto pode ser obtido nas explicações básicas do TS

Basicamente o que estamos dizendo aqui é que vamos compilar o nosso código em ES6 e colocado na pasta `app/js` e vamos fazer isso só com os arquivos que estão na chave `include`, ou seja, todas as coisas dentro da pasta `app/ts` e suas subpastas.

Agora vamos adicionar um script `compile` no `package.json` que vai chamar o compilador `tsc` do Typescript:

```json
{
  "name": "alurabank",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "compile": "tsc"
  },
  "devDependencies": {
    "typescript": "2.3.2"
  }
}
```

Se rodarmos `yarn compile` vamos ter vários erros no nosso arquivo `Negociacao.ts`. Isto porque nossa sintaxe é inválida para o Typescript.

## Utilizando Typescript

O primeiro erro que tivemos é que _"propriedade `data` não existe no tipo `Negociacao`"_, ou seja, temos que declarar propriedades de classe usando a sintaxe Typescript:

```ts
class Negociacao {
  _data;
  _quantidade;
  _valor;

  // Estamos "forçando" uma negociação a receber esses tres parâmetros
  constructor (data, quantidade, valor) {
    this._data = data
    this._quantidade = quantidade
    this._valor = valor
  }

  get data () {
    return this._data
  }

  get quantidade () {
    return this._quantidade
  }

  get valor () {
    return this._valor
  }

  get volume () {
    return this._quantidade * this._valor
  }
}
```

Ou seja, temos que mudar nossa classe para conter nossas propriedades dentro do escopo da classe inicialmente, não dentro do constructor.

### Propriedades privadas

Como no JS não temos como definir propriedades privadas, usamos o TS para poder definir essas propriedades e evitar que o programador possa alterar a propriedade. Então na nossa classe `Negociacao` vamos alterar o seguinte:

```ts
class Negociacao {
  private _data;
  private _quantidade;
  private _valor;

  // Estamos "forçando" uma negociação a receber esses tres parâmetros
  constructor (data, quantidade, valor) {
    this._data = data
    this._quantidade = quantidade
    this._valor = valor
  }

  get data () {
    return this._data
  }

  get quantidade () {
    return this._quantidade
  }

  get valor () {
    return this._valor
  }

  get volume () {
    return this._quantidade * this._valor
  }
}
```

### Evitando JS com erros de compilação

Se rodarmos nosso compilador com o nosso `app.ts` alterando a propriedade privada, então receberemos um erro em tempo de compilação, mas o problema é que ele vai gerar o arquivo compilado mesmo com este erro acontecendo, vamos atualizar o nosso `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "es6",
    "outDir": "app/js",
    "noEmitOnError": true
  },
  "include": [
    "app/ts/**/*"
  ]
}
```

A opção `noEmitOnError` faz com que o compilador __não gere a pasta final__ enquanto estivermos tendo erros de compilação.

## Watch

Compilar toda hora é bastante chato, então podemos fazer um watch nos arquivos TS para rodar o compilador sempre que salvarmos um arquivo TS, para isto vamos criar um novo script no `package.json`:

```json
{
  "name": "alurabank",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "compile": "tsc",
    "watch": "tsc -w"
  },
  "devDependencies": {
    "typescript": "2.3.2"
  }
}
```

Nosso script watch vai executar o `tsc` no modo _watch_, de forma que ele vai ficar ouvindo nossas alterações. Desta forma não vamos precisar ficar recompilando tudo a toda hora.

## Obtendo dados do formulário

Vamos criar um controlador de negociações na nossa pasta `controllers`. Nele vamos ter as propriedades que são os campos do formulário e os métodos de adição e outros:

```ts
class NegociacaoController {
  private _inputData;
  private _inputQuantidade;
  private _inputValor;

  constructor () {
    this._inputData = document.querySelector('#data')
    this._inputQuantidade = document.querySelector('#quantidade')
    this._inputValor = document.querySelector('#valor')
  }

  adiciona (evento) {
    // Lógica de inserção de negociação aqui
  }
}
```

Agora vamos fazer uma alteração no nosso `app.ts` criando nossa lógica de instanciamento de controllers e vamos criar os eventos HTML do formulário:

```ts
const controller = new NegociacaoController();

document.querySelector('.form').addEventListener('submit', controller.adiciona.bind(controller));
```

> Note que `controller.adiciona` está com o `.bind(controller)`, pois o método `adiciona` vai perder a referência para `this` do controller quando executarmos no `app.ts`, o `this` será dinamicamente referenciado para o `this` do `app.ts`

No nosso controller vamos criar a lógica de adição:

```ts
class NegociacaoController {
  private _inputData
  private _inputQuantidade
  private _inputValor

  constructor () {
    this._inputData = document.querySelector('#data')
    this._inputQuantidade = document.querySelector('#quantidade')
    this._inputValor = document.querySelector('#valor')
  }

  adiciona (evento) {
    evento.preventDefault()
    const negociacao = new Negociacao(this._inputData.value, this._inputQuantidade.value, this._inputValor.value)
    console.log(negociacao)
  }
}
```

> Lembre-se que, quando formos importar o script no nosso `index.html` o `app.js` deve ser o __último__ script, pois ele depende de todos os demais.

## Definindo tipos

Temos um problema que vai nos dar uma dor de cabeça razoável. O tipo `any`, ele define que um valor não pode ser inferido por um tipo estático, então vamos ver a nossa classe de `Negociacao` e o nosso controller. Veja que os campos do formulário são sempre `strings`, então vamos ter erros de tipo, pois todas as variáveis serão `strings`.

Um dos pontos mais importantes do TS é que podemos tipar todas as variáveis. Quando não tivermos nenhum tipo explicito definido, ele vai adotar um tipo `any`. Para impedirmos a compilação de arquivos com este tipo implicito configurado, temos que adicionar uma nova linha no nosso `tsconfig`:

```json
{
  "compilerOptions": {
    "target": "es6",
    "outDir": "app/js",
    "noEmitOnError": true,
    "noImplicitAny": true
  },
  "include": [
    "app/ts/**/*"
  ]
}
```

> O `noImplicitAny` fará com que o compilador de erros quando encontrar qualquer um dos tipos Any implicitos
>
> O TS obtém os tipos de um tipo de arquivos chamado `ts.d`, chamado normalmente de _Typescript Definition_, estes tipos vem definidos diretamente no compilador quando instalamos o pacote

Para podermos tipar nossas variáveis, vamos adicionar `:` seguido do tipo que queremos logo depois da definição das mesmas:

```ts
class NegociacaoController {
  private _inputData: Date
  private _inputQuantidade: number
  private _inputValor: number
}
```

Podemos fazer isso na nossa classe de negociação:

```ts
class Negociacao {
  private _data: Date
  private _quantidade: number
  private _valor: number

  // Estamos "forçando" uma negociação a receber esses tres parâmetros
  constructor (data: Date, quantidade: number, valor: number) {
    this._data = data
    this._quantidade = quantidade
    this._valor = valor
  }

  get data () {
    return this._data
  }

  get quantidade () {
    return this._quantidade
  }

  get valor () {
    return this._valor
  }

  get volume () {
    return this._quantidade * this._valor
  }
}
```

### Definindo tipos (mais rápido)

Veja que isto ficou um pouco verboso, podemos simplesmente resumir desta forma:

```ts
class Negociacao {
  // Estamos "forçando" uma negociação a receber esses tres parâmetros
  constructor (private _data: Date, private _quantidade: number, private _valor: number) {}

  get data () {
    return this._data
  }

  get quantidade () {
    return this._quantidade
  }

  get valor () {
    return this._valor
  }

  get volume () {
    return this._quantidade * this._valor
  }
}
```

Agora estamos falando que as propriedades do construtor vão também ser propriedades da própria classe.

## Casting

Quando formos fazer a mesma coisa no `NegociacaoController` vamos ter que definir o tipo como um `Element`, visto que o tipo retornado por `document.querySelector` é sempre um elemento.

```ts
class NegociacaoController {
  private _inputData: Element
  private _inputQuantidade: Element
  private _inputValor: Element

  constructor () {
    this._inputData = document.querySelector('#data')
    this._inputQuantidade = document.querySelector('#quantidade')
    this._inputValor = document.querySelector('#valor')
  }

  adiciona (evento: Event) {
    evento.preventDefault()
    const negociacao = new Negociacao(this._inputData.value, this._inputQuantidade.value, this._inputValor.value)
    console.log(negociacao)
  }
}
```

Mas agora vamos ter um outro tipo de erro, falando que o _"Elemento não tem a propriedade value"_. Isto porque o que estamos falando para o TS é que nosso tipo é um elemento genérico, ele não sabe se há ou não uma propriedade value, ou seja, temos que explicitamente falar que este tipo é um `input`, só assim podemos pegar o `value` a partir de um elemento. Para podermos definir um tipo específico que não seja um genérico, precisamos fazer um _casting_ explicito.

Para alterar no nosso controller, vamos substituir `Element` por `HTMLInputElement`:

```ts
class NegociacaoController {
  private _inputData: HTMLInputElement
  private _inputQuantidade: HTMLInputElement
  private _inputValor: HTMLInputElement

  constructor () {
    this._inputData = document.querySelector('#data')
    this._inputQuantidade = document.querySelector('#quantidade')
    this._inputValor = document.querySelector('#valor')
  }

  adiciona (evento: Event) {
    evento.preventDefault()
    const negociacao = new Negociacao(this._inputData.value, this._inputQuantidade.value, this._inputValor.value)
    console.log(negociacao)
  }
}
```

Mas vamos ter erros porque o `document.querySelector` não nos retorna um `HTMLInputElement` então temos que forçar. Para fazer essa conversão explicita, vamos usar `<tipo>` na frente da chamada:

```ts
class NegociacaoController {
  private _inputData: HTMLInputElement
  private _inputQuantidade: HTMLInputElement
  private _inputValor: HTMLInputElement

  constructor () {
    this._inputData = <HTMLInputElement>document.querySelector('#data')
    this._inputQuantidade = <HTMLInputElement>document.querySelector('#quantidade')
    this._inputValor = <HTMLInputElement>document.querySelector('#valor')
  }

  adiciona (evento: Event) {
    evento.preventDefault()
    const negociacao = new Negociacao(this._inputData.value, this._inputQuantidade.value, this._inputValor.value)
    console.log(negociacao)
  }
}
```

Agora temos um problema nesta execução `this._inputData.value`, pois o tipo retornado é uma `string` e não um `Date`. O mesmo vale para os outros dois campos, porque são strings e não números:

```ts
class NegociacaoController {
  private _inputData: HTMLInputElement
  private _inputQuantidade: HTMLInputElement
  private _inputValor: HTMLInputElement

  constructor () {
    this._inputData = <HTMLInputElement>document.querySelector('#data')
    this._inputQuantidade = <HTMLInputElement>document.querySelector('#quantidade')
    this._inputValor = <HTMLInputElement>document.querySelector('#valor')
  }

  adiciona (evento: Event) {
    evento.preventDefault()
    const negociacao = new Negociacao(
      new Date(this._inputData.value.replace(/-/g, ',')), // Temos que substituir a data com - por data com , para poder passar para o date
      parseInt(this._inputQuantidade.value),
      parseFloat(this._inputValor.value)
    )
    console.log(negociacao)
  }
}
```

Com isso removemos os erros.

## Listando negociações

Para podermos listar as nossas negociações, vamos ter que seguir algumas regras:

- Na lista, só podemos inserir
- Não podemos remover
- Não podemos alterar

Para isso, vamos criar um novo modelo chamado `Negociacoes`:

```ts
class Negociacoes {
  private _negociacoes: Array<Negociacao> = []

  adiciona (negociacao: Negociacao) {
    this._negociacoes.push(negociacao)
  }

  toArray () {
    return this._negociacoes
  }
}
```

> Veja que temos um array privado _de negociações_, ele leva o tipo negociação como parâmetro para especificar o tipo de array que estamos usando. Da mesma forma estamos criando o método adiciona com um parâmetro do tipo de negociação.
>
> Uma outra forma de escrever um array específico é `Negociacao[]` ao invés de `Array<Negociacao>`.

### Inferição de tipos

Quando formos alterar o nosso controller vamos colocar uma nova propriedade chamada de `Negociacoes`:

```ts
class NegociacaoController {
  private _inputData: HTMLInputElement
  private _inputQuantidade: HTMLInputElement
  private _inputValor: HTMLInputElement
  private _negociacoes = new Negociacoes()

  constructor () {
    this._inputData = <HTMLInputElement>document.querySelector('#data')
    this._inputQuantidade = <HTMLInputElement>document.querySelector('#quantidade')
    this._inputValor = <HTMLInputElement>document.querySelector('#valor')
  }

  adiciona (evento: Event) {
    evento.preventDefault()
    const negociacao = new Negociacao(
      new Date(this._inputData.value.replace(/-/g, ',')),
      parseInt(this._inputQuantidade.value),
      parseFloat(this._inputValor.value)
    )
    console.log(negociacao)
  }
}
```

> Veja que não passamos o tipo para `_negociacoes` porque ele já inferiu automaticamente que nosso tipo é `Negociacoes`

O mesmo vale para este código:

```ts
class NegociacaoController {
  private _inputData: HTMLInputElement
  private _inputQuantidade: HTMLInputElement
  private _inputValor: HTMLInputElement
  private _negociacoes = new Negociacoes()

  constructor () {
    this._inputData = <HTMLInputElement>document.querySelector('#data')
    this._inputQuantidade = <HTMLInputElement>document.querySelector('#quantidade')
    this._inputValor = <HTMLInputElement>document.querySelector('#valor')
  }

  adiciona (evento: Event) {
    evento.preventDefault()
    const negociacao = new Negociacao(
      new Date(this._inputData.value.replace(/-/g, ',')),
      parseInt(this._inputQuantidade.value),
      parseFloat(this._inputValor.value)
    )

    this._negociacoes.adiciona(negociacao)

    this._negociacoes.toArray().forEach((negociacao) => {
      console.log(negociacao.data, negociacao.quantidade, negociacao.valor, negociacao.volume)
    })
  }
}
```

Veja que estamos inferindo o tipo também de `negociacao` porque nosso método `toArray()` retorna um array de negociacoes.

### Imutabilidade de valores

Nosso array não pode ser alterado, ou seja, não podemos remover nem alterar nada neste array de negociações, mas se utilizarmos `this._negociacoes.toArray().length = 0`, estaremos limpando o Array da mesma forma. Então temos que retornar uma referência para este array, uma cópia:

```ts
class Negociacoes {
  private _negociacoes: Array<Negociacao> = []

  adiciona (negociacao: Negociacao) {
    this._negociacoes.push(negociacao)
  }

  toArray () {
    return [].concat(this._negociacoes)
  }
}
```

Mas agora temos um problema, o TS perdeu o intellisense sobre isso... Porque estamos retornando um array e não um tipo específico, vamos ter que tipar o retorno também:

```ts
class Negociacoes {
  private _negociacoes: Array<Negociacao> = []

  adiciona (negociacao: Negociacao): void {
    this._negociacoes.push(negociacao)
  }

  toArray (): Negociacao[] { // Tipamos o retorno
    return [].concat(this._negociacoes)
  }
}
```

Agora temos o nosso intellisense funcionando novamente.

> É uma boa prática sempre tipar os retornos porque isso evita que retornemos coisas diferentes do que nossos métodos precisam, evitando que ela quebre ao longo da aplicação.

## Exibindo o modelo ao usuário

Para podermos exibir a lista de negociações para o usuário vamos precisar manipular o DOM. Existem duas formas de fazer isso, da forma imperativa (falando como vamos alterar o DOM), ou declarativa (de forma que temos templates para fazer isso).

Em um novo arquivo `NegociacoesView.ts` dentro da pasta `views` com este conteúdo:

```ts
class NegociacoesView {

  template(): string {
    return `
      <table class="table table-hover table-bordered">
      <thead>
        <tr>
          <th>DATA</th>
          <th>QUANTIDADE</th>
          <th>VALOR</th>
          <th>VOLUME</th>
        </tr>
      </thead>
      <tbody>
      </tbody>
      <tfoot>
      </tfoot>
    </table>`
  }
}
```

Agora vamos dentro do nosso controller de negociações e criaremos uma nova propriedade nessa classe:

```ts
private _negociacoesView = new NegociacoesView('#negociacoesView')
```

E no arquivo `index.html` vamos incluir uma nova div com o ID `negociacoesView` e, de volta na view vamos incluir o seletor e um método de update:

```ts
private _elemento: Element

constructor (seletor: string) {
  this._elemento = document.querySelector(seletor)
}

update (): void {
  this._elemento.innerHTML = this.template()
}
```

Para podermos exibir os dados da nossa negociação vamos ter que adicionar a chamada para este método dentro do nosso controller para, assim que a página for carregada, nós renderizemos a tabela.

```ts
constructor () {
  // Código
  this._negociacoesView.update()
}
```

Agora temos que passar os dados para isto vamos chamar este método tanto no construtor quanto no método `adiciona` do nosso controller. Mas agora vamos passar o modelo que vamos adicionar na tabela:

```ts
import NegociacoesView from "../views/NegociacoesView"

class NegociacaoController {
  private _inputData: HTMLInputElement
  private _inputQuantidade: HTMLInputElement
  private _inputValor: HTMLInputElement
  private _negociacoes = new Negociacoes()
  private _negociacoesView = new NegociacoesView('#negociacoesView')

  constructor () {
    this._inputData = <HTMLInputElement>document.querySelector('#data')
    this._inputQuantidade = <HTMLInputElement>document.querySelector('#quantidade')
    this._inputValor = <HTMLInputElement>document.querySelector('#valor')
    this._negociacoesView.update(this._negociacoes)
  }

  adiciona (evento: Event): void {
    evento.preventDefault()
    const negociacao = new Negociacao(
      new Date(this._inputData.value.replace(/-/g, ',')),
      parseInt(this._inputQuantidade.value),
      parseFloat(this._inputValor.value)
    )

    this._negociacoes.adiciona(negociacao)

    this._negociacoesView.update(this._negociacoes)
  }
}
```

Vamos refletir isso na nossa view:

```ts
export default class NegociacoesView {

  private _elemento: Element

  constructor (seletor: string) {
    this._elemento = document.querySelector(seletor)
  }

  update (model: Negociacoes): void {
    this._elemento.innerHTML = this.template(model)
  }

  template(model: Negociacoes): string {
    return `
      <table class="table table-hover table-bordered">
      <thead>
        <tr>
          <th>DATA</th>
          <th>QUANTIDADE</th>
          <th>VALOR</th>
          <th>VOLUME</th>
        </tr>
      </thead>
      <tbody>
      </tbody>
      <tfoot>
      </tfoot>
    </table>`
  }
}
```

Agora é só completar a tabela:

```js
export default class NegociacoesView {

  private _elemento: Element

  constructor (seletor: string) {
    this._elemento = document.querySelector(seletor)
  }

  update (model: Negociacoes): void {
    this._elemento.innerHTML = this.template(model)
  }

  template(model: Negociacoes): string {
    return `
      <table class="table table-hover table-bordered">
      <thead>
        <tr>
          <th>DATA</th>
          <th>QUANTIDADE</th>
          <th>VALOR</th>
          <th>VOLUME</th>
        </tr>
      </thead>
      <tbody>
        ${
          model.toArray().map((negociacao: Negociacao) => {
            return `
              <tr>
                <td>${negociacao.data.getDate()}/${negociacao.data.getMonth()+1}/${negociacao.data.getFullYear()}</td>
                <td>${negociacao.quantidade}</td>
                <td>${negociacao.valor}</td>
                <td>${negociacao.volume}</td>
              </tr>
            `
          }).join('')
        }
      </tbody>
      <tfoot>
      </tfoot>
    </table>`
  }
}
```

### Evitando repetição de código

Agora que exibimos o model para o usuário, queremos mostrar uma mensagem falando que esta negociação foi inserida com sucesso. Para isso vamos criar uma nova view que será muito parecida com a nossa `NegociacaoView`, chamada `MensagemView`:

```ts
class MensagemView {

  private _elemento: Element

  constructor (selector: string) {
    this._elemento = document.querySelector(selector)
  }

  update (model: string) {
    this._elemento.innerHTML = this.template(model)
  }

  template (model: string) {
    return `<p class="alert alert-info">${model}</p>`
  }
}
```

Vamos usar nossa mensagem no nosso controlador de negociação:

```ts
import NegociacoesView from "../views/NegociacoesView"

class NegociacaoController {
  private _inputData: HTMLInputElement
  private _inputQuantidade: HTMLInputElement
  private _inputValor: HTMLInputElement
  private _negociacoes = new Negociacoes()
  private _negociacoesView = new NegociacoesView('#negociacoesView')
  private _mensagemView = new MensagemView('#mensagemView')

  constructor () {
    this._inputData = <HTMLInputElement>document.querySelector('#data')
    this._inputQuantidade = <HTMLInputElement>document.querySelector('#quantidade')
    this._inputValor = <HTMLInputElement>document.querySelector('#valor')
    this._negociacoesView.update(this._negociacoes)
  }

  adiciona (evento: Event): void {
    evento.preventDefault()
    const negociacao = new Negociacao(
      new Date(this._inputData.value.replace(/-/g, ',')),
      parseInt(this._inputQuantidade.value),
      parseFloat(this._inputValor.value)
    )

    this._negociacoes.adiciona(negociacao)

    this._negociacoesView.update(this._negociacoes)
    this._mensagemView.update('Negociação adicionada com sucesso!')
  }
}
```

Mas existe um problema, estamos repetindo muito o código de `MensagemView` e `NegociacoesView`. Para não repetirmos isso, vamos utilizar o recurso de herança.

#### Herança

Herança já é nativo do ES6 desde 2015 através da keyword `extends`. Vamos refatorar o código para utilizar herança. Primeiro vamos criar uma nova classe `View`:

```ts
class View {

  protected _elemento: Element

  constructor (selector: string) {
    this._elemento = document.querySelector(selector)
  }
}
```

E agora vamos estender de ambas a views:

```ts
export default class NegociacoesView extends View {


  update (model: Negociacoes): void {
    this._elemento.innerHTML = this.template(model)
  }

  template(model: Negociacoes): string {
    return `
      <table class="table table-hover table-bordered">
      <thead>
        <tr>
          <th>DATA</th>
          <th>QUANTIDADE</th>
          <th>VALOR</th>
          <th>VOLUME</th>
        </tr>
      </thead>
      <tbody>
        ${
          model.toArray().map((negociacao: Negociacao) => {
            return `
              <tr>
                <td>${negociacao.data.getDate()}/${negociacao.data.getMonth()+1}/${negociacao.data.getFullYear()}</td>
                <td>${negociacao.quantidade}</td>
                <td>${negociacao.valor}</td>
                <td>${negociacao.volume}</td>
              </tr>
            `
          }).join('')
        }
      </tbody>
      <tfoot>
      </tfoot>
    </table>`
  }
}
```

e `MensagemView`:

```ts
class MensagemView extends View{

  update (model: string) {
    this._elemento.innerHTML = this.template(model)
  }

  template (model: string) {
    return `<p class="alert alert-info">${model}</p>`
  }
}
```

Mas veja que também nas duas classes filhas, o método `update` é quase identico, sendo diferenciado apenas pelo tipo do parâmetro, vamos refatorar para podermos inclui-lo também na classe pai `View`.

### Generics

Primeiramente, vamos passar o método `update` para a classe `View`, depois vamos ver que precisaremos do método `template` também na classe `View`, uma vez que eles são dependentes entre si.

```ts
class View {

  protected _elemento: Element

  constructor (selector: string) {
    this._elemento = document.querySelector(selector)
  }

  update (model: string) {
    this._elemento.innerHTML = this.template(model)
  }

  template (model: string) {
    return `<p class="alert alert-info">${model}</p>`
  }
}
```

Isso vai dar um erro, porque nossa `NegociacoesView`, está utilizando uma assinatura de função diferente da assinatura estendida da classe pai. Então obrigatóriamente temos que fazer a classe filha sobrescrever o método da classe pai:

```ts
class View {

  protected _elemento: Element

  constructor (selector: string) {
    this._elemento = document.querySelector(selector)
  }

  update (model: string) {
    this._elemento.innerHTML = this.template(model)
  }

  template (model: string) {
    throw new Error('O método template precisa ser sobrescrito')
  }
}
```

Mas ai teremos um problema de tipos, porque na nossa classe `MensagemView`, temos uma função `update` e `template` que recebem, ambas, uma string como parâmetro, mas na nossa classe `NegociacoesView` ambas recebem `Negociacao` como parâmetro, então temos que trabalhar com __tipos genéricos__.

Para isso vamos incluir `<T>` na nossa `View`, T significa "type" e é um tipo genérico, depois vamos passar para todas as funções da `View` o tipo T como sendo o tipo do parâmetro:

```ts
class View<T> {

  protected _elemento: Element

  constructor (selector: string) {
    this._elemento = document.querySelector(selector)
  }

  update (model: T): void {
    this._elemento.innerHTML = this.template(model)
  }

  template (model: T): string {
    throw new Error('O método template precisa ser sobrescrito')
  }
}
```

Agora vamos ter que alterar nossas duas views implementadas para trabalharem com seus tipos especificos:

```ts
export default class NegociacoesView extends View<Negociacoes> {

  template (model: Negociacoes): string {
    return `
      <table class="table table-hover table-bordered">
      <thead>
        <tr>
          <th>DATA</th>
          <th>QUANTIDADE</th>
          <th>VALOR</th>
          <th>VOLUME</th>
        </tr>
      </thead>
      <tbody>
        ${
          model.toArray().map((negociacao: Negociacao) => {
            return `
              <tr>
                <td>${negociacao.data.getDate()}/${negociacao.data.getMonth()+1}/${negociacao.data.getFullYear()}</td>
                <td>${negociacao.quantidade}</td>
                <td>${negociacao.valor}</td>
                <td>${negociacao.volume}</td>
              </tr>
            `
          }).join('')
        }
      </tbody>
      <tfoot>
      </tfoot>
    </table>`
  }
}
```

Veja que a view de negociações trabalha com o tipo de negociações, enquanto abaixo a view de mensagens trabalha com string:

```ts
class MensagemView extends View<string> {

  template (model: string): string {
    return `<p class="alert alert-info">${model}</p>`
  }
}
```

Agora também não precisamos mais do `protected` no arquivo `View`, podendo retornar para `private`, pois ambos os métodos que utilizavam ele já estão dentro da própria classe.

### Classes Abstratas

Não faz muito sentido termos uma instancia de `View`, apenas podemos estender da mesma, então vamos adicionar na nossa `View` o modificador `abstract`:

```ts
abstract class View<T> {

  private _elemento: Element

  constructor (selector: string) {
    this._elemento = document.querySelector(selector)
  }

  update (model: T): void {
    this._elemento.innerHTML = this.template(model)
  }

  template (model: T): string {
    throw new Error('O método template precisa ser sobrescrito')
  }
}
```

Isto previne coisas do tipo: `const a = new View<string>`.

Vamos fazer o mesmo com o método `template` dentro da `View`, pois este é o método que a classe filha vai ter que sobrescrever obrigatóriamente e, no nosso atual código o programador só vai saber disso na hora que o código estiver executando.

```ts
abstract class View<T> {

  private _elemento: Element

  constructor (selector: string) {
    this._elemento = document.querySelector(selector)
  }

  update (model: T): void {
    this._elemento.innerHTML = this.template(model)
  }

  abstract template (model: T): string
}
```

## Usando definitions

Utilizamos o jQuery simplesmente porque precisamos manter a compatibilidade entre navegadores antigos e outras diferenciações pequenas de sistemas (principalmente sistemas mobile). Primeiramente vamos importar o jQuery no nosso `index.html` e vamos substituir o nosso `document.querySelector` pelo famoso `$` do jQuery. Isso vai nos dar um erro, pois o seletor de dólar não está definido no TypeScript.

Para resolver esse problema teremos que silenciar o compilador, não temos como importar globalmente este tipo de variável. Para declarar, vamos usar uma sintaxe especial no nosso arquivo `View.ts`:

```ts
declare var $: any

abstract class View<T> {

  private _elemento: Element

  constructor (selector: string) {
    this._elemento = $(selector)
  }

  update (model: T): void {
    this._elemento.innerHTML = this.template(model)
  }

  abstract template (model: T): string
}
```

Agora caímos em outro problema, também vamos ter que alterar a sintaxe de `innerHTML` para `html`, pois estamos agora trabalhando com jQuery, e isto é uma função, não mais uma propriedade. Então teremos outro erro porque a função não espera receber o tipo `Element` que enviamos. Para isto vamos alterar o tipo de `_elemento` para `any`:

```ts
declare var $: any

abstract class View<T> {

  private _elemento: any

  constructor (selector: string) {
    this._elemento = $(selector)
  }

  update (model: T): void {
    this._elemento.innerHTML = this.template(model)
  }

  abstract template (model: T): string
}
```

Fazer este tipo de solução não é uma boa, pois perderemos o intellisense e todo o autocomplete

### Typescript Definitions

Arquivos TSD (Typescript Definitions) são mapas de funções e tipos que dizem ao TS quais são os tipos que cada função recebe e como elas se comportam, é uma forma de criar um intellisense externo. Estes arquivos podem ser criados pelos autores das bibliotecas ou então por terceiros.

Para instalarmos esses tipos vamos executar, na pasta do nosso projeto, o comando `npm install @types/jquery@2.0.42 --save-dev`. Isto irá instalar os arquivos de definição do TS para o jQuery.

> Como TSDs são arquivos externos, um bom jeito de encontrar um é pesquisar no Google "<framework> typescript definitions" pelo typing mais atualizado.

Depois de fazermos a instalação precisaremos reiniciar o nosso editor para que as alterações sejam computadas.

Agora, em nosso arquivo `View.ts` vamos alterar para o tipo correto:

```ts
abstract class View<T> {

  private _elemento: jQuery

  constructor (selector: string) {
    this._elemento = $(selector)
  }

  update (model: T): void {
    this._elemento.innerHTML = this.template(model)
  }

  abstract template (model: T): string
}
```

Veja que não precisamos mais do `declare var $`.

Vamos fazer a alteração no nosso arquivo `NegociacaoController.ts`:

```ts
import NegociacoesView from "../views/NegociacoesView"

class NegociacaoController {
  private _inputData: JQuery
  private _inputQuantidade: JQuery
  private _inputValor: JQuery
  private _negociacoes = new Negociacoes()
  private _negociacoesView = new NegociacoesView('#negociacoesView')
  private _mensagemView = new MensagemView('#mensagemView')

  constructor () {
    this._inputData = $('#data')
    this._inputQuantidade = $('#quantidade')
    this._inputValor = $('#valor')
    this._negociacoesView.update(this._negociacoes)
  }

  adiciona (evento: Event): void {
    evento.preventDefault()
    const negociacao = new Negociacao(
      new Date(this._inputData.val().replace(/-/g, ',')),
      parseInt(this._inputQuantidade.val()),
      parseFloat(this._inputValor.val())
    )

    this._negociacoes.adiciona(negociacao)

    this._negociacoesView.update(this._negociacoes)
    this._mensagemView.update('Negociação adicionada com sucesso!')
  }
}
```

Vamos fazer o mesmo no nosso arquivo `app.ts`:

```ts
const controller = new NegociacaoController();

$('.form').submit(controller.adiciona.bind(controller));
```

Agora temos a possibilidade de utilizar bibliotecas externas.

## Removendo partes inúteis do código

Comentários no código são documentações que não precisam ser enviadas para o código de produção, então vamos extirpa-los do processo de compilação alterando no nosso arquivo `tsconfig.json`:

```ts
{
  "compilerOptions": {
    "target": "es6",
    "outDir": "app/js",
    "noEmitOnError": true,
    "noImplicitAny": true,
    "removeComments": true
  },
  "include": [
    "app/ts/**/*"
  ]
}
```

Vamos reiniciar o nosso compilador e percebemos que todos os arquivos não tem mais nenhum comentário.