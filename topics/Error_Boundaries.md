# Error Boundaries

Un error de JavaScript en una parte de la interfaz no debería romper toda la aplicación. Para resolver este problema, React 16 introduce el nuevo concepto de **Error Boundaries**.

Son componentes de React que capturan errores de JavaScript en cualquier parte de su árbol de componentes hijo y muestran otra UI por defecto en lugar del árbol de componentes que ha fallado. Los errores capturados son los que se dan durante el renderizado, en métodos del ciclo de vida y en constructores de todo el árbol bajo ellos.

> No se capturan errores que suceden dentro de _event handlers_, durante la ejecución de código asíncrono, dentro de un Error Boundary o en SSR.

## Construcción de un Error Boundary

Un componente de clase se convierte en un **Error Boundary** si define uno (o ambos) de los métodos del ciclo de vida `static getDerivedStateFromError()` o `componentDidCatch()`.

```javascript
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    // Actualiza el estado para que el siguiente renderizado muestre la UI de error
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // Envía un reporte del error con información del mismo
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <ErrorUI />;
    }

    return this.props.children; 
  }
}
```

Utilizar un **Error Boundary** luego, es tan simple como:

```html
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

> Los límites de errores funcionan como un bloque `catch{}` de JavaScript, pero para componentes.

## Dónde poner Error Boundaries

Si bien se puede definir la granularidad de los **Error Boundaries** como se quiera dentro de un proyecto, típicamente se suele wrappear los componentes que definen diferentes segmentos de la UI (como paneles, menúes y demás) con un **Error Boundary** propio para cada caso en pos de mostrar un panel de error acorde al segmento de UI que falló.

> **Nota:** A partir de React 16, los errores que no sean capturados por ningún **Error Boundary** resultarán en el desmontado de todo el árbol de componentes de React.

## ¿Por qué Error Boundaries no funciona para manejo de eventos?

A diferencia del método `render` y de los métodos del ciclo de vida, los __event handlers__ no ocurren durante el renderizado, por lo que, si lanzan una excepción, React sigue sabiendo qué mostrar en la pantalla.

Para capturar un error dentro de un __event handler__, basta con utilizar un `try/catch` de JavaScript:

```javascript
class MyComponent extends React.Component {
  state = { error: null };

  _handleClick = () => {
    try {
      // ...
    } catch (error) {
      this.setState({ error });
    }
  }

  render() {
    if (this.state.error) {
      return <h1>Hubo un error en el event handler.</h1>
    }

    return <button onClick={this._handleClick}>Haga click</button>
  }
}
```
