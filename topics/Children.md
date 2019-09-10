# Prop children

`props.children` es una prop disponible en cualquier componente. Su valor es el del contenido (cadena de texto, elemento o conjunto de elementos) ubicado entre las etiquetas de apertura y cierre del componente. Veamos un ejemplo:

```html
<Welcome>
  Hello world!
</Welcome>
```

La cadena de texto `Hello world!` está disponible en la prop `children` del componente `Welcome`:

```javascript
function Welcome(props) {
  return <p>{props.children}</p>;
}
```

---

Veamos ahora un ejemplo conteniendo otro componente de React:

```html
<ParentComponent title="I'm a container">
  <ChildComponent>
</ParentComponent>
```

El componente `ChildComponent` está disponible en la prop `children` del componente `ParentComponent`:

```javascript
class ParentComponent extends React.Component {
  render() {
    <div>
      <h1>{this.props.title}</h1>
      {this.props.children}
    </div>
  }
}
```
