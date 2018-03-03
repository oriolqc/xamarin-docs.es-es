---
title: "Introducción a Backgrounding en iOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: e46520122b54fabfe27f73370e2ab736a349f7f4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introducción a Backgrounding en iOS

iOS regula muy estrechamente procesamiento en segundo plano y ofrece tres enfoques para implementarlo:

-  **Registrar una tarea en segundo plano** -si una aplicación necesita completar una tarea importante, puede pedir a iOS no deben interrumpir la tarea cuando se mueve la aplicación en segundo plano. Por ejemplo, una aplicación deba finalizar la sesión de un usuario o terminar de descargar un archivo grande.
-  **Registrar como una aplicación de fondo necesario** -puede registrar una aplicación como un tipo específico de aplicación que se conoce, requisitos backgrounding específicos, como *Audio* , *VoIP* ,  *Accesorio externo* , *Newsstand* , y *ubicación* . Estas aplicaciones se permiten procesamiento privilegios siempre que están realizando tareas que están dentro de los parámetros del tipo de aplicación registrada en segundo plano continua.
-  **Habilitar actualizaciones en segundo plano** -aplicaciones pueden desencadenar actualizaciones en segundo plano con *región supervisión* o realizando escuchas para *cambios significativos de ubicación* . A partir de iOS 7, las aplicaciones también pueden registrar para actualizar el contenido en segundo plano utilizando *fondo capturar* o *notificaciones remoto* .


## <a name="application-states-and-application-delegate-methods"></a>Estados de la aplicación y los métodos de delegados de la aplicación

Antes de profundizar en el código de procesamiento de iOS en segundo plano, es necesario comprender afecta a cómo backgrounding el ciclo de vida de una aplicación de iOS.

