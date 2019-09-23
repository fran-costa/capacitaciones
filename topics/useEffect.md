# Hook de efecto

Las operaciones tales como pedido de datos al backend, suscripción a eventos o modificación manual del DOM desde componentes de React son conocidas como **side effect**, o simplemente **effects**, ya que pueden afectar a otros componentes y no se pueden hacer durante el renderizado.

El hook de efecto, `useEffect`, agrega la capacidad de realizar **side effects** desde un componente funcional. Tiene el mismo propósito que `componentDidMount`, `componentDidUpdate` y `componentWillUnmount` en las clases React, pero unificadas en una sola API.

Por ejemplo, este componente establece el título del documento después de que React actualiza el DOM:

```javascript
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // Actualiza el título del documento usando la Browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

Al llamar a `useEffect`, se le está indicando a React que ejecute la función definida en el _effect_ luego de cada renderizado. Como los _effects_ se declaran dentro del componente, se tiene acceso a sus props y a sus estados.

Los _effects_ puede también especificar una función de _clean up_ que se utiliza para "limpiar" los efectos del mismo. Para definir una función de _clean up_, basta simplemente con retornar la función deseada como resultado del _effect_ y React hará el resto. Veamos el siguiente ejemplo de un componente que utiliza un _effect_ para suscribirse al estado (online/offline) de un contacto y una función de _clean up_ para anular la suscripción:

```javascript
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  const handleStatusChange = (status) => setIsOnline(status.isOnline);

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);

    return () => ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  });

  if (isOnline === null) return 'Loading...';

  return isOnline ? 'Online' : 'Offline';
}
```

En este ejemplo, React cancelará la suscripción de nuestra `ChatAPI` cuando se desmonte el componente, así como antes de volver a ejecutar el _effect_ debido a un renderizado posterior.

Al igual que con `useState`, es posible usar tanto _effects_ como sean necesarios.

> **Importante:** Resulta interesante remarcar del ejemplo anterior que la suscripción a `ChatAPI` y su cancelación queda totalmente contenida en el _effect_, agrupando la toda la lógica relacionada a este proceso en este hook.
>
> Por el contrario, si quisiéramos hacer esto utilizando componentes de clase, tendríamos que hacer la suscripción dentro del método `componentDidMount` y cancelar la suscripción dentro del método `componentWillUnmount`. Estaríamos entonces separando la lógica por el momento en que es ejecutada y no por el "concepto que implicada". En más, podría implicar que la misma sea ejecutada con otra serie de llamados sin ninguna relación (otras suscripciones, modificaciones manuales del DOM, etc).
>
> **"Los hooks permiten organizar _effects_ en un componente según qué partes están relacionadas (como agregar y eliminar una suscripción), en lugar de forzar una división basada en métodos del ciclo de vida."**

## Algunos detalles sobre useEffect

- La función que se le pasa a `useEffect` es distinta en cada renderizado. Esto es intencionado y es lo que nos permite leer las props y el estado del componente sin preocuparnos de que sus valores estén obsoleto. Cada vez que renderizamos, se ejecuta un _effect_ diferente, reemplazando el anterior. En cierta manera, esto hace que los _effects_ funcionen más como parte del resultado del renderizado. Cada _effect_ pertenece a su correspondiente renderizado.

<!-- TODO: Add link to useLayoutEffect -->
- A diferencia de los métodos de ciclo de vida `componentDidMount` o `componentDidUpdate`, `useEffect` no bloquean la actualización de la pantalla del navegador. Esto hace que una aplicación responda mejor. Si bien la mayoría de _effects_ no necesitan suceder de manera síncrona y pueden aprovechar esto, en los casos en los que se necesita una ejecución síncrona (como en mediciones de la disposición de elementos), podemos usar el hook `useLayoutEffect` con una API idéntica a la de `useEffect`.

- React ejecuta la función de _clean up_ de un _effect_ cuando el componente se desmonta. Sin embargo, dado que los _effects_ se ejecutan en cada renderizado, React ejecuta la función de _clean up_ del _effect_ anterior antes de ejecutar el _effect_ actual.

## ¿Por qué los efectos se ejecutan en cada actualización?

Al inicio de esta sección, presentamos un componente `FriendStatus` que muestra si un amigo está conectado o no. Si en lugar de usar hooks usáramos clases, deberíamos usar los métodos de ciclo de vida `componentDidMount` y `componentWillUnmount` como sigue:

```javascript
class FriendStatus extends React.Component {
  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(this.props.friend.id, this.handleStatusChange);
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
  }
}
```

Ahora bien, ¿qué sucede si la prop `friend` cambia mientras el componente está montado? Nuestro componente continuaría mostrando el estado de un amigo diferente. Esto es un error. Además podríamos causar un memory leak o un fallo crítico al desmontar dado que la llamada que cancela la suscripción usaría un identificador erróneo. Necesitaríamos entonces añadir `componentDidUpdate` para manejar este caso:

```javascript
  componentDidUpdate(prevProps) {
    // Cancela la suscripción del friend.id anterior
    ChatAPI.unsubscribeFromFriendStatus(prevProps.friend.id, this.handleStatusChange);

    // Se suscribe al siguiente friend.id
    ChatAPI.subscribeToFriendStatus(this.props.friend.id, this.handleStatusChange);
  }
