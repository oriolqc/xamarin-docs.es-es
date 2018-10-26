---
title: Servicios de intención en Xamarin.Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1301f34ad1f7a0069c542ba81bf237a673fd239d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112863"
---
# <a name="intent-services-in-xamarinandroid"></a>Servicios de intención en Xamarin.Android

## <a name="intent-services-overview"></a>Información general de servicios de intención

Ambos había iniciado y enlaza los servicios se ejecuten en el subproceso principal, lo que significa que para mantener el rendimiento sin problemas, un servicio necesita para realizar el trabajo de forma asincrónica. Una de las maneras más sencillas para solucionar este problema es con un _patrón del procesador de cola de trabajo_, donde se coloca el trabajo se realice en una cola que atiende un único subproceso. 

El [ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/) es una subclase de la `Service` clase que proporciona una implementación específica de Android de este patrón. Que va a administrar el trabajo de puesta en cola, inicia un subproceso de trabajo para atender la cola, y las solicitudes de extracción desactivada la cola para ejecutarse en el subproceso de trabajo. Un `IntentService` silenciosamente dejarán de sí mismo y quitar el subproceso de trabajo cuando no hay ningún trabajo más en la cola.
 
Se envía un trabajo a la cola mediante la creación de un `Intent` y, a continuación, pasando que `Intent` a la `StartService` método.

No es posible detener o interrumpir la `OnHandleIntent` método `IntentService` mientras está en funcionamiento. Debido a este diseño, un `IntentService` deben mantenerse sin estado &ndash; no debe confiar en una conexión activa o la comunicación desde el resto de la aplicación. Un `IntentService` está pensado para statelessly procesar las solicitudes de trabajo.

Hay dos requisitos para la creación de subclases `IntentService`:

1. El nuevo tipo (creado por la creación de subclases `IntentService`) solo invalida la `OnHandleIntent` método.
2. El constructor para el nuevo tipo requiere una cadena que se usa para denominar el subproceso de trabajo que va a controlar las solicitudes. El nombre de este subproceso de trabajo se utiliza principalmente al depurar la aplicación.

El código siguiente muestra un `IntentService` implementación con invalidado `OnHandleIntent` método:

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

Trabajo se envía a un `IntentService` creando un `Intent` y, a continuación, llamar a la [ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/) método con dicha intención como un parámetro. La intención se pasarán al servicio como un parámetro en el `OnHandleIntent` método. Este fragmento de código es un ejemplo de enviar una solicitud de trabajo a una intención: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

El `IntentService` puede extraer los valores de la intención, como se muestra en este fragmento de código:  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>Vínculos relacionados

- [Clase IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
