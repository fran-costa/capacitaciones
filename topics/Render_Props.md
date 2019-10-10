# Render Props

**Render Props** es una técnica para reutilizar una determinada lógica en múltiples componentes. Su nombre hace referencia al hecho de que un componente que implemente esta técnica, "reemplaza" su función `render` por una función que recibe a través de una `prop` (la cual, debe retornar un componente).

## Uso de Render Props

Para enterder qué es y para qué sirve esta técnica, es interesante analizar el siguiente ejemplo que trackea la posición del cursor:

```javascript
class MouseTracker extends React.Component {
  state = { x: 0, y: 0 };

  handleMouseMove = event => this.setState({
    x: event.clientX,
    y: event.clientY
  });

  render() {
    const { x, y } = this.state;

    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        <p>Current mouse position: ({x}, {y})</p>
      </div>
    );
  }
}
```

Ahora bien, supongamos que quisieramos agregar un nuevo componente que en lugar de indicar la posición del cursor, dibuje una pelota en dicha posición.

Suponiendo que contamos con un componente `<Ball>` que renderiza nuestra pelota en las coordinadas (x, y) que recibe en su prop `coordinates`, podríamos intentar algo como lo siguiente:

```javascript
class BallMouseTracker extends React.Component {
  state = { x: 0, y: 0 };

  handleMouseMove = event => this.setState({
    x: event.clientX,
    y: event.clientY
  });

  render() {
    const { x, y } = this.state;

    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        <Ball coordinates={{ x, y }} />
      </div>
    );
  }
}
```

Si bien el ejemplo anterior cumple su cometido, estamos duplicando la lógica del primer ejemplo.

Si analizamos los dos ejemplos anteriores, la única diferencia entre los dos componentes está en su método `render`... Aquí es donde la técnica de **render prop** entra en juego.

Veamos el siguiente componente:

```javascript
class MouseCoordinates extends React.Component {
  state = { x: 0, y: 0 };

  handleMouseMove = event => this.setState({
    x: event.clientX,
    y: event.clientY
  });

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    );
  }
}
```

En lugar de tener una representación estática de lo que `MouseCoordinates` debe renderizar, estamos haciendo uso de su `render` prop para determinar en forma dinámica que se debe renderizar. Es importante notar aquí que dicha prop es una función cuyos parámetros son las coordenadas ({ x, y }) de la posición del cursor.

Ahora bien, tratemos de reimplementar los componentes `MouseTracker` y `BallMouseTracker` usando `MouseCoordinates`:

```javascript
class MouseTracker extends React.Component {
  render() {
    return (
      <MouseCoordinates render={({ x, y }) => (
        <p>Current mouse position: ({x}, {y})</p>
      )}/>
    );
  }
}

class BallMouseTracker extends React.Component {
  render() {
    return (
      <MouseCoordinates render={({ x, y }) => (
        <Ball coordinates={{ x, y }} />
      )}/>
    );
  }
}
```

Como podemos ver, esta técnica nos permite reutilizar una determinada lógica en nuestros componentes. Podemos, de hecho, entenderla como una forma de crear componentes sin definir su método `render`.

## Uso de la prop children

En muchos casos, resulta interesante utilizar la prop `children` para hacer uso de esta técnica. El cambio es muy sencillo y podemos verlo reescribiendo nuestro ejemplo anterior:

```javascript
class MouseCoordinates extends React.Component {
  state = { x: 0, y: 0 };

  handleMouseMove = event => this.setState({
    x: event.clientX,
    y: event.clientY
  });

  render() {
    return (
      <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
        {this.props.children(this.state)} <-- Change render for children
      </div>
    );
  }
}
```

```javascript
class MouseTracker extends React.Component {
  render() {
    return (
      <MouseCoordinates>
        {({ x, y }) => (
          <p>Current mouse position: ({x}, {y})</p>
        )}
      </MouseCoordinates>
    );
  }
}
```

## Uso de Render Props en PureComponents

El uso de una render prop puede no aprovechar la ventaja del uso de un PureComponent si se hace uso de funciones anónimas. Esto se debe a que se estaría creando una nueva función en cada renderizado y el método `shouldComponentUpdate` pre-implementado de un PureComponent siempre devolvería `true`. Para evitar esto, podemos utilizar un método de clase. Teniendo esto en cuenta, podemos modificar el ejemplo anterior llevándolo a la siguiente forma:

```javascript
class MouseCoordinates extends React.PureComponent { // Cambiamos de Component a PureComponent
  // Misma implementación
}

class MouseTracker extends React.Component {
  _renderMousePosition = ({ x, y }) => <p>Current mouse position: ({x}, {y})</p>;

  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <MouseCoordinates>
          {this._renderMousePosition}
        </MouseCoordinates>
      </div>
    );
  }
}
```
