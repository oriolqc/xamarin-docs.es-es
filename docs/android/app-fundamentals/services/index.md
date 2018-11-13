---
title: Creación de servicios de Android
description: Esta guía trata los servicios de Xamarin.Android, que son componentes de Android que permiten que el trabajo se realiza sin una interfaz de usuario activas. Los servicios se usan con mucha frecuencia para las tareas que se realizan en segundo plano, como los cálculos mucho tiempo, descarga de archivos, reproducir música y así sucesivamente. Explica los diferentes escenarios más adecuados para servicios y se muestra cómo implementarlos tanto para realizar tareas en segundo plano de larga ejecución, así como para proporcionar una interfaz para las llamadas a procedimiento remoto.
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: dfc0e1cb7239381ef2f495b0f9774d390b0dc82e
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527201"
---
# <a name="creating-android-services"></a>Creación de servicios de Android

_Esta guía trata los servicios de Xamarin.Android, que son componentes de Android que permiten que el trabajo se realiza sin una interfaz de usuario activas. Los servicios se usan con mucha frecuencia para las tareas que se realizan en segundo plano, como los cálculos mucho tiempo, descarga de archivos, reproducir música y así sucesivamente. Explica los diferentes escenarios más adecuados para servicios y se muestra cómo implementarlos tanto para realizar tareas en segundo plano de larga ejecución, así como para proporcionar una interfaz para las llamadas a procedimiento remoto._

## <a name="android-services-overview"></a>Introducción a los servicios de Android

Aplicaciones móviles no son como las aplicaciones de escritorio. Equipos de escritorio tienen ingentes cantidades de recursos, como una fuente de alimentación conectado, espacio de almacenamiento, memoria y espacio en pantalla, dispositivos móviles no lo hace. Estas restricciones forzar las aplicaciones móviles para comportarse de manera diferente. Por ejemplo, la pequeña pantalla en un dispositivo móvil normalmente significa que solo una aplicación (es decir, la actividad) está visible en un momento. Otras actividades se mueven al fondo y se insertan en un estado suspendido, donde no se puede realizar cualquier trabajo. Sin embargo, sólo porque una aplicación Android está en segundo plano no significa que es imposible que la aplicación siga funcionando. 

Las aplicaciones de Android se componen de al menos uno de los siguientes cuatro componentes principales: _actividades_, _receptores de difusión_, _proveedores de contenido_y _Servicios_. Las actividades son la piedra angular de muchas excelentes aplicaciones de Android, ya que proporcionan la interfaz de usuario que permite al usuario interactuar con la aplicación. Sin embargo, cuando se trata de realizar simultáneas o trabajo en segundo plano, las actividades no son siempre la mejor opción.
 
El mecanismo principal para el trabajo en segundo plano en Android es el _servicio_. Un servicio de Android es un componente que está diseñado para realizar algún trabajo sin una interfaz de usuario. Un servicio podría descargar un archivo, reproducir música o aplicar un filtro a una imagen. También se pueden usar servicios para la comunicación entre procesos (_IPC_) entre las aplicaciones de Android. Por ejemplo, una aplicación de Android puede usar el servicio de Reproductor de música es desde otra aplicación o una aplicación podría exponer los datos (por ejemplo, información de contacto de una persona) a otras aplicaciones a través de un servicio. 

Servicios y su capacidad para realizar el trabajo en segundo plano, son cruciales para proporcionar una interfaz de usuario fluida y sin problemas. Todas las aplicaciones de Android tienen un _subproceso principal_ (también conocido como un _el subproceso de interfaz de usuario_) en que se ejecutan las actividades. Para que el dispositivo siga respondiendo, Android debe ser capaz de actualizar la interfaz de usuario a una velocidad de 60 fotogramas por segundo. Si una aplicación Android realiza demasiado trabajo en el subproceso principal, entonces Android coloque fotogramas, lo que a su vez hace que la interfaz de usuario aparece irregular (también a veces se denomina _janky_). Esto significa que debe completar todo el trabajo realizado en el subproceso de interfaz de usuario en el intervalo de tiempo entre los dos marcos, aproximadamente 16 milisegundos (1 segundo cada 60 fotogramas). 

Para solucionar este problema, un desarrollador puede usar subprocesos en una actividad para realizar algún trabajo que se bloquearía la interfaz de usuario. Sin embargo, esto podría producir problemas. Es muy posible que Android destruirá y volver a crear las distintas instancias de la actividad. Sin embargo, Android destruirá automáticamente los subprocesos, lo que podrían dar lugar a pérdidas de memoria. Un buen ejemplo de esto es cuando la [se gira el dispositivo](~/android/app-fundamentals/handling-rotation.md) &ndash; Android intentará destruye la instancia de la actividad y, a continuación, volver a crear una nueva:

