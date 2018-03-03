---
title: "Introducción al desarrollo de juegos con Xamarin"
description: "La naturaleza del desarrollo de juegos puede diferir considerablemente desde el desarrollo de otros tipos de aplicaciones. Este artículo es una introducción al desarrollo de juegos que ofrece tecnologías que pueden utilizarse con Xamarin.iOS y Xamarin.Android. Proporciona un análisis de alto nivel de cómo se realizan los juegos y un muestreo de tecnologías disponibles para su uso con Xamarin.iOS y Xamarin.Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 9d1ce2da87d6f169efb5431f734695f6876cf3f0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introducción al desarrollo de juegos con Xamarin

_La naturaleza del desarrollo de juegos puede diferir considerablemente desde el desarrollo de otros tipos de aplicaciones. Este artículo es una introducción al desarrollo de juegos que ofrece tecnologías que pueden utilizarse con Xamarin.iOS y Xamarin.Android. Proporciona un análisis de alto nivel de cómo se realizan los juegos y un muestreo de tecnologías disponibles para su uso con Xamarin.iOS y Xamarin.Android._

Desarrollo de juegos puede ser muy interesante, especialmente teniendo en cuenta lo fácil que puede ser publicar las tareas en plataformas móviles. En este artículo se explica los conceptos y tecnologías relacionadas con el desarrollo de juegos que le ayudarán a crean juegos, si su objetivo es crear una alta calidad AAA juego o simplemente al programa para la diversión.

En este artículo se tratan los siguientes temas:

- **Juego frente a los conceptos de programación de juego no** – exploraremos algunos conceptos que son únicas para el desarrollo de juegos, o se comparten con otros tipos de desarrollo pero merecer énfasis aquí debido a su importancia.
- **Equipo de desarrollo de juegos** : en esta sección se examina los diferentes roles en un equipo de desarrolladores de juegos.
- **Crear una juego idea** : en esta sección puede ayudarle a crear una idea de juego nuevo: el primer paso en la creación de una nueva partida.
- **Tecnología de desarrollo de juegos** – aquí podrá se indican algunas de las tecnologías de multiplataforma disponibles que pueden mejorar la productividad como desarrollador de juego.


# <a name="game-vs-non-game-programming-concepts"></a>Frente a juegos. Conceptos de programación no juego

Los programadores mover al desarrollo de juegos a menudo se enfrentan a nuevos conceptos y patrones de desarrollo. Esta sección presenta una vista general de algunos de estos conceptos.


## <a name="the-game-loop"></a>El bucle de juego

Un juego habitual requiere movimiento constante o un cambio en se está produciendo en la pantalla en respuesta a la interacción del usuario y la lógica de juego automática. Esto se consigue mediante lo que se conoce normalmente como un *juego bucle*. Un bucle de juego es algún tipo de instrucción (por ejemplo, un bucle while) que se ejecuta en una alta frecuencia, por ejemplo, 30 o 60 en bucle *fotogramas por segundo*.

El siguiente es un diagrama de un bucle de juego simple:

![](images/image1.png "Se trata de un diagrama de un bucle de juego simple")

Las tecnologías que trataremos más adelante se condensan y eliminan el bucle while real, pero a pesar de esta abstracción estará presente el concepto de actualizaciones de cada fotograma.

Rendimiento del código puede tienen prioridad incluso simple de juegos. Por ejemplo: una función que tarda 10 milisegundos para ejecutar podría tener un impacto significativo en el rendimiento de un juego, especialmente si se llama más de una vez por fotograma. Si el juego se está ejecutando en 30 fotogramas por segundo, a continuación, significa que debe ejecutarse cada fotograma en 33 en milisegundos. Por el contrario, esa función no incluso puede estar apreciable si solo se ejecuta en respuesta a un clic del botón en una aplicación de juego no.

Tipos comunes de lógica que se pueden realizar cada fotograma se incluyen:

- **Leer entrada** : el juego que necesite comprobar si el usuario ha interactuado con el juego comprobando el hardware de entrada, como la pantalla táctil, el teclado, el mouse o el dispositivo de juego.
- **Movimiento** : objetos que mover desde un lugar a otro se suele mover muy pequeña cantidad de cada marco para dar la impresión de un movimiento fluido.
- **Colisión** – muchos juegos exigen que las pruebas frecuentes de si varios objetos están superpuestos o intersección. Hablaremos sobre colisión con más detalle en una sección más adelante en este artículo. Un sistema de simulación física dedicado pueden controlar el movimiento y colisión.
- **Comprobación de las condiciones específicas del juego** : el estado de un juego puede controlarse mediante ciertas condiciones, como si el jugador ha ganado suficientes puntos o si ha agotado el tiempo asignado.
- **Comportamiento de AI** : lógica de cada fotograma que puede utilizarse para controlar el comportamiento de objetos que no se controlan mediante el Reproductor, como el patrolling de un enemigo o el movimiento de los controladores adversario alrededor de un ovalada.
- **Representación** : mayoría de los juegos actualizará lo que se muestra en pantalla todos los fotogramas. Esto puede realizarse en respuesta a los cambios que tienen impacto en el juego (por ejemplo, un carácter que se desplazan por un nivel) o simplemente para proporcionar visual polaco (por ejemplo, está reduciendo nieve o iconos animados).

