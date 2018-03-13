---
title: Crear servicios de Android
description: "Esta guía describen los servicios de Xamarin.Android, que son componentes de Android que permiten que el trabajo puede realizar sin una interfaz de usuario activo. Los servicios se utilizan con frecuencia para las tareas que se realizan en segundo plano, como los cálculos mucho tiempo, descarga de archivos, reproducir música y así sucesivamente. Explica los diferentes escenarios apropiados para servicios y muestra cómo se implementan tanto para llevar a cabo tareas de larga duración en segundo plano, así como para proporcionar una interfaz para las llamadas a procedimiento remoto."
ms.topic: article
ms.prod: xamarin
ms.assetid: BA371A59-6F7A-F62A-02FC-28253504ACC9
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 5dc1fb0fb02014e123b3a161394155bde725f288
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="creating-android-services"></a>Crear servicios de Android

_Esta guía describen los servicios de Xamarin.Android, que son componentes de Android que permiten que el trabajo puede realizar sin una interfaz de usuario activo. Los servicios se utilizan con frecuencia para las tareas que se realizan en segundo plano, como los cálculos mucho tiempo, descarga de archivos, reproducir música y así sucesivamente. Explica los diferentes escenarios apropiados para servicios y muestra cómo se implementan tanto para llevar a cabo tareas de larga duración en segundo plano, así como para proporcionar una interfaz para las llamadas a procedimiento remoto._

## <a name="android-services-overview"></a>Introducción a los servicios de Android

Aplicaciones móviles no son similares a las aplicaciones de escritorio. Escritorios tienen gran cantidad de recursos, como el espacio real en pantalla, memoria, espacio de almacenamiento y una fuente de alimentación conectado, no así los dispositivos móviles. Estas restricciones forzar aplicaciones móviles para comportarse de manera diferente. Por ejemplo, la pantalla pequeña en un dispositivo móvil normalmente significa que solo una aplicación (es decir, la actividad) está visible en el momento. Otras actividades se mueven al fondo y se inserta en un estado suspendido donde no pueden realizar cualquier trabajo. Sin embargo, solo porque una aplicación Android está en segundo plano no significa que es imposible para la aplicación seguir trabajando. 

Aplicaciones Android se componen de al menos uno de los siguientes cuatro componentes principales: _actividades_, _difusión receptores_, _proveedores de contenido_y _Servicios_. Las actividades son la piedra angular de muchas aplicaciones de Android excelentes ya que proporcionan la interfaz de usuario que permite al usuario interactuar con la aplicación. Sin embargo, cuando se trata de realizar simultánea o de trabajo en segundo plano, las actividades no son siempre la mejor opción.
 
El mecanismo principal para el trabajo en segundo plano en Android es el _servicio_. Un servicio Android es un componente que está diseñado para realizar algún trabajo sin una interfaz de usuario. Un servicio puede descargar un archivo, reproducir música o aplicar un filtro a una imagen. Servicios también pueden usarse para la comunicación entre procesos (_IPC_) entre aplicaciones Android. Por ejemplo, una aplicación Android podría usar el servicio de Reproductor de música que esté en otra aplicación o una aplicación podría exponer los datos (por ejemplo, información de contacto de una persona) a otras aplicaciones a través de un servicio. 

Servicios y su capacidad para realizar tareas en segundo plano, son cruciales para proporcionar una interfaz de usuario suave y fluida. Todas las aplicaciones de Android tienen un _subproceso principal_ (también conocido como un _subproceso de interfaz de usuario_) que se ejecutan las actividades. Para que el dispositivo siga respondiendo, Android debe ser capaz de actualizar la interfaz de usuario a la velocidad de 60 fotogramas por segundo. Si una aplicación Android realiza mucho trabajo en el subproceso principal, Android, quitará los marcos, lo que a su vez hace que la interfaz de usuario aparece irregular (también conocida como _janky_). Esto significa que se debe completar todo el trabajo realizado en el subproceso de interfaz de usuario en el intervalo de tiempo entre los dos marcos, aproximadamente 16 milisegundos (1 segundo cada 60 marcos). 

Para solucionar este problema, un desarrollador puede usar subprocesos en una actividad para realizar algún trabajo que podría bloquear la interfaz de usuario. Sin embargo, esto podría causar problemas. Es muy posible que Android destruirá y volver a crear las instancias de la actividad. Sin embargo, Android destruirá automáticamente los subprocesos, lo que pudieron dar lugar a pérdidas de memoria. Un buen ejemplo de esto es cuando la [dispositivo se gira](~/android/app-fundamentals/handling-rotation.md) &ndash; Android intentará destruye la instancia de la actividad y, a continuación, vuelva a crear uno nuevo:

![Cuando se gira el dispositivo, se destruye la instancia 1 y se crea la instancia 2](images/image-01.png)

