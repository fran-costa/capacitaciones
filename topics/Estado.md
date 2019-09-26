# Estado de un componente

En [Renderizado de elementos](topics/Renderizado.md?id=actualizando-el-elemento-renderizado), aprendimos a actualizar la interfaz de usuario invocando a `ReactDOM.render()` cada vez que queríamos actualizar la UI.

En esta sección, aprenderemos como hacer un componente `Clock` (como el que construimos en la sección de _Renderizado de elementos_) que sea reutilizable y contenga la lógica necesaria para actualizarse por sí mismo. Idealmente, queremos construir un componente completamente encapsulado de forma tal que podamos utilizarlo de la siguiente manera:

```javascript
ReactDOM.render(
  // El componente Clock debe renderizar el reloj y
  // actualizar la hora automáticamente cada segundo
  <Clock />,
  document.getElementById('root')
);
```

Para implementar esto, necesitamos que el componente `Clock` maneje su propio **estado (state)** interno.

El state es similar a las props, pero es privado y está completamente controlado por el componente.

## Convertir un componente funcional en un componente de clase

Previo a la aparición de **React Hooks** (incorporados formalmente en la versión 16.8 de React), la manera de crear componentes capaces de manejar su propio **state** (es decir, su propio estado interno), era creando componentes de clase. Resulta interesante entonces entender los pasos a seguir para convertir un componente funcional en un componente de clase. Veamos esto usando el ejemplo de nuestro componente `Clock`:

```javascript
// Componente funcional Clock
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}
```

Para convertir el componente funcional `Clock` en un componente de clase debemos seguir los siguientes pasos:

1. Crear una clase ES6 con el mismo nombre que herede de `React.Component`.
2. Agregar un único método vacío llamado `render()`.
3. Mover el cuerpo de la función al método `render()`.
4. Reemplazar `props` con `this.props` en el cuerpo de `render()`.
5. Borrar el resto de la declaración de la función ya vacía.

```javascript
// Componente de clase Clock
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

El método `render()` se invocará cada vez que ocurre una actualización, siempre y cuando rendericemos `<Clock />` en el mismo nodo del DOM (se usará solo una única instancia de la clase `Clock`). Esto nos permite utilizar características adicionales como el state local y los métodos de ciclo de vida.

## Agregar state a un componente de clase

Basándonos nuevamente en nuestro componente (ahora de clase) `Clock`, vemos que el mismo sigue renderizando la fecha que recibe de su componente padre; Esto implica por tanto que la lógica de actualización de la fecha seguirá estando por fuera del componente. Como mencionamos al inicio de esta clase, lo que buscamos es encapsular la lógica de funcionamiento de nuestro `Clock` dentro del mismo componente, de forma tal que reutilizarlo sea algo sencillo. Para hacer esto, haremos uso del state del componente.

Como se indicó anteriormente, el state de un componente es similar a sus props, sólo que es privado y controlado completamente por el componente. Movamos entonces el valor de `date` de las props al state. Para hacer esto, debemos seguir los dos pasos siguientes:

1\. Reemplazar `this.props.date` con `this.state.date` en el método `render()`:

```javascript
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

2\. Añadir un `constructor` de clase que asigne el valor inicial del state:

```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      date: new Date()
    };
  }

  ...
}
```

