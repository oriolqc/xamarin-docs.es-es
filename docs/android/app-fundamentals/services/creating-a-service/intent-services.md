---
title: Servicios de intención en Xamarin.Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 80849213649707615f8bd8e941e1a51c6b54e76e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="intent-services-in-xamarinandroid"></a>Servicios de intención en Xamarin.Android

## <a name="intent-services-overview"></a>Introducción a los servicios de intención

Ambos se iniciaron y enlazado se ejecutan en el subproceso principal, lo que significa que para mantener el rendimiento suave, un servicio necesita para realizar el trabajo de forma asincrónica los servicios. Una de las maneras más sencillas para solucionar este problema es con un _patrón de procesador de cola de trabajo_, donde el trabajo que se va a realizarse se coloca en una cola es atendida por un solo subproceso. 

El [ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/) es una subclase de la `Service` clase que proporciona una implementación específica Android de este patrón. Que va a administrar el trabajo de puesta en cola, iniciar un subproceso de trabajo a la cola de servicio y las solicitudes de extracción desactivada la cola que se ejecutará en el subproceso de trabajo. Un `IntentService` silenciosamente detendrá propio y quitará el subproceso de trabajo cuando no hay ningún trabajo más en la cola.
 
Trabajo se envía a la cola mediante la creación de un `Intent` y, a continuación, pasar que `Intent` a la `StartService` método.

No es posible detener o interrumpir la `OnHandleIntent` método `IntentService` mientras está en funcionamiento. Debido a este diseño, un `IntentService` deben mantenerse sin estado &ndash; no debe confiar en una conexión activa o la comunicación desde el resto de la aplicación. Un `IntentService` está pensado para statelessly procesar las solicitudes de trabajo.

Hay dos requisitos para la creación de subclases `IntentService`:

1. El nuevo tipo (creado por subclases `IntentService`) sólo las invalidaciones el `OnHandleIntent` método.
2. El constructor para el nuevo tipo requiere una cadena que se utiliza para denominar el subproceso de trabajo que va a controlar las solicitudes. El nombre de este subproceso de trabajo se utiliza principalmente al depurar la aplicación.

El código siguiente muestra un `IntentService` implementación con el invalidado `OnHandleIntent` método:

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }
    
    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

Trabajo se envía a un `IntentService` creando un `Intent` y, a continuación, llamar a la [ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/) método con dicha intención como un parámetro. La intención se pasarán al servicio como un parámetro en el `OnHandleIntent` método. Este fragmento de código es un ejemplo de enviar una solicitud de trabajo a un intento de: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

La `IntentService` puede extraer los valores de la intención, como se muestra en este fragmento de código:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>Vínculos relacionados

- [IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
