# Hooks Personalizados

Construir Hooks personalizados permite extraer la lógica del componente en funciones reutilizables.

Cuando veíamos el Hook de Efecto, vimos este componente de una aplicación de chat que muestra un mensaje indicando si un contacto está conectado o desconectado:

```javascript
function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    const handleStatusChange = (status) => setIsOnline(status.isOnline);

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  });

  if (isOnline === null) return 'Loading...';

  return isOnline ? 'Online' : 'Offline';
}
```

Supongamos ahora que nuestra aplicación de chat tiene una lista de contactos y queremos que renderice nombres de usuarios conectados con color verde. Podríamos copiar y pegar la lógica adaptada a nuestro componente `FriendListItem`, pero eso no sería ideal:

```javascript
function FriendListItem(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    const handleStatusChange = (status) => setIsOnline(status.isOnline);

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  });

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

En React (previo a hooks), las dos formas más comunes para compartir lógica de estados entre componentes eran **Render props** y **Higher Order Components**. Ahora veremos como los Hooks resuelven muchos de los mismos problemas sin forzarte a añadir más componentes al árbol.

## Extrayendo un Hook personalizado

Cuando queremos compartir lógica entre dos funciones de Javascript, lo extraemos en una tercera función. Ambos, componentes y Hooks, son funciones, así que esto funciona también.

Un Hook personalizado es una función de JavaScript cuyo nombre comienza con ”use” y que puede llamar a otros Hooks. Por ejemplo, a continuación `useFriendStatus` es nuestro primer Hook personalizado:

```javascript
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    const handleStatusChange = (status) => setIsOnline(status.isOnline);

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  });

  return isOnline;
}
```

No hay nada nuevo dentro, la lógica es copiada de los componentes anteriores. Al igual que en un componente, asegúrate de solo llamar a otros Hooks incondicionalmente en el nivel superior de tu Hook personalizado.

A diferencia de un componente de React, un Hook personalizado no necesita tener una firma específica. Podemos decidir lo que adopta como argumentos y que, si lo hace, debería devolver. En otras palabras, es como una función normal. Su nombre debería siempre empezar con "use" así se puede decir que de un vistazo que las reglas de Hooks se le aplican.

## Usando un Hook personalizado

Originalmente, nuestro objetivo fue eliminar la duplicación de lógica de los componentes `FriendStatus` y `FriendListItem`. Ambos necesitan saber cuando un amigo está conectado.

Ahora que hemos extraído esta lógica a un Hook `useFriendStatus`, podemos usarlo y nuestro componentes quedaría tan simples como:

```javascript
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) return 'Loading...';

  return isOnline ? 'Online' : 'Offline';
}

function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

Es importante notar que, aunque `FriendStatus` y `FriendListItem` usan el mismo Hook, no comparten estado entre ellos, ya que en todo Hook, el estado y los _effects_ quedan aislados completamente. Esto se debe a que nuestro hook personalizado `useFriendStatus` usa internamente `useState` y `useEffect` que, como vimos anteriormente, son completamente independientes entre llamadas.

> **Nota:** Es importante que el nombre de nuestros hooks personalizados comiencen con "use", ya que el plugin de ESLint hace las verificaciones de las reglas de hooks teniendo en cuenta esta convención.

## Pasar información entre Hooks

Ya que los Hooks son funciones, podemos pasar información entre ellos.

Para demostrar esto, vamos a usar otro componente de nuestro hipotético ejemplo de chat. Este es un selector del destinatario del mensaje de chat que muestra si el amigo seleccionado está conectado:

```javascript
const friendList = [
  { id: 1, name: 'Phoebe' },
  { id: 2, name: 'Rachel' },
  { id: 3, name: 'Ross' },
];

function ChatRecipientPicker() {
  const [recipientID, setRecipientID] = useState(1);
  const isRecipientOnline = useFriendStatus(recipientID);

  return (
    <>
      <Circle color={isRecipientOnline ? 'green' : 'red'} />
      <select
        value={recipientID}
        onChange={e => setRecipientID(Number(e.target.value))}
      >
        {friendList.map(friend => (
          <option key={friend.id} value={friend.id}>
            {friend.name}
          </option>
        ))}
      </select>
    </>
  );
}
```

Mantenemos el amigo seleccionado actual en la variable de estado `recipientID`, y la actualizamos si el usuario elige a un amigo diferente en el selector `<select>`.

Como la llamada al Hook `useState` nos da el último valor de la variable de estado `recipientID`, podemos pasarla a nuestro Hook personalizado `useFriendStatus` como un argumento:

```javascript
  const [recipientID, setRecipientID] = useState(1);
  const isRecipientOnline = useFriendStatus(recipientID);
```

Esto nos permite saber cuando el amigo seleccionado está en línea. Si elegimos un amigo diferente y actualizamos la variable de estado `recipientID`, nuestro Hook `useFriendStatus` va a eliminara su suscripción del amigo previamente seleccionado, y se suscribirá al estado del nuevo seleccionado.
