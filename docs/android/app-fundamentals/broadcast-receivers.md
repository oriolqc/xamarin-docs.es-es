---
title: Receptores de difusión en Xamarin.Android
description: Esta sección describe cómo utilizar un receptor de difusión.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/20/2018
ms.openlocfilehash: 6b2e316eaf67e51801be4fcd670e80ec81c8ff08
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935404"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Receptores de difusión en Xamarin.Android

_Esta sección describe cómo utilizar un receptor de difusión._

## <a name="broadcast-receiver-overview"></a>Información general de receptor de difusión

A _receptor difusión_ es un componente de Android que permite que una aplicación responder a mensajes (un Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) que se emiten por el sistema operativo Android o por una aplicación. Siguen las difusiones un _suscripción-publicación_ modelo &ndash; un evento hace una difusión que se publican y recibidos por los componentes que están interesados en el evento. 

Android identifica dos tipos de difusiones:

* **Difusión explícita** &ndash; estos tipos de difusiones destinados a una aplicación específica. El uso más común de una difusión explícita es iniciar una actividad. Un ejemplo de una difusión explícita cuando una aplicación necesita llamar a un número de teléfono; enviará un intento destinado a la aplicación de teléfono en Android y pasada en el número de teléfono que se debe marcar. Android, a continuación, enrutará el intento de la aplicación de teléfono.
* **Difusión implícita** &ndash; estas difusiones se envían a todas las aplicaciones en el dispositivo. Un ejemplo de una difusión implícito es el `ACTION_POWER_CONNECTED` intención. Esta intención se publica cada vez que Android detecta que se está cargando la batería en el dispositivo. Android enrutará esta intención para todas las aplicaciones que se han registrado para este evento.

El receptor de difusión es una subclase de la `BroadcastReceiver` tipo y se deben invalidar el [ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/) método. Se ejecutará Android `OnReceive` en el subproceso principal, por lo que este método debe diseñarse para ejecutar rápidamente. Se debe tener cuidado al generando subprocesos en `OnReceive` porque Android puede finalizar el proceso cuando finalice el método. Si un receptor de difusión debe realizar el trabajo de larga ejecución, a continuación, se recomienda programar una _trabajo_ mediante la `JobScheduler` o _Firebase trabajo distribuidor_. Programación de trabajo a un trabajo se tratarán en una guía independiente.

Un _filtro intención_ se usa para registrar un receptor de difusión para que Android puede enrutar correctamente los mensajes. Se puede especificar el filtro de intención en tiempo de ejecución (Esto se conoce a veces como un _receptor registrado en contexto_ o como _registro dinámico_) o se puede definir estáticamente en el manifiesto Android (un _receptor registrado manifiesto_). Xamarin.Android proporciona un atributo de C#, `IntentFilterAttribute`, que registren estáticamente el filtro intención (Esto se explica con más detalle más adelante en esta guía). A partir de Android 8.0, no es posible que una aplicación registre estáticamente para una difusión implícita.

La principal diferencia entre el destinatario registrado de manifiesto y el receptor registrado en contexto es que un receptor registrado en contexto solo responderá a las difusiones mientras se ejecuta una aplicación, mientras que un receptor registrado manifiesto puede responder a difunde aunque no se puede ejecutar la aplicación.  

Hay dos conjuntos de API para administrar un receptor de difusión y enviar las difusiones:

1. **`Context`** &ndash; La `Android.Content.Context` clase puede usarse para registrar un receptor de difusión que responderá a los eventos de todo el sistema. El `Context` también se utiliza para publicar las difusiones de todo el sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; Se trata de una API que está disponible a través de la [paquete NuGet de biblioteca de compatibilidad de Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Esta clase se utiliza para mantener las difusiones y difusión receptores aislados en el contexto de la aplicación que está usando. Esta clase puede ser útil para evitar que otras aplicaciones de responder a las difusiones de solo aplicación o enviar mensajes a los receptores privados.

Es posible que un receptor de difusión no muestre los cuadros de diálogo y se recomienda encarecidamente no usar para iniciar una actividad desde un receptor de difusión. Si un receptor de difusión debe notificar al usuario, debe publicar una notificación.

No es posible enlazar a o iniciar un servicio desde dentro de un receptor de difusión. 

Esta guía explica cómo crear un receptor de difusión y cómo registrar, por lo que puede recibir las difusiones.

## <a name="creating-a-broadcast-receiver"></a>Creación de un receptor de difusión

Para crear un receptor de difusión en Xamarin.Android, una aplicación debe subclase la `BroadcastReceiver` de clases, adornar con el `BroadcastReceiverAttribute`e invalidar el `OnReceive` método:

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

Cuando Xamarin.Android compila la clase, también actualizará la AndroidManifest con los metadatos necesarios para registrar el receptor. Para un receptor de difusión registrar estáticamente, la `Enabled` correctamente, se debe establecer en `true`, en caso contrario Android no será capaz de crear una instancia del receptor.
 
El `Exported` propiedad controla si el receptor de difusión puede recibir mensajes desde fuera de la aplicación. Si la propiedad no se establece explícitamente, el valor predeterminado de la propiedad se determina en función de si existen los filtros de intención asociados con el receptor de difusión de Android. Si hay al menos un filtro de intención para el receptor de difusión, Android asumirá que el `Exported` propiedad es `true`. Si hay ningún filtro de intención asociado con el receptor de difusión, Android asumirá que el valor es `false`. 

El `OnReceive` método recibe una referencia a la `Intent` que se distribuyó al receptor de difusión. Esto hace que es posible que el remitente de la intención de pasar valores al receptor de difusión.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Registrar estáticamente un receptor de difusión con un filtro de intención

Cuando un `BroadcastReceiver` se decora con el [ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/), Xamarin.Android agregará necesaria `<intent-filter>` elemento a la Android manifiesto en tiempo de compilación. El fragmento de código siguiente es un ejemplo de un receptor de difusión que se ejecutará cuando un dispositivo ha terminado de iniciarse (si los permisos adecuados de Android concedidos por el usuario):

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

También es posible crear un filtro de intención que responderá intentos personalizados. Considere el ejemplo siguiente: 

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

Aplicaciones destinadas a Android 8.0 (API nivel 26) o versiones posteriores no pueden registrar estáticamente para una difusión implícita. Las aplicaciones todavía estáticamente pueden registrarse para una difusión explícita. Hay una pequeña lista de difusiones implícita que están exentos de esta restricción. Estas excepciones se describen en la [excepciones de difusión implícita](https://developer.android.com/guide/components/broadcast-exceptions.html) guía en la documentación de Android. Las aplicaciones que están interesadas en difusiones implícita deben hacer uso de forma dinámica el `RegisterReceiver` método. Este procedimiento se describe a continuación.

### <a name="context-registering-a-broadcast-receiver"></a>Registro de contexto de un receptor de difusión

Contexto de registro (conocido también como registro dinámico) de un receptor se realiza invocando la `RegisterReceiver` deben anular el registro con una llamada a método y el receptor de difusión el `UnregisterReceiver` método. Para evitar la pérdida de recursos, es importante anular el registro el receptor cuando ya no es relevante para el contexto (la actividad o servicio). Por ejemplo, un servicio puede emitir un intento para informar a una actividad que las actualizaciones están disponibles para mostrarse al usuario. Cuando se inicia la actividad, se registrará para dichos intentos. Cuando se mueve la actividad en segundo plano y ya no es visible para el usuario, debe anular el registro del receptor porque la interfaz de usuario para mostrar las actualizaciones ya no está visible. El siguiente fragmento de código es un ejemplo de cómo registrar y anular el registro de un receptor de difusión en el contexto de una actividad:

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

En el ejemplo anterior, cuando la actividad entra en primer plano, registrará un receptor de difusión que realizará escuchas para un color personalizado mediante el uso de la `OnResume` método de ciclo de vida. Cuando se desplace la actividad en segundo plano, el `OnPause()` método anulará el receptor.

## <a name="publishing-a-broadcast"></a>Publicar una difusión

Puede publicar una difusión a todas las aplicaciones instaladas en el dispositivo de creación de un objeto por intención y enviar con el `SendBroadcast` o `SendOrderedBroadcast` método.  

1. **Métodos de Context.SendBroadcast** &ndash; hay varias implementaciones de este método.
   Estos métodos difundirá la intención de todo el sistema. Receptores de difusión thatwill recibir la intención en un orden indeterminado. Esto proporciona un gran flexibilidad, pero significa que es posible que otras aplicaciones se registre y reciba el intento. Esto puede suponer un riesgo de seguridad. Las aplicaciones pueden necesitar implementar la seguridad de suma para evitar accesos no autorizados. Una posible solución consiste en utilizar el `LocalBroadcastManager` que solo enviará mensajes dentro del espacio privado de la aplicación. Este fragmento de código es un ejemplo de cómo enviar un intento de usar uno de los `SendBroadcast` métodos:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   intent.PutExtra("key", "value");
   SendBroadcast(intent);
   ```

    Este fragmento de código es otro ejemplo de enviar una difusión mediante el `Intent.SetAction` método para identificar la acción:

    ```csharp 
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash; trata de método es muy similar a `Context.SendBroadcast`, con la diferencia consiste en que la intención será publicado en tiempo de destinatarios, en el orden en que se registraron los recievers.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

El [v4 de la biblioteca de compatibilidad de Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) proporciona una clase auxiliar denominada [ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). El `LocalBroadcastManager` está diseñado para las aplicaciones que no desea enviar o recibir las difusiones de otras aplicaciones en el dispositivo. El `LocalBroadcastManager` solo publicarán mensajes dentro del contexto de la aplicación y sólo a los receptores de difusión que se registran con el `LocalBroadcastManager`. Este fragmento de código es un ejemplo de cómo registrar un receptor de difusión con `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Otras aplicaciones en el dispositivo no pueden recibir los mensajes que se publican con la `LocalBroadcastManager`. Este fragmento de código muestra cómo enviar una intención usando el `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
intent.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Vínculos relacionados

- [BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [Intención](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Notificaciones locales en Android](~/android/app-fundamentals/notifications/local-notifications.md)
- [Android admite v4 de biblioteca](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