> **Nota:** Los componentes de clase siempre deben invocar al `constructor` pasando las props. Para más información sobre esto, pueden leer [este artículo](https://overreacted.io/why-do-we-write-super-props/) del blog personal de Dan Abramov.

## Modificar el estado de un componente

Hasta aquí, nuestro componente `Clock` asigna el valor de `this.state.date` en su `constructor`. El `constructor` de una clase se ejecuta únicamente cuando la misma es instanciada. En un componente de clase de React, esto sucede cuando el mismo es montado, es decir, es agregado a un nodo del DOM.

Dado que no estamos modificando el valor de `this.state.date` en ningún momento, el mismo seguirá siendo el asignado al momento de montar el componente y el `Clock` no se actualizará. Para que nuestro `Clock` funcione como esperamos, debemos actualizar este valor periódicamente.

La forma de modificar el state de un componente es mediante el llamado al método `this.setState()`. Este método, recibe el nuevo valor del state y llama al método `render()` del componente para actualizar la UI.

Para el caso específico de nuestro componente `Clock`, debemos actualizar el valor del state cada un segundo. En JavaScript, la forma de realizar una determinada acción cada un intervalo de tiempo determinado es mediante el uso de la función `setInterval()`. Ahora bien, ¿dónde debemos hacer uso de `setInterval()` para hacer funcionar nuestro `Clock`? Para responder a esta pregunta, debemos introducir el concepto de **métodos de ciclo de vida** de un componente de React.

Los **métodos de ciclos de vida** de un componente son, en esencia, métodos especiales que son ejecutados en momentos específicos de la _vida_ de un componente de React: luego de que el componente es montado, cada vez que recibe nuevas props, luego de ser re-renderizado, antes de ser desmontado, etc.

<!-- TODO: Agregar link a la sección de métodos de ciclo de vida cuando se agregue -->
Si bien veremos en detalle los diferentes métodos de ciclo de vida de un componente más adelante, resulta interesante presentar dos de ellos en este momento: `componentDidMount()` y `componentWillUnmount()`:
1. `componentDidMount()` es un método que se ejecuta una única vez, luego del primer renderizado del componente.
2. `componentWillUnmount()` es un método que se ejecuta una única vez, antes de que el componente sea desmontado.

Conociendo estos dos métodos de ciclo de vida y la forma de actualizar el state de un componente, podemos entonces combinarlos para hacer que nuestro `Clock` actualize el valor de `this.state.date` cada segundo, actualizando por tanto la interfaz de usuario como deseamos. Veamos como quedaría finalmente nuestro componente:

```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }

  componentDidMount() {
    this.timerID = setInterval(
      this.tick,
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

Repasemos lo que está sucediendo aquí:

1. Cuando se monta el componente `Clock`, React invoca al constructor del mismo. Ya que necesita mostrar la hora actual, inicializa el valor de `this.state.date` con la hora actual.
2. React invoca entonces al método `render()` del componente `Clock`. Así es como React sabe lo que se debe mostrar en pantalla. React entonces actualiza el DOM para que coincida con la salida del renderizado de `Clock`.
3. Cuando la salida de `Clock` se inserta en el DOM, React invoca al método de ciclo de vida `componentDidMount()`, donde se configura un temporizador para llamar al método `this.tick()` cada un segundo.
4. Cada un segundo, el navegador invoca al método `tick()`. Dentro de él, se actualiza el valor de `this.state.date` con la fecha actual mediante un llamado al método `this.setState()`. Dado que se produjo un llamado a este último método, React sabe que el state fue modificado e invoca de nuevo al método `render()` para computar nuevamente el elemento React que debe renderizarse en pantalla. Como `this.state.date` será distinto cada vez que se re-ejecute el método `render()`, el elemento React será diferente e incluirá la hora actualizada. Conforme a eso React actualiza el DOM.
5. Si el componente `Clock` se elimina en algún momento del DOM, React invoca al método de ciclo de vida `componentWillUnmount()`, por lo que el temporizador se elimina.

## Usar el estado correctamente

Hay tres cosas que importantes a mencionar sobre `setState()`.

### No se debe modificar el estado directamente

Por ejemplo, esto no volverá a renderizar un componente:

```javascript
// Incorrecto
this.state.comment = 'Hello';
```

En su lugar utiliza `setState()`:

```javascript
// Correcto
this.setState({comment: 'Hello'});
```

El único lugar donde es posible asignar `this.state` directamente es el `constructor`.

### Las actualizaciones del estado pueden ser asíncronas

React puede agrupar varias invocaciones a `setState()` en una sola actualización para mejorar el rendimiento.

Debido a que `this.props` y `this.state` pueden actualizarse de forma asincrónica, no debes confiar en sus valores para calcular el siguiente estado.

Por ejemplo, este código puede fallar en actualizar el contador:

```javascript
// Incorrecto
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

En su lugar, usa una segunda forma de `setState()` que acepta una función en lugar de un objeto. Esa función recibirá el estado previo como primer argumento, y las props en el momento en que se aplica la actualización como segundo argumento:

```javascript
// Correcto
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

### Las actualizaciones de estado se fusionan

Al invocar a `setState()`, React combina el objeto proporcionado con el estado actual.

Por ejemplo, el state puede contener varias variables independientes:

```javascript
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```

Luego pueden ser actualizas independientemente con invocaciones separadas a `setState()`:

```javascript
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```

La fusión es superficial, asi que `this.setState({comments})` deja intacto a `this.state.posts`, pero reemplaza completamente `this.state.comments`.

## Los datos fluyen hacia abajo

Ni los componentes padres o hijos pueden saber si un determinado componente tiene o no tiene estado y no les debería importar si se define como una función o una clase.

Por eso es que el estado a menudo se le denomina local o encapsulado. No es accesible desde otro componente excepto de aquel que lo posee y lo asigna.

Un componente puede elegir pasar su estado como props a sus componentes hijos:

Esto también funciona para componentes definidos por el usuario:

```javascript
<FormattedDate date={this.state.date} />
```

El componente `FormattedDate` recibiría `date` en sus props y no sabría si vino del estado de `Clock`, de los props de `Clock`, o si se escribió manualmente.

A esto se le llama flujo de datos **descendente** o **unidireccional**. Cualquier estado siempre es propiedad de algún componente específico, y cualquier dato o interfaz de usuario derivados de ese estado solo pueden afectar los componentes debajo de ellos en el árbol.

> En las aplicaciones de React, si un componente tiene o no estado se considera un detalle de implementación del componente que puede cambiar con el tiempo. Puedes usar componentes sin estado dentro de componentes con estado y viceversa.

## Ejercicio:
A partir del ejemplo de **Clock** como functional component vamos a realizar las siguientes tareas:

- Crear una class component para **Clock**
- En lugar de usar una prop para `date` vamos a usar un `state`
- Con los metodos del ciclo de vida vamos a hacer que el **Clock** se actualice cada 1 segundo

Links:
- Sin resolver: https://codesandbox.io/s/twilight-architecture-g0v2x
- Resuelto: ...