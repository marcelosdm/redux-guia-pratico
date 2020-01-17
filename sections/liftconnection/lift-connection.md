# Lift Connection

Agora vamos _elevar_ nossa _connection_ do componente **Jobs**, para o componente **Job**. O motivo? Porque a função **onFavorite** será usada por um item da lista, e não pela lista toda. Os jobs ficarão em seu lugar, e aqui vamos alterar apenas parte da _conection_, que é justamente a função **onFavorite**.

## Mão na massa

- Antes de mais nada, remova a propriedade **onFavorite** do componente **Jobs**.
  - Remova também a função **mapDispatchToProps**, que era utilizada aqui apenas para disparar a função;
  - Por último, remova a importação da função **doFavorite**.

O componente **Jobs** ficará da seguinte maneira:

```javascript
import React from 'react';
import { connect } from 'react-redux';
import { getAllJobs } from '../selectors/job';
import Job from './Job';

const Jobs = ({ jobs }) => (
  <div>
    {(jobs || []).map(job => (
      <Job key={job.id} job={job} />
    ))}
  </div>
);

const mapStateToProps = state => ({
  jobs: getAllJobs(state)
});

export default connect(mapStateToProps)(Jobs);
```

Veja que o método **connect** passa a ter apenas a função _mapStateToProps_ como argumento, já que a _mapDispatchToProps_ não está mais lá.

- Agora, conecte o componente **Job** que irá receber a função que dispara a **action** O :

```javascript
import React from 'react';
import { connect } from 'react-redux';
import { doFavorite } from '../actions/favorite';

const Job = ({ job, onFavorite }) => (
  <div>
    <li>
      {job.title} - {job.company} - {job.favorite}
      <button type="button" onClick={() => onFavorite(job)}>
        <span role="img" aria-label="star">
          ⭐
        </span>
        Favorito
      </button>
    </li>
  </div>
);

const mapDispatchToProps = dispatch => ({
  onFavorite: id => dispatch(doFavorite(id))
});

export default connect(null, mapDispatchToProps)(Hero);
```

Veja que o método **connect**, na última linha, recebe o primeiro argumento como **null**. Isto acontece pois neste componente, não temos a função _mapStateToProps_ (esta sempre é o primeiro argumento do método **connect**)

---

Agora, temos dois componentes conectados que alteram/obtêm o _state_ da _store_ do **Redux**. Também temos total controle de onde queremos usar os componentes conectados. Com isso em mente, faz mais sentido usar o **Redux** e definir qual componente irá **obter os dados** _state_ e qual irá **alterar** o _state_.
