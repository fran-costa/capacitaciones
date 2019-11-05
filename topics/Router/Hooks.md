# Hooks

React Router provee los siguientes Hooks que dan acceso al estado del `Router` y permiten acceder a la funciones de navegación en la app:
- `useHistory`
- `useLocation`
- `useParams`
- `useRouteMatch`

## useHistory

Este Hook nos da acceso a la instancia de `history`. El objeto `history` tiene las siguientes propiedades:

- `length` {`number`}: Cantidad de entradas del array
- `action` {`string`}: La última acción ejecuta (`PUSH`, `REPLACE` o `POP`)
- `location` {`object`}: La "ubicación" actual. Sus propiedades son:
  - `pathname` {`string`}: El path de la URL
  - `search` {`string`}: Los query strings de la URL
  - `hash` {`string`}: El hash de la URL
  - `state` {`object`}: Estado provisto al hacer, por ejemplo `push(path, state)`
- `push` {`function(path, [state])`}: Agrega una nueva entrada al array
- `replace` {`function(path, [state])`}: Reemplaza la última (actual) entrada del array por una nueva
- `go` {`function(n)`}: Mueve `n` veces la posición actual del array
- `goBack` {`function()`}: Equivalente a `go(-1)`
- `goForward` {`function()`}: Equivalente a `go(1)`
- `block` {`function()`: Evita la navegación

> **Nota:** `history`, en el entorno de React Router, hace referencia a la librería [history](https://github.com/ReactTraining/history).

## useLocation

Este Hook nos retorna el objeto `location` que representa la URL actual. El objeto `location` tiene las siguientes propiedades:

- `key` {`string`}: Identificación del objeto (usado en `BrowserRouter`)
- `pathname` {`string`}: El path de la URL
- `search` {`string`}: Los query strings de la URL
- `hash` {`string`}: El hash de la URL
- `state` {`object`}: Estado provisto al hacer, por ejemplo `push(path, state)`

> **Nota:** Cuando utilizamos un `Link` por ejemplo, es muy común utilizar un `string` en lugar de un objeto `location`:
> ```javascript
> const location = {
>   pathname: "/somewhere",
>   search: "?some=search-string",
>   hash: "#howdy",
> };
>
> const locationString = "/somewhere?some=search-string#howdy";
> ```

## useParams

Este Hook nos retorna el objeto `match` con los parámetros en la URL. Las propiedades de este objeto son las siguiente:

- `params` {`object`}: Parámetros incluidos en la URL de pares `key`/`value`
- `isExact` {`boolean`}: Es `true` si la URL fue matcheada en forma exacta
- `path` {`string`}: El `path` usado para el matcheo de la URL
- `url` {`string`}: La porción de la URL matcheada

> **Nota:** Dado que en la forma de ruta `<Route children={() => {}} />` la función `children` es llamada haya matcheo con la URL o no, `match` será `null` en los casos en los que la URL no sea matcheada.

## useRouteMatch

Este Hooks nos permite matchear la URL como lo haríamos con `Route`:

**Con `Route`:**
```javascript
import { Route } from "react-router-dom";

function BlogPost() {
  return (
    <Route
      path="/blog/:slug"
      render={({ match }) => {
        // `match` será `null` en caso de no matchear
        return ...;
      }}
    />
  );
}
```

**Con `useRouteMatch`:**
```javascript
import { useRouteMatch } from "react-router-dom";

function BlogPost() {
  const match = useRouteMatch("/blog/:slug");

  // `match` será `null` en caso de no matchear
  return ...;
}
```
