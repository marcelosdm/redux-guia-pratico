# Decolando - Componentes Básicos do React 🚀

Nesta sessão, vamos ver o que é e como é feito o **prop drilling**, com o intuito de aprender um dos problemas que o **Redux** vai nos ajudar a resolver.

## Organizando a casa

Antes de mais nada, organize seu projeto para que a estrutura de pastas, fique semalhante ao exemplo abaixo. Não se preocupe, ao longo de nossa jornada, novas pastas e arquivos serão criados conforme formos evoluindo.

```bash
  -src/
  --index.js
  --components/
  ---App.js
  ---Jobs.js
  ---Job.js

```

## Prop Drilling

Ao pé da letra, o **prop drilling**, ou **vazamento de propriedades** é quando as _propriedades_ são passadas de um componente pai para um componente filho. O filho, passa para o filho dele, e assim por diante. Isso gera o **prop drilling**.

Ou seja, na utilização mais básica do React, o **prop drilling** é algo comum, e bem... não tão legal assim!

## Mão na massa

### index.js - a raiz de tudo

- Após criar a aplicação (seguindo o início do projeto, foi sugerido utilizar o _create-react-app_), faça seu arquivo **src/index.js** ficar semelhante ao exemplo abaixo:

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';
import './index.css';
ReactDOM.render(<App />, document.getElementById('root'));
```

- Em seguida, crie um _array_ que nos servirá como amostra de dados, apenas para fazer a simulação de Prop Drilling. Veja também, que no momento que chamamos o componente **App**, estamos passando duas _propriedades_. São elas:

1. **jobs** - é o array criado.
2. **onFavorite** - uma função, que por enquanto, está vazia e não faz nada.

```javascript
const jobs = [
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

ReactDOM.render(
  <App jobs={jobs} onFavorite={() => ()} />,
  document.getElementById('root')
);
```

- Agora, nosso objetivo é o de _propagar_ as propriedades para o componente que precisa delas. Mas antes disto, vamos _desenhar_ para entender melhor a situação:

`DADOS -> App.js -> Jobs.js -> Job.js`

Veja que os **DADOS** irão percorrer todo um caminho para chegar ao seu destino final, que é o componente **Job.js**. Uau! Mas, não se preocupe! Vamos corrigir isto com o **Redux** nas próximas sessões.

### App.js

Então, vamos continuar!

- No arquivo **src/components/App.js**, importe o componente **Jobs** e retorne-o.

Note também, que na declaração deste componente, já estamos recebendo os mesmos parâmetros **jobs** e **onFavorite**, passados lá no _src/index.js_. Repare que as mesmas propriedades também são passadas ao componente **Jobs**. 🙄

```javascript
import React from 'react';
import './App.css';
import Jobs from './Jobs';

const App = ({ jobs, onFavorite }) => {
  return (
    <div className="App">
      <h1>Redux - Guia Prático</h1>
      <Jobs jobs={jobs} onFavorite={onFavorite} />
    </div>
  );
};

export default App;
```

### Jobs.js

- No componente **Jobs.js**, faça algo similar ao realizado acima:
  - Importe o componente **Job.js**;
  - Propague as propriedades que iremos utilizar.

```javascript
import React from 'react';
import Job from './Job';

const Jobs = ({ jobs, onFavorite }) => (
  <div>
    {(jobs || []).map(job => (
      <Job key={job.id} job={job} onFavorite={onFavorite} />
    ))}
  </div>
);

export default Jobs;
```

### Job.js

- Por último, mas não menos importante, receba e faça uso das **propriedades**. Depois de percorrerem este longo caminho, elas merecem né?!

```javascript
import React from 'react';

const Job = ({ job, onFavorite }) => {
  const { id, name, description, favorite } = job;

  return (
    <div>
      <li>
        {job.name} - {job.description} - {job.favorite}
        <button type="button" onClick={() => onFavorite(job)}>
          ⭐ Favorito
        </button>
      </li>
    </div>
  );
};

export default Job;
```

Quando rodar a aplicação novamente, você verá as _tão faladas_ propriedades aparecerem na página!
