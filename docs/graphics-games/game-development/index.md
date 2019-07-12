---
title: Introducción al desarrollo de juegos con Xamarin
description: Este documento proporciona una descripción general de desarrollo de juegos con Xamarin, que describe un muestreo de las tecnologías disponibles para su uso con Xamarin.iOS y Xamarin.Android y cómo se realizan los juegos.
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 0fa4dbbf256e005fe17841f06acf1338197784b0
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832287"
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introducción al desarrollo de juegos con Xamarin

Desarrollo de juegos puede ser muy emocionante, especialmente teniendo en cuenta sencillo se puede publicar su trabajo en plataformas móviles. En este artículo se explica los conceptos y tecnologías relacionadas con el desarrollo de juegos que le ayudarán a crean juegos, si su objetivo es crear un AAA de alta calidad juego o simplemente al programa para la diversión.

En este artículo se tratan los siguientes temas:

- **Juego frente a los conceptos de programación no juego** – exploraremos algunos conceptos que son exclusivas de desarrollo de juegos, o se comparten con otros tipos de desarrollo pero merecen énfasis aquí debido a su importancia.
- **Equipo de desarrollo de juegos** : esta sección se examinan los distintos roles en un equipo de desarrolladores de juegos.
- **Creación de una idea de juego** : en esta sección puede ayudarle a crear una nueva idea de juego: el primer paso para realizar una nueva partida.
- **Tecnología de desarrollo de juegos** – aquí enumeraremos algunas de las tecnologías de desarrollo multiplataforma disponibles que pueden mejorar su productividad como desarrollador de juegos.

## <a name="game-vs-non-game-programming-concepts"></a>Vs juegos. Conceptos de programación no juego

Mover al desarrollo de juegos de los programadores a menudo se enfrentan a nuevos conceptos y patrones de desarrollo. En esta sección se presenta una visión general de algunos de estos conceptos.

### <a name="the-game-loop"></a>El bucle de juego

Constante movimiento o cambio que se está produciendo en la pantalla en respuesta a la interacción del usuario y la lógica de juego automática requiere que un juego típico. Esto se logra a través de lo que se conoce normalmente como un *bucle de juego*. Un bucle de juego es algún tipo de instrucción (por ejemplo, un bucle while) que se ejecuta en una frecuencia de muy alta, por ejemplo, 30 o 60 de bucle *fotogramas por segundo*.

El siguiente es un diagrama de un bucle de juego simple:

![](images/image1.png "Se trata de un diagrama de un simple bucle de juego")

Las tecnologías que se describen a continuación se abstraen el bucle while real, pero a pesar de esta abstracción estará presente el concepto de las actualizaciones de cada fotograma.

Rendimiento del código puede tienen prioridad en incluso los más sencillos de juegos. Por ejemplo: una función que toma los 10 milisegundos para ejecutar podría tener un impacto significativo en el rendimiento de un juego, especialmente si se llama más de una vez por fotograma. Si el juego se está ejecutando a 30 fotogramas por segundo, a continuación, significa que debe ejecutar cada fotograma en 33 en milisegundos. Por el contrario, dicha función quizás no sea evidente si solo se ejecuta en respuesta a un clic de botón en una aplicación de juego no.

Tipos comunes de lógica que pueden ser realizada cada fotograma se incluyen:

- **Entrada de lectura** : el juego es posible que deba comprobar si el usuario ha interactuado con el juego mediante la comprobación de hardware de entrada, como la pantalla táctil, teclado, mouse o dispositivo de juego.
- **Movimiento** : qué mover desde un solo lugar a otro normalmente moverá muy pequeño de objetos amount, que cada fotograma para la sensación de movimiento fluido.
- **Colisión** – muchos juegos exigen que las pruebas con frecuencia si varios objetos están superpuestos o se cruzan. Hablaremos sobre colisión con más detalle en una sección posterior de este artículo. Movimiento y colisión pueden controlarse mediante un sistema de simulación física dedicada.
- **Comprobación de situaciones específicas de los juegos** : el estado de un juego puede controlarse mediante ciertas condiciones, por ejemplo, si el jugador ha obtenido suficientes puntos o si ha agotado el tiempo asignado.
- **Comportamiento de inteligencia artificial** : lógica de cada fotograma que puede usarse para controlar el comportamiento de objetos que no se controlan mediante el Reproductor, como el patrolling de un enemigo o el movimiento del adversario controladores alrededor de una pista de carreras.
- **Representación** : mayoría de los juegos actualizará a lo que se muestra en la pantalla cada fotograma. Esto puede hacerse en respuesta a los cambios que tienen impacto en el juego (como un carácter de moverse a través de un nivel) o simplemente para ofrecer un estilo visual (por ejemplo, bajan nieve o los iconos animados).

