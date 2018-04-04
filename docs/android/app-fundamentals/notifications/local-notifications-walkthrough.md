---
title: 'Tutorial: usar notificaciones Local en Xamarin.Android'
description: Este tutorial muestra cómo usar notificaciones locales en las aplicaciones de Xamarin.Android. Muestra los conceptos básicos de la creación y publicación de una notificación local. Cuando el usuario hace clic en la notificación en el área de notificación, se inicia una segunda actividad.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/30/2018
ms.openlocfilehash: a2ca3755e3201263584447ba47ec36d2096386da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Tutorial: usar notificaciones Local en Xamarin.Android

_Este tutorial muestra cómo usar notificaciones locales en las aplicaciones de Xamarin.Android. Muestra los conceptos básicos de la creación y publicación de una notificación local. Cuando el usuario hace clic en la notificación en el área de notificación, se inicia una segunda actividad._


## <a name="overview"></a>Información general

En este tutorial, se creará una aplicación de Android que genera una notificación cuando el usuario hace clic en un botón en una actividad. Cuando el usuario hace clic en la notificación, se inicia una segunda actividad que muestra el número de veces que el usuario hace clic en el botón de la primera actividad.

Las capturas de pantalla siguientes muestran algunos ejemplos de esta aplicación:

[![Capturas de pantalla de ejemplo con la notificación](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)



## <a name="walkthrough"></a>Tutorial

Para empezar, vamos a crear un nuevo proyecto de Android mediante el **aplicación Android** plantilla. Vamos a llamar a este proyecto **LocalNotifications**. (Si no está familiarizado con la creación de proyectos de Xamarin.Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)


### <a name="add-the-androidsupportv4app-component"></a>Agregue el componente Android.Support.V4.App

En este tutorial, usamos `NotificationCompat.Builder` para generar la notificación local. Como se explica en [notificaciones Local](~/android/app-fundamentals/notifications/local-notifications.md), debemos incluir la [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet en el proyecto para usar `NotificationCompat.Builder`.

A continuación, editar **MainActivity.cs** y agregue el siguiente `using` instrucción para que los tipos de `Android.Support.V4.App` están disponibles para el código:

```csharp
using Android.Support.V4.App;
```

Además, debemos hacerlo clara al compilador que estamos usando la `Android.Support.V4.App` versión de `TaskStackBuilder` en lugar del `Android.App` versión. Agregue las siguientes `using` instrucción para resolver cualquier ambigüedad:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```


### <a name="define-the-notification-id"></a>Definir el identificador de notificación

Se necesitará un identificador único para la notificación. Editar **MainActivity.cs** y agregue la siguiente variable de instancia estática a la `MainActivity` clase:

```csharp
private static readonly int ButtonClickNotificationId = 1000;
```


### <a name="add-code-to-generate-the-notification"></a>Agregar código para generar la notificación

A continuación, necesitamos crear un nuevo controlador de eventos para el botón `Click` eventos. Agregue el método siguiente a `MainActivity`:

```csharp
private void ButtonOnClick (object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    Bundle valuesForActivity = new Bundle();
    valuesForActivity.PutInt ("count", count);

    // When the user clicks the notification, SecondActivity will start up.
    Intent resultIntent = new Intent(this, typeof (SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras (valuesForActivity);

    // Construct a back stack for cross-task navigation:
    TaskStackBuilder stackBuilder = TaskStackBuilder.Create (this);
    stackBuilder.AddParentStack (Java.Lang.Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent (resultIntent);

    // Create the PendingIntent with the back stack:            
    PendingIntent resultPendingIntent =
        stackBuilder.GetPendingIntent (0, (int)PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
        .SetAutoCancel (true)                    // Dismiss from the notif. area when clicked
        .SetContentIntent (resultPendingIntent)  // Start 2nd activity when the intent is clicked.
        .SetContentTitle ("Button Clicked")      // Set its title
        .SetNumber (count)                       // Display the count in the Content Info
        .SetSmallIcon(Resource.Drawable.ic_stat_button_click)  // Display this icon
        .SetContentText (String.Format(
            "The button has been clicked {0} times.", count)); // The message to display.

    // Finally, publish the notification:
    NotificationManager notificationManager =
        (NotificationManager)GetSystemService(Context.NotificationService);
    notificationManager.Notify(ButtonClickNotificationId, builder.Build());

    // Increment the button press count:
    count++;
}
```

En el `OnCreate` método, asigne este `ButtonOnClick` método para el `Click` eventos del botón (reemplazar el controlador de eventos de delegado proporcionado por la plantilla):

```csharp
button.Click += ButtonOnClick;
```


### <a name="create-a-second-activity"></a>Crear una segunda actividad

Ahora necesitamos crear otra actividad que Android se mostrará cuando el usuario hace clic en la notificación. Agregue otra actividad Android a su proyecto denominada **SecondActivity**. Abra **SecondActivity.cs** y reemplazar su contenido con este código:

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
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Get the count value passed to us from MainActivity:
            int count = Intent.Extras.GetInt ("count", -1);

            // No count was passed? Then just return.
            if (count <= 0)
                return;

            // Display the count sent from the first activity:
            SetContentView (Resource.Layout.Second);
            TextView textView = FindViewById<TextView>(Resource.Id.textView);
            textView.Text = String.Format (
                "You clicked the button {0} times in the previous activity.", count);
        }
    }
}
```

También debemos crear un diseño de recursos para **SecondActivity**. Agregue un nuevo **diseño Android** archivo al proyecto denominado **Second.axml**. Editar **Second.axml** y pegue el siguiente código de diseño:

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
        android:id="@+id/textView" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Agregar un icono de notificación

Por último, vamos a agregar un icono pequeño que va a aparecer en el área de notificación cuando se inicia la notificación. Puede copiar [este icono](local-notifications-walkthrough-images/ic-stat-button-click.png) al proyecto o crear su propio icono personalizado. El archivo de icono **ic\_stat\_botón\_click.png** y cópielo en el **/puede dibujar recursos** carpeta. Recuerde que debe usar **Agregar > elemento existente...**  para incluir este archivo de icono en su proyecto.


### <a name="run-the-application"></a>Ejecutar la aplicación

Vamos a compilar y ejecutar la aplicación. Debe aparecer con la primera actividad, similar a la captura de pantalla siguiente:

[![Primera captura de pantalla de actividad](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Hacer clic en el botón, debe observar que el icono pequeño para la notificación aparece en el área de notificación:

[![Aparece el icono de notificación](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Si se deslice el dedo hacia abajo y expone el cajón de notificaciones, verá la notificación:

[![Mensaje de notificación](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Cuando haga clic en la notificación, debería desaparecer y debería iniciarse otra actividad de nuestro &ndash; buscando algo parecido a la captura de pantalla siguiente:

[![Segunda captura de pantalla de actividad](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

¡Enhorabuena! En este momento ha completado el tutorial de Android notificación local y tiene un ejemplo funcional que se puede consultar. Hay mucho más a las notificaciones que hemos mostrado aquí, por lo que si desea obtener más información, eche un vistazo en [documentación de Google en las notificaciones](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) y el Android [notificaciones](http://developer.android.com/design/patterns/notifications.html) Guía de diseño.



## <a name="summary"></a>Resumen

En este tutorial se utiliza `NotificationCompat.Builder` para crear y mostrar las notificaciones. Hemos visto un ejemplo básico de cómo iniciar una segunda actividad como una manera de responder a la interacción del usuario con la notificación y se muestra a la transferencia de datos desde la primera actividad a la segunda actividad.


## <a name="related-links"></a>Vínculos relacionados

- [LocalNotifications (ejemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Patrones de la Guía de diseño acerca de las notificaciones](http://developer.android.com/design/patterns/notifications.html)
- [Notification](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
