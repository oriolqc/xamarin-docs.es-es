---
title: Introducción al procesamiento en segundo plano en iOS
description: 'Este documento describe el procesamiento en segundo plano en iOS: estados de la aplicación, los métodos del ciclo de vida de aplicación y actualización de aplicación en segundo plano.'
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/24/2018
ms.openlocfilehash: c533dd54e3b6b11465cfd7daf5b9a93265dbe7b7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169767"
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introducción al procesamiento en segundo plano en iOS

iOS regula muy estrechamente procesamiento en segundo plano y ofrece tres enfoques para implementarlo:

-  **Registrar una tarea en segundo plano** -si una aplicación necesita completar una tarea importante, puede pedir a iOS no para interrumpir la tarea cuando la aplicación pase a segundo plano. Por ejemplo, una aplicación podría necesitar finalizar la sesión de un usuario o terminar de descargar un archivo grande.
-  **Registrar como una aplicación en segundo plano necesarias** -puede registrar una aplicación como un tipo específico de aplicación que tenga conocidos, los requisitos específicos de procesamiento en segundo plano, como *Audio* , *VoIP* ,  *Accesorios externos* , *Newsstand* , y *ubicación* . Estas aplicaciones se permiten en segundo plano continua procesamiento privilegios siempre que se llevan a cabo las tareas que están dentro de los parámetros del tipo de aplicación registrados.
-  **Habilitar las actualizaciones en segundo plano** -aplicaciones pueden desencadenar actualizaciones en segundo plano con *región supervisión* o realizando escuchas para *cambios significativos de ubicación* . A partir de iOS 7, también pueden registrar las aplicaciones para actualizar el contenido en segundo plano con *capturar en segundo plano* o *notificaciones remotas* .


## <a name="application-states-and-application-delegate-methods"></a>Estados de la aplicación y los métodos de delegado de aplicación

Antes de profundizar en el código de procesamiento de iOS en segundo plano, es necesario comprender el ciclo de vida de una aplicación de iOS de afecta a cómo procesamiento en segundo plano.

El ciclo de vida de aplicación de iOS es una colección de Estados de la aplicación y los métodos para moverse entre ellas. Una aplicación realiza la transición entre Estados según el comportamiento del usuario y los requisitos de la aplicación backgrounding. El movimiento se muestra en el diagrama siguiente:

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagrama de Estados de la aplicación y los métodos de delegado de aplicación")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **No se está ejecutando** -aún no se ha iniciado la aplicación en el dispositivo.
-  **Ejecución/activo** -la aplicación está en la pantalla y está ejecutando código en primer plano.
-  **Inactivo** -la aplicación se interrumpe por una llamada telefónica, texto u otra interrupción.
-  **Pasa a segundo plano** -la aplicación se mueve en segundo plano y continúa ejecutando código en segundo plano.
-  **Suspended** : si la aplicación no tiene ningún código que se ejecutará en segundo plano, o si se ha completado todo el código, la aplicación estará *Suspended* por el sistema operativo. Se mantiene activo el proceso de la aplicación suspendida, pero la aplicación no puede ejecutar cualquier código en este estado.
-  **Volver a no ejecución/finalización (raras)** : en ocasiones, se destruye el proceso de la aplicación y devuelve la aplicación a la *no se está ejecutando* estado. Esto sucede en situaciones de escasez de memoria, o si el usuario cierra manualmente la aplicación.


