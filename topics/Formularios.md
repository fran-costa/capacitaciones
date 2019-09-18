# Formularios

Los elementos de formularios en HTML funcionan un poco diferente a otros elementos del DOM en React, dado que los elementos de formularios conservan naturalmente un estado interno. Por ejemplo, este formulario solamente en HTML, acepta un solo nombre.

```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

Este formulario tiene el comportamiento predeterminado en HTML y puede usarse tal cual es en una aplicación React. Sin embargo, en la mayoría de casos es conveniente tener una función Javascript que se encargue del envío del formulario, y que tenga acceso a los datos que el usuario introdujo. La forma predeterminada para hacer esto en React es una técnica llamada **Componentes controlados**.

## Componentes controlados

En HTML, los elementos de formularios como los `<input>`, `<textarea>` y `<select>` mantienen su propio estado y lo actualiza de acuerdo a la interacción del usuario. En React, el estado mutable de un componente es mantenido normalmente en el `state` del mismo y solo se actualiza mediante `setState()`.

Es posible combinar ambos funcionamientos de formal tal que el estado de React sea la “única fuente de la verdad”. De esta manera, los componentes React que rendericen un formulario también controlan lo que pasa en ese formulario y lo que sucede con la interacción del usuario con los mismos. Un campo de un formulario cuyos valores son controlados por React de esta forma es denominado **componente controlado**.

Por ejemplo, el ejemplo anterior puede ser reescrito usando la técnica de componentes controlados de la siguiente manera:

```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);

    this.state = { value: '' };
  }

  handleChange = (event) => this.setState({value: event.target.value});

  handleSubmit = (event) => {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

Al asignarle el valor de `this.state.value` al atributo `value` del `<input>`, logramos que el elemento siempre muestre el valor de `this.state.value`, haciendo que el estado de React sea la fuente de la verdad. Por otra parte, manejando el evento `onChange`, logramos modificar el valor de `this.state.value` a medida que el usuario escribe, controlando así completamente el formulario usando React.

Con un componente controlado, resulta directo modificar o validar la entrada del usuario. Por ejemplo, si quisiéramos asegurar que los nombres sean escritos en mayúscula, podríamos escribir el `handleChange` como:

```javascript
handleChange = (event) =>this.setState({value: event.target.value.toUpperCase()});
```

## La etiqueta textarea

En HTML, el elemento `<textarea>` define su texto por su hijo:

```html
<textarea>
  Hello there, this is some text in a text area
</textarea>
```

En React, un `<textarea>` utiliza un atributo `value` en su lugar:

```javascript
<textarea value={this.state.value} onChange={this.handleChange} />
```

## La etiqueta select

En HTML, `<select>` crea una lista desplegable. Por ejemplo:

```html
<select>
  <option value="lime">Lime</option>
  <option value="coconut" selected>Coconut</option>
  <option value="mango">Mango</option>
</select>
```

La opción con el atributo `selected` será la que aparezca seleccionada inicialmente. React, en su lugar utiliza un atributo `value` en la etiqueta `select`. Esto es más conveniente en un componente controlado dado que solo se necesita actualizarlo en un solo lugar, por ejemplo:

```javascript
<select value={this.state.value} onChange={this.handleChange}>
  <option value="lime">Lime</option>
  <option value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```

>**Nota:** Es posible pasar un array al atributo `value`permitiendo una selección múltiple:
>
>```javascript
><select multiple={true} value={['B', 'C']}>
>```

## La etiqueta file input

En HTML, un `<input type="file">` permite que el usuario escoja archivos de su dispositivo para ser cargados o manipulados por Javascript. Ya que su valor es solo de lectura, no puede ser _controlado_ por React, siendo un **componente no controlado**.

> Para leer más sobre la técnica de **componentes no controlados** en [esta sección](https://es.reactjs.org/docs/uncontrolled-components.html) de la [documentación oficial de React].

## Manejo de múltiples inputs

Es posible manejar múltiples elementos `input` controlados, agregando un atributo `name` a cada uno de los elementos y dejar que la función controladora decida que hacer basada en el valor de `event.target.name`.

Por ejemplo:

```javascript
handleInputChange = ({ target }) => {
  const value = target.type === 'checkbox' ? target.checked : target.value;
  const name = target.name;

  this.setState({ [name]: value });
}
```
