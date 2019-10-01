# ES6

ECMAScript 6 (abreviado como ES6 o ES2015) es el estándar que sigue JavaScript desde Junio de 2015. Para utilizar funcionalidades de ES6 (o incluso superiores) en navegadores antiguos que no soportan este estándar, React utiliza [Babel](https://babeljs.io/).

## Mejoras en ES6

Veamos algunas mejoras útiles introducidas en ES6 (si querés ver todas las novedades, podes mirar [ES6 Features](http://es6-features.org)).

#### let

Podemos declarar las variables con `let` en lugar de `var` para que solo sean accesibles dentro de un entorno determinado.

```javascript
if (true) {
  var x = 'var';
  let y = 'let';
}

// Como declaramos x como var, será accessible fuera del if
console.log(x); // "var"

// Como declaramos y como let, no será accesible fuera del if
console.log(y); // Uncaught ReferenceError: y is not defined
```

#### const

Podemos declarar variables de solo lectura usando `const`, accessible únicamente dentro del entorno en que se declararon (al igual que sucede con `let`).

```javascript
const x = 'const';
let y = 'let';

// Como declaramos y como let, podemos modificar su valor
y = 'otro valor';
console.log(y); // "otro valor"

// Como declaramos x como const, al querer modificar su valor, vamos a ver un error:
// 'TypeError: Assignment to constant variable.'
x = 'otro valor';
console.log(x); // "const"
```

#### Template strings

Nos permite combinar variables con strings.

```javascript
//ES6
let es6 = "ES6";
const genial = "genial";
console.log(\`Sólo quiero decir que ${es6} es ${genial}\`);
```

#### Arrow function

Funciones que genera un `bind` automáticamente con el `this`.

```javascript
// ES5
const miFuncion = function(num) {
	return num + num;
}
// ES6
const miFuncion = (num) => num + num;
```

#### Clases

Ya vimos ejemplo de esta mejora pero es interesante saber que en la mayoría de las implementaciones actuales de JavaScript no lo soportan.

```javascript
class Ejemplo extends ClaseBase {
	constructor(atributos) {
    super(atributos);
    this.capitulos = [];
    this.precio = "";
    // ...
  }
  método() {
  	// ...
  }
}
```

#### Destructuring
Esto nos permite asignar elementos de un array o un objeto a variables

```javascript
var [a, b] = ["hola", "mundo"];
console.log(a); // "hola"
console.log(b); // "mundo"
var obj = {nombre: "Fede", apellido: "Gonzalez"};
var { nombre, apellido } = obj;
console.log(nombre); // "Fede"
var foo = function() {
	return ["175", "75"];
};
var [estatura, peso] = foo();
console.log(estatura); //175
console.log(peso); //75
```

#### Valores por defecto
Esto nos permite en las funciónes agregar un valor por defecto a los parámetros

```javascript
//ES5
function(valor) {
	valor = valor || "foo"; // Usualmente se hacia esto a pesar de que tiene problemas con null, undefined, 'undefined', 'null', etc.
}
//ES6
function(valor="foo") {...};
```

#### Spread Operator
Esto nos permite copiar un conjunto de elemento y sumarlos a un array (también existe para objetos pero en versiones mas nuevas de ES)
```javascript
const params = [ "hello", true, 7 ]
const other = [ 1, 2, ...params ] // [ 1, 2, "hello", true, 7 ]
```

#### Rest Parameter
Esto permite guardar el *resto de parámetros* en una variable

```javascript
function f(x, y, ...a) {
    return (x + y) * a.length
}
f(1, 2, "hello", true, 7) === 9
```

#### Property Shorthand
Nos permite simplificar el armado de objetos

```javascript
const x = 0, y = 0
// Antes
obj = { x: x, y: y };
// Ahora
obj = { x, y }
```

#### Export/Import
Soporte para exportar/importar valores de/a módulos sin contaminación del espacio de nombres global.

```javascript
//  lib/math.js
export function sum (x, y) { return x + y }
export var pi = 3.141593
//  someApp.js
import * as math from "lib/math"
console.log("2π = " + math.sum(math.pi, math.pi))
//  otherApp.js
import { sum, pi } from "lib/math"
console.log("2π = " + sum(pi, pi))
```

#### String Repeating
```javascript
' '.repeat(4 * depth)
'foo'.repeat(3) // 'foofoofoo'
```

#### Busquedas en listas
```javascript
[ 1, 3, 4, 2 ].find(x => x > 3) // 4
[ 1, 3, 4, 2 ].includes(3) // true
[ 1, 3, 4, 2 ].findIndex(x => x > 3) // 2
```

#### String Searching
```javascript
"hello".startsWith("ello", 1) // true
"hello".endsWith("hell", 4)   // true
"hello".includes("ell")       // true
"hello".includes("ell", 1)    // true
"hello".includes("ell", 2)    // false
```

#### Number Type Checking
```javascript
Number.isNaN(42) === false
Number.isNaN(NaN) === true
Number.isFinite(Infinity) === false
Number.isFinite(-Infinity) === false
Number.isFinite(NaN) === false
Number.isFinite(123) === true
```

#### Promise
Representación de primera clase de un valor que puede realizarse de forma asíncrona y estar disponible en el futuro.

```javascript
function msgAfterTimeout(msg, who, timeout) {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(`${msg} Hello ${who}!`), timeout)
  })
}
msgAfterTimeout("", "Foo", 100).then((msg) =>
  msgAfterTimeout(msg, "Bar", 200)
).then((msg) => {
  console.log(`done after 300ms:${msg}`)
})
```