![Cuando se gira el dispositivo, se destruye la instancia 1 y se crea la instancia 2](images/image-01.png)

Se trata de una posible pérdida de memoria &ndash; seguirán ejecutando el subproceso creado por la primera instancia de la actividad. Si el subproceso tiene una referencia a la primera instancia de la actividad, esto impedirá Android recopilar el objeto de elementos no utilizados. Sin embargo, se sigue creando la segunda instancia de la actividad (que a su vez podría crear un nuevo subproceso). Girar el dispositivo varias veces en una sucesión rápida puede agotar toda la RAM y forzar Android para terminar la aplicación completa para reclamar la memoria.

Como regla general, si el trabajo que se realizará debe durar más que una actividad, a continuación, un servicio debe crearse para llevar a cabo ese trabajo. Sin embargo, si el trabajo solo es aplicable en el contexto de una actividad, a continuación, crear un subproceso para realizar el trabajo puede ser más adecuado. Por ejemplo, la creación de una miniatura para ver una fotografía que se acaba de agregar a una aplicación de Galería fotográfica probablemente debe producirse en un servicio. Sin embargo, un subproceso podría ser más adecuado para reproducir la música que sólo debe ser escuchada mientras una actividad está en primer plano.

Trabajo en segundo plano puede dividirse en dos clasificaciones amplias:

1. **Tarea larga ejecución** &ndash; se trata de trabajo que está en curso hasta que se detiene explícitamente. Un ejemplo de un _tareas de larga ejecución_ es una aplicación que transmite por secuencias música o que debe supervisar los datos recopilados por un sensor. Aunque la aplicación no tiene ninguna interfaz de usuario visible, deben ejecutar estas tareas.
2. **Tareas periódicas** &ndash; (a veces se denomina un _trabajo_) una tarea periódica es aquel que es de relativamente corta duración (unos segundos) y se ejecuta según una programación (es decir, una vez al día durante una semana o quizás solo una vez en el próximos 60 segundos). Un ejemplo de esto es descargar un archivo desde internet o generar una miniatura de una imagen.

Hay cuatro tipos diferentes de servicios de Android:

* **Enlazar servicio** &ndash; un _enlazado servicio_ es un servicio que tiene algún otro componente (normalmente una actividad) enlazado a él. Un servicio dependiente proporciona una interfaz que permite el componente enlazado y el servicio interactúan entre sí. Una vez que existen más clientes enlazados al servicio, Android apagará el servicio. 

* **`IntentService`** &ndash; Un _`IntentService`_ es una subclase especializada de la `Service` clase que simplifica la creación de servicios y uso. Un `IntentService` está diseñado para controlar las llamadas individuales autónomas. A diferencia de un servicio, que al mismo tiempo se puede administrar varias llamadas, una `IntentService` es más parecido a un _procesador de la cola de trabajo_ &ndash; trabajo está en la cola y un `IntentService` procesa cada trabajo de uno a la vez en un único subproceso de trabajo. Normalmente, un`IntentService` no está enlazado a una actividad o un fragmento. 

* **Servicio iniciado** &ndash; A _servicio iniciado_ es un servicio que se ha iniciado por algún otro componente Android (por ejemplo, una actividad) y se ejecuta continuamente en segundo plano hasta que algo se indica explícitamente el servicio se detenga. A diferencia de un servicio dependiente, un servicio iniciado no tiene ningún cliente enlazado directamente a él. Por este motivo, es importante diseñar servicios iniciados por lo que puede reiniciar correctamente según sea necesario.

* **Servicio híbrido** &ndash; A _servicio híbrido_ es un servicio que tiene las características de un _servicio iniciado_ y un _enlazado servicio_. Puede iniciar un servicio híbrido cuando un componente que se enlaza a ella o se puede iniciar por algún evento. Un componente de cliente puede o no puede enlazarse al servicio híbrido. Un servicio híbrido se seguirá ejecutando hasta que explícitamente se le indica a detener, o hasta que no hay ningún cliente más enlazado a él.

Qué tipo de servicio que se usará es muy dependiente de los requisitos de la aplicación. Como regla general, un `IntentService` o un servicio dependiente son suficientes para la mayoría de las tareas que debe llevar a cabo una aplicación de Android, por lo que debe darse preferencia a uno de esos dos tipos de servicios. Un `IntentService` es una buena elección para las tareas "Monoestable", como la descarga de un archivo, mientras que un servicio dependiente sería adecuado cuando se requiere con frecuencia interacciones con un fragmento de actividad. 

