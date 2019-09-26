# Componentes y propiedades (props)

Los componentes permiten separar la interfaz de usuario en piezas independientes, reutilizables y pensar en cada pieza de forma aislada.

Conceptualmente, los componentes son como las funciones de JavaScript: aceptan entradas arbitrarias (llamadas “props”) y devuelven elementos React que describen lo que debe renderizarse en pantalla.

## Componentes funcionales y de clase

La forma más sencilla de definir un componente es escribir una función JavaScript:

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

Esta función es un componente de React válido porque acepta un solo argumento “props” (un objeto) y devuelve un elemento de React. Un componente así definido es conocido como componente "funcional" porque literalmente es una función JavaScript.

También es posible utilizar una clase de ES6 para definir un componente:

```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Los dos componentes anteriores son equivalentes desde el punto de vista de React.

## Renderizando un componente

Hasta aquí, sólo vimos elementos de React que representan etiquetas del DOM:

```javascript
const element = <div />;
```

Sin embargo, los elementos también pueden representar componentes definidos por el usuario:

```javascript
const element = <Welcome name="Sara" />;
```

Cuando React encuentra un elemento representando un componente definido por el usuario, pasa atributos JSX a este componente como un solo objeto. Llamamos a este objeto “props”.

Veamos el siguiente ejemplo:

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

Veamos en detalle lo que sucede para entender el funcionamiento de React:

1. Llamamos a `ReactDOM.render()` con el elemento `<Welcome name="Sara" />`.
2. React llama al componente `Welcome` con `{name: 'Sara'}` como “props”.
3. Nuestro componente `Welcome` devuelve un elemento `<h1>Hello, Sara</h1>` como resultado.
4. React DOM actualiza el DOM para que coincida con `<h1>Hello, Sara</h1>`.

>Los nombres de los componentes deben comenzar con mayúscula, ya que React interpreta los componentes que empiezan con minúscula como tags HTML.

## Composición de componentes

Los componentes pueden referirse a otros componentes en su salida. Esto nos permite utilizar la misma abstracción de componente para cualquier nivel de detalle. Un botón, un cuadro de diálogo, un formulario, una pantalla: en aplicaciones de React, todos son expresados comúnmente como componentes.

Por ejemplo, podemos crear un componente `App` que renderiza muchas veces el mismo componente `Welcome` creado anteriormente:

```javascript
function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}
```

## Extracción de componentes

La magia de React radica en la creación de componentes (idealmente) pequeños y reutilizables, lo cual nos permiten un mayor entendimiento del código y nos evitan la repetición de código a lo largo de una aplicación.

<!-- TODO - Agregar ejemplo de componente -->

## Las props son de solo lectura

Ya sea que un componente este declarado como función o como clase, éste nunca debe modificar sus props.

Considera esta función `sum`:

```javascript
function sum(a, b) {
  return a + b;
}
```

Tales funciones son llamadas **puras** ya que no modifican sus entradas y siempre devuelven el mismo resultado para las mismas entradas.

En contraste, esta función es impura porque cambia su propia entrada:

```javascript
function withdraw(account, amount) {
  account.total -= amount;
}
```

La única regla estricta en React es:

> Todos los componentes de React deben actuar como funciones puras con respecto a sus props.

## Ejercicio:
A partir de una componente **Comment** con mucho jsx y props, vamos a crear componentes mas chicos en donde cada uno tenga funcionalidad por si mismo y solo requiera las props que utiliza.

- Sin resolver: https://codesandbox.io/s/componentes-oyw7z
- Resuelto: https://codesandbox.io/s/componentes-lbuee