Tenga en cuenta que muchas de las actividades mencionadas anteriormente pueden cambiar el estado de toda la aplicación, mientras que muchas aplicaciones de juego no tienden a cambiar el estado en respuesta a eventos que se está generando.


## <a name="content-loading-and-unloading"></a>Cargar contenido y descargar

Contenido manualmente carga y descargando (o va a eliminar) puede resultar necesario según qué tecnología usa en el desarrollo. Carga y descarga de activos de forma manual pueden ser necesarias para una serie de motivos:

 - Activos pueden tardar mucho tiempo en cargar en relación con la longitud de un solo fotograma. Algunos activos incluso pueden tardar segundos en cargarse, lo que graves afectarían a la experiencia si carga juega mid. Si el tiempo de carga es especialmente largo (por ejemplo, más de uno o dos segundos) que desea mostrar un elemento animado cargar pantalla ni barra de progreso.
 - Activos pueden consumir una gran cantidad de RAM, que requieren administración activa de lo que se carga para que quepan en el que se proporciona con plataformas de destino del juego.
 - Juegos que necesite mostrar más activos que pueden caber en la RAM. "Abra World" juegos a menudo incluyen entornos de gran tamaño qué reproductores puedan navegar por sin problemas, que es con ninguna pantalla de carga. En este caso debe crear un sistema personalizado para el contenido de transmisión por secuencias y administrar el uso de memoria.

Formatos de archivo personalizados que tenga el procesamiento en tiempo de carga, que requieren código carga personalizada.


## <a name="math"></a>Matemáticas

Muchos juegos exigen matemáticas más avanzadas que las aplicaciones no juego. Por supuesto, el nivel de matemáticas depende de la complejidad del juego. Por lo general juegos 3D requieren más matemáticas que 2D. Afortunadamente siempre puede empezar a trabajar con juegos sencillos y obtener información sobre la marcha. Desarrollo de juegos puede ser una buena manera de aprender matemáticas.

Si está familiarizado con el plano cartesiano – que usa coordenadas X e Y para colocar los objetos, a continuación, sabrá suficiente para empezar a trabajar con el desarrollo de juegos. A continuación muestra un plano cartesiano con positivo Y que señala hacia arriba:

![](images/image2.png "Esto muestra un plano cartesiano con positivo Y que señala hacia arriba")

> [!IMPORTANT]
> Algunas API de motores de usar un sistema de coordenadas donde aumentar el valor de un objeto Y lo moverá hacia abajo, mientras que otros sistemas usan un sistema de coordenadas donde positivo Y está activo. Tenga esto en cuenta si va a mover entre sistemas.
Funciones trigonométricas (por ejemplo, seno y coseno) se usan normalmente en juegos 2D que implementan ninguna forma de rotación.



Si planea realizar un juego en 3D, a continuación, es probable que debe estar familiarizado con conceptos de álgebra lineal (sobre la rotación y movimiento en un espacio 3D), así como algunos cálculo (para la implementación de aceleración).


## <a name="content-pipelines"></a>Canalizaciones de contenido

El término *canalización contenido* hace referencia al proceso que tarda un archivo en obtener desde su formato al crear (por ejemplo, un archivo de imagen .png) a su formato final cuando se utiliza en un juego. El formato final depende de que tipo de contenido se está utilizando, así como que se utiliza la tecnología para presentar el contenido.

Algunas canalizaciones contenidos pueden ser muy rápida y no requerir ninguna intervención manual. Por ejemplo, mayoría de los motores de juegos y las API pueden cargar el formato de archivo PNG en su formato sin procesar. Por otro lado, necesitan más complicados formatos (por ejemplo, los modelos 3D) ser procesados en un formato diferente antes de que se va a cargar, y este proceso puede tardar algún tiempo, según el tamaño y la complejidad del recurso.


# <a name="game-development-teams"></a>Equipos de desarrollo de juegos

Desarrollo de juegos presenta nuevos roles y títulos para los usuarios implicados en el proceso. La mayoría de los desarrolladores de juegos no pueden satisfacer el amplio conjunto de destrezas necesarias para liberar un juego completo, por lo que existe un número de disciplinas. Tenga en cuenta que esto no es una lista completa de las áreas de desarrollo: solo algunas de las más comunes.

