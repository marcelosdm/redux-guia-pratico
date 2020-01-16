# Action Creators

Atualmente, a **action** que estamos disparando é um objeto JavaScript. No entanto, nós não queremos definir nossas _actions_ como um objeto JavaScript toda vez. É mais fácil se tivermos uma função reutilizável que retorna este objeto e a única coisa que passamos é a uma variável.

As **action creators** não são obrigatórias, mas elas mantém a arquitetura do **Redux** organizada. Vamos definir nossa primeira **action creator**.

- Crie o arquivo **favorite.js** no diretório **src/actions/** e defina a _action creator_ que recebe o **id** do job para identificar qual job será _favoritada_.

```javascript
import { FAVORITE } from '../constants/actionTypes';

const doFavorite = id => ({
  type: FAVORITE,
  id
});

export { doFavorite };
```

- Agora, utilize-a no nosso arquivo principal, o **src/index.js**:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';
import store from './store';
import { getAllJobs } from './selectors/job';
import {doFavorite} from './actions/favorite';
import './index.css';

ReactDOM.render(
  <App
    jobs={getAllJobs(store.getState())}
    onFavorite={id => store.dispatch({ doFavorite(id) })}
  />,
  document.getElementById('root')
);
```

Ao invés de disparar um objeto **action** diretamente, podemos criar uma **action** utilizando o **action creator**.
A função **action creator** retorna apenas o objeto da **action** ao invés de definir o objeto literalmente, em linha.

À propósito, a aplicação continua funcionando da mesma forma.
