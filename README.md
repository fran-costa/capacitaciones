# React 16.8

## ¿Qué es ReactJS?
Es una tecnología front-end, la cual es una librería de JavaScript open-source creada por Facebook para construir interfaces de usuario.
Surgio para desarrollar aplicaciones SPA (Single Page Applications) de manera más eficiente.
Normalmente se utiliza en el lado cliente, pero también se puede utilizar en el lado servidor, haciendo posible la creación de aplicaciones isomórficas.

## Caracteristicas principales
- Basado en componentes, composición y reutilización de los mismos
- Componentes con estado interno
- Renderización eficiente
- Flujo de datos unidireccional

### Basado en componentes, composición y reutilización de los mismos
Las componentes son elementos independientes y pueden ser reutilizados, además, describen cómo tienen que visualizarse y cómo tienen que comportarse.
Cada uno de estos puede utilizar otro componentes y ser usado por otra componente, esto permite crear aplicaciones grandes pero que cada elemento de la interfaz de usuario sea independiente en cuanto a su funcionalidad.
Crear componentes nos permite aislar el comportamiento en pequenas partes las cuales facilitan el trabajo a la hora de debugear, mejorar y reutilizar código.

![components](https://cdn-images-1.medium.com/max/600/1*m2k00x1C__Nbkd2tyemyqw.png)

### Componentes con estado interno
Las componentes pueden tener (o no) un estado interno en el cual guardar información util para su comportamiento.

### Renderización eficiente
Para renderizar mas eficientemente React implementa Virtual DOM.
Virtual DOM es una copia del DOM en memoria que se utiliza para optimizar los cambios en el DOM real.
El mecanismo utilizado consta en crear una copia del DOM en memoria (virtual DOM), luego utiliza un algoritmo para comparar (diff) las propiedades y actualizar solamente las partes que cambiaron.
Esto permite actualizar solo los elementos que tienen un cambio en la interfaz de usuario.

![render](https://cdn-images-1.medium.com/max/800/1*jb7rWNWkjLcGri_GZhxBGA.png)

### Flujo de datos unidireccional
La mayoría de frameworks para resolver el patrón MVVM (Model-View-ViewModel) utilizan un sistema de data-binding bidireccional, que se encarga de sincronizar automáticamente la vista y el viewmodel.
Sin embargo, React utiliza un sistema de databinding unidireccional, es decir, los datos de la aplicación fluyen en una sola dirección.

![data flow](https://facebook.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png)

## Links relacionados
- Documentación oficial de react: [versión 16.8 español](https://es.reactjs.org/) [versión 16.8 ingles](https://es.reactjs.org/)
- Documentos, videos, talleres y mas cosas relacionadas con react: [awesome-react](https://github.com/enaqx/awesome-react)
- Documentos, videos, talleres y mas cosas relacionadas con react en espanol: [react espanol](https://github.com/jlobos/react-espanol)
- Componentes para reutilizar: [awesome-react-components](https://github.com/brillout/awesome-react-components)
- Componentes de presentación y contenedores: [presentational vs container components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)
- Composición vs herencia: [composition vs inheritance](https://reactjs.org/docs/composition-vs-inheritance.html)
- Flujo unidireccional de datos: [understanding unidirectional data flow](https://medium.com/@lizdenhup/understanding-unidirectional-data-flow-in-react-3e3524c09d8e)
- Flux: [in depth overview](https://facebook.github.io/flux/docs/in-depth-overview.html)
