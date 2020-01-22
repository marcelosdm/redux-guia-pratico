# Redux Saga

O **Redux Saga** é uma biblioteca que visa fazer _efeitos colaterais_ em uma aplicação. Por exemplo: busca de dados, acesso ao cache do browser, etc).

Aqui, vamos utilizar a biblioteca para buscar dados de uma API (neste caso, o exemplo será feito com a API do Github Jobs).

Nosso objetivo é fazer chamdas para a API e depois disparar uma _ação atrasada_ para atualizar o **state** do **Redux**.

## Mão na massa

### Primeiros passos com Redux Saga

- Antes de mais nada, vamos adicionar o **Redux Saga** em nosso projeto:

```bash
yarn add redux-saga
```

- Agora, crie o arquivo **index.js** no diretório **src/sagas/**:

```javascript
import { takeEvery, all } from 'redux-saga/effects';
import { FETCH_JOBS } from '../constants/actionTypes';
import { handleFetchJobs } from './job';

function* watchAll() {
  yield all([takeEvery(FETCH_JOBS, handleFetchJobs)]);
}

export default watchAll;
```

A **store** do **Redux** espera um _root saga_, ou seja, assim como os **reducers**, podemos ter vários _sagas_, combinados em apenas um arquivo _root_. Por isso, dizemos que o _saga_ observa todas as _ativações do saga_, ou _observa todas as funções saga_.

O **root saga** pode ser usado no middleware do **Redux store** quando inicializa o middleware do saga. Ele utiliza middlware, mas também precisa de rodar em um método saga _run()_, separado.

- O arquivo **src/store/index.js** recebe o saga da seguinte maneira:

```javascript
import { createStore, applyMiddleware } from 'redux';
import { createLogger } from 'redux-logger';
import createSagaMiddleware from 'redux-saga';
import rootReducer from '../reducers';
import rootSaga from '../sagas';

const logger = createLogger();
const saga = createSagaMiddleware();

const store = createStore(
  rootReducer,
  undefined,
  applyMiddleware(logger, saga)
);

saga.run(rootSaga);

export default store;
```

- Agora, no arquivo **src/constants/actionTypes.js**, adicione a nova _action type_ que ativa o saga. Podemos inclusive, adicionar uma outra _action type_ que iremos utilizar quando a _request_ for bem sucedida:

```javascript
export const FAVORITE = 'FAVORITE';
export const FETCH_JOBS = 'FETCH_JOBS';
export const ADD_JOBS = 'ADD_JOBS';
```

### Implementando a Busca na API

Nesta sub-sessão, iremos implantar o saga do _job_, encapsulando uma request para a API. Usaremos o método nativo do browser, o **fetch API**. Criaremos também uma função chamada **handleFetchJobs**, que será usada pelo saga, e usaremos nela a declaração **yeild** para escrevermos código assíncrono. Ela é utilizada, pois se a _promise_ da API não retornar nada ou rejeitar nossa requisição, a próxima linha de código após o **yield** não será avaliada. Quando tivermos o resultado da API, poderemos utilizar o efeito **put()** para disparar outra função.

- Crie o arquivo **job.js**, no diretório **src/sagas/**:

```javascript
import { call, put } from 'redux-saga/effects';
import { addJobs } from '../actions/job';

const query = '';

const BASE_URL = `https://jobs.github.com/positions.json?search=${query}`;

const fetchJobs = query =>
  fetch(BASE_URL + query).then(response => response.json());

function* handleFetchJobs(action) {
  const { query } = action;
  const result = yield call(fetchJobs, query);
  yield put(addJobs(result.data.results));
}

export { handleFetchJobs };
```

- Neste passo, iremos definir duas _action creators_ no arquivo **src/actions/job.js**:
  - Uma que ativa o efeito colateral para buscar jobs baseado em um termo de busca
  - Outra que adiciona os jobs obtidos na **Redux store**

O arquivo **job.js** do diretório **src/actions/** fica da seguinte maneira:

```javascript
import { ADD_JOBS, FETCH_JOBS } from '../constants/actionTypes';

const addJobs = jobs => ({
  type: ADD_JOBS,
  jobs
});

const fetchJobs = query => ({
  type: FETCH_JOBS,
  query
});

export { addJobs, fetchJobs };
```

Apenas a função **fetchJobs** precisa de ser _interceptada_ no **jobReducer** (no arquivo **src/reducers/job.js**). A função **addJobs** é usada apenas para ativar o saga em nosso _root saga_.

**IMPORTANTE**: não se esqueça de remover os jobs de exemplo dos reducers, pois agora, vamos obter os dados da API do Github jobs.

- O arquivo **job.js** do diretório **src/reducers/**, fica da seguinte forma:

```javascript
import { ADD_JOBS } from '../constants/actionTypes';

const INITIAL_STATE = [];

const applyAddJobs = (state, action) => action.jobs;

function jobReducer(state = INITIAL_STATE, action) {
  switch (action.type) {
    case ADD_JOBS: {
      return applyAddJobs(state, action);
    }
    default:
      return state;
  }
}

export default jobReducer;
```

Agora temos tudo configurado da perspectiva do **Redux** e do **Redux Saga**.

---

### Implementando a busca

Um último passo: um componente do _React View_ precisa de disparar a ação **FETCH_JOBS** e então ativar o **Redux saga**. Esta ação é interceptada no saga, busca os jobs em um efeito colateral, e guarda eles na **Redux store** usando a ação **ADD_JOBS**.

Vamos criar um componente que terá a funcionalidade para realizarmos a busca por jobs na API.

- Crie o arquivo **SearchJobs**, no diretório **src/components/**:

```javascript
import React, { Component } from 'react';

class SearchJobs extends Component {
  constructor(props) {
    super(props);

    this.state = {
      query: ''
    };

    this.onChange = this.onChange.bind(this);
    this.onSubmit = this.onSubmit.bind(this);
  }

  onSubmit(event) {
    const { query } = this.state;
    if (query) {
      this.props.onFetchJobs(query);

      this.setState({ query: '' });
    }
    event.preventDefault();
  }

  onChange(event) {
    const { value } = event.target;
    this.setState({ query: value });
  }

  render() {
    return (
      <form onSubmit={this.onSubmit}>
        <input type="text" value={this.state.query} onChange={this.onChange} />
        <button type="submit">Search</button>
      </form>
    );
  }
}

export default SearchJobs;
```

- Agora, precisamos de _conectar_ o componente para fazê-lo funcionar:

```javascript
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { fetchJobs } from '../actions/job';

. . .

const mapDispatchToProps = dispatch => ({
  fetchJobs: query => dispatch(fetchJobs(query))
});

export default connect(null, mapDispatchToProps)(SearchJobs);
```

- No componente **App**, podemos agora importar o novo componente **SearchJobs**:

```javascript
import React from 'react';

    import Jobs from './Jobs';
    **import SearchJobs from './SearchJobs';**

    const App = () => {
      return (
        <div className="App">
          <div>
            <SearchJobs />
          </div>
          <h1>Redux Practice</h1>
          <Jobs />
        </div>
      );
    };

    export default App;
```

Reinicie a aplicação, e faça um teste no browser, buscando por "_React_" ou "_Spring_" e veja os resultados.
