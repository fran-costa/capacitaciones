# Listas

Dada la posibilidad de introducir sintaxis JSX dentro de JavaScript y vise versa (ver [JSX en JavaScript](topics/JSX?id=jsx-en-javascript) y [JavaScript en JSX](topics/JSX?id=javascript-en-jsx)), podemos crear listas de elementos o componentes de React usando la función [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) de JavaScript y componentes React o código JSX.

Veamos el siguiente ejemplo donde transformamos un array de números en un array de elementos `<li>`:

```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map(number => <li>{number}</li>);

// Renderizamos `listItems`, la lista de elementos `<li>`
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```

> **Nota:** Típicamente, se suele renderizar una lista de elementos dentro de un componente al cual se le pasa el array de elementos a renderizar como una prop.

# Claves (Keys)

Si ejecutamos el código anterior, veremos una advertencia en la consola indicando que una **key** (clave) debería ser proporcionada para cada ítem de la lista. Una **key** es un atributo especial de tipo `string` que ayudan a React a identificar qué ítems han cambiado o si un ítem es agregado o eliminado. Las keys deben ser dadas a los elementos dentro del array para darles una identidad estable. Las mismas deben ser únicas e inmutables (entre elementos hermanos, no globalmente). Típicamente, se suele utilizar (de tenerlo) el `id` del elemento a renderizar:

```javascript
const todoItems = todos.map(todo =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```

En ocasiones donde no se dispone de un `id` o no se puede utilizar ningún otro dato para identificar inequívocamente un elemento de la lista, se puede usar el índice del ítem como key (de hecho, React usa por defecto este valor en caso de que no se especifique). Esto no es recomendable si la cantidad de elementos de la lista o el orden de los mismos puede variar, ya que puede impactar negativamente el rendimiento y causar problemas con el estado del componente.

> Para tener más detalles técnicos del porqué de la necesidad de incluir un `key`, recomendamos leer sobre [Reconciliation](https://es.reactjs.org/docs/reconciliation.html).
