# Context

Context provee una forma de pasar datos a través del árbol de componentes sin tener que pasar props manualmente en cada nivel.

En una aplicación típica de React, los datos se pasan de arriba hacia abajo (de padre a hijo) a través de props, pero esto puede ser complicado para ciertos tipos de props (por ejemplo, localización, el tema de la interfaz) que son necesarios para muchos componentes dentro de una aplicación. Context proporciona una forma de compartir valores como estos (que podemos considerar "globales") entre componentes sin tener que pasar explícitamente props a través de cada nivel del árbol.

Veamos el siguiente ejemplo, donde pasamos manualmente un prop de `theme` para darle estilo al componente `Button`:

```javascript
class App extends React.Component {
  render() {
    return <Toolbar theme="dark" />;
  }
}

function Toolbar(props) {
  // El componente Toolbar debe tener un prop adicional "theme"
  // y pasarlo a ThemedButton. Esto puede llegar a ser trabajoso
  // si cada botón en la aplicación necesita saber el tema,
  // porque tendría que pasar a través de todos los componentes.
  return (
    <div>
      <ThemedButton theme={props.theme} />
    </div>
  );
}

class ThemedButton extends React.Component {
  render() {
    return <Button theme={this.props.theme} />;
  }
}
```

Usando `Context` podemos evitar pasar props a través de elementos intermedios:

```javascript
// Context nos permite pasar un valor a lo profundo del árbol de componentes
// sin pasarlo explícitamente a través de cada componente.
// Crear un Context para el tema actual (con "light" como valor predeterminado).
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
    // Usa un Provider para pasar el tema actual al árbol de abajo.
    // Cualquier componente puede leerlo, sin importar qué tan profundo se encuentre.
    // En este ejemplo, estamos pasando "dark" como valor actual.
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// Un componente en el medio no tiene que
// pasar el tema hacia abajo explícitamente.
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // Asigna un contextType para leer el contexto del tema actual.
  // React encontrará el Provider superior más cercano y usará su valor.
  // En este ejemplo, el tema actual es "dark".
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}
```

## API de Context

#### React.createContext

```javascript
const MyContext = React.createContext(defaultValue);
```

Crea un objeto Context. Cuando React renderiza un componente que se suscribe a este objeto Context, leerá el valor de contexto actual del `Provider` más cercano en el árbol.

El argumento `defaultValue` es usado únicamente cuando un componente no tiene un `Provider` superior a él en el árbol. Esto puede ser útil para probar componentes de forma aislada sin contenerlos.

> **Nota:** pasar `undefined` como valor al `Provider` no hace que los componentes que lo consumen utilicen `defaultValue`.

#### Context.Provider

```javascript
<MyContext.Provider value={/* algún valor */}>
```

Cada objeto Context viene con un componente `Provider` de React que permite que los componentes que lo consumen se suscriban a los cambios del contexto.

Acepta un prop `value` que se pasará a los componentes consumidores que son descendientes de este `Provider`. Un `Provider` puede estar conectado a muchos consumidores. Los `Provider`s pueden estar anidados para sobreescribir los valores más profundos dentro del árbol.

Todos los consumidores que son descendientes de un `Provider` se vuelven a renderizar cada vez que cambia el prop `value` de dicho `Provider`. La propagación del `Provider` a sus consumidores descendientes no está sujeta al método `shouldComponentUpdate`, por lo que el consumidor se actualiza incluso cuando un componente padre evita la actualización.

#### Class.contextType

```javascript
class MyClass extends React.Component {
  static contextType = MyContext;

  componentDidMount() {
    let value = this.context;
    /* realiza un efecto secundario en el montaje utilizando el valor de MyContext */
  }

  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }

  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }

  render() {
    let value = this.context;
    /* renderiza algo basado en el valor de MyContext */
  }
}
```

A la propiedad especial `contextType` en un componente de clase se le puede asignar un objeto Context creado por `React.createContext()`. Esto permite consumir el valor actual más cercano de ese Context utilizando `this.context`, pudiendo hacer referencia a esto en cualquiera de los métodos del ciclo de vida, incluso en el `render`.

#### Context.Consumer

```javascript
<MyContext.Consumer>
  {value => /* renderiza algo basado en el valor de contexto */}
</MyContext.Consumer>
```

Un componente de React que se suscribe a cambios de contexto. Permite suscribirse a un contexto dentro de un componente de función.

Utiliza el patrón de reutilización de código de [Render Props](topics/Render_Props.md). La función recibe el valor de contexto actual y devuelve un nodo de React. El argumento `value` pasado a la función será igual a la prop `value` del `Provider` más cercano para este contexto en el árbol. Si no hay un `Provider` superior para este contexto, el argumento `value` será igual al `defaultValue` que se pasó a `createContext()`.

#### Context.displayName

El objeto Context acepta una propiedad de cadena de texto `displayName`. Las herramientas de desarrollo de React utilizan esta cadena de texto para determinar que mostrar para el Context.

Por ejemplo, el componente a continuación aparecerá como "MiContexto" en las herramientas de desarrollo:

