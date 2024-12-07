# Seminar 4

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
