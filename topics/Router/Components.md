# Componentes provistos por React Router

Como se mencionó en [la introducción a React Router](topics/React_Router?id=react-router), ésta es librería de ruteo _declarativa_ para React, donde la palabra _declarativa_ hace referencia al hecho de que no debemos especificar de qué forma se hace el ruteo (no lo hacemos _imperativamente_), sino que simplemente debemos "describir" cómo queremos que sea este ruteo.

React es un ejemplo de API declarativa, la cual nos permite describir como será la UI mediante el uso de componentes. React Router, por su parte, nos permitirá describir como será el ruteo de nuestra aplicación (es decir, la UI a mostrar en función de la URL) también mediante el uso de componentes.

Para esto, React Router provée tres tipos de componentes:
  - los [**routers**](topics/Router/Routers.md) que se encargan de gestionar y mantener sincronizada la UI con la URL,
  - las [**componentes de matcheo de rutas**](topics/Router/Routes.md) que se encargan de definir qué componente renderizar en función de la URL,
  - los [**componentes de navegación**](topics/Router/Nav.md) que nos permiten cambiar la URL.

Así mismo, tras el lanzamiento de los Hooks en la versión 16.8 de React, React Router también comenzó a proveer Hooks propios de ruteo.
