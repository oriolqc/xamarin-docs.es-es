---
title: Notificaciones locales
description: En esta sección se muestra cómo implementar notificaciones locales en Xamarin.Android. Explica los distintos elementos de interfaz de usuario de una notificación de Android y habla de la API del implicada en crear y mostrar una notificación.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: c36b31e28011bea287903ee0681a316209abd22d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829995"
---
<a name="compatibility"></a>

# <a name="local-notifications"></a>Notificaciones locales

_En esta sección se muestra cómo implementar notificaciones locales en Xamarin.Android. Explica los distintos elementos de interfaz de usuario de una notificación de Android y habla de la API del implicada en crear y mostrar una notificación._

## <a name="local-notifications-overview"></a>Información general de notificaciones local

Android proporciona dos áreas controlado por el sistema para mostrar iconos de notificación y la información de notificación al usuario. Cuando se publica una notificación por primera vez, su icono se muestra en el *área de notificación*, tal y como se muestra en la captura de pantalla siguiente:

![Área de notificación de ejemplo en un dispositivo](local-notifications-images/01-notification-shade.png)

Para obtener detalles acerca de la notificación, el usuario puede abrir el cajón de notificaciones (que se expande cada icono de notificación para mostrar el contenido de la notificación) y realizar todas las acciones asociadas con las notificaciones. La siguiente captura de pantalla muestra un *cajón de notificaciones* que corresponde al área de notificación que aparece anteriormente:

