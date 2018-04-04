---
title: Actualizar una aplicación en segundo plano
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 370d1cba71fa695e4e01dfb93241536a8df01b11
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="updating-an-application-in-the-background"></a>Actualizar una aplicación en segundo plano

Actualización en segundo plano es el proceso de reactivación de una aplicación que se ha suspendido o no se está ejecutando y actualizarla con el nuevo contenido. iOS proporciona tres opciones para actualizar contenido en segundo plano:

1.  *Supervisión de la región* y *servicio importante de cambios de ubicación* -fondo de desencadenador de APIs con reconocimiento de ubicación actualiza según los cambios en la ubicación del usuario. Estas API pueden usarse con precaución para actualizar el contenido en las aplicaciones no basadas en ubicación iOS 6, que no están disponibles otras opciones.
1.  *Fetch (iOS 7 +) en segundo plano* -un enfoque temporal para actualizar *no críticos* contenido que actualiza *con frecuencia* .
1.  *Notificaciones remotas (iOS 7 +)* -las aplicaciones que reciben notificaciones de inserción pueden usar las notificaciones para desencadenar actualizaciones contenido en segundo plano. Este método puede utilizarse para actualizar con *importante, sujeto a limitación temporal* contenido que actualiza *forma esporádica* .


En las siguientes secciones se describen los conceptos básicos de estas opciones.

## <a name="region-monitoring-and-significant-location-changes"></a>Supervisión de región y cambie la ubicación importantes

iOS proporciona dos API con reconocimiento de ubicación con backgrounding capacidades:

