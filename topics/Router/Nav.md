# Componentes de navegación

Los **componentes de navegación** que nos permiten cambiar la URL (en realidad, la `location`).

`react-router-dom` ofrece tres **componentes de navegación**: `Link`, `NavLink` y `Redirect`.

## Link

Es el equivalente al tag `<a>` y nos proporciona una forma declarativa de navegar por la aplicación.

```javascript
<Link
  to={string || object}
  replace={bool}
>
  {children}
</Link>
```

- `to` {`string` || `object`}: representa la `location` a la cual navegar.

```javascript
// string alternative
<Link to="/courses?sort=name#the-hash">
  ...
</Link>

// object alternative
<Link to={
  pathname: "/courses",
  search: "?sort=name",
  hash: "#the-hash",
}>
  ...
</Link>
```

- `replace` {`Boolean`}: Si es `true`, reemplaza la entrada actual del `history`, sino adiciona una nueva. Valor por defecto: `false`.
- `children` {`string` || `node`}: El texto o el elemento que se renderizará como el link.

> **Nota:** Se pueden pasar también un `className` para definir los estilos del link.

## NavLink

Es una versión especial de `Link` que agrega atributos de estilo específicos para el elemento renderizado cuando matchea con la URL.

```javascript
<NavLink
  to={string || object}
  activeClassName={string}
  activeStyle={object}
  exact={bool}
  strict={bool}
  isActive={func}
  location={object}
>
  {children}
</NavLink>
```

- `to` {`string` || `object`}: Idem a `Link`.
- `activeClassName` {`string`}: Nombre de la clase que especifica el estilo del link cuando matchea con la URL. Valor por defecto: `active`.
- `activeStyle` {`object`}: El estilo a aplicar cuando el componente está activo.
- `exact` {`Boolean`}: Si es `true`, el matcheo del `path` con la URL debe ser exacto. Valor por defecto: `false`.
- `strict` {`Boolean`}: Si es `true`, la `/` al final del `path` será considerado a la hora del matcheo. Valor por defecto: `false`.
- `isActive` {`function`}: Callback a ejecutar cuando el `NavLink` matchee con el valor de `location`.

## Redirect

Componente que navega a la `location` especificada (reemplazando, por defecto, el valor actual).

```javascript
<Redirect
  to={string || object}
  push={bool}
  from={string}
  exact={bool}
  strict={bool}
/>
```

- `to` {`string` || `object`}: representa la `location` a la cual navegar.
- `push` {`Boolean`}: Si es `true`, agrega una nueva entrada al historial en lugar de reemplazar la actual. Valor por defecto: `false`.
- `from` {`string`}: Si se especifica, sólo hará la redirección si este valor coincide con `location`.
- `exact` {`Boolean`}: Si es `true`, el matcheo del `path` con la URL debe ser exacto. Valor por defecto: `false`.
- `strict` {`Boolean`}: Si es `true`, la `/` al final del `path` será considerado a la hora del matcheo. Valor por defecto: `false`.
