# Conectando React com Redux

Chegamos a um ponto muito importante do nosso guia! O momento onde vamos ver e entender como o **Redux** funciona na prática, em uma aplicação **React**.

## Mão na massa

- Instale a biblioteca `react-redux`:

```bash
yarn add react-redux
```

- Agora, vamos utilizar o componente **Provider**, que disponibiliza a _store_ do **Redux** para todos os components filhos do nosso arquivo _root_ do **React**, o **src/index.js**:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';
import { Provider } from 'react-redux';
import store from './store';
import './index.css';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

Demos uma boa enxugada, não é mesmo?! Mas pra quê isso?

Porque agora vamos evitar algumas más práticas, como:

1. _Re-renderizar cada componente_;
2. Utilizar uma instância da _store_ diretamente;
3. Deixar a _store_ disponível de forma global;
4. Fazer **prop drilling** (finalmente!)

Repare que a hierarquia inicial de components é renderizada apenas uma vez. Ninguém _assina_ a _store_ do **Redux** e o componente **App** não está mais recebendo as _props_. Além disto, o componente **App** está apenas renderizando o componente **Jobs**, e não está passando mais as _props_ também.

O arquivo **src/components/App** ficou assim:

```javascript
import React from 'react';
import './App.css';
import Jobs from './Jobs';

const App = () => {
  return (
    <div className="App">
      <h1>Redux - Guia Prático</h1>
      <Jobs />
    </div>
  );
};

export default App;
```

Como é esperado, o único componente que precisa das _props_ é o **Jobs**. Portanto, agora, vamos fazer o upgrade deste componente para o tão famoso _connected component_.

Ele terá acesso ao _state_ e às _actions_ do **Redux**, através do uso de dois _argumentos_. São eles: **mapStateToProps** e **mapDispatchToProps**, em um _higher-order component_, chamado **connect**, do **react-redux**.

Então ao invés de simplesmente exportar o componente **Jobs**, vamos exportar um componente **conectado** com o **connect high-order component**, que tem um acesso implícito à _store_ do **Redux**.

O arquivo **src/components/Jobs.js** ficará assim:

```javascript
import React from 'react';
import { connect } from 'react-redux';
import { doFavorite } from '../actions/favorite';
import { getAllJobs } from '../selectors/job';
import Job from './Job';

const Jobs = ({ jobs, onFavorite }) => (
  <div>
    {(jobs || []).map(job => (
      <Job key={job.id} job={job} onFavorite={onFavorite} />
    ))}
  </div>
);

const mapStateToProps = state => ({
  jobs: getAllJobs(state)
});

const mapDispatchToProps = dispatch => ({
  onFavorite: id => dispatch(doFavorite(id))
});

export default connect(mapStateToProps, mapDispatchToProps)(Jobs);
```

À partir de agora, o componente **Jobs** está conectado com a _store_ do **Redux**!

A função **mapStateToProps()** alimenta o componente com os jobs da _store_. E a **mapDispatchToProps()** dispara a _action_ que faz o job se tornar favorito.

Abaixo, podemos visualizar o fluxo que fizemos até aqui:

```
View -> (mapDispatchToProps) -> Action -> Reducer(s) -> Store -> (mapStateToProps) -> View
```

Ao verificar o app no browser, visualmente ele deve estar idêntico. Mas desta vez, sem usar de _prop drilling_ e com uma sofisticada conexão entre **Redux** e **React**.