1.  *Supervisión de la región* es el proceso de configuración de regiones con los límites y activar el dispositivo cuando el usuario entra o sale de una región. Las regiones son circulares y pueden ser de tamaño variable. Cuando el usuario cruza un límite de región, el dispositivo reactivará para controlar el evento, normalmente mediante desencadenar una notificación o lo que se inicia una tarea. Supervisión de la región requiere GPS y aumenta la batería y el uso de datos.
1.  El *servicio importante de cambios de ubicación* es una opción más sencilla y ahorro de energía disponibles para los dispositivos con radios de telefonía móviles. Una aplicación que escucha los cambios significativos de ubicación le notificará cuando el dispositivo cambia torres de telefonía móvil. Este servicio puede utilizarse para reactivar una aplicación suspendida o terminada y proporciona una oportunidad para comprobar si el nuevo contenido en segundo plano. Actividad en segundo plano se limita a unos 10 segundos, a menos que se empareja con un [tarea en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .


Una aplicación no necesita la ubicación `UIBackgroundMode` usar estas API con reconocimiento de ubicación. Dado que iOS no realiza seguimiento de los tipos de tareas que se pueden ejecutar cuando el dispositivo se activa por los cambios en la ubicación del usuario, estas API ofrecen una solución alternativa para actualizar el contenido en segundo plano en iOS 6. *Tenga en cuenta que se dibujará en los recursos de dispositivo desencadenar actualizaciones en segundo plano con API basadas en ubicación y puede confundir a los usuarios que no entienden por qué una aplicación necesita acceder a su ubicación*. Usar discreción al implementar cambios significativos de ubicación o región de supervisión para el procesamiento en las aplicaciones que ya no están usando la API de ubicación en segundo plano.

Aplicaciones mediante la supervisión de ubicación para el procesamiento en segundo plano exponer un defecto en iOS 6: si las necesidades de la aplicación no quepan en una categoría de segundo plano necesarias, tiene opciones backgrounding limitadas. Con la introducción de dos nuevas API, *fondo capturar* y *notificaciones remoto*, iOS 7 (y versiones posteriores) ofrece la oportunidad de backgrounding a más aplicaciones. Las dos secciones siguientes presentan estas nuevas API.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Captura de fondo (iOS 7 y versiones posteriores)

En iOS 6, una aplicación escribe el primer plano necesita tiempo para cargar contenido nuevo, brevemente presentar a los usuarios con contenido que ya han visto. Captura de fondo permite a las aplicaciones cargar datos nuevos *antes de* un usuario inicia la aplicación y proporcionar al usuario con el contenido más actualizado.

Para implementar la captura de fondo, editar *Info.plist* y comprobar la **habilitar modos en segundo plano** y **captura de fondo** casillas de verificación:

 [![](updating-an-application-in-the-background-images/fetch.png "Editar el Info.plist y Active las casillas de verificación Habilitar modos en segundo plano y fondo capturar")](updating-an-application-in-the-background-images/fetch.png#lightbox)

Después, en la `AppDelegate`, invalidar la `FinishedLaunching` método para establecer el intervalo de captura mínimo. En este ejemplo, se permite el sistema operativo decidir con qué frecuencia se recuperar contenido nuevo:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Por último, realizar la operación de captura invalidando el `PerformFetch` método en el `AppDelegate`y pasando un *controlador de finalización*. El controlador de finalización es un delegado que toma un `UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Cuando hayamos terminados contenido de actualización, informaremos el sistema operativo mediante una llamada al controlador de finalización con el estado adecuado. iOS ofrece tres opciones para el estado del controlador de finalización:

1.  `UIBackgroundFetchResult.NewData` -Se llama cuando se hayan extraído contenido nuevo, y se ha actualizado la aplicación.
1.  `UIBackgroundFetchResult.NoData` -Se llama cuando la operación de captura para el nuevo contenido se dirigió a través, pero ningún contenido está disponible.
1.  `UIBackgroundFetchResult.Failed` -Resulta útil para el control de errores, se llama cuando la operación de captura no pudo recorrer.


Las aplicaciones que usan la captura de fondo pueden realizar llamadas para actualizar la interfaz de usuario desde el fondo. Cuando el usuario abre la aplicación, la interfaz de usuario será hasta la fecha y mostrar el contenido nuevo. Esto también actualizará instantánea de selector de aplicación de la aplicación, por lo que el usuario puede ver cuando la aplicación tiene contenido nuevo.

> [!IMPORTANT]
> Una vez `PerformFetch` es llama, la aplicación tiene aproximadamente 30 segundos para comenzar la descarga de nuevo contenido y llame a los del bloque de controlador de finalización. Si este proceso tarda demasiado largo, se terminará la aplicación. Considere el uso de capturar el fondo de la _servicio de transferencia en segundo plano_ al descargar multimedia u otros archivos grandes.


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

En el ejemplo de código anterior, se permite el sistema operativo decidir con qué frecuencia se capturar contenido nuevo al establecer el intervalo de captura mínima en `BackgroundFetchIntervalMinimum`. iOS ofrece tres opciones para el intervalo de captura:

1.  `BackgroundFetchIntervalNever` -Indica al sistema que nunca capturar contenido nuevo. Utilice esto para desactivar la obtención en determinadas situaciones, como cuando el usuario no ha iniciado sesión. Este es el valor predeterminado para el intervalo de captura. 
1.  `BackgroundFetchIntervalMinimum` -Permitir que el sistema decidir con qué frecuencia se capturar basándose en patrones de usuario, duración de la batería, el uso de datos y las necesidades de otras aplicaciones.
1.  `BackgroundFetchIntervalCustom` -Si sabe con qué frecuencia se actualiza el contenido de una aplicación, puede especificar un intervalo de "suspensión" después de cada captura, durante el cual la aplicación no podrán obtener contenido nuevo. Una vez que ese intervalo es hacia arriba, el sistema determinará cuándo va a recuperar contenido.


Ambos `BackgroundFetchIntervalMinimum` y `BackgroundFetchIntervalCustom` se basan en el sistema para programar capturas. Este intervalo es dinámico y adaptar a las necesidades del dispositivo, así como sus hábitos del usuario individual. Por ejemplo, si un usuario comprueba una aplicación cada mañana y otro comprueba cada hora, iOS garantizará que el contenido está actualizado tanto a los usuarios cada vez que abran la aplicación.

Captura de fondo debe utilizarse para las aplicaciones que se actualizarán con frecuencia con contenido no críticos. Para las aplicaciones con las actualizaciones críticas, se debe utilizar notificaciones remoto. Notificaciones remotas se basan en segundo plano capturar y compartan el mismo controlador de finalización. Podrá ver en profundidad las notificaciones remoto a continuación.

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>Notificaciones remotas (iOS 7 y versiones posteriores)

Notificaciones de inserción son mensajes JSON que se envían desde un proveedor a un dispositivo por medio de la *servicio de notificaciones Push de Apple (APN)*.

En iOS 6, las notificaciones de inserción entrante indica al sistema para alertar al usuario que ha sucedido algo interesante en una aplicación. Al hacer clic en la notificación extrae la aplicación fuera del estado suspendido o terminado, y la aplicación debería empezar a actualizar el contenido. iOS 7 (y versiones posteriores) extiende las notificaciones de inserción normales proporcionando una oportunidad de actualizar contenido en segundo plano de las aplicaciones *antes de* notificar al usuario, por lo que el usuario puede abrir la aplicación y se presentan con nuevo contenido de forma inmediata.

Para implementar notificaciones remotas, editar *Info.plist* y comprobar la **habilitar modos en segundo plano** y **notificaciones remotas** casillas de verificación:

 [![](updating-an-application-in-the-background-images/remote.png "Modo de fondo establecido en Habilitar modos en segundo plano y notificaciones remotas")](updating-an-application-in-the-background-images/remote.png#lightbox)

A continuación, establezca el `content-available` marca en la notificación de inserción en 1. Esto permite que la aplicación saber para capturar el contenido nuevo antes de mostrar la alerta:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

En el *AppDelegate*, invalide el `DidReceiveRemoteNotification` método para comprobar la carga de notificación para el contenido disponible y llamar el bloque de controlador de finalización adecuado:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Notificaciones remotas deben usarse para las actualizaciones poco frecuentes con contenido que es fundamental para la funcionalidad de la aplicación. Para obtener más información acerca de las notificaciones remotas, consulte el Xamarin [notificaciones Push en iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) guía.

> [!IMPORTANT]
> Dado que el mecanismo de actualización en las notificaciones de remoto se basa en la captura de fondo, la aplicación debe iniciar la descarga de nuevo contenido y llame a del bloque de controlador de finalización 30 segundos después de recibir la notificación, o iOS cerrará la aplicación. Considere la posibilidad de emparejamiento remota notificaciones con _servicio de transferencia en segundo plano_ al descargar multimedia u otros archivos grandes en segundo plano.


### <a name="silent-remote-notifications"></a>Notificaciones remotas silenciosas

Las notificaciones de remotas son una manera sencilla para notificar a las aplicaciones de las actualizaciones y comenzar de nuevo contenido de filas, pero hay casos donde no es necesario notificar al usuario que algo ha cambiado. Por ejemplo, si un usuario marca un archivo para una sincronización, no es necesario envíeles una notificación cada vez que actualiza el archivo. Una sincronización de archivo no es un evento sorprendente ni requiere atención inmediata del usuario. Los usuarios esperan simplemente que el archivo debe estar actualizado cuando lo abren.

En casos como la anterior, iOS permite que las notificaciones de inserción pueden enviar en modo silencioso: es decir, sin una alerta. Para activar una notificación regular en una silenciosa, basta con quitar la alerta de la carga de notificación:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Límites de velocidad

La principal diferencia entre las notificaciones normales y silenciosas desde la perspectiva del desarrollador es que inserciones silenciosas son velocidad limitada. APN retrasará la entrega de inserciones silenciosas en el dispositivo si la velocidad de inserción se dispara. Esto sirve para asegurarse de que las aplicaciones no agotan los recursos de dispositivo con demasiadas notificaciones silenciosas.

Sin embargo, APN permitirá silenciosas notificaciones "superponer" junto con una notificación remoto normal o una respuesta persistente. Debido a notificaciones periódicas no están limitado de velocidad, puede utilizar para insertar almacenadas seguridad notificaciones silenciosas de APNs en el dispositivo, como se muestra en el diagrama siguiente:

 [![](updating-an-application-in-the-background-images/silent.png "Notificaciones periódicas pueden utilizarse para insertar almacenadas notificaciones silenciosas de APNs en el dispositivo, tal y como se muestra en este diagrama")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple anima a los desarrolladores para enviar notificaciones de inserción silenciosa cada vez que la aplicación requiere y permite el APN programar su entrega.


En esta sección, hemos analizado las distintas opciones para actualizar contenido en segundo plano para ejecutar las tareas que no se ajustan a una categoría de segundo plano necesarias. Ahora, veamos algunas de estas API en acción.

 [Siguiente: Parte 4 - iOS Backgrounding tutoriales](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
