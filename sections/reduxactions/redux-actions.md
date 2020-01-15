# Redux Actions

Nesta sessão, vamos _despachar_ nossa primeira **action**! Para ser bem claro, esta _action_ vai armazenar um job favorito pelo seu respectivo **id**, em uma lista. Esta _action_ será capturada pelo **favoriteReducer**.

Para iniciar, veremos que o _estado inicial_ (**INITIAL_STATE**) deste reducer será um _array_ vazio. Claro, pois a aplicação inicia sem nenhum job favorito. Mas, quando o usuário marcar um job como favorito, o **id** deste job será adicionado à lista de favoritos em um novo array. O **spread operator** do _JavaScript_ é utilizado aqui para isto.

## Mão na massa

### Action types

Antes de mais nada, para começar, vamos conhecer um conceito muito utilizado no **Redux**. Esse conceito é o **action types**.

> Não há necessidade de definir constantes de action type em um arquivo separado, ou mesmo definí-las. Entretanto, há alguns benefícios em declar explicitamente estas constantes em bases de código maiores. Veja mais em [Reducing Boilerplate](https://redux.js.org/recipes/reducing-boilerplate)

O trecho acima foi retirado da [documentação do Redux, sobre Actions](https://redux.js.org/basics/actions/).

---

Pois bem, vamos seguir adiante.

- Crie o arquivo **actionTypes.js**, no diretório **src/constants/**:

```javascript
export const FAVORITE = 'FAVORITE';
```

### Modificando o favoriteReducer

> Acesse o arquivo **favoriteReducer**, já criado previamente, e faça as modificações para que ele fique da seguinte maneira:

```javascript
import { FAVORITE } from '../constants/actionTypes';

const INITIAL_STATE = [];

const applyFavorite = (state, action) => [...state, action.id];

function favoriteReducer(state = INITIAL_STATE, action) {
  switch (action.type) {
    case FAVORITE: {
      return applyFavorite(state, action);
    }
    default:
      return state;
  }
}

export default favoriteReducer;
```

Repare que além de importarmos nossa **action**, criamos uma nova função, a **applyFavorite**. Veja que ela recebe o state e uma action como parâmetros, e retorna uma cópia do state com o **id** da action.

Veja também que o **favoriteReducer** ganhou uma nova _condição_. Ao invés de simplesmente retornar o state (que continua sendo seu comportamento padrão), ele agora verifica o tipo da **action**. Se ela for **FAVORITE**, o reducer atualiza o state. 😉

### Despachando a **Action**

Agora estamos prontos para importar e _despachar_ a **action** no ponto de entrada da nossa aplicação React. Vamos substituir a função vazia, chamada **onFavorite** ~~que deixamos para encher a linguiça~~, no arquivo **src/index.js**:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';
import store from './store';
import { FAVORITE } from './constants/actionTypes';
import './index.css';

ReactDOM.render(
  <App
    jobs={store.getState().jobState}
    onFavorite={id => store.dispatch({ type: FAVORITE, id })}
  />,
  document.getElementById('root')
);
```

Agora podemos checar novamente o componente **Job**, que usa a **action** quando clicamos o botão de favorito. Quando o botão é clicado, ele aciona a função e passa o **id** do job. Porém, visualmente nada acontece ainda, pois as propriedades dos jobs ainda estão sendo passadas do **src/index.js** para o componente **App**, através do _prop drilling_.
