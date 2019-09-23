# Reglas de los Hooks

Los Hooks son funciones de JavaScript, pero se deben seguir dos reglas cuando se usen.

## 1. Llama Hooks solo en el nivel superior

**No llames Hooks dentro de ciclos, condicionales, o funciones anidadas.** En vez de eso, usa siempre Hooks en el nivel superior de tu función en React. Siguiendo esta regla, te aseguras de que los hooks se llamen en el mismo orden cada vez que un componente se renderiza. Esto es lo que permite a React preservar correctamente el estado de los hooks entre multiples llamados a `useState` y `useEffect`.

## 2. Llama Hooks solo en funciones de React

**No llames Hooks desde funciones JavaScript regulares.** Puedes llamarlos desde componentes funcionales de React o desde Hooks personalizados.

Siguiendo esta regla, te aseguras de que toda la lógica del estado de un componente sea claramente visible desde tu código fuente.

> El plugin de ESLint `eslint-plugin-react-hooks` chequea automáticamente estas dos reglas (y algunas otras cuestiones en torno al uso de Hooks). Puedes añadir este plugin a tu proyecto si quieres probarlo

## Explicación

Como vimos anteriormente, podemos usar multiples Hooks de Estado o Hooks de Efecto en un solo componente:

```javascript
function Form() {
  // 1. Usa la variable de estado del nombre
  const [name, setName] = useState('Mary');

  // 2. Usa un efecto para persistir el formulario
  useEffect(function persistForm() {
    localStorage.setItem('formData', name);
  });

  // 3. Usa la variable de estado del apellido
  const [surname, setSurname] = useState('Poppins');

  // 4. Usa un efecto para la actualización del título
  useEffect(function updateTitle() {
    document.title = name + ' ' + surname;
  });
}
```

Entonces, ¿cómo hace React para saber cuál estado corresponde a cuál llamado del `useState`? La respuesta es que React se basa en el orden en el cual los Hooks son llamados. Nuestro ejemplo funciona porque el orden en los llamados de los Hooks son el mismo en cada render:

```javascript
// ------------
// Primer render
// ------------
useState('Mary')           // 1. Inicializa la variable de estado del nombre con 'Mary'
useEffect(persistForm)     // 2. Agrega un efecto para persistir el formulario
useState('Poppins')        // 3. Inicializa la variable de estado del apellido con 'Poppins'
useEffect(updateTitle)     // 4. Agrega un efecto para la actualización del título

// -------------
// Segundo render
// -------------
useState('Mary')           // 1. Lee la variable de estado del nombre (el argumento es ignorado)
useEffect(persistForm)     // 2. Reemplaza el efecto para persistir el formulario
useState('Poppins')        // 3. Lee la variable de estado del apellido (el argumento es ignorado)
useEffect(updateTitle)     // 4. Reemplaza el efecto de actualización del título

// ...
```

Siempre y cuando el orden de los llamados a los Hooks sea el mismo entre renders, React puede asociar algún estado local con cada uno de ellos. Pero... ¿Qué sucede si ponemos la llamada a un Hook (por ejemplo, el _effect_ `persistForm`) dentro de una condición?

```javascript
  // Estamos rompiendo la primera regla al usar un Hook en una condición
  if (name !== '') {
    useEffect(function persistForm() {
      localStorage.setItem('formData', name);
    });
  }
```

La condición `name !== ''` es `true` en el primer render, entonces corremos el Hook. Sin embargo, en el siguiente render el usuario puede borrar el formulario, haciendo la condición `false`. Ahora que nos saltamos este Hook durante el renderizado, el orden de las llamadas a los Hooks se vuelve diferente:

```javascript
useState('Mary')           // 1. Lee la variable de estado del nombre (el argumento es ignorado)
// useEffect(persistForm)  // X. Este Hook fue saltado
useState('Poppins')        // 2 (pero era el 3). Falla la lectura de la variable de estado del apellido
useEffect(updateTitle)     // 3 (pero era el 4). Falla el reemplazo del efecto
```

En este caso, React no sabe que devolver para la segunda llamada del Hook `useState`. React esperaba que la segunda llamada al Hook en este componente corresponda al _effect_ `persistForm`, igual que en el render anterior, pero ya no lo hace. A partir de este punto, cada siguiente llamada de un Hook después de la que nos saltamos también cambiaría de puesto por uno, lo que llevaría a la aparición de errores.

Es por esto que los Hooks deben ser utilizados en el nivel superior de nuestros componentes. Si queremos ejecutar un _effect_ condicionalmente, podemos poner esa condición dentro de nuestro Hook:

```javascript
  useEffect(function persistForm() {
    // 👍 No vamos a romper la primera regla nunca más.
    if (name !== '') localStorage.setItem('formData', name);
  });
```