```

No gestionar `componentDidUpdate` correctamente es una fuente de errores común en las aplicaciones React.

Ahora consideremos la versión de este componente que usa Hhoks:

```javascript
function FriendStatus(props) {
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  });
```

¡No padece el mismo error y no hemos hecho ningún cambio! 😎

No hay un código especial para gestionar las actualizaciones porque `useEffect` las gestiona por defecto, ya que se hace el _clean up_ de los _effects_ anteriores antes de aplicar los nuevos. Este comportamiento asegura la consistencia por defecto y previene errores que son comunes en los componentes de clase debido a la falta de lógica de actualización.

## Tip: Usa varios effects para separar conceptos

Si bien ya mencionamos esto anteriormente, es importante volver a remarcarlo: Uno de los problemas que hooks vino a solucionar, es el hecho de que los métodos del ciclo de vida de un componente de clase suelen contener lógica que no está relacionada, pero la que lo esta se fragmenta en varios métodos.

Este es un componente que combina la lógica del contador y el indicador de estado de un contacto de los ejemplos anteriores:

```javascript
class FriendStatusWithCounter extends React.Component {
  this.state = { count: 0, isOnline: null };

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;

    ChatAPI.subscribeToFriendStatus(this.props.friend.id, this._handleStatusChange);
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(this.props.friend.id, this._handleStatusChange);
  }

  _handleStatusChange = ({ isOnline }) => this.setState({ isOnline });
```

La lógica que asigna `document.title` se divide entre `componentDidMount` y `componentDidUpdate`. Por su parte, la lógica de la suscripción también se reparte entre `componentDidMount` y `componentWillUnmount`. Y `componentDidMount` contiene código de ambas tareas.

Con hooks, podríamos separar la lógica de suscripción y la de modificación del título usando dos _effects_ distintos:

```javascript
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => document.title = `You clicked ${count} times`);

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    const handleStatusChange = ({ isOnline }) => setIsOnline(status.isOnline);

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  });
}
```

> **Nota:** React aplicará cada _effect_ de un componente en el orden en han sido especificados. Apoyándonos en el ejemplo anterior, por ejemplo, el título será modificado primero y luego se creará la suscripción a `ChatAPI`.

## Tip: Omite effects para optimizar el rendimiento

En algunos casos, aplicar el effect después de cada renderizado puede crear problemas de rendimiento. En los componentes de clase podemos solucionar esto escribiendo una comparación extra con `prevProps` o `prevState` dentro de `componentDidUpdate`:

```javascript
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) document.title = `You clicked ${this.state.count} times`;
}
```

Este requerimiento es tan común que está incorporado en la API de `useEffect`. Puedes indicarle a React que omita aplicar un _effect_ si ciertos valores no han cambiado entre renderizados. Para hacerlo, pasa un array como segundo argumento opcional a `useEffect`:

```javascript
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // Solo se vuelve a ejecutar si count cambia
```

En el ejemplo anterior pasamos `[count]` como segundo argumento, lo que implica que cuando nuestro componente se vuelve a renderizar, React comparará el valor de `count` del renderizado anterior con el del siguiente renderizado. Si `countAnterior === countSiguiente`, React omitirá el _effect_. Si el array contiene varios elementos, React volverá a ejecutar el efecto si cualquiera de los elementos es diferente.

Esto también funciona para _effects_ con función de _clean up_:

```javascript
useEffect(() => {
  const handleStatusChange = (status) setIsOnline(status.isOnline);

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  return () => ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
}, [props.friend.id]); // Solo se vuelve a suscribir si la propiedad props.friend.id cambia
```

> **Nota:** Es importante asegurarse de incluir todos los valores del ámbito del componente (como props y estados) que cambien a lo largo del tiempo y que sean usado por el _effect_. De otra forma, el código referenciará valores obsoletos de renderizados anteriores.

> Si quieres ejecutar un _effect_ y solamente una vez (al montar y desmontar el componente), puedes pasar un array vacío (`[]`) como segundo argumento. Esto le indica a React que el _effect_ no depende de ningún valor proveniente de las props o el estado, de modo que no necesita volver a ejecutarse. Esto no se gestiona como un caso especial, obedece directamente al modo en el que siempre funciona el array de dependencias.
