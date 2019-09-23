# Hooks

Hooks son una característica agregada a React en su versión 16.8. Básicamente, permiten usar el state y otras características de React sin la necesidad de crear componentes de clase.

```javascript
import React, { useState } from 'react';

function Example() {
  // Declara una nueva variable de estado, la cual llamaremos “count”
  const [count, setCount] = useState(0);

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

`useState` es un **hook**. En las siguientes secciones del curso, veremos más en detalle éste y otros hooks.

> **Nota:** Los hooks son una forma de escribir componentes, los cuales pueden convivir con componentes de clase.

## Motivación

Los Hooks fueron incorporados para resolver algunos problemas que se presentaban al escribir componentes en React:

- **Es difícil reutilizar la lógica de un state entre componentes**
- **Los componentes complejos se vuelven difíciles de entender**
- **Trabajar con clases presenta múltiples problemas en JavaScript**

Si bien el cambio a hooks requiere un cambio de mentalidad respecto al uso de clases, nos permite crear componentes más legibles, con una mejor separación de la lógica (que a su vez, es más reutilizable) y con mejoras sustanciales en la performance de los mismos.

## Pero... ¿Qué es un Hook?

Los hooks son funciones que permiten “enganchar” el estado de React y el ciclo de vida desde componentes funcionales. Los hooks no funcionan dentro de clases, sino que permiten usar React sin clases.

React proporciona algunos Hooks incorporados como `useState`, pero también es posible crear hooks propios para reutilizar el comportamiento con estado entre diferentes componentes.
