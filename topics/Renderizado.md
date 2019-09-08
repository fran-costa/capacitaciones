# Renderizado de elementos

Los elementos son los bloques más pequeños de las aplicaciones de React, los cuales describen lo que se verá en la pantalla:

```javascript
const element = <h1>Hello, world</h1>;
```

A diferencia de los elementos del DOM de los navegadores, los elementos de React son objetos planos, y su creación es de bajo costo. React DOM se encarga de actualizar el DOM para igualar los elementos de React.

>Uno podría confundir los elementos con el muy conocido concepto de “componentes”. En la siguiente sección hablaremos de componentes. Los elementos son los que “constituyen” los componentes.

## Renderizando un elemento en el DOM

Digamos que hay un <div> en alguna parte de tu archivo HTML:

```html
<div id="root"></div>
```

Lo llamamos un nodo “raíz” porque todo lo que esté dentro de él será manejado por React DOM.

Las aplicaciones construidas solamente con React usualmente tienen un único nodo raíz en el DOM. Dado el caso que estés integrando React en una aplicación existente, puedes tener tantos nodos raíz del DOM aislados como quieras.

Para renderizar un elemento de React en un nodo raíz del DOM se utiliza `ReactDOM.render()`:

```javascript
const element = <h1>Hello, world</h1>;

// ReactDOM.render(React element, DOM element);
ReactDOM.render(element, document.getElementById('root'));
```

## Actualizando el elemento renderizado

Los elementos de React son inmutables, lo que implica que una vez que se crea un elemento, no es posible cambiar sus hijos o atributos.

Hasta aquí, entonces, la única manera de actualizar la interfaz de usuario sería creando un nuevo elemento y pasándolo a `ReactDOM.render()`.

Veamos el siguiente ejemplo:

```javascript
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );

  ReactDOM.render(element, document.getElementById('root'));
}

setInterval(tick, 1000);
```

El código anterior actualiza el elemento que se renderiza en el nodo raíz llamando a `ReactDOM.render()` cada un segundo con un nuevo elemento con la fecha actual.

> En la práctica, la mayoría de las aplicaciones de React solo llama ReactDOM.render() una vez, ya que encapsulan el código en componentes, como veremos más adelante.

## React solo actualiza lo que es necesario

React DOM compara el elemento y su hijos con el elemento anterior, y solo aplica las actualizaciones del DOM que son necesarias para que el DOM esté en el estado deseado.

Del ejemplo anterior, esto implica que, aunque creamos un elemento que describe el árbol de la interfaz de usuario en su totalidad en cada instante, React DOM solo actualiza el texto del nodo cuyo contenido cambió.

Puedes verificar esto inspeccionando el último ejemplo con las herramientas del navegador:

![clock](https://reactjs.org/granular-dom-updates-c158617ed7cc0eac8f58330e49e48224.gif)

> **Nota:** Para mayores detalles del algoritmo de "renderizado diferencial" de React, recomendamos leer [Reconciliation](https://reactjs.org/docs/reconciliation.html) de la documentación oficial.
