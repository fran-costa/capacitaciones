# JSX

JSX es una extensión de la sintaxis de JavaScript, recomendada para utilizar con React para describir cómo debería ser la interfaz de usuario. Se sintaxis es muy similar a la sintaxis HTML, pero con todas las virtudes de JavaScript.

```javascript
// Ejemplo de sintaxis JSX
const element = <h1>Hello, world!</h1>;
```

JSX produce “elementos” de React que pueden ser renderizados en el DOM con la ayuda de React.

## ¿Por qué JSX?

React acepta el hecho de que la lógica de renderizado está intrínsecamente unida a la lógica de la interfaz de usuario: cómo se manejan los eventos, cómo cambia el estado de un elemento con el tiempo, cómo se preparan los datos para su visualización, etc.

En lugar de separar los aspectos visuales de la interfaz de usuario (HTML + CSS) en un archivo y la lógica (JavaScript) por otro, React utiliza unidades llamadas “componentes” que contienen ambos aspectos.

> El uso de JSX no es obligatorio, pero es la forma más utilizada y cómoda de trabajar en React.

## JavaScript en JSX

Dentro de una expresión JSX, puede insertarse una expresión JavaScript envolviéndola en llaves `{}`.

```javascript
const user = {
  name: 'Juan Pérez'
}

const element = (
  <h1>
    Hello, {user.name}
  </h1>
);
```

Se puede utilizar cualquier expresión JavaScript dentro de llaves en JSX. Por ejemplo, `2 + 2`, `user.firstName` o `formatName(user)`, etc.

## JSX en JavaScript

Después de compilarse, las expresiones JSX se convierten en llamadas a funciones JavaScript regulares y se evalúan en objetos JavaScript.

Esto significa que es posible usar expresiones JSX en JavaScript, asignando su valor a variables, pasándolas como argumentos, etc.

```javascript
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

## Especificando atributos con JSX

Puedes utilizar comillas para especificar strings literales como atributos:

```javscript
const element = <div tabIndex="0"></div>;
```

También puedes usar llaves para insertar una expresión JavaScript en un atributo:

```javscript
const element = <img src={user.avatarUrl}></img>;
```

> Dado que JSX es más cercano a JavaScript que a HTML, React usa la convención camelCase en vez de nombres de atributos HTML.
>
>Por ejemplo, `class` se vuelve `className` en JSX, y `tabindex` se vuelve `tabIndex`.

## Elementos JSX con tags anidados (hijos)

Si una etiqueta está vacía, puedes cerrarla inmediatamente con `/>`, como en XML:

```javascript
const element = <img src={user.avatarUrl} />;
```

Las etiquetas pueden contener hijos:

```javascript
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

## JSX previene ataques de inyección

Por defecto, React escapa<sup>1</sup> cualquier valor insertado en JSX antes de renderizarlo. De este modo, se asegura de que nunca se pueda inyectar nada que no esté explícitamente escrito en tú aplicación. Básicamente, todo es convertido en un string antes de ser renderizado, previniendo vulnerabilidades [XSS (cross-site-scripting)](https://en.wikipedia.org/wiki/Cross-site_scripting).

```javascript
const title = response.potentiallyMaliciousInput;
// Esto es seguro:
const element = <h1>{title}</h1>;
```

<sub>1. Reemplaza caracteres como `<`, `>` y otros por sus entidades HTML.</sub>
