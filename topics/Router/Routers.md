# Routers

Un **router** es el _core_ de React Router. Básicamente, es un componente que se encargan de mantener sincronizada la UI con la URL al igual que gestionar el historial de navegación del sitio.

React Router ofrece un componente genérico llamado `Router` que acepta dos props:
- `children`: que define el elemento/componente que estará en el marco de nuestro **router**,
- `history`: que define el elemento que usaremos para gestionar el historial en nuestra aplicación (de dónde venimos, dónde estamos actaulmente, hacia dónde vamos, etc).

De este componente, se derivan cinco alternativas de **routers** diferentes que vienen con React Router:
- `BrowserRouter`
- `HashRouter`
- `MemoryRouter`
- `NativeRouter`
- `StaticRouter`

En este curso, nos centraremos en los dos primeros tipos de **routers**.

## BrowserRouter

`Router` que usa la [API History de HTML5](https://developer.mozilla.org/en-US/docs/Web/API/History).

```javascript
<BrowserRouter
  basename={string}
  getUserConfirmation={func}
  forceRefresh={bool}
  keyLength={number}
>
  <App/>
</BrowserRouter>
```

- `basename` {`string`}: permite setear una URL base para el `Router`. Debe iniciar con `/` (y terminar sin). Valor por defecto: `/`.
```javascript
<BrowserRouter basename="/account">
  ...
  // Matchea cuando la URL es /account/details
  <Route exact path="/details" component={AccountDetails}>
  ...
</BrowserRouter>
```
- `getUserConfirmation` {`function`}: permite sobrescribir `window.confirm`.
- `forceRefresh` {`Boolean`}: si es `true`, fuerza la recarga del sitio ante eventos de navegación. Valor por defecto: `false`.
- `keyLength` {`Number`}: define el valor de `location.key`. Valor por defecto: `6`.
- `children` {`node`}: componente/elemento que será renderizado.

## HashRouter

`Router` que usa el hash de la URL.

```javascript
<HashRouter
  basename={string}
  getUserConfirmation={func}
  hashType={"slash" || "noslash" || hashbang"}
>
  <App/>
</HashRouter>
```

- `basename`: Idem a `BrowserRouter`.
- `getUserConfirmation`: Idem a `BrowserRouter`.
- `hashType`: Tipo de hash a utilizar:
  - `slash`: `#/whatever` (hash por defecto).
  - `noslash`: `#whatever`.
  - `hashbang`: `#!/whatever` (deprecado).
- `children`: Idem a `BrowserRouter`.
