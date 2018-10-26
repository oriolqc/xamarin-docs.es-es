---
title: Receptores de difusión en Xamarin.Android
description: Esta sección describe cómo utilizar un receptor de difusión.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 51bd3dd4c27dce19344f7660c31a0d4e741e1ad4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121144"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Receptores de difusión en Xamarin.Android

_Esta sección describe cómo utilizar un receptor de difusión._

## <a name="broadcast-receiver-overview"></a>Información general de receptor de difusión

Un _receptor de difusión_ es un componente de Android que permite que una aplicación responder a mensajes (Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) que se difunden por el sistema operativo Android o por una aplicación. Siguen las difusiones un _suscripción-publicación_ modelo &ndash; una difusión que se publican y recibidos por los componentes que están interesados en el evento hace que un evento.

Android identifica dos tipos de difusiones:

* **Difusión explícita** &ndash; estos tipos de las difusiones de destino una aplicación específica. El uso más común de una difusión explícita es iniciar una actividad. Un ejemplo de una difusión explícita cuando una aplicación necesita para marcar un número de teléfono; enviará una intención que tenga como destino la aplicación de teléfono en Android y pasada en el número de teléfono que se deben marcar. Android, a continuación, enrutará la intención a la aplicación de teléfono.
* **Difusión implícita** &ndash; estas difusiones se envían a todas las aplicaciones en el dispositivo. Un ejemplo de una difusión implícita es el `ACTION_POWER_CONNECTED` intención. Esta intención se publica cada vez que Android detecta que la batería en el dispositivo está cargando. Android enrutará esta intención a todas las aplicaciones que se han registrado para este evento.

El receptor de difusión es una subclase de la `BroadcastReceiver` tipo y se deben invalidar el [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) método. Se ejecutará Android `OnReceive` en el subproceso principal, por lo que este método debe diseñarse para ejecutar rápidamente. Debe tener cuidado al generar subprocesos en `OnReceive` ya que Android puede finalizar el proceso cuando finaliza el método. Si un receptor de difusión debe realizar el trabajo de larga ejecución, a continuación, se recomienda programar una _trabajo_ utilizando el `JobScheduler` o _distribuidor de trabajo de Firebase_. Programar el trabajo con un trabajo se tratarán en una guía independiente.

Un _filtro de intento_ se usa para registrar un receptor de difusión para que Android puede enrutar correctamente los mensajes. Se puede especificar el filtro de intento en tiempo de ejecución (Esto se denomina a veces un _receptor contexto registrado_ o como _registro dinámico_) o puede ser definido estáticamente en el manifiesto de Android (un _registrado por el manifiesto de receptor_). Xamarin.Android proporciona un C# atributo, `IntentFilterAttribute`, que registrarán estáticamente el filtro de intento (Esto se explicará con más detalle más adelante en esta guía). A partir de Android 8.0, no es posible que una aplicación registre estáticamente para una difusión implícita.

La principal diferencia entre el receptor del manifiesto registrados y el receptor contexto registrado es que un receptor de contexto registrado solo responderá a las difusiones mientras se ejecuta una aplicación, mientras que un receptor registrado por el manifiesto puede responder a difunde aun cuando la aplicación no se está ejecutando.  

Hay dos conjuntos de API para administrar un receptor de difusión y enviar difusiones:

1. **`Context`** &ndash; La `Android.Content.Context` clase puede usarse para registrar un receptor de difusión para responder a eventos de todo el sistema. El `Context` también se usa para publicar las difusiones de todo el sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; Se trata de una API que está disponible a través de la [paquete NuGet de biblioteca de compatibilidad de Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Esta clase se utiliza para mantener las difusiones y receptores de difusión de forma aislados en el contexto de la aplicación que está usando. Esta clase puede ser útil para evitar que otras aplicaciones de responder a las difusiones de solo aplicación o enviar mensajes a los receptores privados.

Un receptor de difusión no se muestren los cuadros de diálogo, y es recomendable para iniciar una actividad desde dentro de un receptor de difusión. Si un receptor de difusión debe notificar al usuario, debe publicar una notificación.

No es posible enlazar o iniciar un servicio desde dentro de un receptor de difusión. 

Esta guía tratará cómo crear un receptor de difusión y cómo registrarlo para que ésta pueda recibir difusiones.

## <a name="creating-a-broadcast-receiver"></a>Creación de un receptor de difusión

Para crear un receptor de difusión en Xamarin.Android, una aplicación debe subclase la `BroadcastReceiver` class, adornan con el `BroadcastReceiverAttribute`e invalidar la `OnReceive` método:

```csharp
[BroadcastReceiver(Enabled = true, Exported = false)]
public class SampleReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here.

        String value = intent.GetStringExtra("key");
    }
}
```

Cuando se compila la clase en Xamarin.Android, también actualizará el AndroidManifest con los metadatos necesarios para registrar el receptor. Para un receptor de difusión registrados estáticamente el `Enabled` correctamente, se debe establecer en `true`, de lo contrario Android no será posible crear una instancia del receptor.
 
El `Exported` propiedad controla si el receptor de difusión puede recibir mensajes de fuera de la aplicación. Si la propiedad no se establece explícitamente, el valor predeterminado de la propiedad viene determinada por Android basándose en si hay intención-filtros asociados con el receptor de difusión. Si hay al menos un filtro de intención para el receptor de difusión, Android asumirá que el `Exported` propiedad es `true`. Si no hay ninguna intención-filtros asociados a los receptores de difusión, Android asumirá que el valor es `false`. 

El `OnReceive` método recibe una referencia a la `Intent` que se distribuyó a los receptores de difusión. Esto hace que es posible que el remitente de la intención para pasar valores a los receptores de difusión.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Registrar estáticamente un receptor de difusión con un filtro de intención

Cuando un `BroadcastReceiver` está decorada con el [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), Xamarin.Android agregará el necesario `<intent-filter>` elemento el Android manifiesto en tiempo de compilación. El fragmento de código siguiente es un ejemplo de un receptor de difusión que se ejecutará cuando un dispositivo ha terminado de arranque (si se han concedido los permisos adecuados de Android por el usuario):

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { Android.Content.Intent.ActionBootCompleted })]
public class MyBootReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Work that should be done when the device boots.     
    }
}
```

También es posible crear un filtro de intento que responderá a intentos personalizados. Considere el ejemplo siguiente: 

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { "com.xamarin.example.TEST" })]
public class MySampleBroadcastReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here
    }
}
```

