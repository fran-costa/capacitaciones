# Higher Order Components

Un componente de orden superior (HOC) es una técnica de reutilización de lógica entre componentes de clase que surge de la naturaleza composicional de React.

En concreto, un HOC es una función que recibe un componente y devuelve un nuevo componente.

```javascript
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

Mientras que un componente transforma props en interfaz de usuario, un HOC transforma un componente en otro.

## Reutilización de lógica mediante HOCs

Los componentes son la unidad primaria de reutilización de código en React. Sin embargo, algunos patrones no se ajustan directamente a componentes tradicionales.

Por ejemplo, supongamos que tenemos un componente `CommentList` que se suscribe a una fuente de datos externa para renderizar una lista de comentarios:

```javascript
class CommentList extends React.Component {
  state = {
    // "DataSource" es alguna fuente de datos global
    comments: DataSource.getComments()
  };

  componentDidMount() {
    // Suscribirse a los cambios
    DataSource.addChangeListener(this._handleChange);
  }

  componentWillUnmount() {
    // Eliminar el manejador de eventos de cambios
    DataSource.removeChangeListener(this._handleChange);
  }

  // Actualizar el estado del componente cuando la fuente de datos cambie
  _handleChange = () => this.setState({ comments: DataSource.getComments() });

  render() {
    return (
      <div>
        {this.state.comments.map((comment) => (
          <Comment comment={comment} key={comment.id} />
        ))}
      </div>
    );
  }
}
```

Supongamos ahora que se necesita un nuevo componente que se subscribe a un único post de un blog con un patrón similar:

```javascript
class BlogPost extends React.Component {
  state = {
    blogPost: DataSource.getBlogPost(props.id)
  };

