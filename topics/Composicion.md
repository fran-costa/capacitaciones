# Composición

React tiene un potente modelo de composición, siendo ésta la forma recomendada para la reutilización de código entre componentes por sobre la herencia.

Veamos algunas situaciones en las que típicamente se emplea la herencia y veamos como resolverlas con composición.

## Contención

Algunos componentes no conocen sus hijos de antemano. En React, es recomendable que estos componentes usen la prop especial `children` para pasar elementos hijos directamente en su resultado:

```javascript
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```

Esto permite que otros componentes les pasen hijos arbitrarios anidando el JSX:

```javascript
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

Cualquier cosa dentro de la etiqueta JSX `<FancyBorder>` se pasa dentro del componente `FancyBorder` como la prop `children`. Como `FancyBorder` renderiza `{props.children}` dentro de un `<div>`, los elementos que se le han pasado aparecen en el resultado final.

También pueden usarse otras props para especificar elementos hijos en determinadas posiciones:

```javascript
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={<Contacts />}
      right={<Chat />}
    />
  );
}
```

Los elementos como `<Contacts />` y `<Chat />` son simplemente objetos, por lo que puedes pasarlos como props como cualquier otro dato.

## Especialización

A veces pensamos en componentes como casos específicos de otros componentes. Por ejemplo, podríamos decir que un `WelcomeDialog` es un caso concreto de `Dialog`.

En React, esto también se consigue por composición, en la que un componente más específico renderiza uno más genérico y lo configura con props:

```javascript
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!"
    />
  );
}
```

> **Nota:** La composición funciona igualmente bien para componentes definidos como clases.