Tenga en cuenta que muchas de las actividades mencionadas pueden cambiar el estado de toda la aplicación, mientras que muchas aplicaciones de juego no tienden a cambiar el estado en respuesta a eventos provocados.

### <a name="content-loading-and-unloading"></a>Carga de contenido y la descarga

Contenido manualmente cargando y descargando (o eliminación) puede ser necesarias según la tecnología que usa en el desarrollo. Manualmente, carga y descarga de activos pueden ser necesarios para una serie de motivos:

- Los recursos pueden tardar mucho tiempo en cargar en relación con la longitud de un solo fotograma. Algunos activos incluso pueden tardar segundos en cargarse, que esto interrumpen gravemente la experiencia si carga mid juego. Si el tiempo de carga es especialmente largo (por ejemplo, más de un segundo o dos) que desea mostrar un elemento animado pantalla ni barra de progreso de carga.
- Los recursos pueden consumir una gran cantidad de RAM, que requieren administración activa de lo que se carga para ajustarse a los proporcionados por las plataformas de destino del juego.
- Juegos que deba mostrar más activos que pueden caber en la memoria RAM. "Abra World" juegos suelen incluyen entornos de gran tamaño que los jugadores pueden desplazarse a la perfección, este es con ninguna carga de pantallas. En este caso es posible que deba crear un sistema personalizado para el contenido de streaming en y administrar el uso de memoria.

Formatos de archivo personalizados que necesite el procesamiento en tiempo de carga, que requieren código carga personalizada.

### <a name="math"></a>Matemáticas

Muchos juegos exigen matemáticas más avanzadas que las aplicaciones que no sean juego. Por supuesto, el nivel de matemáticas depende de la complejidad del juego. En general, juegos en 3D requieren más matemáticas a 2D. Afortunadamente siempre puede empezar a trabajar con juegos sencillos y obtenga información sobre la marcha. Desarrollo de juegos puede ser una excelente manera de aprender matemáticas.

Si está familiarizado con el plano cartesiano – que usa coordenadas X e Y para colocar objetos – entonces sabe lo suficiente como para empezar a trabajar con el desarrollo de juegos. A continuación muestra un plano cartesiano con positivo Y que señala hacia arriba:

![](images/image2.png "Esto muestra un plano cartesiano con positivo Y que señala hacia arriba")

> [!IMPORTANT]
> Algunas API de motores de usar un sistema de coordenadas donde aumentar el valor de un objeto Y lo moverá hacia abajo, mientras que otros sistemas usan un sistema de coordenadas donde es positivo Y seguridad. Téngalo en cuenta si va a mover entre sistemas.
Funciones trigonométricas (por ejemplo, seno y coseno) se usan normalmente en juegos en 2D que implementan ninguna forma de rotación.

Si planea crear un juego 3D, a continuación, probablemente deberá estar familiarizado con conceptos de álgebra lineal (para el movimiento en un espacio 3D y giro), así como algunos cálculo (para la implementación de aceleración).

### <a name="content-pipelines"></a>Canalizaciones de contenido

El término *canalización de contenido* hace referencia al proceso que un archivo que se tarda en obtener desde su formato cuando se creó (por ejemplo, un archivo de imagen .png) a su formato final cuando se usa en un juego. El formato final depende de que el tipo de contenido se está utilizando, así como los que se utiliza la tecnología para presentar el contenido.