  componentDidMount() {
    DataSource.addChangeListener(this._handleChange);
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this._handleChange);
  }

  _handleChange = () => this.setState({ blogPost: DataSource.getBlogPost(this.props.id) });

  render() {
    return <TextBlock text={this.state.blogPost} />;
  }
}
```

`CommentList` y `BlogPost` no son idénticos, ambos llaman a métodos distintos en `DataSource`, y renderizan un elemento de React diferente. Pero gran parte de su implementación es la misma:
- Al montar, añadir un manejador de eventos de cambio al `DataSource`.
- En el manejador de eventos, llamar `setState` cada vez que la fuente de datos cambie.
- Al desmontar, eliminar el manejador de eventos de cambio.

Si este mismo patrón (u otro) se repitiése a lo largo de una aplicación, sería interesante tener una abstracción que nos permita definir la lógica en un solo lugar y compartirla a través de múltiples componentes. Aquí entran en juego los HOCs.

Podemos crear una función que cree componentes, como `CommentList` y `BlogPost`, que se subscriben a `DataSource`. La función aceptará como uno de sus argumentos un componente hijo que recibirá los datos suscritos como un prop. Llamemos esta función `withSubscription`:

```javascript
const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
);
```

El primer parámetro es el componente al que se le debe agregar la lógica. El segundo parámetro obtiene los datos en los que estamos interesados dado un `DataSource` y los props actuales.

Cuando `CommentListWithSubscription` y `BlogPostWithSubscription` son renderizados, `CommentList` y `BlogPost` recibirán un prop `data` con los datos más actualizados recibidos de `DataSource`:

```javascript
// Esta función recibe un componente...
function withSubscription(WrappedComponent, selectData) {
  // ...y devuelve otro componente...
  return class extends React.Component {
    this.state = {
      data: selectData(DataSource, props)
    };

    componentDidMount() {
      // ... que se encarga de la suscripción...
      DataSource.addChangeListener(this._handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this._handleChange);
    }

    _handleChange = () => this.setState({ data: selectData(DataSource, this.props) });

    render() {
      // ... y renderiza el componente envuelto con los datos frescos!
      // Toma nota de que pasamos cualquier prop adicional
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}
```

Nótese que un HOC no modifica el componente de entrada, ni usa herencia para copiar su comportamiento. En su lugar, un HOC compone el componente original al envolverlo en un componente contenedor. Un HOC es una función pura sin efectos colaterales.

Vemos a su vez que el componente wrappeado recibe todos los props del contenedor, junto con un nuevo prop, `data`, el cual es usado para renderizar su salida. Al HOC no le importa cómo o porqué los datos son usados, y al componente envuelto no le importa de dónde proceden los datos.

Debido a que `withSubscription` es una función normal, puedes añadir tantos, o tan pocos, argumentos como desees. Por ejemplo, se puede hacer configurable el nombre del prop `data` para aislar aún más el HOC del componente wrappeado o aceptar un argumento que configure `shouldComponentUpdate`, o alguno que configure la fuente de datos. Todo esto es posible porque el HOC tiene el control total sobre como se define el componente.

Tal como los componentes, el contrato entre `withSubscription` y el componente wrappeado está basado completamente en los props. Esto hace fácil intercambiar un HOC por otro, siempre y cuando provean los mismos props al componente envuelto. Esto puede ser útil si cambias de bibliotecas de obtención de datos, por ejemplo.

## Convenciones

### Pasa los Props no relacionados al componente wrappeado

Los HOCs añaden funcionalidad a un componente. Se espera que el componente wrappeado por un HOC tenga una interfaz similar al componente wrappeado en sí mismo.

Los HOCs deben pasar directamente los props que no tengan relación con su accionar específico. La mayoría de los HOCs contienen un método render que se ve algo como esto:

```javascript
render() {
  // Filtra los props extras que son específicos de este HOC y que no deben
  // ser pasados
  const { extraProp, ...passThroughProps } = this.props;

  // Inyecta los props en el componente envuelto. Estos son usualmente valores
  // de estado o métodos de instancia
  const injectedProp = someStateOrInstanceMethod;

  // Pasa los props al componente envuelto
  return <WrappedComponent injectedProp={injectedProp} {...passThroughProps} />;
}
```

Esta convención ayuda a asegurar que los HOCs sean tan flexibles y reutilizables como sea posible.

### Maximizar la componibilidad

No todos los HOCs se ven igual. Algunas veces aceptan tan solo un argumento, el componente envuelto:

```javascript
const NavbarWithRouter = withRouter(Navbar);
```

Usualmente aceptan argumentos adicionales. En este ejemplo de Relay un objeto de configuración es usado para especificar las dependencias de datos de un componente:

```javascript
const CommentWithRelay = Relay.createContainer(Comment, config);
```

La firma más común para los HOCs es la siguiente:

```javascript
// `connect` en react-redux
const ConnectedComment = connect(commentSelector, commentActions)(CommentList);
```

Que si la dividimos podemos entenderla mejor:

```javascript
// connect es una función que retorna otra función
const enhance = connect(commentListSelector, commentListActions);
// La función retornada es un HOC, el cual retorna un componente conectado
// al `store` de Redux
const ConnectedComment = enhance(CommentList);
```

Podemos ver que `connect` es una función de orden superior (HOF) que devuelve un componente de orden superior.

Esta forma puede parecer confusa o innecesaria, pero tiene una propiedad útil. Los HOCs de un solo argumento, como los que devuelve la función connect tienen la firma `Component => Component`. Las funciones cuyo tipo de salida es el mismo que el de entrada son muy fáciles de componer.

### Use displayName para una depuración más fácil

Los componentes contenedores creados por los HOCs se muestran en las Herramientas de Desarrollo de React como cualquier otro componente. Para facilitar la depuración elige que se muestre un nombre que comunique que es el resultado de un HOC.

La técnica más común consiste en "wrappear" el `displayName` del componente wrappeado. De esta forma si un HOC se llama `withSubscription`, y el `displayName` del componente wrappeado es `CommentList`, el `displayName` será `WithSubscription(CommentList)`:

```javascript
function withSubscription(WrappedComponent) {
  class WithSubscription extends React.Component {/* ... */}
  WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
  return WithSubscription;
}

function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component';
}
```

## Consideraciones

### No uses HOCs dentro del método render

El algoritmo de detección de diferencias de React utiliza la identidad del componente para determinar si debe actualizar el subárbol existente o desecharlo y montar uno nuevo. Si el componente devuelto por render es idéntico (===) al componente de la llamada a render previa, React actualiza el subárbol calculando las diferencias con el nuevo. Si no son iguales, el subárbol anterior es desmontado completamente.

Normalmente no es necesario pensar acerca de esto. Pero importa para los HOCs porque significa que no puedes aplicar un HOC a un componente dentro del método render de otro componente:

```javascript
render() {
  // Una nueva versión de EnhancedComponent es creada en cada render
  // EnhancedComponent1 !== EnhancedComponent2
  const EnhancedComponent = enhance(MyComponent);
  // Esto causa que el subárbol entero se desmonte/monte cada vez!
  return <EnhancedComponent />;
}
```

El problema aquí mostrado no es tan solo acerca del rendimiento, desmontar un componente causa que el estado de ese componente y de todos sus hijos se pierda.

En su lugar, aplica los HOCs por fuera de la definición del componente de manera que el componente resultante se creado solo una vez. De esta forma su identidad será consistente entre llamadas a render. Esto, de todas formas, es lo que usualmente se desea.

En aquellos casos extraños donde necesites aplicar un HOC de forma dinámica, también puedes hacerlo en los métodos del ciclo de vida, o en su constructor.

### Los métodos estáticos deben ser copiados

Cuando aplicas un HOC a un componente con métodos o atributos estáticos, estos no "pasan" al componente final y por lo tanto, es neesario copiarlos. Podemos copiarlos manualmente o haciendo uso de `hoistNonReactStatic` del paquete `hoist-non-react-statics`.