Las aplicaciones que tienen como destino Android 8.0 (API nivel 26) o versiones posteriores no pueden registrar estáticamente para una difusión implícita. Las aplicaciones pueden registrarse estática para una difusión explícita. Hay una pequeña lista de difusiones implícitas que están exentos de esta restricción. Estas excepciones se describen en la [implícita excepciones difundir](https://developer.android.com/guide/components/broadcast-exceptions.html) guía en la documentación de Android. Las aplicaciones que están interesadas en la difusión implícita deben hacer uso de forma dinámica el `RegisterReceiver` método. Esto se describe a continuación.

### <a name="context-registering-a-broadcast-receiver"></a>Registro de contexto de un receptor de difusión

Contexto de registro (también denominado registro dinámico) de un receptor se realiza invocando la `RegisterReceiver` deben anular el registro con una llamada al método y el receptor de difusión el `UnregisterReceiver` método. Para evitar pérdidas de recursos, es importante anular el registro del receptor cuando ya no es relevante para el contexto (la actividad o servicio). Por ejemplo, un servicio puede emitir una intención para informar a una actividad que las actualizaciones están disponibles para mostrarse al usuario. Cuando se inicia la actividad, registraría para esos intentos. Cuando se mueve la actividad en segundo plano y ya no es visible para el usuario, debe anular el registro del receptor porque la interfaz de usuario para mostrar las actualizaciones ya no está visible. El fragmento de código siguiente es un ejemplo de cómo registrar y anular el registro de un receptor de difusión en el contexto de una actividad:

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity 
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver()

        // Code omitted for clarity
    }

    protected override OnResume() 
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override OnPause() 
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

En el ejemplo anterior, cuando la actividad entra en primer plano, registrará un receptor de difusión que realizará escuchas para un propósito personalizado mediante el uso de la `OnResume` método del ciclo de vida. Como la actividad se mueve en segundo plano, el `OnPause()` método anulará el registro del receptor.

## <a name="publishing-a-broadcast"></a>Publicar una difusión

Se puede publicar una difusión a todas las aplicaciones instaladas en el dispositivo de creación de un objeto de intención y enviarlo con la `SendBroadcast` o `SendOrderedBroadcast` método.  

1. **Métodos de Context.SendBroadcast** &ndash; hay varias implementaciones de este método.
   Estos métodos se difundirá la intención de todo el sistema. Receptores de difusión que recibirán la intención en un orden indeterminado. Esto proporciona un gran flexibilidad, pero significa que es posible que otras aplicaciones registrar y recibir la intención. Esto puede suponer un riesgo de seguridad. Las aplicaciones pueden necesitar implementar la adición de seguridad para evitar el acceso no autorizado. Una posible solución consiste en usar el `LocalBroadcastManager` que sólo enviará mensajes dentro del espacio privado de la aplicación. Este fragmento de código es un ejemplo de cómo enviar una intención mediante uno de los `SendBroadcast` métodos:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    Este fragmento de código es otro ejemplo de enviar una difusión mediante la `Intent.SetAction` método para identificar la acción:

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash; esto es el método es muy similar a `Context.SendBroadcast`, con la diferencia está en que la intención será publicado en tiempo de destinatarios, en el orden en que se registraron los receptores.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

El [v4 de la biblioteca de compatibilidad de Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) proporciona una clase auxiliar denominada [ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). El `LocalBroadcastManager` está pensado para las aplicaciones que no desean enviar o recibir transmisiones de otras aplicaciones en el dispositivo. El `LocalBroadcastManager` solo publicarán mensajes dentro del contexto de la aplicación y sólo a los receptores de difusión que se registran con el `LocalBroadcastManager`. Este fragmento de código es un ejemplo de cómo registrar un receptor de difusión con `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Otras aplicaciones en el dispositivo no pueden recibir los mensajes que se publican con la `LocalBroadcastManager`. Este fragmento de código muestra cómo enviar una intención utilizando el `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Vínculos relacionados

- [BroadcastReceiver API](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast API](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [API de intención](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter API](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager (Android docs)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Notificaciones locales en Android](~/android/app-fundamentals/notifications/local-notifications.md) guía
- [Android Support v4 de biblioteca (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