Aunque la mayoría de los servicios se ejecuta en segundo plano, hay una subcategoría especial, conocida como un _servicio de primer plano_. Se trata de un servicio que se asigna una prioridad más alta (en comparación con un servicio normal) para realizar algún trabajo para el usuario (por ejemplo, reproducir música). 

También es posible ejecutar un servicio en su propio proceso en el mismo dispositivo, esto se denomina a veces un _servicio remoto_ o como un _servicio fuera de proceso_. Esto requiere más esfuerzo para crear, pero puede ser útil para cuando una aplicación necesita compartir la funcionalidad con otras aplicaciones y puede, en algunos casos, mejorar la experiencia del usuario de una aplicación. 

### <a name="background-execution-limits-in-android-80"></a>Límites de ejecución en segundo plano en Android 8.0

A partir de Android 8.0 (nivel de API 26), una aplicación Android ya no tiene la capacidad de ejecutar libremente en segundo plano. En primer plano, una aplicación puede iniciar y ejecutar los servicios sin restricciones. Cuando una aplicación se mueve en segundo plano, Android le dará a la aplicación de una cierta cantidad de tiempo para iniciar y usar los servicios. Una vez transcurrido ese tiempo, la aplicación ya no puede iniciar los servicios y se terminarán todos los servicios que se iniciaron. En este momento no es posible que la aplicación realizar cualquier trabajo. Android considera que una aplicación puede estar en primer plano, si se cumple una de las condiciones siguientes:

* Hay una actividad visible (iniciado o en pausa).
* La aplicación ha iniciado un servicio de primer plano.
* Otra aplicación está en primer plano y utiliza los componentes de una aplicación que de lo contrario en segundo plano. Un ejemplo de esto es si una aplicación, que se encuentra en primer plano, se enlaza a un servicio suministrado por aplicación B. aplicación B, a continuación, también sería considera en primer plano y no termina con Android para que se está en segundo plano.

Existen algunas situaciones donde, aunque es una aplicación en segundo plano, Android reactivar la aplicación y relajar estas restricciones durante unos minutos, que permite a la aplicación realizar algún trabajo:
* La aplicación recibe una mensaje en la nube de Firebase de prioridad alta.
* La aplicación recibe una difusión. 
* La aplicación recibe una ejecuta un `PendingIntent` en respuesta a una notificación.

Las aplicaciones existentes de Xamarin.Android que tenga que cambiar la forma de realizar tareas en segundo plano para evitar cualquier problema que pueda surgir en Android 8.0. Estas son algunas alternativas prácticas para un servicio de Android:

* **Programar el trabajo para ejecutar en segundo plano mediante el programador de trabajos de Android o el [distribuidor de trabajo de Firebase](~/android/platform/firebase-job-dispatcher.md)**  &ndash; estas dos bibliotecas proporcionan un marco para aplicaciones separar los trabajos en segundo plano en _trabajos_, una unidad de trabajo discreta. Aplicaciones, a continuación, pueden programar el trabajo con el sistema operativo junto con algunos criterios sobre cuándo puede ejecutar el trabajo.
* **Iniciar el servicio en primer plano** &ndash; un servicio de primer plano es útil para cuando la aplicación debe realizar alguna tarea en segundo plano y el usuario que deba periódicamente interactuar con esa tarea. El servicio de primer plano mostrará una notificación persistente para que el usuario sea consciente de que la aplicación se está ejecutando una tarea en segundo plano y también proporciona una manera de supervisar o interactuar con la tarea. Un ejemplo de esto sería una aplicación de podcasting reproducir un podcast para el usuario o quizás descarga un episodio del podcast para que tenían más adelante. 
* **Usar una mensaje de nube de Firebase (FCM) de alta prioridad** &ndash; Android cuando recibe una prioridad alta FCM para una aplicación, permitirá que esa aplicación ejecutar servicios en segundo plano durante un breve período de tiempo. Esto sería una buena alternativa a tener un servicio en segundo plano que sondea una aplicación en segundo plano. 
* **Aplazar el trabajo de la aplicación cuando se pone en primer plano** &ndash; si ninguna de las soluciones anteriores son viable, las aplicaciones deben desarrollar su propia manera de pausar y reanudar el trabajo cuando la aplicación se pone en primer plano.

## <a name="related-links"></a>Vínculos relacionados

* [Limita la ejecución en segundo plano de Android Oreo](https://www.youtube.com/watch?v=Pumf_4yjTMc)
