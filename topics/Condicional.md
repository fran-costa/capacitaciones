# Renderizado condicional

En React, es posible crear distintos componentes que encapsulan un determinado comportamiento. Luego, es posible renderizar solamente algunos de ellos, dependiendo del estado de tu aplicación.

El renderizado condicional en React funciona de la misma forma que lo hacen las condiciones en JavaScript. Se usan operadores de JavaScript como `if` o el [operador ternario](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) (`condición ? true : false`) para crear elementos representando el estado actual y React actualiza la interfaz de usuario según corresponda.

Por ejemplo, es posible crear un componente `Greeting` que renderice el componente `UserGreeting` o `GuestGreeting` según corresponda:

```javascript
function UserGreeting() {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting() {
  return <h1>Please sign up.</h1>;
}

function Greeting(props) {
  if (props.isLoggedIn) return <UserGreeting />;

  return <GuestGreeting />;
}
```

>Si repasamos lo visto en [JSX en JavaScript](http://localhost:3000/#/topics/JSX?id=jsx-en-javascript), recordaremos que una expresión JSX (que es ni más ni menos que lo que devuelve un componente React) es asignable a una variable JavaScript, con lo cual, el ejemplo anterior puede reescribirse también como:
>
>```javascript
>function Greeting(props) {
>   // Hacemos uso del operador ternario para hacer if-else en una sola línea
>   const componentToRender = (props.isLoggedIn) ? <UserGreeting /> : <GuestGreeting />;
>
>   return componentToRender;
>}
>```

## El operador lógico &&

Dado que es posible embeber expresiones [JavaScript en JSX](http://localhost:3000/#/topics/JSX?id=javascript-en-jsx), una forma simple de incluir condicionalmente un elemento/componente es haciendo uso del operador lógico `&&` de JavaScript:

```javascript
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}
```

> Esto funciona porque en JavaScript, `true && expresión` siempre evalúa a `expresión`, y `false && expresión` siempre evalúa a `false`.

## Evitar que el componente se renderice

Es posible que en ciertas ocasiones se necesite que un componente se oculte a sí mismo aunque haya sido renderizado por otro componente. Para hacer esto, simplemente basta con retornar `null` en lugar del resultado de renderizado.

En el siguiente ejemplo, el `<WarningBanner />` se renderiza dependiendo del valor de la prop llamado `warn`. Si el valor de la prop es `false`, entonces el componente no se renderiza:

```javascript
function WarningBanner(props) {
  if (!props.warn) return null;

  return (
    <div className="warning">
      Warning!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = { showWarning: true };
  }

  handleToggleClick = () => this.setState({ showWarning: !this.state.showWarning });

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}
```

> **Importante:** Devolver `null` desde el método `render()` de un componente no implica que los métodos del ciclo de vida del mismo no se ejecuten.
