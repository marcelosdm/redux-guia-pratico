# Seletores do Redux

No **Redux**, os seletores servem, justamente, para realizar realizar uma espécie de _filtro_ dos dados.

Neste momento, iremos fazer dois seletores bem simples, que chegam até a serem bobos: iremos retornar todo o _state_ dos jobs e em outro arquivo, todo o _state_ dos favoritos.

Mas entenda que o conceito vai muito além disto. Imagine uma _lista de tarefas_, em que poderíamos filtrar apenas as tarefas que não estejam concluídas, daquelas que já estão concluídas.

No nosso caso, à princípio, ficaria estranho **não mostrar** os _jobs favoritos_, na tela inicial. Portanto, vamos fazer dois filtros:

1. **Todos** os jobs;
2. Somente os jobs **favoritos**.

## Mão na massa

### Selecionando todos os Jobs

- Crie o arquivo **job.js** no diretório **src/selectors/**:

```javascript
const getAllJobs = ({ jobState }) => jobState;

export { getAllJobs };
```

Lembre-se que o _jobState_, é a lista que contém todos os jobs.

---

### Selecionando somente os Jobs Favoritos

- Crie agora o arquivo **favorite.js**, também no diretório **src/selectors/**:

```javascript
const getFavorites = ({ favoriteState }) => favoriteState;

export { getFavorites };
```

Lembre-se de que o _favoriteState_ é a lista com os _ids_ favoritos.

---

### Jobs **não** favoritos

Vamos imaginar que não quiséssemos mostrar somente os jobs que não foram marcados como favoritos.

Criaríamos o filtro da seguinte maneira:

```javascript
const notFavorites = favoriteIds => job => favoriteIds.indexOf(job.id) === -1;

const getNotFavorites = ({ jobState, favoriteState }) =>
  jobState.filter(notFavorites(favoriteState));

export { getNotFavorites };
```

No código acima, primeiro criamos a função **notFavorites**, que busca os _ids_ dos jobs que **não são** favoritos.

Em seguida, criamos a função **getNotFavorites** que faz uso da primeira para percorrer o _state_ dos favoritos e verificar quais _ids_ não estão lá e retorná-los.

Desta forma, teríamos somente os jobs que **não são** favoritos.

Sim, dá mais trabalho para explicar, do que para entender!

![mind blowing](/assets/images/mind-blowing.gif)

### Usando o Selector

- Para utilizar o seletor, no arquivo **src/index.js**, faça a importação do mesmo e o utilize na propriedade, no nosso caso, a propriedade **jobs**:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';
import store from './store';
import { getAllJobs } from './selectors/job';
import { FAVORITE } from './constants/actionTypes';
import './index.css';

ReactDOM.render(
  <App
    jobs={getAllJobs(store.getState())}
    onFavorite={id => store.dispatch({ type: FAVORITE, id })}
  />,
  document.getElementById('root')
);
```

Repare que na propriedade **jobs**, não referenciamos mais o _state_, pois este agora é utilizado pelo nosso _selector_.

Você pode estar se perguntando qual o motivo de termos criado o _selector_ dos **favoritos**, se nem ao menos o utilizamos. Neste caso, podemos fazer uso dele mais adiante, quando criarmos um _component_ que liste somente os _jobs favoritos_.