Se trata de una posible pérdida de memoria &ndash; seguirán ejecutando el subproceso creado por la primera instancia de la actividad. Si un subproceso tiene una referencia a la primera instancia de la actividad, esto impedirá Android recopilar el objeto de elementos no utilizados. Sin embargo, la segunda instancia de la actividad todavía se crea (que a su vez podría crear un nuevo subproceso). Girar el dispositivo varias veces en una sucesión rápida, se puede agotar toda la RAM y forzar Android para terminar de toda la aplicación para reclamar memoria.

Como regla general, si el trabajo que se realice debe sobreviven a una actividad, a continuación, un servicio se debe crear para realizar el trabajo. Sin embargo, si el trabajo solo es aplicable en el contexto de una actividad, a continuación, crear un subproceso para realizar el trabajo puede ser más adecuado. Por ejemplo, la creación de una vista en miniatura para una foto que acaba de agregar a una aplicación de la Galería fotográfica probablemente debe producirse en un servicio. Sin embargo, un subproceso puede ser más adecuado para reproducir la música que solo debe escucharán mientras una actividad se encuentra en primer plano.

Trabajo en segundo plano puede dividirse en dos clasificaciones amplias:

1. **Tarea de ejecución de larga duración** &ndash; se trata de trabajo que está en curso hasta que detenga explícitamente. Un ejemplo de un _tareas de larga ejecución_ es una aplicación que se transmite por secuencias música o que debe supervisar los datos recopilados de un sensor. Aunque la aplicación no tiene ninguna interfaz de usuario visible, deben ejecutar estas tareas.
2. **Las tareas periódicas de** &ndash; (a veces se denomina un _trabajo_) una tarea periódica es uno de relativamente cortas en duración (unos segundos) y se ejecuta según una programación (es decir, una vez al día durante una semana o quizás solo una vez en el próximos 60 segundos). Un ejemplo de esto es descargar un archivo de internet o generar una miniatura de una imagen.

Hay cuatro tipos diferentes de servicios Android:

* **Enlazar servicio** &ndash; A _enlazados servicio_ es un servicio que tiene otros componentes (normalmente una actividad) enlazado a él. Un servicio dependiente proporciona una interfaz que permite que el componente enlazado y al servicio interactuar entre sí. Una vez que no hay ningún cliente más enlazado al servicio, Android apagará el servicio.

* **`IntentService`** &ndash; Un  _`IntentService`_  es una subclase especializada de la `Service` clase que simplifica la creación de servicios y uso. Un `IntentService` está diseñado para controlar las llamadas autónomas individuales. A diferencia de un servicio que simultáneamente se puede satisfacer varias llamadas, un `IntentService` más parecido a un _procesador de colas de trabajo_ &ndash; trabajo está en la cola y un `IntentService` procesa cada trabajo uno a la vez en un único subproceso de trabajo. Normalmente, un`IntentService` no está enlazado a una actividad o un fragmento. 

* **Servicio iniciado** &ndash; A _iniciado el servicio_ es un servicio que se ha iniciado por otros componentes Android (por ejemplo, una actividad) y se ejecuta continuamente en segundo plano hasta que algo indica explícitamente el servicio que se va a detener. A diferencia de un servicio dependiente, un servicio iniciado no tiene ningún cliente enlazado directamente a él. Por esta razón, es importante diseñar servicios iniciados por lo que pueden reiniciar correctamente según sea necesario.

* **Servicio híbrido** &ndash; A _servicio híbrido_ es un servicio que tiene las características de un _iniciado el servicio_ y un _enlazados servicio_. Un servicio híbrido puede iniciarse por cuando un componente se enlaza a él o que se puede iniciar por algún evento. Un componente de cliente puede o no se puede enlazar al servicio híbrido. Un servicio híbrido seguirán ejecutándose hasta que se indique explícitamente que deba detener, o hasta que no haya ningún cliente más enlazado a él.

Qué tipo de servicio que se va a usar es muy depende de los requisitos de la aplicación. Como regla general, un `IntentService` o un servicio dependiente son suficientes para la mayoría de las tareas que debe llevar a cabo una aplicación Android, por lo que debe darse preferencia a uno de los dos tipos de servicios. Un `IntentService` es una buena elección para "un disparo" tareas, como descargar un archivo, mientras que un servicio dependiente es adecuado cuando se requiere la interacción frecuente con un fragmento de actividad. 

Aunque la mayoría de los servicios se ejecuta en segundo plano, hay una subcategoría especial conocida como un _servicio de primer plano_. Se trata de un servicio que se asigna una prioridad más alta (en comparación con un servicio normal) para realizar algún trabajo para el usuario (por ejemplo, para reproducir música). 

También es posible ejecutar un servicio en su propio proceso en el mismo dispositivo, esto se conoce a veces como un _servicio remoto_ o como un _servicio fuera de proceso_. Esto requiere más esfuerzo para crear, pero puede ser útil para cuando se necesita una aplicación compartir la funcionalidad con otras aplicaciones y puede, en algunos casos, mejorar la experiencia del usuario de una aplicación. 

Cada uno de estos servicios tiene sus propias características y comportamientos y, por lo que se tratarán con más detalle en las guías de sus propios.
