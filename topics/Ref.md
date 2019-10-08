# Referencias

Las referencias proporcionan una forma de acceder a los nodos del DOM o a elementos React creados en el método de renderizado.

En un flujo normal en datos de React, las propiedades son la única forma en la que los componentes padres pueden interactuar con sus hijos. Para modificar un hijo, vuelves a renderizarlo con propiedades nuevas. Sin embargo, hay ciertos casos donde necesitamos modificar imperativamente un hijo fuera del flujo de datos típico. El hijo a ser modificado puede ser una instancia de un componente React, o un elemento del DOM. Para ambos casos, React proporciona una herramienta llamada **Referencias (o refs)**.

## Cuándo usar referencias

Existen una serie de casos típicos para el uso de referencias:

- Controlar foco, selección de texto, o reproducción de medios.
- Activar animaciones imperativas.
- Integración con bibliotecas DOM de terceros.

> **Nota:** Se recomienda no usar referencias en los casos donde se puede implementar una solución en forma declarativa (por ejemplo, mediante el uso de props).

## Creación de referencias

Las referencias son creadas usando `React.createRef()` y agregándolas a elementos de React mediante el atributo `ref`. Las referencias son asignadas comúnmente a una propiedad de instancia cuando un componente es construido, así pueden ser referenciadas por el componente.

```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }

  render() {
    return <div ref={this.myRef} />;
  }
}
```

## Accediendo a referencias

Cuando una referencia es pasada a un elemento en el renderizado, una referencia al nodo pasa a ser accesible en el atributo `current` de la referencia.

```javascript
const node = this.myRef.current;
```

El valor de la referencia es diferente dependiendo del tipo de nodo:

- Si es un elemento HTML, la referencia recibe el elemento DOM adyacente como su propiedad `current`.
- Si es un componente de clase, el objeto de la referencia recibe la instancia montada del componente como su atributo `current`.

> No es posible hacer referencia a componentes funcionales (dado que no tienen instancia).

## Ejemplo de referencia a node HTML

```javascript
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    // Crea una referencia para guardar el elemento _textInput del DOM
    this._textInput = React.createRef();
  }

  _focusTextInput = () => {
    // Hace foco explícitamente del campo de texto, haciendo uso de un API del DOM
    this._textInput.current.focus();
  }

  render() {
    return (
      <div>
        <input
          type="text"
          ref={this._textInput}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this._focusTextInput}
        />
      </div>
    );
  }
}
```

> **Nota:** La referencia es actualizada antes de los métodos `componentDidMount` o `componentDidUpdate`.

## Referencias mediante callback

React también ofrece la posibilidad de agregar referencias "mediante callbacks”. En lugar de pasar un atributo a `ref` creado por `React.createRef()`, se pasa una función. La función recibe la instancia del componente React o el elemento DOM del HTML como su argumento, que puede ser guardado y accedido desde otros lugares.

```javascript
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    this._textInput = null;
  }

  componentDidMount() {
    // Autofocus al campo después de que el componente se monta
    this.focusTextInput();
  }

  _setTextInputRef = element => this._textInput = element;

  // Hace foco del campo de texto usando un método propio del DOM
  focusTextInput = () => {
    if (this._textInput) this._textInput.focus();
  };

  render() {
    // Usa el `ref` mediante callback para guardar una referencia al campo de texto del DOM
    // en una propiedad de la instancia (por ejemplo, this._textInput)
    return (
      <div>
        <input
          type="text"
          ref={this._setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

React llama al callback de `ref` con el elemento del DOM cuando el componente es montado, y lo llama con `null` cuando este se desmonte.