- **Programador** : la mayoría de los usuarios leer este artículo entrarán dentro de esta categoría. El rol de programador en el desarrollo de juego es similar al rol del programador en una aplicación de juego no. Responsabilidades incluyen la lógica para controlar el flujo de un juego, desarrollo de sistemas para tareas comunes en el contexto de un proyecto determinado, agregar y mostrar el contenido y, por supuesto: corrige los errores de escritura.
- **Intérprete 2D** – intérpretes 2D son responsables de crear *2D activos*. Incluyen archivos de imagen para la interfaz gráfica de usuario, partículas, entornos y caracteres del juego. Si el juego que está desarrollando es 3D, artistas 2D no pueden ser responsables de entornos y caracteres. Puede encontrar libre carátula de su juego en [http://opengameart.org/](http://opengameart.org/) .
- **Intérpretes 3D** – intérpretes 3D son responsables de crear *activos 3D*. Esto incluye los modelos 3D para entornos y caracteres, props (mobiliario, plantas y otros objetos inanimados). Algunos equipos de diferencian entre intérpretes 3D y animadores 3D según el tamaño del equipo. Puede encontrar libre material gráfico 3D para tu juego en [http://opengameart.org/](http://opengameart.org/) .
- **Diseñador de juegos** – diseñadores juegos están responsables de definir cómo se reproduce el juego. Esto puede incluir las decisiones de alto nivel, como la configuración de la partida, el objetivo global de la partida y cómo progresa un Reproductor a través del juego. Diseñadores de juego también pueden participar en las decisiones muy detalladas como entrada de asignación a las acciones, definir coeficientes de movimiento o nivel de seguridad y diseñar el diseño del nivel. Tenga en cuenta que el término *diseñador* pueden hacer referencia a un juego diseñador o en un diseñador visual dependiendo del contexto.
- **Diseñador de sonido** – diseñadores sonidos son responsables de activos de audio de un juego. Algunos equipos pueden distinguir entre los usuarios responsables de crear los efectos de sonido y compositores, mientras que los equipos más pequeños pueden tener un único individuo responsable de todo el audio.


# <a name="creating-a-game-idea"></a>Crear una Idea de juego

Diseñar un juego puede aparecer para que sea fácil que hacer: después de todo, el único requisito es "hacer algo divertido." Por desgracia, muchos desarrolladores consideran a sí mismos con pérdida cuando llegue el momento de crear una idea de que se va a iniciar el desarrollo.

No se explica fácilmente la disciplina de diseño de juego y requiere práctica para mejorar al igual que la carátula del o does de programación, pero en esta sección puede ayudarle a empezar hacia abajo de la ruta de acceso.

Nuevos programadores deben empiece poco a poco. Puede ser difícil resistir la tentación de recreación de un juego de vídeo grande y moderno, pero los juegos más pequeños pueden ser un mejor entorno de aprendizaje y hace que el progreso más rápido para ofrecer una experiencia más productiva.

Muchos juegos, ambos con el fin de juegos de aprendizaje, así como comerciales, comienzan como una mejora o modificación de un juego existente. Una manera de generar ideas consiste en Buscar en otros juegos para obtener ideas. Por ejemplo, puede considerar un juego que desee personal y pruebe a identificar qué características sobre el juego que sea divertido. Es posible que la exploración, dominio de los mecanismos del juego o cómo avanzar a través de un caso. No olvide tener en cuenta también los juegos "los" cuando se busca nuevas ideas.

Otra técnica para generar nuevas ideas es tener en cuenta un género específico, como juegos de rompecabezas, juegos de estrategia o platformers. Un género familiar para el desarrollador puede proporcionar un buen punto de partida.

Rehacer juegos existentes, también es una experiencia educativa, aunque esto puede limitar la viabilidad comercial del producto terminado. El proceso de creación de un juego, incluso uno que es un clon preciso, proporciona una valiosa experiencia educativa.


# <a name="game-development-technology"></a>Tecnología de desarrollo de juegos

Los desarrolladores que utilizan Xamarin.Android y Xamarin.iOS tienen una amplia gama de tecnologías disponibles para ayudar en el desarrollo de juego. En esta sección se describe algunas de las soluciones multiplataforma más populares.


## <a name="cocossharp"></a>CocosSharp

CocosSharp es una versión de código abierto, multiplataforma del motor de juegos 2D Cocos. El motor de proporciona acceso a Android, iOS, Mac OS X, el escritorio de Windows, Windows RT y Windows Phone.

CocosSharp se centra en un programador simple API para el desarrollo de juegos en 2D. El crecimiento en juegos en dispositivos móviles ha ayudado a reignite la popularidad del desarrollo de juegos en 2D realizar CocosSharp tecnología viable para pasatiempo y proyectos comerciales similares. Se proporciona como archivos de código o .dll de origen (que pueden obtenerse a través de NuGet), pero no proporciona un editor visual; por lo tanto, cualquier interacción con el motor de CocosSharp requiere conocimientos de programación.

Para empezar a trabajar con CocosSharp, visite nuestro [CocosSharp guías](~/graphics-games/cocossharp/index.md).

El juego enfadado Ninjas se crea con CocosSharp y, si está buscando un juego ya se está ejecutando para varias plataformas puede ser un buen punto de partida:

![](images/image3.png "El juego Ninjas enfadado se creó con CocosSharp")

Puede descargarlo y obtener más información en el [AngryNinjas Github página](https://github.com/xamarin/AngryNinjas).


## <a name="monogame"></a>MonoGame

MonoGame es un origen de abrir, entre las versiones de plataforma de API de Microsoft XNA. MonoGame puede utilizarse para crear juegos para iOS, Android, Mac OS X, Linux, Windows, Windows RT y Windows Phone.

A diferencia de CocosSharp, MonoGame es técnicamente no un juego motor, pero en su lugar un desarrollo de juegos API. Esto significa que para trabajar con MonoGame requiere directamente administrar objetos de juegos, manualmente objetos de dibujo e implementar objetos comunes como cámaras y *gráficos de escena* (la jerarquía primario-secundario entre los objetos del juego). Para ayudar a comprender la diferencia, tenga en cuenta que CocosSharp se basa en MonoGame. MonoGame generaliza algunas de las tecnologías específicas de la plataforma, como gráficos, representación y audio, mientras que CocosSharp proporciona código para organizar e implementar la lógica de juego.

MonoGame no ofrece un entorno de desarrollo visual estándar para trabajar con MonoGame requiere conocimientos de programación.

Algunos ejemplos importantes de juegos con MonoGame son:

FEZ:

![](images/image7.png "FEZ")

Bastión:

![](images/image8.jpg "Bastion")

Para empezar a trabajar con MonoGame, diríjase a nuestro [MonoGame guías](~/graphics-games/monogame/index.md).


## <a name="urhosharp"></a>UrhoSharp

UrhoSharp es un motor 2D y 3D de alto nivel de multiplataforma que puede usarse para crear escenas 3D y 2D animados para sus aplicaciones mediante los objetos Geometry, materiales, luces y cámaras.

![](images/urhosharp.gif "UrhoSharp es un motor 2D y 3D de alto nivel de multiplataforma que puede usarse para crear escenas 3D y 2D animados")

Extraer del repositorio el [UrhoSharp guías](~/graphics-games/urhosharp/index.md) para empezar a trabajar.

## <a name="additional-technology"></a>Tecnología adicional

Las tecnologías destacadas anteriormente es sólo un ejemplo de las tecnologías disponibles. Otras tecnologías importantes son:

- **Kit de Sprite** – Xamarin proporciona compatibilidad para el marco de juegos de Sprite Kit de Apple, que proporciona acceso a toda la funcionalidad de la API nativa. Puesto que el Kit de Sprite tecnología creada por Apple, proporciona fuerte integración con el resto del ecosistema de iOS. Por supuesto, Sprite Kit no es multiplataforma, por lo que no se puede usar en Android. Para obtener más información sobre el uso de Sprite Kit, vea esta entrada: [http://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](http://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Kit de escena** – Xamarin también proporciona compatibilidad para el marco de escena Kit de Apple, que simplifica la implementación de gráficos 3D en aplicaciones de iOS. Kit de escena también es tecnología proporcionada por Apple, por lo que tiene la integración y consideraciones específicas de la plataforma mencionadas anteriormente para Sprite Kit. Para obtener más información sobre el Kit de escena, consulte esta publicación: [http://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](http://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (que es el acrónimo abra Kit de herramientas) proporciona acceso de bajo nivel OpenGL para Mac, iOS y Apple hardware. Para obtener más información sobre OpenTK, vea la página principal en: [http://www.opentk.com/](http://www.opentk.com/)


# <a name="summary"></a>Resumen

Este artículo trata los conceptos principales del desarrollo de juegos y proporciona información sobre cómo comenzar a realizar su primer juego. Una vez que haya terminado de este artículo, son los siguientes pasos elegir la tecnología y empezar a trabajar a través de nuestra serie de tutoriales vinculados en las secciones correspondientes anteriores.

## <a name="related-links"></a>Vínculos relacionados

- [Guías de CocosSharp](~/graphics-games/cocossharp/index.md)
- [Guías de MonoGame](~/graphics-games/monogame/index.md)
- [Guías de UrhoSharp](~/graphics-games/urhosharp/index.md)