Desde la introducción de soporte técnico de multitarea, rara vez finaliza inactivas aplicaciones iOS y en su lugar mantiene sus procesos *Suspended* en memoria. Mantiene el proceso de la aplicación, se garantiza que la aplicación se inicia rápidamente la próxima vez que el usuario lo abre. También significa que las aplicaciones se pueden mover libremente desde el *Suspended* estado en el *Backgrounded* estado sin recursos del sistema de dibujo. iOS 7 aprovecha esta característica con nuevas API que permiten que las aplicaciones hacer una pausa de tareas en segundo plano cuando el dispositivo entra en suspensión, el contenido de actualización directamente desde el plano sin interacción del usuario y mucho más. Trataremos las nuevas API en [técnicas de procesamiento en segundo plano de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Métodos de ciclo de vida de aplicaciones

Cuando una aplicación cambia de estado, iOS notifica a la aplicación a través de métodos de evento en el `AppDelegate` clase:

-  `OnActivated` -Esto se denomina la primera vez que se inicia la aplicación, y cada vez que la aplicación vuelve al primer plano. Este es el lugar para colocar el código que debe ejecutarse cada vez que se abre la aplicación.
-  `OnResignActivation` -Si el usuario recibe una interrupción, como un texto o llamada de teléfono, se llama a este método y la aplicación se ha desactivado temporalmente. ¿El usuario debe aceptar la llamada de teléfono, la aplicación se enviará al fondo.
-  `DidEnterBackground` -Se le llama cuando la aplicación entra en el estado backgrounded, este método proporciona una aplicación unos cinco segundos para preparar la posible finalización. Utilice esta vez para guardar los datos de usuario y tareas y quitar información confidencial de la pantalla.
-  `WillEnterForeground` -Cuando un usuario vuelve a una aplicación pasa a segundo plano o suspendida y lo inicia en primer plano, `WillEnterForeground` recibe la llamada. Se trata del tiempo para preparar la aplicación para aprovechar el primer plano por cualquier estado guardado durante la rehidratación `DidEnterBackground` .  `OnActivated` se llamará inmediatamente después de completa este método.
-  `WillTerminate` -La aplicación se cierra y se destruye su proceso. Este método se llama solo si no está disponible en el dispositivo o la versión del sistema operativo, la multitarea si hay poca memoria o si el usuario cierra manualmente una aplicación backgrounded. Tenga en cuenta que no llame las aplicaciones suspendidas que termine `WillTerminate` .


El siguiente diagrama ilustra cómo Estados de la aplicación y los métodos del ciclo de vida funcionan conjuntamente:

 [![](introduction-to-backgrounding-in-ios-images/image2.png "Este diagrama ilustra cómo Estados de la aplicación y los métodos del ciclo de vida funcionan conjuntamente")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controles de usuario para el procesamiento en segundo plano en iOS

iOS 7 presentó varias funciones para proporcionar a los usuarios más control sobre el estado de backgrounded de la aplicación. El modificador de la aplicación y la configuración de actualización de aplicación en segundo plano afectan el ciclo de vida de la aplicación.

### <a name="app-switcher"></a>Selector de la aplicación

El modificador de la aplicación es una característica de control más importante introducida en iOS 7. Se inicia pulsando dos veces la **inicio** botón y muestra las aplicaciones cuyos procesos están activos:

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Mover entre aplicaciones mediante el modificador de la aplicación")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

Mediante el modificador de la aplicación, los usuarios pueden desplazarse por las instantáneas de todas las aplicaciones suspendidas y pasa a segundo plano. Al puntear en una aplicación, inicia en primer plano. Deslice el dedo hacia la quita la aplicación de segundo plano, terminar su proceso. Tomamos una visión más detallada para el modificador de la aplicación en el [demostración de ciclo de vida de la aplicación de iOS](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) en la sección siguiente.

> [!IMPORTANT]
> El modificador de la aplicación no se muestra una diferencia entre las aplicaciones suspendidas y pasa a segundo plano.



### <a name="background-app-refresh-settings"></a>Configuración de actualización de la aplicación en segundo plano

iOS 7 aumenta el control de usuario sobre el ciclo de vida de la aplicación, ya que permite a los usuarios participar en procesamiento en segundo plano para aplicaciones [registrado para el procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Esto no impide que las aplicaciones se ejecuten tareas en segundo plano*.

Los usuarios pueden cambiar esta configuración, vaya a <span class="uiitem">configuración > General > aplicación de actualización en segundo plano</span> y edición de los privilegios backgrounding de una aplicación seleccionada. Si la aplicación de actualización en segundo plano se establece en off, la aplicación se suspende inmediatamente después de escribir el fondo e impide realizar cualquier procesamiento en segundo plano:

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Configuración de actualización de la aplicación en segundo plano")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Los programadores pueden comprobar el estado de la aplicación de actualización en segundo plano con el `BackgroundRefreshStatus` API. Para obtener un ejemplo, consulte el [receta comprobar configuración de actualización en segundo plano](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting).

Nos hemos tratado los conceptos básicos de las características para controlar el ciclo de vida de aplicación y ciclo de vida de aplicación de iOS. A continuación, vamos a ver el ciclo de vida de aplicación de iOS en acción.

