# Otros Hooks de React

A parte de `useState` y `useEffect`, React provée otros Hooks adicionales para cubrir determinadas situaciones. Veamos a continuación una lista con estos hooks.

## useCallback

```javascript
const memoizedCallback = useCallback(
  () => doSomething(a, b),
  [a, b],
);
```

Devuelve un callback memorizado.

`useCallback` devolverá una versión memorizada del callback que solo cambia si las dependencias cambian. Esto es útil cuando se transfieren callbacks a componentes hijos optimizados que dependen de la igualdad de referencia para evitar renders innecesarias (por ejemplo, `shouldComponentUpdate`).

`useCallback(fn, deps)` es igual a `useMemo(() => fn, deps)`.

## useMemo

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

Devuelve un valor memorizado.

`useMemo` solo volverá a calcular el valor memorizado cuando una de las dependencias haya cambiado. Esta optimización ayuda a evitar cálculos costosos en cada render.

La función pasada a `useMemo` se ejecuta durante el renderizado, por tanto no se debe utilizar para hacer cosas que no se deben hacer en el renderizado (por ejemplo, utilizarlos como si fuese un `useEffect`).

Es importante notar que React puede "olvidar" valores memoizados para, por ejemplo, liberar memoria, necesitando luego recalcularlos en el próximo renderizado.

> **Nota:** Si no se proporciona un arreglo, se calculará un nuevo valor en cada renderizado.

## useRef

```javascript
const refContainer = useRef(initialValue);
```

`useRef` devuelve un objeto `ref` mutable cuya propiedad `.curren`t se inicializa con el argumento pasado (`initialValue`). El objeto devuelto se mantendrá persistente durante la vida completa del componente.

Un caso de uso común es para acceder a un hijo imperativamente:

```javascript
function TextInputWithFocusButton() {
  const inputEl = useRef(null);

  // `current` apunta al elemento de entrada de texto montado
  const onButtonClick = () => inputEl.current.focus();

  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

En esencia, `useRef` es un lugar donde mantener en una variable mutable en su propiedad `.current`.

Si pasas un objeto de referencia a React con `<div ref={myRef} />`, React configurará su propiedad `.current` al nodo del DOM correspondiente cuando sea que el nodo cambie.

Sin embargo, `useRef()` es útil para más que el atributo `ref`. Es conveniente para mantener cualquier valor mutable que es similiar a como usarías campos de instancia en las clases.

Esto funciona debido a que `useRef()` crea un objeto JavaScript plano. La única diferencia entre `useRef()` y crear un objeto `{current: ...}` por ti mismo es que `useRef` te dará el mismo objeto de referencia en cada renderizado.

`useRef` no notifica cuando su contenido cambia, es decir, mutar la propiedad `.current` no causa otro renderizado. Para correr algún código cuando React agregue o quite una referencia de un nodo del DOM, es posible hacerlo utilizando una referencia mediante callback.



## useImperativeHandle

```javascript
useImperativeHandle(ref, createHandle, [deps])
```

`useImperativeHandle` personaliza el valor de instancia que se expone a los componentes padres cuando se usa `ref`. Como siempre, el código imperativo que usa `ref`s debe evitarse en la mayoría de los casos. `useImperativeHandle` debe usarse con `forwardRef`:

```javascript
function FancyInput(props, ref) {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus()
  }));

  return <input ref={inputRef} ... />;
}

FancyInput = forwardRef(FancyInput);
```

En este ejemplo, un componente padre que muestra `<FancyInput ref={fancyInputRef} />` podría llamar a `fancyInputRef.current.focus()`.

## useLayoutEffect

Es idéntico a `useEffect`, solo que se dispara de forma síncrona después de todas las mutaciones de DOM. Se usa para leer el diseño del DOM y volver a renderizar de forma sincrónica. Las actualizaciones programadas dentro de `useLayoutEffect` se ejecutarán sincrónicamente, antes de que el navegador tenga la oportunidad de actualizar la UI.

> **Nota:** Al ser síncrono, `useLayoutEffect` bloquea la actualización visual del navegador, por tanto es recomendable evitarlo siempre que sea posible.

## useDebugValue

```javascript
useDebugValue(value)
```

`useDebugValue` se usa para mostrar una label para Hooks personalizados en React DevTools.

Por ejemplo, veamos el Hook personalizado `useFriendStatus` visto en la [sección anterior](/topics/Personalizados?id=extrayendo-un-hook-personalizado):

```javascript
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // ...

  // Mostrar una etiqueta en DevTools junto a este Hook
  // por ejemplo: "FriendStatus: Online"
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
}
```

En algunos casos, formatear un valor para mostrar puede ser una operación costosa. También es innecesario a menos que un Hook sea realmente inspeccionado. Por esto, `useDebugValue` acepta una función de formateo como un segundo parámetro opcional que se llama solo si se inspeccionan los Hooks. Recibe el valor de depuración como parámetro y debe devolver un valor de visualización formateado.

```javascript
useDebugValue(date, date => date.toDateString());
```

> **Nota:** En esta lista no se han incluído dos Hooks adicionales llamados `useContext` y `useReducer` que viene incluídos en React. Los mismos se verán en las siguientes secciones de este curso como parte de las APIs en las cuales se utilizan.
