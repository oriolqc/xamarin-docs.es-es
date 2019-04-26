---
title: 'Tutorial: uso de notificaciones locales en Xamarin.Android'
description: Este tutorial muestra cómo usar notificaciones locales en las aplicaciones de Xamarin.Android. Muestra los conceptos básicos de creación y publicación de una notificación local. Cuando el usuario hace clic en la notificación en el área de notificación, se inicia una segunda actividad.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: e60ed6cc49921fc7b6e8e2616a6b0bf6f8abb401
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026872"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Tutorial: uso de notificaciones locales en Xamarin.Android

_Este tutorial muestra cómo usar notificaciones locales en las aplicaciones de Xamarin.Android. Muestra los conceptos básicos de creación y publicación de una notificación local. Cuando el usuario hace clic en la notificación en el área de notificación, se inicia una segunda actividad._


## <a name="overview"></a>Información general

En este tutorial, creamos una aplicación Android que genera una notificación cuando el usuario hace clic en un botón en una actividad. Cuando el usuario hace clic en la notificación, inicia una segunda actividad que muestra el número de veces que el usuario hace clic en el botón en la primera actividad.

Las capturas de pantalla siguientes muestran algunos ejemplos de esta aplicación:

[![Capturas de pantalla de ejemplo con la notificación](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> Esta guía se centra en la [NotificationCompat APIs](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) desde el [biblioteca de compatibilidad de Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Estas API asegurará de máximo hacia atrás compatibilidad para Android 4.0 (API nivel 14).

## <a name="creating-the-project"></a>Crear el proyecto

Para empezar, vamos a crear un nuevo proyecto de Android mediante el **aplicación Android** plantilla. Vamos a llamar a este proyecto **LocalNotifications**. (Si no está familiarizado con la creación de proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)

Editar el archivo de recursos **values/Strings.xml** para que contenga dos recursos de cadena adicional que se usará cuando llega el momento de crear el canal de notificación:


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>Agregue el paquete NuGet Android.Support.V4

En este tutorial, usamos `NotificationCompat.Builder` para compilar nuestra notificación local. Como se explica en [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md), debemos incluir el [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet en nuestro proyecto usar `NotificationCompat.Builder`.

A continuación, vamos a editar **MainActivity.cs** y agregue la siguiente `using` instrucción para que los tipos de `Android.Support.V4.App` están disponibles para el código:

```csharp
using Android.Support.V4.App;
```

Además, se impone clara al compilador que estamos usando el `Android.Support.V4.App` verzi `TaskStackBuilder` en lugar de `Android.App` versión. Agregue las siguientes `using` instrucción para resolver cualquier ambigüedad:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>Crear el canal de notificación

A continuación, agregue un método para `MainActivity` que creará un canal de notificación (si es necesario):

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Actualización de la `OnCreate` método para llamar a este nuevo método:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>Definir el Id. de notificación

Necesitaremos un identificador único para la notificación y canal de notificación. Vamos a editar **MainActivity.cs** y agregue la siguiente variable de instancia estática para el `MainActivity` clase:

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>Agregue código para generar la notificación

A continuación, se debe crear un nuevo controlador de eventos para el botón `Click` eventos. Agregue el siguiente método a `MainActivity`:

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

El `OnCreate` método MainActivity debe realizar la llamada para crear el canal de notificación y asignar la `ButtonOnClick` método a la `Click` eventos del botón (reemplazar el controlador de eventos delegado proporcionado por la plantilla):

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```


### <a name="create-a-second-activity"></a>Cree una segunda actividad

Ahora debemos crear otra actividad que Android se mostrará cuando el usuario hace clic en la notificación. Agregue otra actividad de Android al proyecto denominado **SecondActivity**. Abra **SecondActivity.cs** y reemplace su contenido con este código:

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

También debemos crear un diseño de recursos para **SecondActivity**. Agregue un nuevo **diseño de Android** archivo al proyecto denominado **Second.axml**. Editar **Second.axml** y pegue el siguiente código de diseño:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Agregar un icono de notificación

Por último, agregue un pequeño icono que aparecerá en el área de notificación cuando se inicia la notificación. Puede copiar [este icono](local-notifications-walkthrough-images/ic-stat-button-click.png) al proyecto o crear su propio icono personalizado. Nombre del archivo de icono **ic\_stat\_botón\_click.png** y cópielo en el **recursos/drawable** carpeta. Recuerde que debe usar **Agregar > elemento existente...**  para incluir este archivo de icono en el proyecto.


### <a name="run-the-application"></a>Ejecutar la aplicación

Compile y ejecute la aplicación. Debe estar presente en la primera actividad, similar a la captura de pantalla siguiente:

[![Primera captura de pantalla de actividad](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Hacer clic en el botón, debe observar que el icono pequeño de la notificación aparece en el área de notificación:

[![Aparece el icono de notificación](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Si se deslice el dedo hacia abajo y expone el cajón de notificaciones, debería ver la notificación:

[![Mensaje de notificación](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Cuando haga clic en la notificación, debería desaparecer y se debe iniciar nuestra otra actividad &ndash; creando algo parecido a la captura de pantalla siguiente:

[![Segunda captura de pantalla de actividad](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

¡Enhorabuena! En este momento ha completado el tutorial de notificaciones local de Android y tiene un ejemplo funcional que se puede consultar. Hay mucho más a las notificaciones que hemos mostrado aquí, si desea obtener más información, eche un vistazo a [documentación de Google sobre notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="summary"></a>Resumen

En este tutorial usa `NotificationCompat.Builder` para crear y mostrar las notificaciones. Se ha mostrado un ejemplo básico de cómo iniciar una segunda actividad como una manera de responder a la interacción del usuario con la notificación y muestra a la transferencia de datos desde la primera actividad a la segunda actividad.


## <a name="related-links"></a>Vínculos relacionados

- [LocalNotifications (ejemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Canales de notificación de Android Oreo](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Notificación](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