```javascript
const MyContext = React.createContext(/* some value */);
MyContext.displayName = 'MiContexto';

<MyContext.Provider> // "MiContexto.Provider" en las herramientas de desarrollo
<MyContext.Consumer> // "MiContexto.Consumer" en las herramientas de desarrollo
```

## Ejemplos

#### Context dinámico

Un ejemplo más complejo con valores dinámicos para el tema:

```javascript
const themes = {
  light: {
    foreground: '#000000',
    background: '#eeeeee',
  },
  dark: {
    foreground: '#ffffff',
    background: '#222222',
  },
};

const ThemeContext = React.createContext(
  themes.dark // valor por defecto
);

class ThemedButton extends React.Component {
  static contextType = ThemeContext;

  render() {
    let props = this.props;
    let theme = this.context;

    return (
      <button style={{backgroundColor: theme.background}} {...props} />
    );
  }
}

// Un componente intermedio que utiliza ThemedButton.
function Toolbar(props) {
  return (
    <ThemedButton onClick={props.changeTheme}>
      Change Theme
    </ThemedButton>
  );
}

class App extends React.Component {
  state = { theme: themes.light };

  toggleTheme = () => this.setState(state => ({
    theme:
      state.theme === themes.dark
        ? themes.light
        : themes.dark,
  }));

  render() {
    // El botón ThemedButton dentro de ThemeProvider
    // usa el tema del estado mientras que el exterior usa
    // el tema oscuro predeterminado
    return (
      <Page>
        <ThemeContext.Provider value={this.state.theme}>
          <Toolbar changeTheme={this.toggleTheme} />
        </ThemeContext.Provider>
        <Section>
          <ThemedButton />
        </Section>
      </Page>
    );
  }
}

ReactDOM.render(<App />, document.root);
```

#### Actualizando Context desde un componente anidado

A menudo es necesario actualizar el contexto desde un componente que está anidado en algún lugar del árbol de componentes. En este caso, puedes pasar una función a través del contexto para permitir a los consumidores actualizar el contexto:

```javascript
// Make sure the shape of the default value passed to
// createContext matches the shape that the consumers expect!
const ThemeContext = React.createContext({
  theme: themes.dark,
  toggleTheme: () => {},
});

function ThemeTogglerButton() {
  // The Theme Toggler Button receives not only the theme
  // but also a toggleTheme function from the context
  return (
    <ThemeContext.Consumer>
      {({theme, toggleTheme}) => (
        <button
          onClick={toggleTheme}
          style={{backgroundColor: theme.background}}>
          Toggle Theme
        </button>
      )}
    </ThemeContext.Consumer>
  );
}

class App extends React.Component {
  // State also contains the updater function so it will
  // be passed down into the context provider
  state = {
    theme: themes.light,
    toggleTheme: this.toggleTheme,
  };

  toggleTheme = () => this.setState(state => ({
    theme:
      state.theme === themes.dark
        ? themes.light
        : themes.dark,
  }));

  render() {
    // The entire state is passed to the provider
    return (
      <ThemeContext.Provider value={this.state}>
        <Content />
      </ThemeContext.Provider>
    );
  }
}

function Content() {
  return (
    <div>
      <ThemeTogglerButton />
    </div>
  );
}

ReactDOM.render(<App />, document.root);
```

#### Consumir múltiples Contexts

Para mantener el renderizado de Context rápido, React necesita hacer que cada consumidor de contexto sea un nodo separado en el árbol.

```javascript
// Theme context, default to light theme
const ThemeContext = React.createContext('light');

// Contexto de usuario registrado
const UserContext = React.createContext({
  name: 'Guest',
});

class App extends React.Component {
  render() {
    const {signedInUser, theme} = this.props;

    // Componente App que proporciona valores de contexto iniciales
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
  }
}

function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  );
}

// Un componente puede consumir múltiples contextos.
function Content() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}
```

Si dos o más valores de contexto se usan a menudo juntos, es posible que desees considerar la creación de tu propio componente de procesamiento que proporcione ambos.

## Aclaración importante sobre performance

Debido a que Context usa la identidad por referencia para determinar cuándo se debe volver a renderizar, hay algunos errores que podrían provocar renderizados involuntarios en los consumidores cuando se vuelve a renderizar en el padre del proveedor. Por ejemplo, el código a continuación volverá a renderizar a todos los consumidores cada vez que el `Provider` se vuelva a renderizar porque siempre se crea un nuevo objeto para value:

```javascript
class App extends React.Component {
  render() {
    return (
      <Provider value={{something: 'something'}}>
        <Toolbar />
      </Provider>
    );
  }
}
```

Para evitar esto, se puede mover el valor del `Provider` al estado del componente padre:

```javascript
class App extends React.Component {
  state = { value: {something: 'something'} };

  render() {
    return (
      <Provider value={this.state.value}>
        <Toolbar />
      </Provider>
    );
  }
}
```

## Context y Hooks

A partir de React 16.8 y la incorporación de la API de Hooks, se incorporó el Hook [`useContext`](topics/API_Hooks?id=usecontext), el cual permite una interacción más sencilla con la API de Context reemplazando al `contextType` o al `Consumer` en un componente de clase.