El ciclo de vida de aplicación de iOS es una colección de Estados de la aplicación y métodos para moverse entre ellas. Una aplicación realiza la transición entre Estados según el comportamiento del usuario y los requisitos de la aplicación backgrounding. El movimiento se muestra en el diagrama siguiente:

 [ ![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagrama de Estados de la aplicación y los métodos de delegados de la aplicación")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png)

-  **No se está ejecutando** -aún no se ha iniciado la aplicación en el dispositivo.
-  **Ejecutar/activo** -la aplicación aparece en la pantalla y se está ejecutando código en primer plano.
-  **Inactiva** -la aplicación se interrumpe por una llamada telefónica entrante, texto u otras interrupciones.
-  **Backgrounded** -la aplicación se mueve en segundo plano y continúa ejecutando código en segundo plano.
-  **Suspended** : si la aplicación no tiene ningún código que se ejecuta en segundo plano, o si se ha completado todo el código, la aplicación será *Suspended* por el sistema operativo. Proceso de la aplicación suspendido se mantiene activo, pero la aplicación no se puede ejecutar cualquier código en este estado.
-  **Devolver para no ejecutar/finalización (raras)** : en ocasiones, se destruye el proceso de la aplicación y la aplicación vuelva a la *no se está ejecutando* estado. Esto sucede en situaciones de memoria insuficiente, o si el usuario termina manualmente la aplicación.


Desde la introducción de soporte técnico de multitarea, iOS rara vez termina aplicaciones inactivas y en su lugar mantiene sus procesos *Suspended* en la memoria. Mantiene el proceso de la aplicación se asegura de que la aplicación se inicia rápidamente la próxima vez que el usuario lo abre. También significa que las aplicaciones pueden mover libremente desde el *Suspended* estado nuevo en la *Backgrounded* estado sin dibujar en recursos del sistema. iOS 7 aprovecha esta característica con nuevas API que permiten a las aplicaciones pausar tareas en segundo plano cuando el dispositivo pasa al modo de suspensión, contenido de la actualización directamente desde el fondo sin interacción del usuario y mucho más. Trataremos las nuevas API de [iOS Backgrounding técnicas](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Métodos de ciclo de vida de aplicaciones

Cuando una aplicación cambia de estado, iOS notifica a la aplicación a través de métodos de evento en el `AppDelegate` clase:

-  `OnActivated` -Esto se denomina la primera vez que se inicia la aplicación, y cada vez que la aplicación vuelve a estar en primer plano. Éste es el lugar para colocar el código que debe ejecutarse cada vez que se abre la aplicación.
-  `OnResignActivation` -Si el usuario recibe una interrupción, como una llamada de teléfono o de texto, llama a este método y la aplicación se desactiva temporalmente. ¿El usuario debe aceptar la llamada de teléfono, la aplicación se enviarán al fondo.
-  `DidEnterBackground` -Se llama cuando la aplicación entra en el estado backgrounded, este método proporciona a una aplicación aproximadamente cinco segundos para preparar la finalización posibles. Esta hora se pueden usar para guardar datos de usuario y tareas y quitar información confidencial de la pantalla.
-  `WillEnterForeground` -Cuando un usuario vuelve a una aplicación backgrounded o suspendida e inicia en primer plano, `WillEnterForeground` se llamará. Este es el tiempo necesario para preparar la aplicación tomar al primer plano utilizando cualquier estado guardado durante `DidEnterBackground` .  `OnActivated` se llamará inmediatamente después de completar este método.
-  `WillTerminate` -La aplicación se cierra y se destruye su proceso. Este método solo se llamará si multitarea no está disponible en el dispositivo o la versión del sistema operativo, si hay poca memoria o si el usuario termina manualmente una aplicación backgrounded. Tenga en cuenta que no llame aplicaciones suspendidas que obtengan finalizó `WillTerminate` .


El siguiente diagrama ilustra cómo Estados de la aplicación y los métodos del ciclo de vida de encajar:

 [ ![](introduction-to-backgrounding-in-ios-images/image2.png "Este diagrama ilustra cómo Estados de la aplicación y los métodos del ciclo de vida de encajar")](introduction-to-backgrounding-in-ios-images/image2.png)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controles de usuario para Backgrounding en iOS

iOS 7 introdujeron varias características para proporcionar a los usuarios más control sobre el estado de backgrounded de una aplicación. El selector de la aplicación y la configuración de actualización en segundo plano aplicación afecta al ciclo de vida de la aplicación.

### <a name="app-switcher"></a>Selector de aplicación

El selector de la aplicación es una característica de control importantes introducida en iOS 7. Se inicia al hacer doble punteo en el **inicio** botón y muestra las aplicaciones cuyos procesos están activos:

 [ ![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Mover entre las aplicaciones mediante el selector de la aplicación")](introduction-to-backgrounding-in-ios-images/app-switcher-.png)

Mediante el selector de la aplicación, los usuarios puedan desplazarse a través de las instantáneas de todas las aplicaciones de backgrounded y suspendidas. Al puntear en una aplicación, inicia en primer plano. Deslizar rápidamente la quita la aplicación de segundo plano, finalizando su proceso. Echaremos un vistazo el selector de la aplicación en el [iOS demostración de ciclo de vida de la aplicación](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) en la sección siguiente.

> [!IMPORTANT]
> **Tenga en cuenta**: el modificador de la aplicación no se muestra una diferencia entre las aplicaciones backgrounded y suspendidas.



### <a name="background-app-refresh-settings"></a>Opciones de actualización de la aplicación de fondo

iOS 7 aumenta el control de usuario sobre el ciclo de vida de aplicación permitiendo a los usuarios optar por no backgrounding para aplicaciones [registrado para el procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Esto no evita que las aplicaciones se ejecuten tareas en segundo plano*.

Los usuarios pueden cambiar esta configuración, vaya a <span class="uiitem">configuración > General > actualización en segundo plano aplicación</span> y editar los privilegios backgrounding de una aplicación seleccionada. Si la aplicación de actualización en segundo plano se establece en off, la aplicación se suspende inmediatamente cuando se especifica el fondo e impide realizar cualquier procesamiento en segundo plano:

 [ ![](introduction-to-backgrounding-in-ios-images/settings-.png "Opciones de actualización de la aplicación de fondo")](introduction-to-backgrounding-in-ios-images/settings-.png)

Los programadores pueden comprobar el estado de aplicación de actualización en segundo plano con el `BackgroundRefreshStatus` API. Para obtener un ejemplo, consulte el [solo puede conducir al comprobar configuración de actualización de fondo](https://developer.xamarin.com/recipes/ios/multitasking/check_background_refresh_setting/).

Hemos analizado los conceptos básicos del ciclo de vida de aplicación de iOS y características para controlar el ciclo de vida de la aplicación. A continuación, veamos el ciclo de vida de aplicación de iOS en acción.

