# Hook de estado

El siguiente ejemplo renderiza un contador que se incrementa al apretar un botón. El mismo, está escrito usando hooks:

```javascript
import React, { useState } from 'react';

function Example() {
  // Declara una nueva variable de estado, que llamaremos "count".
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

`useState` es un hook que nos permite agregar un estado local a un componente funcional. React mantendrá este estado entre re-renderizados. Del ejemplo anterior, se pueden observar que `useState`:
- devuelve un array con dos elementos: el valor del estado actual y una función que nos permite actualizarlo. Esta última, es similar al método `setState` de un componente de clase excepto que no combina el estado anterior y el siguiente.
- espera un único argumento: el estado inicial, el cual se utiliza solo durante el primer renderizado. En el ejemplo anterior, es `0`. Es importante notar que a diferencia del atributo de clase `this.state`, el estado aquí no tiene que ser necesariamente un objeto.

## Declarando múltiples variables de estado

Es posible usar múltiples hooks de estado en un mismo componente:

```javascript
function ExampleWithManyStates() {
  // Declarar múltiple variables de estado!
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```

## ¿Qué hace la llamada a useState?

Básicamente, declara una “variable de estado”. En el ejemplo anterior, la variable se llama `count`. Esta es una forma de preservar algunos valores entre las llamadas de la función. `useState` es una nueva forma de usar exactamente las mismas funciones que `this.state` nos da en una clase. Normalmente, las variables "desaparecen” cuando se sale de la función, pero las variables de estado son conservadas por React. Esto quiere decir que React recordará su valor actual entre re-renderizados y devolverá el valor más reciente a nuestra función. Si se quiere actualizar el valor de `count` actual, se puede llamar a `setCount`.

## Leyendo el estado

Cuando queremos mostrar el valor actual de `count` en una clase lo obtenemos de `this.state.count`:

```javascript
  <p>You clicked {this.state.count} times</p>
```

En una función podemos usar `count` directamente:

```javascript
  <p>You clicked {count} times</p>
```

## Actualizando el estado

En una clase, necesitamos llamar a `this.setState()` para actualizar el estado `count`:

```javascript
  <button onClick={() => this.setState({ count: this.state.count + 1 })}>
    Click me
  </button>
```

En una función ya tenemos `setCount` y `count` como variables, así que no necesitamos `this`:

```javascript
  <button onClick={() => setCount(count + 1)}>
    Click me
  </button>
```

## Tip: ¿Qué significan los corchetes?

Probablemente te habrá llamado la atención los corchetes utilizados al invocar `useState`:

```javascript
  const [count, setCount] = useState(0);
```

Los nombres `count` y `setCount` no son parte de la API de React, de hecho, puede usarse cualquier nombre para estos valores. En realidad, esto es simplemente un tipo de asignación muy utilizada en JavaScript llamada [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) que suele utilizarse tanto para arrays (utilizando `[]`) como para objetos (utilizando `{}`). Básicamente, significa que se crean dos variables `count` y `setCount`, cuyos valores son el primer y el segundo elemento del array, respectivamente. Esto es equivalente a:

```javascript
  const counterStateVariable = useState(0); // Returns an array with two elements
  const count = counterStateVariable[0];
  const setCount = counterStateVariable[1];
```
