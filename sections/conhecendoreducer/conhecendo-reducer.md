# Conhecendo o Reducer

> O **Reducer** é uma função _pura_, que executa o estado anterior e uma ação, e retorna o próximo estado. É chamado de **reducer**, porque é o tipo de função que você passaria para o _Array.prototype_.

Que linda definição o pessoal do [Redux](https://redux.js.org/) nos deu sobre o que é Reducer. Você pode não ter entendido muita coisa... pois é, eu também não! 😉

Mas procurando na [documentação](https://redux.js.org/basics/reducers/), podemos encontrar algo mais fácil de entender:

> Os **Reducers** especificam como o estado da aplicação é alterado em resposta às ações enviadas para a _store_. Lembre-se de que as ações apenas descrevem o que aconteceu, mas não descrevem como o estado é alterado.

Sendo assim, vamos praticar e entender melhor como funciona.

## Mão na massa

### Criando o Reducer

- Antes de mais nada, crie o nosso primeiro **reducer**, dentro do diretório **src/reducers/**. O nome do arquivo pode ser algo como _jobReducer_:

```javascript
const INITIAL_STATE = [
  {
    id: 1,
    title: 'Desenvolvedor Senior',
    company: 'LuizaLabs',
    url:
      'https://www.99jobs.com/luizalabs/jobs/70283-desenvolvedor-senior-squad-contas-a-receber'
  },
  {
    id: 2,
    title: 'Desenvolvedor Frontend Pleno',
    company: 'LuizaLabs',
    url: 'https://www.99jobs.com/luizalabs/jobs/66947-desenvolvedor-a-front-end'
  }
];

function jobReducer(state = INITIAL_STATE, action) {
  switch (action.type) {
    default:
      return state;
  }
}

export default jobReducer;
```

Por enquanto, ainda vamos utilizar os dados estáticos como exemplo. Portanto, veja que apenas copiamos as informações do _array de objetos_ da sessão anterior e inserimos dentro deste array que chamaremos de **INITIAL_STATE**.

Além dos _dados estáticos_, temos a uma função, que nada mais é que o nosso **reducer**: a função **jobReducer**!

E nesse momento ela apenas retorna o **estado padrão** dos nossos dados.

### Criando uma Store

Basicamente, as principais responsabilidades de uma **store**, que nos interessa no momento, são:

- Mantém o _estado da aplicação_;
- Permite o acesso ao estado via `getState()`;
- Permite o estado ser alterado via `dispatch(action)`;

É importante dizer também que uma aplicação **Redux** possui apenas uma **única store**.

---

Neste momento, se sua aplicação estiver rodando no terminal, eu recomendo que você pare e faça a instalação de uma nova dependência: o **redux**.

- Para isto, rode o comando `yarn add redux`, no terminal.

- Crie um arquivo chamado **index.js**, dentro do diretório **src/store/**.

A princípio, o arquivo ficará da seguinte maneira:

```javascript
import { createStore } from 'redux';
import jobReducer from '../reducers/jobReducer';

const store = createStore(jobReducer);

export default store;
```

Veja que simplesmente estamos _criando uma store_ e recebendo os dados do **jobReducer**.

- No arquivo **index.js**, da diretório **src/**, importe a **store** e atribua ela ao componente renderizado, nesse caso, o componente **App**:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';
import store from './store';
import './index.css';

ReactDOM.render(
  <App jobs={store.getState()} onFavorite={() => {}} />,
  document.getElementById('root')
);
```

Note que estamos utilizando o método _getState_, que é uma forma de buscar os dados da **store**.

### Criando outro Reducer

- Crie um novo arquivo em **src/reducers/favoriteReducer.js**:

```javascript
const INITIAL_STATE = [];

function favoriteReducer(state = INITIAL_STATE, action) {
  switch (action.type) {
    default:
      return state;
  }
}

export default favoriteReducer;
```

Por enquanto, ainda não iremos importá-lo. Veja o motivo no próximo tópico.

### Combinando Reducers

No **Redux**, podemos criar quantos _reducers_ forem necessários para nossa aplicação. No entanto, apenas um será utilizado pela **store**, ou seja, é necessário centralizar todos os _reducers_ em um só.

- No diretório **src/reducers/**, crie o arquivo **index.js**.
- Importe os dois reducers criados (**jobReducer** e **favoriteReducer**)
- Exporte-os como um único **reducer**, utilizando o _helper_ **combineReducer**, disponível no **redux**.

O arquivo ficará desta maneira:

```javascript
import { combineReducers } from 'redux';
import jobReducer from './job';
import favoriteReducer from './favorite';

const rootReducer = combineReducers({
  jobState: jobReducer,
  favoriteState: favoriteReducer
});

export default rootReducer;
```

- Agora o arquivo **store** pode _receber_ o **rootReducer**.

```javascript
import { createStore } from 'redux';
import rootReducer from '../reducers';

const store = createStore(rootReducer);

export default store;
```

- Finalmente, no arquivo **src/index.js**, devemos especificar qual estado estamos passando:

```javascript
ReactDOM.render(
  <App jobs={store.getState().jobState} onFavorite={() => {}} />,
  document.getElementById('root')
);
```