Algunas canalizaciones de contenido pueden ser muy rápida y no requerir ningún esfuerzo manual. Por ejemplo, la mayoría de los motores de juegos y API pueden cargar el formato de archivo .png en su formato sin procesar. Por otro lado, formatos más complicados (por ejemplo, modelos 3D) que deba procesarse en un formato diferente antes de cargarse, y este proceso puede tardar algún tiempo según el tamaño y la complejidad del recurso.

## <a name="game-development-teams"></a>Equipos de desarrollo de juegos

Desarrollo de juegos presenta nuevos roles y títulos de personas implicadas en el proceso. La mayoría de los desarrolladores de juegos no son puede satisfacer el amplio conjunto de habilidades necesarias para liberar un juego completo, por lo que existe un número de disciplinas. Tenga en cuenta que esto no es una lista completa de las áreas de desarrollo: sólo algunas de las más comunes.

- **Programador** : la mayoría de las personas leyendo este artículo entrarán dentro de esta categoría. El rol de programador en el desarrollo de juegos es similar al rol del programador en una aplicación de juego no. Responsabilidades incluyen la escritura de lógica para controlar el flujo de un juego, desarrollo de sistemas para tareas comunes en el contexto de un proyecto determinado, agregar y mostrar el contenido y, evidentemente, corrección de errores.
- **Artista 2D** – artistas 2D son responsables de crear *activos 2D*. Estos incluyen archivos de imagen para la interfaz gráfica de usuario, partículas, entornos y caracteres del juego. Si el juego que se está desarrollando es 3D, artistas 2D no pueden ser responsables de entornos y caracteres. Puede encontrar libre prediseñadas para su juego en [ http://opengameart.org/ ](http://opengameart.org/) .
- **Diseñadores de 3D** – artistas 3D son responsables de crear *recursos 3D*. Estos incluyen modelos 3D para props (muebles, plantas y otros objetos inanimados), caracteres y entornos. Algunos equipos diferencian entre los artistas 3D y animadores 3D según el tamaño del equipo. Puede encontrar libre material gráfico 3D para su juego en [ http://opengameart.org/ ](http://opengameart.org/) .
- **Diseñador de juegos** : diseñadores de juegos son responsables de definir cómo se reproduce el juego. Esto puede incluir las decisiones de alto nivel como la configuración de la partida, el objetivo global del juego, y cómo un reproductor progresa a través del juego. Diseñadores de juegos también se pueden participar en las decisiones más detalladas como entrada de asignación a las acciones, definir los coeficientes para el nivel de seguridad o de movimiento y diseñar un diseño de nivel. Tenga en cuenta que el término *diseñador* pueden hacer referencia a un diseñador de juego o un diseñador visual en función del contexto.
- **Diseñador de sonido** : diseñadores de sonido son responsables de recursos de audio de un juego. Algunos equipos pueden diferenciar entre individuos responsables de crear efectos de sonido y compositores, mientras que los equipos más pequeños pueden tener un solo individuo responsable de todo el audio.

## <a name="creating-a-game-idea"></a>Creación de una Idea de juego

Diseñar un juego puede aparecer para que sea fácil hacer: después de todo, el único requisito es "hacer algo divertido". Por desgracia, muchos desarrolladores se encuentran en una pérdida cuando llega el momento de crear una idea de que se va a iniciar el desarrollo.

No se explica con facilidad la disciplina de diseño de juegos y requiere práctica para mejorar así el arte o does de programación, pero en esta sección puede ayudarle a empezar la ruta de acceso.

Deben comenzar nuevos desarrolladores. Puede ser difícil resistir la tentación de recreación de un juego de vídeo grande y moderno, pero los juegos más pequeños pueden ser un mejor entorno de aprendizaje y hace que el progreso más rápido para una experiencia más gratificante.

Empezar muchos juegos, ambos con el fin de juegos de aprendizaje, así como comerciales, como una mejora o modificación de un juego existente. Una manera de generar ideas es mirar otros juegos para inspirarse. Por ejemplo, puede considerar un juego que le guste personal e intenta identificar qué características sobre el juego que sea divertido. Es posible la exploración, el dominio de la mecánica del juego o progresa a través de un caso. No olvide tener en cuenta también los juegos "retro" cuando se busca nuevas ideas.

Otra técnica para generar nuevas ideas es considerar un género específico, como los juegos de rompecabezas, juegos de estrategia o plataformas. Un género familiar para el desarrollador puede proporcionar un buen punto de partida.

Transformación de juegos existentes también es una experiencia educativa, aunque esto puede limitar la viabilidad comercial del producto terminado. El proceso de creación de un juego, incluso uno que es un clon de preciso, proporciona una experiencia educativa valiosa.

## <a name="game-development-technology"></a>Tecnología de desarrollo de juegos

Los desarrolladores que usan Xamarin.iOS y Xamarin.Android tienen una amplia gama de tecnologías disponibles para ayudar en el desarrollo de juegos. En esta sección se tratan algunos de las más populares soluciones multiplataforma.

### <a name="monogame"></a>MonoGame

MonoGame es un código abierto entre versiones de plataforma de API de Microsoft XNA. MonoGame puede utilizarse para crear juegos para iOS, Android, Mac OS X, Linux, Windows, Windows RT, PS4, PSVita, Xbox One y conmutador.

MonoGame es técnicamente no un motor de juego, sino más bien un desarrollo de juegos de API. Esto significa que el trabajo con MonoGame requiere directamente la administración de objetos del juego, manualmente objetos de dibujo e implementar objetos comunes tales como cámaras y *gráficos de escena* (la jerarquía primaria-secundaria entre objetos del juego).

MonoGame no ofrece un entorno de desarrollo visual estándar, por lo que trabajar con MonoGame requiere conocimientos de programación.

Algunos ejemplos importantes de juegos con MonoGame son:

FEZ:

![](images/image7.png "FEZ")

Bastión:

![](images/image8.jpg "Bastión")

Para empezar a trabajar con MonoGame, diríjase a nuestro [MonoGame guías](~/graphics-games/monogame/index.md).

### <a name="urhosharp"></a>UrhoSharp

UrhoSharp es un motor 2D y 3D de alto nivel de desarrollo multiplataforma que puede usarse para crear animados escenas 3D y 2D para las aplicaciones con geometrías, material, luces y cámaras.

![](images/urhosharp.gif "UrhoSharp es un motor 2D y 3D de alto nivel de desarrollo multiplataforma que puede usarse para crear escenas 3D y 2D animados")

Consulte la [guías de UrhoSharp](~/graphics-games/urhosharp/index.md) para empezar a trabajar.

### <a name="additional-technology"></a>Tecnología adicional

Las tecnologías resaltadas arriba es sólo una muestra de las tecnologías disponibles. Otras tecnologías importantes incluyen:

- **Kit de Sprite** : Xamarin ofrece soporte técnico para el marco de juego de Spritekit de Apple, que proporciona acceso a toda la funcionalidad de la API nativa. Puesto que el Sprite Kit es tecnología creada por Apple, proporciona una integración profunda con el resto del ecosistema de iOS. Por supuesto, Sprite Kit no es multiplataforma, por lo que no puede usarse en Android. Para obtener más información sobre el uso de Sprite Kit, vea esta entrada:  [https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Kit de escena** : Xamarin también proporciona compatibilidad para el marco Scene Kit de Apple, lo que simplifica la implementación de gráficos 3D en aplicaciones de iOS. Kit de escena también es tecnología proporcionada por Apple, por lo que tiene la integración y consideraciones específicas de la plataforma mencionadas anteriormente para Spritekit. Para obtener más información sobre el Kit de escena, vea esta entrada: [https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (lo que significa abrir Kit de herramientas) proporciona acceso de bajo nivel OpenGL para iOS, Apple y Mac hardware. Para obtener más información sobre OpenTK, consulte la página principal en:  [http://www.opentk.com/](http://www.opentk.com/)

## <a name="related-links"></a>Vínculos relacionados

- [Guías de MonoGame](~/graphics-games/monogame/index.md)
- [Guías de UrhoSharp](~/graphics-games/urhosharp/index.md)
