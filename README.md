# Как создать собственный Middleware в Redux, и в каких случаях это может быть полезно?

Структура middleware

```js
const logger = (store) => {
  return (next) => {
    return (action) => {
    };
  };
};
```

<b>Внешняя функция</b>

Эта функция нужна для подключения мидлвары к хранилищу. Она передается в Redux-метод applyMiddleware(). Функция получает на вход объект store, который содержит методы dispatch() и getState() для работы с Redux внутри мидлвары.

<b>Первая вложенная функция</b>

Ее аргументом будет функция next(). Вызов этой функции в теле мидлвары с действием в качестве аргумента может прокидывать действие дальше по цепочке мидлвар. Если функция next() вызвана в последней мидлваре цепочки, то она передает действие в редьюсер и вызывает обновление состояния.

<b>Вторая вложенная функция</b>

В качестве аргумента эта функция принимает действие action при его отправке. Мидлвара перехватит любое действие в приложении, отправленное в редьюсер.

### Использование кастомного middleware

Для добавления middleware к хранилищу необходимо использовать метод applyMiddleware

```js
import { createStore, applyMiddleware } from 'redux';

const logger = (store) => (next) => (action) => {
};

const store = createStore(
  reducer,
  applyMiddleware(logger),
);
```
При необходимости можно компоновать несколько middleware с помощью метода compose:

```js
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


### Комбинация собственного middleware со встроенным в Redux Toolkit middleware
Помимо middleware, предусмотренного по умолчнию при конфигурции хранилища (в котором в том числе встроен middleware redux-thunk) существует возможность добавить свой middleware, при помощи его добавления к массиву middleware
```js
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

При подключении middleware по умолчанию, существует возможность отключения некоторых встроенных элементов middleware 

```js
const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      thunk: true, // Включить redux-thunk
      serializableCheck: false, // Отключить проверку сериализуемости
      immutableCheck: true, // Включить проверку неизменяемости
    }),
});

```


### В каких случаях удобно использовать свой middleware:

+ Логирование состояния до вызова action и после
```js
  const loggerMiddleware = store => next => action => {
    console.log('Dispatching:', action);
    const result = next(action);
    console.log('Next state:', store.getState());
    return result;
  };
```
+ Валидация данных
```js
const validationMiddleware = store => next => action => {
if (action.type === 'SUBMIT_FORM' && !action.payload.email) {
  console.error('Email is required!');
  return;
}
return next(action);
};
```
+ Обработка side эффектов
```  js
const analyticsMiddleware = store => next => action => {
  if (action.type === 'USER_CLICKED') {
    sendAnalyticsEvent(action.payload.eventName);
  }
  return next(action);
};
```
+ Модификация action (изменение payload до reducer либо до следующего middleware)
```js
const enrichMiddleware = store => next => action => {
  if (action.type === 'ADD_USER') {
    const enrichedAction = {
      ...action,
      payload: {
        ...action.payload,
        timestamp: Date.now(),
      },
    };
    return next(enrichedAction);
  }
  return next(action);
};
```




