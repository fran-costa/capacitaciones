# Métodos de ciclo de vida de un componente

Cada componente tiene varios **métodos de ciclo de vida** que se utilizan para ejecutar código en momentos particulares del proceso. A continuación, veremos algunos de los métodos de ciclo de vida más utilizados.

![Ciclos](./Ciclos.png)

## Montaje

Estos métodos se llaman cuando se crea una instancia de un componente y se inserta en el DOM:

- **constructor()** - Se invoca antes de que el componente sea montado y se utiliza para inicializar el state (`this.state = { ... }`) y/o enlazar (bindear) métodos para el manejo de eventos (`this._myEventHandler = this._myEventHandler.bind(this)`).

- **static getDerivedStateFromProps()** - Se invoca justo antes de llamar al método de `render`y devuelve un objeto para actualizar el state o `null` para no actualizar nada. Se usa para actualizar el `state` cuando hubo cambios en las `props`. Este método no tiene acceso a la instancia del componente.

- **render()** - Es el único método requerido en un componente de clase, se ejecuta cada vez que es necesario actualizar la UI y devuelve un elemento de React (típicamente creado con JSX: `<div />` o `<MyComponent />`) o varios de ellos (usando Fragments), Strings, Booleans o `null` (estos dos últimos no renderizan nada y se usan a los fines de ejecutar cierta lógica). Este método debe ser una función pura.

- **componentDidMount()** - Se invoca inmediatamente después de que un componente es montado (y por única vez) y se utiliza para para establecer cualquier suscripción, pedir datos al backend, etc.

- **UNSAFE_componentWillMount()**

> El método **UNSAFE_componentWillMount()** está deprecado y su uso debe evitarse.

## Actualización

Una actualización puede ser causada por cambios en los props o el state. Estos métodos se llaman en el siguiente orden cuando un componente se vuelve a renderizar:

- **static getDerivedStateFromProps()**

- **shouldComponentUpdate()** - Se invoca previo al renderizado y se utiliza para determinar si el método `render` debe ser ejecutado o no en función de las nuevas `props` y el nuevo `state`.

- **render()**

- **getSnapshotBeforeUpdate()** - Se invoca justo antes de que la salida renderizada más reciente se entregue, por ejemplo, al DOM. Permite al componente capturar cierta información del DOM (por ejemplo, la posición del scroll) antes de que se cambie potencialmente. Cualquier valor que se devuelva en este ciclo de vida se pasará como parámetro al método `componentDidUpdate()`.

- **componentDidUpdate()** - Se invoca inmediatamente después de que el componente fue actualizado y se utiliza para acceder al DOM cuando el componente se haya actualizado, hacer solicitudes de red siempre, etc.

- **UNSAFE_componentWillUpdate()**

- **UNSAFE_componentWillReceiveProps()**

> Los métodos **UNSAFE_componentWillUpdate()** y **UNSAFE_componentWillReceiveProps()** están deprecados y su uso debe evitarse.

## Desmontaje

Este método es llamado cuando un componente se elimina del DOM:

- **componentWillUnmount()** - Se invoca inmediatamente antes de desmontar y destruir un componente y se usa para realizar las tareas de limpieza necesarias como la invalidación de timers, cancelación de solicitudes y/o suscripciones, etc.

## Manejo de errores

Estos métodos se invocan cuando hay un error durante la renderización, en un método en el ciclo de vida o en el constructor de cualquier componente hijo.

- **static getDerivedStateFromError()**
- **componentDidCatch()**

<!-- TODO: Add link to Error Boundaries -->
Veremos estos métodos en detalle más adelante.


## Tip: Uso de PureComponents

React nos ofrece la posibilidad de crear componentes de clase heredando de `React.Component` como así también, de `React.PureComponent`. En esencia, un `PureComponent` es un `Component` que cuenta con una implementación del método `shouldComponentUpdate()` que realiza una comparación superficial de props y state de forma tal de reducir la posibilidad de ejecutar actualización innecesarias de un componente.
