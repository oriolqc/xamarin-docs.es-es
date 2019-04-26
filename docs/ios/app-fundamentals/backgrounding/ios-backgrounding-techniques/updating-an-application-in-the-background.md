---
title: Actualizar una aplicación de Xamarin.iOS en segundo plano
description: Este documento describe distintas formas para actualizar una aplicación de Xamarin.iOS que se encuentra en segundo plano, como supervisión de la región, captura de fondo y las notificaciones remotas.
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 835dccaea79467582f56fd4b8b6b3b8f42acd632
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392351"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>Actualizar una aplicación de Xamarin.iOS en segundo plano

Actualización en segundo plano es el proceso de reactivar una aplicación que se suspende o no se está ejecutando y actualizarlo con nuevo contenido. iOS proporciona tres opciones para actualizar contenido en segundo plano:

1.  *Supervisión de la región* y *servicio importante de cambios de ubicación* -según los cambios en la ubicación del usuario se actualiza en segundo plano de desencadenador de APIs con reconocimiento de ubicación. Estas API pueden usarse con precaución para actualizar el contenido en las aplicaciones basadas en ubicaciones que no son iOS 6, donde no hay disponibles otras opciones.
1.  *En segundo plano (iOS 7 +) captura* -un enfoque temporal para actualizar *no críticos* contenido que actualiza *con frecuencia* .
1.  *Notificaciones remotas (iOS 7 +)* : las aplicaciones que reciben notificaciones de inserción pueden usar las notificaciones para desencadenar actualizaciones de contenido en segundo plano. Este método puede usarse para actualizar con *importante, sujeto a limitación temporal* contenido que actualiza *esporádicamente* .


Las siguientes secciones describen los conceptos básicos de estas opciones.

## <a name="region-monitoring-and-significant-location-changes"></a>Supervisión de la región y cambios significativos de ubicación

iOS proporciona dos API con reconocimiento de ubicación con procesamiento en segundo plano capacidades:

1.  *Supervisión de la región* es el proceso de configuración de las regiones con los límites y reactivar el dispositivo cuando el usuario entra o sale de una región. Las regiones son circulares y pueden ser de distinto tamaño. Cuando el usuario cruza un límite de región, se activará el dispositivo para controlar el evento, normalmente mediante la activación de una notificación o iniciar una tarea. Supervisión de la región requiere GPS y aumenta la batería y el uso de datos.
1.  El *servicio importante de cambios de ubicación* es una opción más sencilla y ahorro de energía disponibles para dispositivos con radio de telefonía móvil. Una aplicación que escucha los cambios significativos de ubicación le notificará cuando el dispositivo cambia la celda towers. Este servicio se puede usar para activar una aplicación suspendida o terminada y proporciona una oportunidad para buscar nuevo contenido en segundo plano. Actividad en segundo plano se limita a unos 10 segundos, a menos que se empareja con un [tarea en segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .


Una aplicación no necesita la ubicación `UIBackgroundMode` para usar estas API con reconocimiento de ubicación. Porque iOS no realiza un seguimiento de los tipos de tareas que se pueden ejecutar cuando el dispositivo se ha activado por los cambios en la ubicación del usuario, estas API ofrecen una solución alternativa para actualizar el contenido en segundo plano en iOS 6. *Tenga en cuenta que se dibujará en los recursos de dispositivo desencadenar actualizaciones en segundo plano con las API basadas en ubicación y puede confundir a los usuarios que no entienden por qué una aplicación requiere acceso a su ubicación*. Tenga precaución al implementar la supervisión de la región o cambios significativos de ubicación para el procesamiento en aplicaciones que aún no está usando la API de ubicación en segundo plano.

Las aplicaciones mediante la supervisión de ubicación para el procesamiento en segundo plano exponer un defecto en iOS 6: si las necesidades de la aplicación no se ajustan a una categoría en segundo plano necesarios, tiene opciones de procesamiento en segundo plano limitadas. Con la introducción de dos nuevas API, *capturar en segundo plano* y *notificaciones remotas*, iOS 7 (y versiones posteriores) ofrece la oportunidad de procesamiento en segundo plano a más aplicaciones. Las dos secciones siguientes presentan estas nuevas API.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Captura de fondo (iOS 7 y versiones posteriores)

En iOS 6, una aplicación de escribir el primer plano necesité tiempo para cargar contenido nuevo, presenta brevemente los usuarios con contenido que ya han visto. Captura en segundo plano permite que las aplicaciones cargar datos nuevos *antes* un usuario inicia la aplicación y proporcionar al usuario con el contenido más actualizado.

Para implementar la captura de fondo, editar *Info.plist* y compruebe el **habilitar modos en segundo plano** y **captura de fondo** casillas de verificación:

 [![](updating-an-application-in-the-background-images/fetch.png "Edite el archivo Info.plist y Active las casillas de verificación Habilitar modos en segundo plano y fondo capturar")](updating-an-application-in-the-background-images/fetch.png#lightbox)

A continuación, en el `AppDelegate`, invalidar el `FinishedLaunching` método para establecer el intervalo de recuperación mínimo. En este ejemplo, permitimos el sistema operativo que decidir con qué frecuencia se recuperar contenido nuevo:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Por último, realizar la operación de captura invalidando el `PerformFetch` método en el `AppDelegate`y pase un *controlador de finalización*. El controlador de finalización es un delegado que toma un `UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Cuando hayamos terminados actualización del contenido, permitimos el sistema operativo sabe llamando al controlador de finalización con el estado adecuado. iOS ofrece tres opciones para el estado del controlador de finalización:

1.  `UIBackgroundFetchResult.NewData` -Se le llama cuando se han obtenido el contenido nuevo y se ha actualizado la aplicación.
1.  `UIBackgroundFetchResult.NoData` -Se le llama cuando la operación de captura para el contenido nuevo se realizara, pero no hay disponible ningún contenido.
1.  `UIBackgroundFetchResult.Failed` : Útil para controlar errores, se llama cuando la operación de captura no pudo recorrer.


Las aplicaciones que usan captura en segundo plano pueden realizar llamadas para actualizar la interfaz de usuario desde un segundo plano. Cuando el usuario abre la aplicación, la interfaz de usuario estará actualizada y mostrar el contenido nuevo. Esto también actualizará instantánea Mezclador de aplicación de la aplicación, por lo que el usuario puede ver cuando la aplicación tiene contenido nuevo.

> [!IMPORTANT]
> Una vez `PerformFetch` es llama, la aplicación tiene aproximadamente 30 segundos para iniciar la descarga de contenido nuevo y llamar al bloque de controlador de finalización. Si esto tarda demasiado tiempo, se terminará la aplicación. Considere el uso de captura en segundo plano con el _el servicio de transferencia en segundo plano_ al descargar medios u otros archivos grandes.


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

En el código de ejemplo anterior, permitimos que el sistema operativo decidir con qué frecuencia se recuperar contenido nuevo estableciendo el intervalo de recuperación mínimo en `BackgroundFetchIntervalMinimum`. iOS ofrece tres opciones para el intervalo de captura:

1.  `BackgroundFetchIntervalNever` : Indicar al sistema para no capturar nunca contenido nuevo. Úselo para desactivar la obtención en determinadas situaciones, como cuando el usuario no ha iniciado sesión. Este es el valor predeterminado para el intervalo de captura. 
1.  `BackgroundFetchIntervalMinimum` -Permitir que el sistema decidir con qué frecuencia se capturar basándose en patrones de usuario, duración de la batería, el uso de datos y las necesidades de otras aplicaciones.
1.  `BackgroundFetchIntervalCustom` -Si sabe con qué frecuencia se actualiza el contenido de la aplicación, puede especificar un intervalo de "suspensión" después de cada captura, durante el cual se evitará la aplicación de la captura de contenido nuevo. Una vez que ese intervalo esté activo, el sistema determinará cuándo se va a recuperar contenido.


Ambos `BackgroundFetchIntervalMinimum` y `BackgroundFetchIntervalCustom` se basan en el sistema para programar recopilaciones. Este intervalo es dinámica, para adaptarse a las necesidades del dispositivo, así como los hábitos del usuario individual. Por ejemplo, si un usuario protege una aplicación de todas las mañanas y otro comprueba cada hora, iOS garantizará el contenido está actualizado tanto a los usuarios cada vez que abran la aplicación.

Captura de fondo debe usarse para las aplicaciones que se actualizan frecuentemente con contenido no críticas. Para las aplicaciones con las actualizaciones críticas, se debe usar las notificaciones remotas. Notificaciones remotas se basan en segundo plano capturar y compartan el mismo controlador de finalización. Nos dedicaremos a continuación a las notificaciones remotas.

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>Notificaciones remotas (iOS 7 y versiones posteriores)

Las notificaciones Push son mensajes JSON enviados de un proveedor a un dispositivo por medio de la *Apple Push Notification service (APNs)*.

En iOS 6, una entrada notificaciones de inserción indica al sistema para alertar al usuario que ha sucedido algo interesante en una aplicación. Al hacer clic en la notificación extrae la aplicación salga del estado suspendido o terminado y la aplicación podría empezar a actualizar el contenido. iOS 7 (y versiones posteriores) amplía las notificaciones de inserción normales al ofrecer a las aplicaciones una oportunidad de actualizar el contenido en segundo plano *antes* notificar al usuario, por lo que el usuario puede abrir la aplicación y se presentan con nuevo contenido de forma inmediata.

Para implementar notificaciones remotas, editar *Info.plist* y compruebe el **habilitar modos en segundo plano** y **notificaciones remotas** casillas de verificación:

 [![](updating-an-application-in-the-background-images/remote.png "Establece el modo en segundo plano para habilitar modos en segundo plano y las notificaciones remotas")](updating-an-application-in-the-background-images/remote.png#lightbox)

A continuación, establezca el `content-available` marca en la notificación de inserción en 1. Esto permite que la aplicación saber para capturar el contenido nuevo antes de mostrar la alerta:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

En el *AppDelegate*, invalidar el `DidReceiveRemoteNotification` método para comprobar la carga de notificación para el contenido disponible y llamar el bloque del controlador de finalización adecuado:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Notificaciones remotas se deben usar para las actualizaciones frecuentes con contenido que es fundamental para la funcionalidad de la aplicación. Para obtener más información sobre las notificaciones remotas, vea el Xamarin [notificaciones Push en iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) guía.

> [!IMPORTANT]
> Dado que el mecanismo de actualización en las notificaciones remotas se basa en la captura de fondo, la aplicación debe iniciar la descarga de contenido nuevo y llamar el bloque de controlador de finalización dentro de 30 segundos después de recibir la notificación o iOS finalizará la aplicación. Considere la posibilidad de emparejar las notificaciones remotas con _el servicio de transferencia en segundo plano_ al descargar medios u otros archivos grandes en segundo plano.


### <a name="silent-remote-notifications"></a>Notificaciones remotas silenciosas

Notificaciones remotas son una manera sencilla para notificar a las aplicaciones de las actualizaciones y ponga en marcha la obtención de contenido nuevo, pero hay casos donde no es necesario notificar al usuario que algo ha cambiado. Por ejemplo, si un usuario marca un archivo para una sincronización, no necesitamos envíeles una notificación cada vez que actualiza el archivo. Sincronizar el archivo no es un evento sorprendente ni requiere atención inmediata del usuario. Los usuarios simplemente esperan que el archivo debe estar actualizado cuando lo abren.

En casos como el anterior, iOS permite enviar notificaciones push a enviarse en modo silencioso: es decir, sin una alerta. Para activar una notificación normal en una silenciosa, basta con quitar la alerta de la carga de notificación:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Límites de velocidad

La principal diferencia entre las notificaciones normales y silenciosas desde la perspectiva del desarrollador es que inserciones silenciosas son limitados. APNs, retrasará la entrega de inserciones silenciosas en el dispositivo si la tasa de inserción sea demasiado alta. Esto es para asegurarse de que las aplicaciones no agotan los recursos de dispositivo con demasiadas notificaciones silenciosas.

Sin embargo, Apple Push Notification Service permitirá notificaciones silenciosas "se incluyen" junto con una notificación remota normal o una respuesta persistente. Debido a notificaciones periódicas no tienen velocidad limitada, puede utilizar para insertar notificaciones silenciosas almacenadas copia de APNs en el dispositivo, como se muestra en el diagrama siguiente:

 [![](updating-an-application-in-the-background-images/silent.png "Notificaciones periódicas se pueden usar para insertar notificaciones silenciosas almacenadas de APNs en el dispositivo, como se muestra en este diagrama")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Anima a los desarrolladores para enviar notificaciones push silenciosas siempre que la aplicación requiere de Apple y permite el APN programar su entrega.


En esta sección, nos hemos tratado las diversas opciones para actualizar el contenido en segundo plano para ejecutar las tareas que no se ajustan a una categoría en segundo plano necesarias. Ahora, veamos algunas de estas API en acción.

 [Siguiente: Parte 4: tutoriales de procesamiento en segundo plano de iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
