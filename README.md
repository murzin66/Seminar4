# Seminar 4


Существуют различные сценарии использования middleware, как отдельно от middleware, встроенного в redux, так и совместно с ним, рассмотрим оба варианта. 

###Использование только своего middleware

```
import { createStore, applyMiddleware } from 'redux';

const logger = (store) => (next) => (action) => {
  // Код мидлвары
};

const store = createStore(
  reducer,
  applyMiddleware(logger),
);
```
При необходимости можно компоновать несколько middleware с помощью метода compose:

```
import { createStore, applyMiddleware, compose } from 'redux';

//middleware 1
const logger = (store) => (next) => (action) => {
};
//middleware 2
const updater = (store) => (next) => (action) => {
};

const store = createStore(
  reducer,
  compose(
    applyMiddleware(logger),
    applyMiddleware(updater),
  ),
);
```


###Комбинация собственного middleware с gefault middleware
Помимо middleware, предусмотренного по умолчнию при конфигурции хранилища существует возможность добавить свой middleware, при помощи его добавления к массиву middleware
```
const store = window.RTK.configureStore({
  reducer: updateStore,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      thunk: {
        extraArgument: 'some data or function',
      },
    }).concat(logIt),
});
```
