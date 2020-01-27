# Separação da Lógica da API

Vamos refatorar nosso código para separar as funcionalidades da API e das sagas. Aqui, iremos extrair a chamada da API da saga do job. Depois, outras sagas podem usar da mesma lógica da API também.

## Mão na massa

- Extraia a funcionalidade que está no arquivo **job.js**, do diretório **src/sagas/** e realize a importação dele neste arquivo:

```javascript
import { call, put } from 'redux-saga/effects';
import { addJobs } from '../actions/job';
import { fetchJobs } from '../api/job';

function* handleFetchJobs(action) {
  const { query } = action;
  const result = yield call(fetchJobs, query);
  yield put(doAddJobs(result));
}

export { handleFetchJobs };
```

- Crie um novo arquivo, chamado **job.js** no diretório **src/api/** e insira a lógica:

```javascript
const query = '';

const BASE_URL = `/positions.json?page=1&search=${query}`;

const fetchJobs = query =>
  fetch(BASE_URL + query).then(response => response.json());

export { fetchJobs };
```

Pronto! Rode novamente o app e veja tudo funcionando!