[![Cajón de notificaciones de ejemplo que muestre tres notificaciones](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Las notificaciones de Android utilizan dos tipos de diseño:

-   ***Diseño de base*** &ndash; un formato de presentación compacto y fijo.

-   ***Diseño ampliado*** &ndash; un formato de presentación que se pueda expandir a un tamaño mayor para obtener más información.

En las secciones siguientes se explican cada uno de estos tipos de diseño (y cómo crearlos).

> [!NOTE]
> Esta guía se centra en la [NotificationCompat APIs](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) desde el [biblioteca de compatibilidad de Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Estas API asegurará de máximo hacia atrás compatibilidad para Android 4.0 (API nivel 14).


### <a name="base-layout"></a>Diseño de base

Todas las notificaciones de Android se basan en el formato de diseño base, que, como mínimo, incluye los siguientes elementos:

1.  Un *icono de notificación*, que representa la aplicación original, o el tipo de notificación si la aplicación es compatible con diferentes tipos de notificaciones.

2.  La notificación *título*, o el nombre del remitente si la notificación es un mensaje personal.

3.  El mensaje de notificación.

4.  Un *timestamp*.

Estos elementos aparecen como se muestra en el diagrama siguiente:

[![Ubicación de los elementos de notificación](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Los diseños de base se limitan a 64 píxeles independientes de la densidad (dp) en el alto. Android crea este estilo de notificaciones básico de forma predeterminada.

Si lo desea, las notificaciones pueden mostrar un icono grande que representa la aplicación o una foto del remitente. Cuando se utiliza un icono grande en una notificación en Android 5.0 y versiones posteriores, el icono pequeño de notificación se muestra como un distintivo sobre el icono de gran tamaño:

![Foto de notificación simple](local-notifications-images/04-simple-notification-photo.png)

A partir de Android 5.0, las notificaciones también pueden aparecer en la pantalla de bloqueo:

[![Notificación de pantalla de bloqueo de ejemplo](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

El usuario puede pulse dos veces la notificación de pantalla de bloqueo para desbloquear el dispositivo y saltar a la aplicación que se originó dicha notificación, o pasar el dedo para descartarla. Las aplicaciones pueden establecer el nivel de visibilidad de una notificación para controlar lo que se muestra en la pantalla de bloqueo y los usuarios pueden elegir si se permite contenido confidencial que se mostrará en las notificaciones de pantalla de bloqueo.

Android 5.0 introdujo un formato de presentación de notificación de alta prioridad denominado *Heads-Up*. Las notificaciones de aviso deslice hacia abajo desde la parte superior de la pantalla durante unos segundos y, a continuación, retreat copia de seguridad en el área de notificación:

[![Notificación de pantalla de datos de ejemplo](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Las notificaciones de aviso hacen posible para el sistema de la interfaz de usuario para colocar información importante delante del usuario sin interrumpir el estado de la actividad que se está ejecutando.

Android incluye compatibilidad con metadatos de la notificación, por lo que pueden organizarse y muestra de forma inteligente las notificaciones. Los metadatos de la notificación también controla cómo se presentan las notificaciones en la pantalla de bloqueo y en formato de aviso. Las aplicaciones pueden establecer los siguientes tipos de metadatos de notificación:

-   **Prioridad** &ndash; el nivel de prioridad determina cómo y cuándo se presentan las notificaciones. Por ejemplo, en Android 5.0, se muestran las notificaciones de alta prioridad como notificaciones de aviso.

-   **Visibilidad** &ndash; especifica cuánto contenido de la notificación se mostrará cuando aparezca la notificación en la pantalla de bloqueo.

-   **Categoría** &ndash; informa al sistema cómo controlar la notificación en diversas circunstancias, como cuando el dispositivo se *no molestar* modo.

> [!NOTE]
> **Visibilidad** y **categoría** se presentaron en Android 5.0 y no están disponibles en versiones anteriores de Android. A partir de Android 8.0, [canales de notificación](#notif-chan) sirven para controlar cómo se presentan las notificaciones al usuario.


### <a name="expanded-layouts"></a>Diseños expandidos

A partir de Android 4.1, se pueden configurar las notificaciones con los estilos de diseño expandida que permiten al usuario expandir el alto de la notificación para ver más contenido. Por ejemplo, en el ejemplo siguiente se muestra una notificación de diseño expandido en el modo contraído:

![Notificación contratado](local-notifications-images/07-contracted-notification.png)

Cuando se expande esta notificación, revela el mensaje completo:

![Notificación expandido](local-notifications-images/08-expanded-notification.png)

Android admite tres estilos de diseño expandida para las notificaciones de evento:

-   ***Texto grande*** &ndash; en modo contraído, muestra un extracto de la primera línea del mensaje seguido de dos puntos. En el modo expandido, muestra el mensaje completo (como se muestra en el ejemplo anterior).

-   ***Bandeja de entrada*** &ndash; en modo contraído, muestra el número de mensajes nuevos. En el modo expandido, muestra el primer mensaje de correo electrónico o una lista de los mensajes en la Bandeja de entrada.

-   ***Imagen*** &ndash; en modo contraído, muestra sólo el texto del mensaje. En el modo expandido, muestra el texto y una imagen.

[Más allá de la notificación básica](#beyond-the-basic-notification) (más adelante en este artículo) explica cómo crear *texto grande*, *Bandeja de entrada*, y *imagen* notificaciones.

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Canales de notificación

A partir de Android 8.0 (Oreo), puede usar el *canales de notificación* característica para crear un canal personalizables por el usuario para cada tipo de notificación que desea mostrar. Canales de notificación permiten que las notificaciones de grupo para que todas las notificaciones que registren en un anexo de canal el mismo comportamiento. Por ejemplo, podría tener un canal de notificación está pensado para las notificaciones que requieren atención inmediata y un canal "más silencioso" independiente que se usa para los mensajes informativos.

El **YouTube** aplicación que se instala con Android Oreo muestra dos categorías de notificación: **Descargue las notificaciones** y **notificaciones generales**:

[![Pantallas de notificación de YouTube en Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Cada una de estas categorías se corresponde a un canal de notificación. Implementa la aplicación de YouTube un **descargar notificaciones** canal y un **notificaciones generales** canal. El usuario puede pulsar **descargar notificaciones**, que muestra la pantalla de configuración para la aplicación de descarga de canal de notificaciones:

[![Descargar las notificaciones pantalla de la aplicación de YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

En esta pantalla, el usuario puede modificar el comportamiento de la **descargar** notificaciones del canal haciendo lo siguiente:

-   Establecer el nivel a la importancia **urgente**, **alta**, **medio**, o **baja**, que configura el nivel de la interrupción de sonido y visual.

-   Activar y desactivar el punto de notificación.

-   Activar y desactivar la luz intermitente.

-   Mostrar u ocultar notificaciones en la pantalla de bloqueo.

-   Invalidar el **no molestar** configuración.

El **notificaciones generales** canal tiene una configuración similar:

[![Pantalla de notificaciones generales para la aplicación de YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Tenga en cuenta que no tiene control absoluto sobre cómo interactúan los canales de notificación con el usuario &ndash; el usuario puede modificar la configuración de cualquier canal de notificación en el dispositivo, tal como se muestra en las capturas de pantalla anterior. Sin embargo, puede configurar los valores predeterminados (como se describe a continuación). Como se muestran en estos ejemplos, la nueva característica de los canales de notificación hace posible para ofrecer a los usuarios un mayor control sobre los diferentes tipos de notificaciones.


## <a name="notification-creation"></a>Creación de notificación

Para crear una notificación en Android, use el [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) clase desde el [Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) paquete NuGet. Esta clase permite crear y publicar las notificaciones en las versiones anteriores de Android. Para obtener más información sobre el uso de `NotificationCompat.Builder`, consulte [compatibilidad](#compatibility) más adelante en este tema.

`NotificationCompat.Builder` Proporciona métodos para establecer las diversas opciones en una notificación, como:

-   El contenido, incluido el título, el texto del mensaje y el icono de notificación.

-   El estilo de la notificación, como *texto grande*, *Bandeja de entrada*, o *imagen* estilo.

-   La prioridad de la notificación: mínimo, bajo, valor predeterminado, alta, o máximo. En Android 8.0 y versiones posteriores, la prioridad se establece a través de un [ _canal de notificación_](#notification-channels).

-   La visibilidad de la notificación en la pantalla de bloqueo: public, private o secreto.

-   Metadatos de la categoría que Android le ayuda a clasificar y filtrar la notificación.

-   Una intención opcional que indica una actividad que se va a iniciar cuando se pulsa la notificación.

-   El identificador del canal de notificación que se publicará la notificación de (Android 8.0 y versiones posterior).

Después de establecer estas opciones en el generador, generar un objeto de notificación que contiene la configuración. Para publicar la notificación, pasa este objeto de notificación para el *Notification Manager*. Android proporciona el [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) (clase), que es responsable de las notificaciones de publicación y mostrarlos al usuario. Puede obtener una referencia a esta clase desde cualquier contexto, como una actividad o un servicio.


### <a name="creating-a-notification-channel"></a>Creación de un canal de notificación

Las aplicaciones que se ejecutan en Android 8.0 deben crear un canal de notificación para sus notificaciones. Un canal de notificación requiere los siguientes fragmentos de información:

* Una cadena de identificador que es única para el paquete que identificará el canal.
* El nombre del canal que se mostrará al usuario.  El nombre debe ser entre 1 y 40 caracteres.
* La importancia del canal.

Las aplicaciones se deberán comprobar la versión de Android que se está ejecutando.
Los dispositivos que ejecutan versiones anteriores a Android 8.0 no deberían crear un canal de notificación. El método siguiente es un ejemplo de cómo crear un canal de notificación en una actividad:

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

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Se debe crear el canal de notificación cada vez que se crea la actividad. Para el `CreateNotificationChannel` método, se debe llamar el `OnCreate` método de una actividad.

### <a name="creating-and-publishing-a-notification"></a>Creación y publicación de una notificación

Para generar una notificación en Android, siga estos pasos:

1.  Crear una instancia de un `NotificationCompat.Builder` objeto.

2.  Llamar a métodos distintos en el `NotificationCompat.Builder` objeto para establecer las opciones de notificación.

3.  Llame a la [compilar](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) método de la `NotificationCompat.Builder` objeto para crear instancias de un objeto de notificación.

4.  Llame a la [Notify](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) método del Administrador de notificaciones para la notificación de publicación.

Debe proporcionar al menos la siguiente información para cada notificación:

-   Un pequeño icono (24 x 24 dp de tamaño)

-   Un título corto

-   El texto de la notificación

El ejemplo de código siguiente muestra cómo usar `NotificationCompat.Builder` para generar una notificación básica. Tenga en cuenta que `NotificationCompat.Builder` métodos admiten [encadenamiento de método](https://en.wikipedia.org/wiki/Method_chaining); es decir, cada método devuelve el objeto de generador para que pueda usar el resultado de la última llamada de método para invocar la llamada al método siguiente:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

En este ejemplo, un nuevo `NotificationCompat.Builder` objeto denominado `builder` se crea una instancia, junto con el identificador del canal de notificación que se usará. El título y el texto de la notificación se establecen y se carga el icono de notificación desde **Resources/drawable/ic_notification.png**. La llamada al generador de la notificación `Build` método crea un objeto de notificación con esta configuración. El paso siguiente consiste en llamar a la `Notify` método para el Administrador de notificaciones. Para encontrar el Administrador de notificaciones, se llama a `GetSystemService`, como se indicó anteriormente.

El `Notify` método acepta dos parámetros: el identificador de notificación y el objeto de notificación. El identificador de notificación es un entero único que identifica la notificación a la aplicación. En este ejemplo, el identificador de notificación se establece en cero (0); No obstante, en una aplicación de producción, le interesará asigne un identificador único de cada notificación. Volver a usar el valor de identificador anterior en una llamada a `Notify` hace que la última notificación que se sobrescriban.

Cuando este código se ejecuta en un dispositivo Android 5.0, genera una notificación de que es similar al ejemplo siguiente:

![Resultado de notificación para el código de ejemplo](local-notifications-images/09-hello-world.png)

El icono de notificación se muestra en el lado izquierdo de la notificación &ndash; esta imagen de un círculo con &ldquo;&rdquo; tiene un canal alfa para que Android puede dibujar un fondo gris circular detrás de él. También puede proporcionar un icono sin un canal alfa. Para mostrar una imagen fotográfica como un icono, vea [formato de icono grande](#large-icon-format) más adelante en este tema.

La marca de tiempo se establece automáticamente, pero puede invalidar esta configuración mediante una llamada a la [predeterminadoSi](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) método del generador de notificación. Por ejemplo, el siguiente ejemplo de código establece la marca de tiempo a la hora actual:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Habilitación de vibración y sonido

Si desea que la notificación también reproducir un sonido, puede llamar el generador de notificación [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) método y pase el `NotificationDefaults.Sound` marca:

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Esta llamada a `SetDefaults` hará que el dispositivo reproducir un sonido cuando se publica la notificación. Si desea que el dispositivo vibre en lugar de reproducir un sonido, puede pasar `NotificationDefaults.Vibrate` a `SetDefaults.` si desea que el dispositivo para reproducir un sonido y vibrar el dispositivo, puede pasar a ambos marcadores de `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Si habilita sin especificar un sonido para reproducir sonido, Android usa el sonido de notificación del sistema de forma predeterminada. Sin embargo, puede cambiar el sonido que se reproducirá mediante una llamada del generador de notificación [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) método. Por ejemplo, para reproducir la alarma sonido con la notificación (en lugar del sonido de notificación predeterminado), puede obtener el URI de la alarma sonido desde el [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) y pasarlo a `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Como alternativa, puede usar el tono de predeterminado system sonido para la notificación:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Después de crear un objeto de notificación, es posible establecer las propiedades de notificación en el objeto de notificación (en lugar de configurarlos a través de antemano `NotificationCompat.Builder` métodos). Por ejemplo, en lugar de llamar el `SetDefaults` método para habilitar la vibración en una notificación, puede modificar directamente el indicador de bits de la notificación [el valor predeterminado es](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) propiedad:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

En este ejemplo hace que el dispositivo vibre cuando se publica la notificación.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>Actualización de una notificación

Si desea actualizar el contenido de una notificación una vez se ha publicado, puede volver a usar el existente `NotificationCompat.Builder` objeto para crear un nuevo objeto de notificación y publicar esta notificación con el identificador de la última notificación. Por ejemplo:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

En este ejemplo, existente `NotificationCompat.Builder` objeto se usa para crear un nuevo objeto de notificación con un título y diferentes mensajes.
El nuevo objeto de notificación se publicó con el identificador de la notificación anterior, y se actualiza el contenido de la notificación de que se publicó anteriormente:

![Notificación actualizados](local-notifications-images/12-updated-notification.png)

El cuerpo de la notificación anterior se vuelve a usar &ndash; solo el título y el texto de la notificación cambia mientras la notificación se muestra en el cajón de notificaciones. El texto del título cambia de "Notificación de ejemplo" a "Actualiza la notificación" y el texto del mensaje cambia de "¡Hello World! Este es mi primera notificación!" a "Se ha cambiado para este mensaje."

Una notificación permanece visible hasta que se produce una de estas tres cosas:

-   El usuario descarta la notificación (o lo pulsa *Borrar todo*).

-   La aplicación realiza una llamada a `NotificationManager.Cancel`, pasando el identificador de notificación única que se asignó cuando se publicó la notificación.

-   La aplicación llama a `NotificationManager.CancelAll`.

Para obtener más información acerca de cómo actualizar las notificaciones de Android, consulte [modificar una notificación](https://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>A partir de una actividad desde una notificación

En Android, es habitual que una notificación se asocie a un *acción* &ndash; una actividad que se inicia cuando el usuario pulsa la notificación. Esta actividad puede residir en otra aplicación o incluso en otra tarea. Para agregar una acción a una notificación, se crea un [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) de objetos y asociar el `PendingIntent` con la notificación. Un `PendingIntent` es un tipo especial de intención que permite que la aplicación del destinatario ejecutar un fragmento de código predefinido con los permisos de la aplicación de envío. Cuando el usuario pulsa la notificación, Android se inicia la actividad especificada por el `PendingIntent`.

El fragmento de código siguiente muestra cómo crear una notificación con un `PendingIntent` que iniciará la actividad de la aplicación original, `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Este código es muy similar al código de notificación en la sección anterior, salvo que un `PendingIntent` se agrega al objeto de notificación. En este ejemplo, el `PendingIntent` está asociado con la actividad de la aplicación original antes de pasarlo en el generador de notificación [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) método. El `PendingIntentFlags.OneShot` marca se pasa a la `PendingIntent.GetActivity` método para que el `PendingIntent` se usa solo una vez. Cuando se ejecuta este código, se muestra la siguiente notificación:

![Primera notificación de acción](local-notifications-images/10-first-action-notification.png)

Al puntear en esta notificación lleva al usuario a la actividad de origen.

En una aplicación de producción, la aplicación debe controlar la *pila de retroceso* cuando el usuario presiona el **volver** botón dentro de la actividad de notificación (si no está familiarizado con las tareas de Android y la pila de retroceso, consulte [ Tareas y pila de retroceso](https://developer.android.com/guide/components/tasks-and-back-stack.html)).
En la mayoría de los casos, navegar hacia atrás fuera de la actividad de notificación debe devolver el usuario fuera de la aplicación y volver a la pantalla principal. Para administrar la pila de retroceso, la aplicación usa el [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) clase para crear un `PendingIntent` con una pila de retroceso.

Otra consideración del mundo real es que la actividad de origen que necesite enviar datos a la actividad de notificación. Por ejemplo, la notificación puede indicar que ha llegado un mensaje de texto, y la actividad de notificación (mensaje ver la pantalla), se requiere el identificador del mensaje para mostrar el mensaje al usuario. La actividad que crea el `PendingIntent` puede usar el [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) método para agregar datos (por ejemplo, una cadena) a la intención para que estos datos se pasan a la actividad de notificación.

Ejemplo de código siguiente muestra cómo usar `TaskStackBuilder` administrar la pila de retroceso e incluye un ejemplo de cómo enviar una cadena de mensaje único a una actividad de notificación denominada `SecondActivity`:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

En este ejemplo de código, la aplicación consta de dos actividades: `MainActivity` (que contiene el código de notificación anterior), y `SecondActivity`, la pantalla que el usuario ve después de pulsar la notificación. Cuando se ejecuta este código, se presenta una notificación sencilla (similar al ejemplo anterior). Pulsar la notificación lleva al usuario a la `SecondActivity` pantalla:

![Segunda captura de pantalla de actividad](local-notifications-images/11-second-activity.png)

El mensaje de cadena (pasar a la intención `PutExtra` método) se recupera en `SecondActivity` a través de esta línea de código:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Este mensaje recuperado, "Saludos de MainActivity!", se muestra en el `SecondActivity` de pantalla, como se muestra en la captura de pantalla anterior. Cuando el usuario presiona el **Atrás** en el botón mientras `SecondActivity`, navegación conduce fuera de la aplicación y volver a la pantalla anterior el inicio de la aplicación.

Para obtener más información acerca de cómo crear pendientes intenciones, consulte [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Más allá de la notificación básica

Las notificaciones de forma predeterminada un formato de diseño base simple en Android, pero puede mejorar este formato básico realizando adicionales `NotificationCompat.Builder` llamadas al método. En esta sección, obtendrá información sobre cómo agregar un icono de foto grande a la notificación y verá ejemplos de cómo crear notificaciones de diseño expandida.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato de iconos grandes

Las notificaciones de Android suelen mostrar el icono de la aplicación original (en el lado izquierdo de la notificación). Sin embargo, pueden mostrar las notificaciones de una imagen o una foto (un *iconos grandes*) en lugar del icono pequeño estándar. Por ejemplo, una aplicación de mensajería podría mostrar una foto del remitente en lugar de con el icono de la aplicación.

Este es un ejemplo de una notificación de Android 5.0 básica &ndash; muestra sólo el icono de aplicación pequeña:

![Notificación normal de ejemplo](local-notifications-images/13-sample-notification.png)

Y aquí es una captura de pantalla de la notificación después de modificarlo para mostrar un icono grande &ndash; utiliza un icono creado a partir de una imagen de un objeto monkey código de Xamarin:

![Notificación de icono grande de ejemplo](local-notifications-images/14-large-icon-sample.png)

Tenga en cuenta que, cuando una notificación se presenta en formato de iconos grandes, el icono pequeño de aplicación se muestra como un distintivo en la esquina inferior derecha del icono grande.

Para usar una imagen como un icono grande en una notificación, se llama el generador de notificación [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) método y pase un mapa de bits de la imagen. A diferencia de `SetSmallIcon`, `SetLargeIcon` solo acepta un mapa de bits. Para convertir un archivo de imagen en un mapa de bits, utilice el [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) clase. Por ejemplo:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Este código de ejemplo abre el archivo de imagen **Resources/drawable/monkey_icon.png**, lo convierte en un mapa de bits y pasa el mapa de bits resultante `NotificationCompat.Builder`. Normalmente, la resolución de imagen de origen es mayor que el pequeño icono &ndash; pero no mucho más grandes. Una imagen que es demasiado grande puede provocar que las operaciones de cambio de tamaño innecesarias que podrían retrasar la publicación de la notificación.


### <a name="big-text-style"></a>Estilo de texto grande

El *texto grande* estilo es una plantilla de diseño expandida que usa para mostrar mensajes largos en las notificaciones. Al igual que todas las notificaciones expandidas de diseño, la notificación de texto grande se muestra inicialmente en un formato compacto de presentación:

![Notificación de texto grande de ejemplo](local-notifications-images/15-big-text-notification.png)

En este formato, se muestra solo un fragmento del mensaje, termina con dos puntos. Cuando el usuario arrastra hacia abajo en la notificación, se expande para mostrar el mensaje de notificación completo:

![Notificación de texto grande expandido](local-notifications-images/16-big-text-expanded.png)

Este formato expandido de diseño también incluye el texto de resumen en la parte inferior de la notificación. El alto máximo de la *texto grande* notificación es 256 dp.

Para crear un *texto grande* notificación, crear una instancia de un `NotificationCompat.Builder` objeto, como antes y, a continuación, crear instancias y agregue un [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) de objeto para el `NotificationCompat.Builder` objeto. Este es un ejemplo:

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

En este ejemplo, el texto del mensaje y el texto de resumen se almacenan en el `BigTextStyle` objeto (`textStyle`) antes de pasarlo a `NotificationCompat.Builder.`


### <a name="image-style"></a>Estilo de imagen

El *imagen* estilo (también denominado el *panorama* estilo) es un formato de notificación expandida que puede usar para mostrar una imagen en el cuerpo de una notificación. Por ejemplo, puede usar una aplicación de captura de pantalla o una aplicación fotográfica la *imagen* notificación de estilo para proporcionar al usuario con una notificación de la última imagen que se capturó. Tenga en cuenta que el alto máximo de la *imagen* notificación es 256 dp &ndash; Android cambiará de tamaño de la imagen para ajustarse a esta restricción de altura máxima, dentro de los límites de memoria disponible.

Igual que todos se expanden las notificaciones de diseño, *imagen* las notificaciones se muestran por primera vez en un formato compacto que muestra un extracto del texto del mensaje de que lo acompaña:

![Notificación Compact imagen muestra ninguna imagen](local-notifications-images/17-image-compact.png)

Cuando el usuario arrastra hacia abajo la *imagen* notificación, se expande para mostrar una imagen. Por ejemplo, esta es la versión expandida de la notificación anterior:

![Imagen expandida notificación revela imagen](local-notifications-images/18-image-expanded.png)

Tenga en cuenta que, cuando la notificación se muestra en formato compacto, muestra el texto de notificación (el texto que se pasa al generador de la notificación `SetContentText` método, como se mostró anteriormente). Sin embargo, cuando la notificación se expande para mostrar la imagen, muestra el texto de resumen sobre la imagen.

Para crear un *imagen* notificación, crear una instancia de un `NotificationCompat.Builder` objeto igual que antes y, a continuación, crear e insertar un [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) objeto en el `NotificationCompat.Builder` objeto. Por ejemplo:

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

Al igual que el `SetLargeIcon` método de `NotificationCompat.Builder`, el [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) método `BigPictureStyle` requiere un mapa de bits de la imagen que desea mostrar en el cuerpo de la notificación. En este ejemplo, el [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) método `BitmapFactory` lee el archivo de imagen que se encuentra en **Resources/drawable/x_bldg.png** y lo convierte en un mapa de bits.

También puede mostrar las imágenes que no se empaquetan como un recurso. Por ejemplo, el siguiente código de ejemplo carga una imagen de la tarjeta SD local y lo muestra en un *imagen* notificación:

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

En este ejemplo, el archivo de imagen ubicada en **/sdcard/Pictures/my-tshirt.jpg** cargado, cambia de tamaño a la mitad de su tamaño original y, a continuación, se convierte en un mapa de bits para su uso en la notificación:

![Imagen de camiseta de ejemplo en la notificación](local-notifications-images/19-tshirt-notification.png)

Si no conoce de antemano el tamaño del archivo de imagen, es una buena idea encapsular la llamada a [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) en un controlador de excepciones &ndash; un `OutOfMemoryError` se podría producir la excepción si la imagen es demasiado grande para Android para cambiar el tamaño.

Para obtener más información sobre cómo cargar y descodificar imágenes de mapa de bits grande, consulte [cargar eficazmente mapas de bits grandes](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).


### <a name="inbox-style"></a>Estilo de la Bandeja de entrada

El *Bandeja de entrada* formato es una plantilla de diseño expandido pensada para mostrar diferentes líneas de texto (por ejemplo, una bandeja de entrada de correo electrónico resumen) en el cuerpo de la notificación. El *Bandeja de entrada* notificación de formato aparece por primera vez en un formato compacto:

![Notificación de bandeja de entrada compact de ejemplo](local-notifications-images/20-inbox-compact.png)

Cuando el usuario arrastra hacia abajo en la notificación, se expande para mostrar un resumen de correo electrónico tal como se muestra en la captura de pantalla siguiente:

![Expandidos la notificación de bandeja de entrada de ejemplo](local-notifications-images/21-inbox-expanded.png)

Para crear un *Bandeja de entrada* notificación, crear una instancia de un `NotificationCompat.Builder` objeto, como antes y agregue un [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) de objeto para el `NotificationCompat.Builder`. Este es un ejemplo:

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

Para agregar nuevas líneas de texto al cuerpo de la notificación, llame el [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) método de la `InboxStyle` objeto (el alto máximo de la *Bandeja de entrada* notificación es 256 dp). Tenga en cuenta que, a diferencia de *texto grande* estilo, el *Bandeja de entrada* estilo admite líneas individuales de texto en el cuerpo de notificación.

También puede usar el *Bandeja de entrada* estilo para las notificaciones que se deba mostrar líneas individuales de texto en un formato expandido. Por ejemplo, el *Bandeja de entrada* estilo notificación puede utilizarse para combinar varias notificaciones pendientes en una notificación de resumen &ndash; puede actualizar un único *Bandeja de entrada* estilo notificación con new líneas de contenido de la notificación (consulte [actualizando una notificación](#updating-a-notification) anteriormente), en lugar de generar una secuencia continua de las notificaciones nuevas, principalmente similares.


## <a name="configuring-metadata"></a>Configuración de metadatos

`NotificationCompat.Builder` incluye métodos que se pueden llamar para establecer los metadatos de la notificación, como la prioridad, visibilidad y categoría. Android usa esta información &mdash; junto con la configuración de preferencias de usuario &mdash; para determinar cómo y cuándo quiere mostrar las notificaciones.


### <a name="priority-settings"></a>Configuración de prioridad

Aplicaciones que se ejecutan en Android 7.1 y versiones inferiores se deben establecer la prioridad directamente en la notificación. El valor de prioridad de una notificación determina los dos resultados cuando se publica la notificación:

-   Donde aparezca la notificación en relación con otras notificaciones.
    Por ejemplo, las notificaciones de prioridad alta se presentan por encima de las notificaciones de prioridad inferior en el cajón de notificaciones, con independencia de cuando se publica cada notificación.

-   Si la notificación se muestra en el formato de notificación de pantalla de datos (Android 5.0 y versiones posterior). Solo *alta* y *máximo* las notificaciones de prioridad se muestran como notificaciones de aviso.

Xamarin.Android define las siguientes enumeraciones para establecer la prioridad de notificación:

-   `NotificationPriority.Max` &ndash; Alerta al usuario a una condición urgente o crítica (por ejemplo, una llamada entrante, giro por instrucciones o una alerta de emergencia). En Android 5.0 y dispositivos posteriores, las notificaciones de prioridad máximo se muestran en formato de aviso.

-   `NotificationPriority.High` &ndash; Informa al usuario de eventos importantes (por ejemplo, los correos electrónicos importantes o la llegada de mensajes de chat en tiempo real). En Android 5.0 y dispositivos posteriores, las notificaciones de prioridad alta se muestran en formato de aviso.

-   `NotificationPriority.Default` &ndash; Notifica al usuario sobre las condiciones que tienen un nivel medio de importancia.

-   `NotificationPriority.Low` &ndash; Para obtener información que no sean urgentes que el usuario debe ser informado de (por ejemplo, los avisos de actualización de software o actualizaciones de red social).

-   `NotificationPriority.Min` &ndash; Para obtener información general que el usuario solo cuando los avisos visualización de notificaciones (por ejemplo, información de ubicación o el tiempo).

Para establecer la prioridad de una notificación, llame a la [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) método de la `NotificationCompat.Builder` objeto, pasando el nivel de prioridad. Por ejemplo:

```csharp
builder.SetPriority (NotificationPriority.High);
```

En el ejemplo siguiente, la notificación de alta prioridad, "Un mensaje importante!" aparece en la parte superior del cajón de notificaciones:

![Notificación de alta prioridad de ejemplo](local-notifications-images/22-hi-priority-drawer.png)

Dado que esta es una notificación de alta prioridad, también se muestra como una notificación de aviso por encima de la pantalla de actividad actual del usuario en Android 5.0:

![Notificación de aviso de ejemplo](local-notifications-images/23-heads-up-example.png)

En el ejemplo siguiente, se muestra la notificación "Pensado para el día" de prioridad baja en una notificación de nivel de batería de mayor prioridad:

![Notificación de prioridad baja de ejemplo](local-notifications-images/24-lo-priority-drawer.png)

Dado que la notificación "Pensamiento del día" es una notificación de prioridad baja, Android no lo mostrará en formato de pantalla de datos.

> [!NOTE]
> En Android 8.0 y versiones posteriores, la prioridad de la configuración de usuario y de canal de notificación determina la prioridad de la notificación.

### <a name="visibility-settings"></a>Configuración de visibilidad

A partir de Android 5.0, el *visibilidad* configuración está disponible para controlar cuánto contenido de la notificación aparece en la pantalla de bloqueo seguras.
Xamarin.Android define las siguientes enumeraciones para establecer la visibilidad de notificación:

-   `NotificationVisibility.Public` &ndash; Todo el contenido de la notificación se muestra en la pantalla de bloqueo seguras.

-   `NotificationVisibility.Private` &ndash; Información esencial solo se muestra en la pantalla de bloqueo seguras (por ejemplo, el icono de notificación y el nombre de la aplicación que registró), pero el resto de los detalles de la notificación están ocultos. De forma predeterminada todas las notificaciones `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; No se mostrará en la pantalla de bloqueo seguras, ni siquiera en el icono de notificación. El contenido de la notificación está disponible solo después de que el usuario desbloquea el dispositivo.

Para establecer la visibilidad de una notificación, la llamada a las aplicaciones la `SetVisibility` método de la `NotificationCompat.Builder` objeto, pasando la configuración de visibilidad. Por ejemplo, esta llamada a `SetVisibility` hace que la notificación `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Cuando un `Private` se publique una notificación, solo el nombre y el icono de la aplicación se muestra en la pantalla de bloqueo seguras. En lugar del mensaje de notificación, el usuario ve "Desbloquear el dispositivo para ver esta notificación":

![Desbloquee el mensaje de notificación de dispositivo](local-notifications-images/25-lockscreen-private.png)

En este ejemplo, **NotificationsLab** es el nombre de la aplicación original. Esta versión de la notificación censurada aparece solo cuando la pantalla de bloqueo es segura (es decir, protegido mediante contraseña, el patrón o PIN) &ndash; si la pantalla de bloqueo no es segura, todo el contenido de la notificación está disponible en la pantalla de bloqueo.


### <a name="category-settings"></a>Configuración de la categoría

A partir de Android 5.0, las categorías predefinidas están disponibles para clasificar y filtrar las notificaciones. Xamarin.Android proporciona las siguientes enumeraciones para estas categorías:

-   `Notification.CategoryCall` &ndash; Llamada telefónica entrante.

-   `Notification.CategoryMessage` &ndash; Mensaje de texto entrante.

-   `Notification.CategoryAlarm` &ndash; Una alarma condición o expiración del temporizador.

-   `Notification.CategoryEmail` &ndash; Mensaje de correo electrónico entrante.

-   `Notification.CategoryEvent` &ndash; Un evento de calendario.

-   `Notification.CategoryPromo` &ndash; Un mensaje promocional o anuncio.

-   `Notification.CategoryProgress` &ndash; El progreso de una operación en segundo plano.

-   `Notification.CategorySocial` &ndash; Actualización de redes sociales.

-   `Notification.CategoryError` &ndash; Error de un proceso de autenticación o la operación en segundo plano.

-   `Notification.CategoryTransport` &ndash; Actualización de la reproducción de medios.

-   `Notification.CategorySystem` &ndash; Reservado para uso del sistema (estado del sistema o dispositivo).

-   `Notification.CategoryService` &ndash; Indica que se está ejecutando un servicio en segundo plano.

-   `Notification.CategoryRecommendation` &ndash; Un mensaje de recomendación relacionada con la aplicación en ejecución.

-   `Notification.CategoryStatus` &ndash; Información sobre el dispositivo.

Cuando se ordenan las notificaciones, prioridad de la notificación tiene prioridad sobre el valor de la categoría. Por ejemplo, una notificación de alta prioridad se mostrará como sobreimpresa incluso si pertenece a la `Promo` categoría. Para establecer la categoría de una notificación, llame a la `SetCategory` método de la `NotificationCompat.Builder` objeto, pasando el valor de la categoría. Por ejemplo:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

El *no molestar* (nueva característica de Android 5.0) filtra las notificaciones basadas en categorías. Por ejemplo, el *no molestar* pantalla en **configuración** permite al usuario exentos notificaciones para llamadas telefónicas y mensajes:

![No molestar modificadores de la pantalla](local-notifications-images/26-do-not-disturb.png)

Cuando el usuario configura *no molestar* para bloquear todas las interrupciones, excepto las llamadas de teléfono (como se muestra en la captura de pantalla anterior), permite que las notificaciones con un valor de la categoría de Android `Notification.CategoryCall` esté presente mientras el dispositivo se encuentra en *no molestar* modo. Tenga en cuenta que `Notification.CategoryAlarm` notificaciones nunca se bloquean *no molestar* modo.

El [LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) ejemplo muestra cómo usar `NotificationCompat.Builder` para iniciar una segunda actividad desde una notificación. Este código de ejemplo se explica en el [usando notificaciones locales en Xamarin.Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) tutorial.


### <a name="notification-styles"></a>Estilos de notificación

Para crear *texto grande*, *imagen*, o *Bandeja de entrada* estilo notificaciones con `NotificationCompat.Builder`, la aplicación debe usar las versiones de compatibilidad de estos estilos. Por ejemplo, para usar el *texto grande* estilo, crear una instancia de `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

De forma similar, puede usar la aplicación `NotificationCompat.InboxStyle` y `NotificationCompat.BigPictureStyle` para *Bandeja de entrada* y *imagen* estilos, respectivamente.


### <a name="notification-priority-and-category"></a>Notificación prioridad y categoría

`NotificationCompat.Builder` admite la `SetPriority` (método) (disponible a partir Android 4.1). Sin embargo, el `SetCategory` método es *no* admite `NotificationCompat.Builder` porque categorías forman parte del nuevo sistema de metadatos de notificación que se introdujo en Android 5.0.

Compatibilidad con versiones anteriores de Android, dónde `SetCategory` es no está disponible, el código puede comprobar el nivel de API en tiempo de ejecución para llamar condicionalmente `SetCategory` cuando el nivel de API es igual o mayor que Android 5.0 (API nivel 21):

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

En del este ejemplo, la aplicación **.NET Framework de destino** está establecido en Android 5.0 y **versión mínima de Android** está establecido en **Android 4.1 (nivel de API 16)** . Dado que `SetCategory` está disponible en el nivel de API 21 o posterior, llamará a este código de ejemplo `SetCategory` solo cuando está disponible &ndash; no llamará a `SetCategory` cuando el nivel de API es inferior a 21.


### <a name="lock-screen-visibility"></a>Visibilidad de la pantalla de bloqueo

Dado que Android no eran compatibles con las notificaciones de pantalla de bloqueo antes de Android 5.0 (API nivel 21), `NotificationCompat.Builder` no admite el `SetVisibility` método. Como se explicó anteriormente para `SetCategory`, el código puede comprobar el nivel de API en tiempo de ejecución y llamada `SetVisiblity` solo cuando está disponible:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>Resumen

En este artículo se explica cómo crear notificaciones locales en Android. Describe la Anatomía de una notificación, se ha explicado cómo usar `NotificationCompat.Builder` para crear notificaciones, cómo las notificaciones de estilo en iconos grandes, *texto grande*, *imagen* y *Bandeja de entrada*  formatos, cómo establecer la configuración de los metadatos como la prioridad, visibilidad y categoría de notificación y cómo iniciar una actividad desde una notificación. En este artículo también se describe cómo funcionan estas opciones de notificación con el nuevo aviso, pantalla de bloqueo, y *no molestar* las características introducidas en Android 5.0. Por último, ha aprendido a usar `NotificationCompat.Builder` para mantener la notificación de compatibilidad con versiones anteriores de Android.

Para obtener instrucciones acerca de las notificaciones de diseño para Android, vea [notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="related-links"></a>Vínculos relacionados

- [NotificationsLab (sample)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (ejemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notificaciones locales en el tutorial de Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notificar al usuario](https://developer.android.com/training/notify-user/index.html)
- [Notificación](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
