# Componentes no controlados

Como vimos en la clase de [Formularios](/topics/Formularios.md), los datos del formulario son manejados por un componente React. Esta estrategia se conoce como **Componentes controlados**, ya que el estado interno del elemento del DOM es manejado por el componente de React. Una técnica alternativa a esta, son los **Componentes no controlados**, donde los datos del formulario son manejados por el propio DOM.

Para escribir un componente no controlado, en lugar de escribir un controlador de eventos para cada actualización de estado, puedes usar una referencia para que obtengas los valores del formulario desde el DOM.

```javascript
class NameForm extends React.Component {
  _input = React.createRef();

  _handleSubmit = (event) => {
    alert('A name was submitted: ' + this._input.current.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this._handleSubmit}>
        <label>
          Name:
          <input type="text" ref={this._input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

## Valores predeterminados

Dentro de la técnica de **Componentes no controlados**, es posible definir el valor inicial para un elemento de un formulario haciendo uso del atributo `defaultValue`:

```javascript
render() {
  return (
    <form onSubmit={this._handleSubmit}>
      <label>
        Name:
        <input
          defaultValue="Bob"
          type="text"
          ref={this._input} />
      </label>
      <input type="submit" value="Submit" />
    </form>
  );
}
```

Del mismo modo, `<input type="checkbox">` e `<input type="radio">` admiten `defaultChecked`, y `<select>` y `<textarea>` admiten `defaultValue`.

## Input de archivos

En HTML, un `<input type="file">` permite al usuario elegir uno o más archivos del almacenamiento en sus dispositivos para cargarlos a un servidor o manipularlos mediante JavaScript a través de la API de archivos.

```javascript
<input type="file" />
```

En React, un `<input type="file" />` siempre es un componente no controlado porque su valor solo puede ser establecido por un usuario.

Para interactuar con un archivo, se debe hacer uso de la [API File](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications). El siguiente ejemplo muestra cómo crear un referencia al nodo DOM para acceder a los archivos en un controlador de envío:

```javascript
class FileInput extends React.Component {
  _fileInput = React.createRef();

  _handleSubmit = (event) => {
    event.preventDefault();
    alert(`Selected file - ${this._fileInput.current.files[0].name}`);
  }

  render() {
    return (
      <form onSubmit={this._handleSubmit}>
        <label>
          Upload file:
          <input type="file" ref={this._fileInput} />
        </label>
        <br />
        <button type="submit">Submit</button>
      </form>
    );
  }
}
```
