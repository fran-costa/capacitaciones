# Manejado de eventos

Manejar eventos en elementos de React es muy similar a manejar eventos con elementos del DOM. Hay algunas diferencias de sintaxis:

- Los eventos de React se nombran usando camelCase, en vez de minúsculas.
- Con JSX, se usa una función como manejador del evento, en vez de un string.

Por ejemplo, el siguiente elemento HTML:

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

En React se escribe:

```javascript
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

Otra diferencia es que en React no es posible retornar `false` para prevenir el comportamiento por defecto. Para hacer esto, en su lugar, debe llamarse explícitamente a la función `preventDefault`. Por ejemplo, en HTML plano, para prevenir el comportamiento por defecto de un enlace de abrir una nueva página, es posible escribir:

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

En cambio en React, esto podría ser:

```javascript
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

Aquí, `e` es un _SyntheticEvent_. React define estos eventos sintéticos acorde a las especificaciones W3C, garantizando la compatibilidad a tráves de los navegadores. Para leer más sobre _SyntheticEvent_ [aquí](https://es.reactjs.org/docs/events.html).

## Manejo de eventos en componentes de clase

Cuando defines un componente usando una clase de ES6, un patrón muy común es que los manejadores de eventos sean un método de la clase. Por ejemplo, este componente `Toggle` renderiza un botón que permite al usuario cambiar el estado entre "ON" y "OFF":

```javascript
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // Es necesario hacer un binding del `this` para
    // que sea accesible desde el método `handleClick`
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
```

Es posible evitar la necesidad de hacer un binding del `this` en el constructor. Para esto, se debe hacer uso de (arrow functions)[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions], en cuyo caso, el valor de `this` corresponde a dicho valor en el contexto en que fueron llamadas. Veamos un ejemplo:

```javascript
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

Esta construcción, tiene la particularidad de que utiliza una función anónima, lo que implica que una función diferente es creada por cada renderizado del componente. En la mayoría de los casos, esto está bien. Sin embargo, si este callback se pasa como una propiedad a componentes más bajos, estos componentes podrían renderizarse nuevamente. Para evitar esto, es posible utilizar un atributo de clase de forma tal que la función de callback sea siempre la misma:

```javascript
class LoggingButton extends React.Component {
  handleClick = () => console.log('this is:', this);

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

## Pasando argumentos a escuchadores de eventos

Es muy común querer pasar un parámetro extra a un manejador de eventos. Por ejemplo, si id es el ID de una fila en una tabla, cualquiera de los códigos a continuación podría funcionar:

```javascript
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

Las dos líneas anteriores son equivalentes, y utilizan una arrow function y `Function.prototype.bind` respectivamente.

En ambos casos, el argumento `e` que representa el evento de React va a ser pasado como un segundo argumento después del ID. Con una función flecha, tenemos que pasarlo explícitamente, pero con bind cualquier argumento adicional es pasado automáticamente.
