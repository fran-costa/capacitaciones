# Componentes de matcheo de rutas

Los **componentes de matcheo de rutas** se encargan de definir qué componente/s se renderizará/n en función de la URL.

`react-router-dom` ofrece dos **componentes de matcheo de rutas**: `Route` y `Switch`.

## Route

Componente que renderiza un determinado componente de React cuando la URL (en realidad, la `location`) actual matchea con su `path` (o `null` cuando no matchea).

```javascript
<Route
  path={string || string[]}
  exact={bool}
  strict={bool}
  location={object}
  sensitive={bool}
  component || render || children
/>
```

- `path` {`string`}: URL (o arreglo de URLs) que se comparará con la URL del navegador (si no se especifica, matchea siempre).
```javascript
...
path="/whatever"
path="/account/details"
path="/user/:id"        // :id indica que es un parámetro
...
```
- `exact` {`Boolean`}: Si es `true`, el matcheo del `path` con la URL debe ser exacto. Valor por defecto: `false`.
```javascript
...
path="/account"         // Matchea con URLs que empiece con /account, ej /account/details
exact path="/account"   // Matchea solo cuando la URL es /account
...
```
- `strict` {`Boolean`}: Si es `true`, la `/` al final del `path` será considerado a la hora del matcheo. Valor por defecto: `false`.
- `location` {`object`}: Un objecto `location` para matchear contra el `path`. Si no se utiliza, será el provisto por el Router.
- `sensitive` {`Boolean`}: Si es `true`, hace el matcheo diferenciando mayúsculas de minúsculas.
- `component` || `render` || `children`: Permite especificar el componente a renderizar cuando la ruta matchea:

  - `component`: Permite especificar directamente un componente a renderizar cuando la ruta matchea:
    ```javascript
    <Route path="/about" component={AboutUsComponent}
    ```
  - `render`: Permite especificar una función (que retorne un componente) a ejecutar cuando la ruta matchea:
    ```javascript
    <Route path="/about" render={() => <AboutUsComponent />}
    ```
  - `children`: Idem a `render`, solo que se ejecuta siempre, sin importar si la ruta matchea o no.
    ```javascript
    <Route>
      <MyComponent />
    </Route>
    ```
  
> **Nota:** Si la prop `component` está definida, se omitirán los valores de las prop `render` y/o `children` (de estar definidas también). De igual forma, si `render` está definida y `component` no, el valor de `children` será omitido.

#### Parametrización de URLs

Es posible definir `path`s con valores parametrizables mediante el uso de `:parameterName`. Esto permite crear `path`s que acepten un parámetro y, una vez que matcheen, obtener el valor de dicho parámetro. Por ejemplo, si definimos `path="/user/:id"`, la URL `.../user/1` matcheará con dicho `path` y podremos saber en el componente a renderizar el valor del `id` especificado en la URL.

## Switch

Componente que renderiza sólo el primer componente `Route` o `Redirect` que matchee.

```javascript
<Switch location={object}>
  ...
  <Route ...>
  <Redirect ...>
  ...
</Switch>
```

- `location` {`object`}: Un objecto `location` para matchear contra el `path`. Si no se utiliza, será el provisto por el Router.
- `children` {`node`}: Una serie de `Route`s y/o `Redirect`s. Solo el primer hijo que matchee con el valor de `location` será renderizado (los `Route`s son matcheados con su prop `path`, mientras que los `Redirect`s con su prop `from`).
```javascript
<Switch>
  <Route exact path="/" component={Home}>
  <Route path="/user" component={User}>
  <Redirect to="/" />
</Switch>
```
