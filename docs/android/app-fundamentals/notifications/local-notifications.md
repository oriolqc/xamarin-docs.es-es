---
title: Notificaciones locales
description: Esta sección muestra cómo implementar notificaciones locales en Xamarin.Android. Explica los distintos elementos de interfaz de usuario de una notificación de Android y se describe la API del implica crear y mostrar una notificación.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 97c8372656f0cbfa5b8f7bb12d15b00feac4b5c3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773997"
---
# <a name="local-notifications"></a>Notificaciones locales

_Esta sección muestra cómo implementar notificaciones locales en Xamarin.Android. Explica los distintos elementos de interfaz de usuario de una notificación de Android y se describe la API del implica crear y mostrar una notificación._

## <a name="local-notifications-overview"></a>Información general de notificaciones local

En este tema se explica cómo implementar notificaciones locales en una aplicación Xamarin.Android. Describe las distintas partes de una notificación de Android, se explican los estilos diferentes de notificación que están disponibles para los desarrolladores de aplicaciones y lo presenta algunas de las API que se usan para crear y publicar las notificaciones.

Android proporciona dos áreas controlados por el sistema para mostrar iconos de notificación y la información de notificación al usuario. Cuando se publica una notificación por primera vez, su icono se muestra en el *área de notificación*, tal y como se muestra en la siguiente captura de pantalla:

![Área de notificación de ejemplo en un dispositivo](local-notifications-images/01-notification-shade.png)

Para obtener detalles acerca de la notificación, el usuario puede abrir el cajón de notificaciones (que se expande cada icono de notificación para mostrar el contenido de la notificación) y realizar las acciones asociadas a las notificaciones. La siguiente captura de pantalla muestra una *cajón de notificaciones* que corresponde al área de notificación muestra sobre:

[![Cajón de notificaciones de ejemplo muestra tres notificaciones](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Notificaciones Android usan dos tipos de diseños:

-   ***Diseño base*** &ndash; un formato de presentación de compact, fijo.

-   ***Diseño expandido*** &ndash; un formato de presentación que se pueda expandir a un tamaño mayor para obtener más información.

En las secciones siguientes se explican cada uno de estos tipos de diseño (y cómo crearlos).


### <a name="base-layout"></a>Diseño de base

Todas las notificaciones Android están integradas en el formato de diseño de base, que, como mínimo, incluye los siguientes elementos:

1.  A *icono de notificación*, que representa la aplicación de origen o el tipo de notificación si la aplicación admite distintos tipos de notificaciones.

2.  La notificación *título*, o el nombre del remitente si la notificación es un mensaje personal.

3.  El mensaje de notificación.

4.  A *marca de tiempo*.

Estos elementos se muestran como se muestra en el diagrama siguiente:

[![Ubicación de los elementos de notificación](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Diseños de base se limita a 64 independiente de la densidad de píxeles (dp) de alto. Android crea este estilo de notificaciones básico de forma predeterminada.

Si lo desea, las notificaciones pueden mostrar un icono grande que representa la aplicación o una foto del remitente. Cuando se utiliza un icono grande en una notificación de Android 5.0 y versiones posteriores, el icono de notificación pequeña se muestra como un distintivo sobre el icono de gran tamaño:

![Foto de notificación simple](local-notifications-images/04-simple-notification-photo.png)

A partir de Android 5.0, las notificaciones también pueden aparecer en la pantalla de bloqueo:

[![Notificación de pantalla de bloqueo de ejemplo](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

El usuario hacer doble punteo en la notificación de pantalla de bloqueo para desbloquear el dispositivo y saltar a la aplicación que se originó dicha notificación, o pase a descartar la notificación. Las aplicaciones pueden establecer el nivel de visibilidad de una notificación para controlar lo que se muestra en la pantalla de bloqueo y los usuarios pueden elegir si desea permitir contenido confidencial que se mostrará en las notificaciones de la pantalla de bloqueo.

Android 5.0 introdujo un formato de presentación de notificación de alta prioridad denominado *aviso*. Las notificaciones de aviso deslice hacia abajo desde la parte superior de la pantalla durante unos segundos y, a continuación, retreat copia de seguridad en el área de notificación:

[![Notificación de pantalla de datos de ejemplo](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Las notificaciones de aviso hacen posible para el sistema de la interfaz de usuario para colocar información importante delante del usuario sin interrumpir el estado de la actividad que se está ejecutando.

Android incluye compatibilidad con metadatos de la notificación para que las notificaciones se pueden ordenar y mostrar cambien. Metadatos de la notificación también controla cómo se presentan las notificaciones en la pantalla de bloqueo y en formato de aviso. Las aplicaciones pueden establecer los siguientes tipos de metadatos de la notificación:

-   **Prioridad** &ndash; el nivel de prioridad determina cómo y cuándo se presentan las notificaciones. Por ejemplo, en Android 5.0, se muestran las notificaciones de alta prioridad como notificaciones de aviso.

-   **Visibilidad** &ndash; especifica cuánto contenido de la notificación se mostrará cuando aparezca la notificación en la pantalla de bloqueo.

-   **Categoría** &ndash; informa al sistema cómo controlar la notificación en diversas circunstancias, como cuando el dispositivo se *no molestar* modo.

**Nota:** **visibilidad** y **categoría** se presentaron en Android 5.0 y no están disponibles en versiones anteriores de Android. A partir de Android 8.0, [canales de notificación](#notif-chan) se utilizan para controlar cómo se presentan las notificaciones al usuario.


### <a name="expanded-layouts"></a>Diseños expandidos

A partir de Android 4.1, las notificaciones pueden configurarse con los estilos de diseño expandida que permiten al usuario expandir el alto de la notificación para ver más contenido. Por ejemplo, en el ejemplo siguiente se muestra una notificación de diseño expandido en modo contratado:

![Notificación contratado](local-notifications-images/07-contracted-notification.png)

Cuando se expande esta notificación, muestra el mensaje completo:

![Notificación expandido](local-notifications-images/08-expanded-notification.png)

Android admite tres estilos de diseño ampliadas para las notificaciones de eventos único:

-   ***Texto grande*** &ndash; en modo contratado, muestra un extracto de la primera línea del mensaje seguido de dos puntos. En el modo expandido, muestra el mensaje completo (como se muestra en el ejemplo anterior).

-   ***Bandeja de entrada*** &ndash; en modo contratado, muestra el número de mensajes nuevos. En el modo expandido, muestra el primer mensaje de correo electrónico o una lista de los mensajes en la Bandeja de entrada.

-   ***Imagen*** &ndash; en modo contratado, muestra sólo el texto del mensaje. En el modo expandido, muestra el texto y una imagen.

[Más allá de la notificación básica](#beyond-the-basic-notification) (más adelante en este artículo), explica cómo crear *texto grande*, *Bandeja de entrada*, y *imagen* las notificaciones.


## <a name="notification-creation"></a>Creación de notificación

Para crear una notificación en Android, use la [Notification.Builder](https://developer.xamarin.com/api/type/Android.App.Notification+Builder/) clase. `Notification.Builder` se introdujo en Android 3.0 para simplificar la creación de objetos de notificación. Para crear las notificaciones que son compatibles con versiones anteriores de Android, puede usar el [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html) clase en lugar de `Notification.Builder` (consulte [compatibilidad](#compatibility) más adelante en este tema para Para obtener más información sobre el uso de `NotificationCompat.Builder`).
`Notification.Builder` Proporciona métodos para establecer las distintas opciones en una notificación, como:

-   El contenido, incluido el título, el texto del mensaje y el icono de notificación.

-   El estilo de la notificación, como *texto grande*, *Bandeja de entrada*, o *imagen* estilo.

-   La prioridad de la notificación: mínimo, baja, valor predeterminado, alto o máximo.

-   La visibilidad de la notificación en la pantalla de bloqueo: public, private o secreto.

-   Los metadatos de categoría que le ayuda a Android clasificar y filtrar la notificación.

-   Un intento opcional que indica una actividad para iniciar al que se derivan la notificación.

Después de establecer estas opciones en el generador, generar un objeto de notificación que contiene la configuración. Para publicar la notificación, se pasa este objeto de notificación para el *Notification Manager*. Android proporciona el [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) (clase), que es responsable de la publicación de las notificaciones y mostrarlos al usuario. Una referencia a esta clase puede obtenerse de cualquier contexto, por ejemplo, una actividad o un servicio.


### <a name="how-to-generate-a-notification"></a>Cómo generar una notificación

Para generar una notificación de Android, siga estos pasos:

1.  Crear una instancia de un `Notification.Builder` objeto.

2.  Llamar a varios métodos en la `Notification.Builder` objeto para establecer las opciones de notificación.

3.  Llame a la [generar](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) método de la `Notification.Builder` objeto que se va a crear una instancia de un objeto de notificación.

4.  Llame a la [notificar](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) método del Administrador de notificación para la notificación de publicación.

Debe proporcionar al menos la siguiente información para cada notificación:

-   Un pequeño icono (24 x 24 dp de tamaño)

-   Un título corto

-   El texto de la notificación

En el ejemplo de código siguiente se muestra cómo utilizar `Notification.Builder` para generar una notificación básica. Tenga en cuenta que `Notification.Builder` métodos admiten [método encadenamiento](http://en.wikipedia.org/wiki/Method_chaining); es decir, cada método devuelve el objeto de generador para que pueda usar el resultado de la última llamada de método para invocar la llamada de método siguiente:

```csharp
// Instantiate the builder and set notification elements:
Notification.Builder builder = new Notification.Builder (this)
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

En este ejemplo, un nuevo `Notification.Builder` objeto denominado `builder` es crear una instancia, se establecen el título y el texto de la notificación y el icono de notificación se cargan desde **Resources/drawable/ic_notification.png**. La llamada para el generador de notificación `Build` método crea un objeto de notificación con esta configuración. El paso siguiente consiste en llamar a la `Notify` método para el Administrador de notificaciones. Para encontrar el Administrador de notificaciones, se llama a `GetSystemService`, como se indicó anteriormente.

El `Notify` método acepta dos parámetros: el identificador de notificación y el objeto de notificación. El identificador de notificación es un entero único que identifica la notificación a la aplicación. En este ejemplo, el identificador de notificación se establece en cero (0); Sin embargo, en una aplicación de producción, deberá proporcionar un identificador único a cada notificación. Volver a usar el valor de identificador anterior en una llamada a `Notify` hace que la última notificación que se sobrescriba.

Cuando se ejecuta este código en un dispositivo Android 5.0, genera una notificación que es similar al ejemplo siguiente:

![Resultado de notificación para el código de ejemplo](local-notifications-images/09-hello-world.png)

El icono de notificación se muestra en el lado izquierdo de la notificación &ndash; esta imagen de un círculo con &ldquo;&rdquo; tiene un canal alfa para que Android puede dibujar un fondo gris circular detrás de él. También puede proporcionar un icono sin un canal alfa. Para mostrar una imagen fotográfica como un icono, consulte [un formato de iconos grandes](#large-icon-format) más adelante en este tema.

La marca de tiempo se establece automáticamente, pero puede invalidar esta configuración mediante una llamada a la [predeterminadoSi](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) método del generador de notificación. Por ejemplo, en el ejemplo de código siguiente se establece la marca de tiempo a la hora actual:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Habilitar sonidos y vibración

Si desea que la notificación también reproducir un sonido, puede llamar el generador de notificación [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) método y pase la `NotificationDefaults.Sound` marca:

```csharp
// Instantiate the notification builder and enable sound:
Notification.Builder builder = new Notification.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Esta llamada a `SetDefaults` hará que el dispositivo reproducir un sonido cuando se publica la notificación. Si desea que el dispositivo Vibrar en lugar de reproducir un sonido, puede pasar `NotificationDefaults.Vibrate` a `SetDefaults.` si desea que el dispositivo para reproducir un sonido y vibrar el dispositivo, puede pasar ambas marcas para `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Si habilita el sonido sin especificar un sonido que se va a reproducir, Android usa el sonido predeterminado de notificación del sistema. Sin embargo, puede cambiar el sonido que se reproducirá mediante una llamada del generador de notificación [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) método. Por ejemplo, para reproducir la alarma sonido con la notificación (en lugar del sonido de notificación predeterminado), puede obtener el URI para la alarma sonido desde el [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) y pasarlo a `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Como alternativa, puede usar el tono de predeterminado del sistema sonido para la notificación:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Después de crear un objeto de notificación, es posible establecer las propiedades de notificación en el objeto de notificación (en lugar de configurarlos de antemano a través `Notification.Builder` métodos). Por ejemplo, en lugar de llamar a la `SetDefaults` método para habilitar la vibración en una notificación, puede modificar directamente el marcador de bits de la notificación [tiene como valor predeterminado](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) propiedad:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

Este ejemplo hace que el dispositivo Vibrar cuando se publica la notificación.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>Actualizar una notificación

Si desea actualizar el contenido de una notificación una vez publicado, puede volver a existente `Notification.Builder` objeto para crear un nuevo objeto de notificación y publicar esta notificación con el identificador de la última notificación. Por ejemplo:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

En este ejemplo, existente `Notification.Builder` objeto se usa para crear un nuevo objeto de notificación con un mensaje y un título diferente.
El nuevo objeto de notificación se publicó con el identificador de la notificación anterior y se actualiza el contenido de la notificación de que se publicó anteriormente:

![Notificación actualizada](local-notifications-images/12-updated-notification.png)

El cuerpo de la notificación anterior se reutiliza &ndash; solo el título y el texto de la notificación cambia mientras se muestra la notificación en el cajón de notificaciones. El texto del título cambia de "Notificación de ejemplo" a "Actualizar notificación" y el texto del mensaje cambia de "¡Hello World! Este es mi primera notificación!" para "Cambiado a este mensaje".

Una notificación permanece visible hasta que se produce una de estas tres cosas:

-   El usuario descarta la notificación (o puntea *Borrar todo*).

-   La aplicación realiza una llamada a `NotificationManager.Cancel`, pasando el identificador único de notificación que se asignó cuando se publicó la notificación.

-   La aplicación llama `NotificationManager.CancelAll`.

Para obtener más información acerca de cómo actualizar notificaciones Android, consulte [modificar una notificación](http://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>A partir de una actividad desde una notificación

En Android, es habitual que una notificación asociar a un *acción* &ndash; una actividad que se inicia cuando el usuario puntea la notificación. Esta actividad puede residir en otra aplicación o incluso en otra tarea. Para agregar una acción a una notificación, se crea un [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) objeto y asociar la `PendingIntent` con la notificación. A `PendingIntent` es un tipo especial de intención de que permite a la aplicación del destinatario ejecutar un fragmento de código predefinido con los permisos de la aplicación de envío. Cuando el usuario puntea la notificación, Android inicia la actividad especificada por el `PendingIntent`.

El fragmento de código siguiente muestra cómo crear una notificación con un `PendingIntent` que iniciará la actividad de la aplicación original, `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
Notification.Builder builder = new Notification.Builder(this)
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

Este código es muy similar al código de notificación en la sección anterior, salvo que un `PendingIntent` se agrega al objeto de notificación. En este ejemplo, el `PendingIntent` está asociado a la actividad de la aplicación de origen antes de que se pasa al generador de la notificación [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) método. El `PendingIntentFlags.OneShot` marca se pasa a la `PendingIntent.GetActivity` método para que la `PendingIntent` se utiliza una sola vez. Cuando se ejecuta este código, se muestra la siguiente notificación:

![Primera notificación de acción](local-notifications-images/10-first-action-notification.png)

Al puntear en esta notificación lleva al usuario a la actividad de origen.

En una aplicación de producción, la aplicación debe controlar la *pila de retroceso* cuando el usuario presiona el **volver** botón dentro de la actividad de notificación (si no está familiarizado con la pila de retroceso y tareas de Android, consulte [ Tareas y pila de retroceso](http://developer.android.com/guide/components/tasks-and-back-stack.html)).
En la mayoría de los casos, navegar hacia atrás fuera de la actividad de notificación debe devolver al usuario de la aplicación y volver a la pantalla Inicio. Para administrar la pila de retroceso, su aplicación usa el [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) clase para crear un `PendingIntent` con una pila de retroceso.

Otra consideración del mundo real es que la actividad de origen puede necesario enviar datos a la actividad de notificación. Por ejemplo, la notificación puede indicar que ha llegado un mensaje de texto, y la actividad de notificación (un mensaje ver pantalla), necesita el identificador del mensaje para mostrar el mensaje al usuario. La actividad que crea la `PendingIntent` puede utilizar el [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) método para agregar datos (por ejemplo, una cadena) para el propósito para que estos datos se pasan a la actividad de notificación.

El siguiente ejemplo de código muestra cómo usar `TaskStackBuilder` administrar la pila de retroceso e incluye un ejemplo de cómo enviar una cadena de mensaje único a una actividad de notificación denominada `SecondActivity`:

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
Notification.Builder builder = new Notification.Builder (this)
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

En este ejemplo de código, la aplicación consta de dos actividades: `MainActivity` (que contiene el código de notificación anterior), y `SecondActivity`, la pantalla que el usuario ve después de puntear en la notificación. Cuando se ejecuta este código, se presenta una notificación simple (similar al ejemplo anterior). Puntee en la notificación lleva al usuario a la `SecondActivity` pantalla:

![Segunda captura de pantalla de actividad](local-notifications-images/11-second-activity.png)

El mensaje de cadena (pasará la intención `PutExtra` método) se recupera en `SecondActivity` a través de esta línea de código:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Este mensaje recuperado, "Saludos de MainActivity!", se muestra en el `SecondActivity` pantalla, como se muestra en la captura de pantalla anterior. Cuando el usuario presiona el **Atrás** en el botón al `SecondActivity`, navegación conduce fuera de la aplicación y vuelve a la pantalla anterior del inicio de la aplicación.

Para obtener más información acerca de cómo crear pendientes del color, vea [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="notif-chan" />

## <a name="notification-channels"></a>Canales de notificación

A partir de Android 8.0 (Oreos), puede usar el *canales de notificación* característica para crear un canal personalizables por el usuario para cada tipo de notificación que desea mostrar. Canales de notificación que lo posible a notificaciones de grupo para que todas las notificaciones que publican en un documento de canal el mismo comportamiento. Por ejemplo, podría tener un canal de notificación está pensado para las notificaciones que requieren atención inmediata y un canal "funcionamiento" independiente que se usa para mensajes informativos.

El **YouTube** aplicación que se instala con Android Oreos muestra dos categorías de notificación: **descargar notificaciones** y **notificaciones General**:

[![Pantallas de notificación de YouTube en Oreos Android](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Cada una de estas categorías corresponde a un canal de notificación. Implementa la aplicación de YouTube un **descargar notificaciones** canal y un **General notificaciones** canal. El usuario puede pulsar **descargar notificaciones**, que muestra la pantalla de configuración para la aplicación de la descarga del canal de notificaciones:

[![Descargar la pantalla de notificaciones para la aplicación de YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

En esta pantalla, el usuario puede modificar el comportamiento de la **descargar** notificaciones del canal mediante las acciones siguientes:

-   Establecer la importancia nivel en **urgente**, **alta**, **medio**, o **bajo**, que configura el nivel de interrupción de sonido y visual.

-   Activar o desactivar la el punto de notificación.

-   La luz intermitente activar o desactivar.

-   Mostrar u ocultar notificaciones en la pantalla de bloqueo.

-   Invalidar el **no molestar** configuración.

El **General notificaciones** canal tiene una configuración similar:

[![Pantalla de notificaciones general para la aplicación de YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Observe que no tiene un control absoluto sobre cómo interactúan los canales de notificación con el usuario &ndash; el usuario puede modificar la configuración de cualquier canal de notificación en el dispositivo, tal como se muestra en las capturas de pantalla anterior. Sin embargo, puede configurar valores predeterminados (tal y como se describe a continuación). Como se muestran en estos ejemplos, la nueva característica de canal de notificación permite para que pueda proporcionar a los usuarios control más preciso sobre los diferentes tipos de notificaciones.

¿Debe agregar compatibilidad para los canales de notificación a la aplicación? Si va a usar Android 8.0, la aplicación *debe* implementar canales de notificación.
Se producirá un error en una aplicación de destino para Oreos que intenta enviar una notificación local al usuario sin usar un canal de notificación mostrar la notificación en los dispositivos Oreos. Si no destino Android 8.0, la aplicación se ejecutará en 8.0 Android, pero con el mismo comportamiento de notificación tal y como mostraría una cuando se ejecuta en Android 7.1 o versiones anteriores.


### <a name="creating-a-notification-channel"></a>Crear un canal de notificación

Para crear un canal de notificación, haga lo siguiente:

1. Construir un [NotificationChannel](https://developer.android.com/reference/android/app/NotificationChannel.html) objeto con lo siguiente:

    - Una cadena de identificador que es única dentro de su paquete. En el ejemplo siguiente, la cadena `com.xamarin.myapp.urgent` se utiliza.

    - El nombre de usuario visible del canal (menos de 40 caracteres). En el ejemplo siguiente, el nombre **urgente** se utiliza.

    - La importancia del canal, que controla cómo molestos notificaciones se registran en el `NotificationChannel`. La importancia puede ser `Default`, `High`, `Low`, `Max`, `Min`, `None`, o `Unspecified`.

    Pasar estos valores para la [constructor](https://developer.android.com/reference/android/app/NotificationChannel.html#NotificationChannel%28java.lang.String,%20java.lang.CharSequence,%20int%29) (en este ejemplo, `Resource.String.noti_chan_urgent` está establecido en **urgente**):

    ```csharp
    public const string URGENT_CHANNEL = "com.xamarin.myapp.urgent";
    . . .
    string chanName = GetString (Resource.String.noti_chan_urgent);
    var importance = NotificationImportance.High;
    NotificationChannel chan =
       new NotificationChannel (URGENT_CHANNEL, chanName, importance);
    ```

2.  Configurar la `NotificationChannel` objeto con la configuración inicial.
    Por ejemplo, el siguiente código configura el `NotificationChannel` objeto forma que las notificaciones que se publican en este canal se vibrar el dispositivo y aparecen en la pantalla de bloqueo predeterminada:

    ```csharp
    chan.EnableVibration (true);
    chan.LockscreenVisibility = NotificationVisibility.Public;
    ```

3.  Enviar el objeto de canal de notificación para el Administrador de notificaciones:

    ```csharp
    NotificationManager notificationManager =
        (NotificationManager) GetSystemService (NotificationService);
    notificationManager.CreateNotificationChannel (chan);
    ```


### <a name="posting-to-a-notifications-channel"></a>Exponer un elemento en un canal de notificaciones

Para enviar una notificación a un canal de notificación, haga lo siguiente:

1.  Configurar la notificación mediante el `Notification.Builder`, pasando el Id. de canal para el `SetChannelId` método. Por ejemplo:

    ```csharp
    Notification.Builder builder = new Notification.Builder (this)
        .SetContentTitle ("Attention!")
        .SetContentText ("This is an urgent notification message!")
        .SetChannelId (URGENT_CHANNEL);
    ```

2.  Crear y emitir la notificación mediante el Administrador de notificaciones [notificar](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/p/System.Int32/Android.App.Notification/) método:

    ```csharp
    const int notificationId = 0;
    notificationManager.Notify (notificationId, builder.Build());
    ```

Puede repetir los pasos anteriores para crear otro canal de notificación para mensajes informativos. Este segundo canal podría, de forma predeterminada, deshabilitar vibración, establezca la visibilidad de pantalla de bloqueo predeterminado en `Private`y establece la importancia de notificación en `Default`.

Para obtener un ejemplo de código completo de canales de notificación de Android Oreos en acción, vea la [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) ejemplo; esta aplicación de ejemplo administra dos canales y establece las opciones de notificación adicionales.



<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Más allá de notificaciones básico

Las notificaciones de forma predeterminada un formato sencillo diseño de base de Android, pero puede mejorar este formato básico realizando adicionales `Notification.Builder` llamadas al método. En esta sección, obtendrá información sobre cómo agregar un icono de foto grande a la notificación y podrá ver ejemplos de cómo crear notificaciones de diseño expandido.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato de iconos grandes

Notificaciones Android suelen mostrar el icono de la aplicación de origen (en el lado izquierdo de la notificación). Sin embargo, pueden mostrar las notificaciones de una imagen o una fotografía (un *iconos grandes*) en lugar del icono pequeño estándar. Por ejemplo, una aplicación de mensajería podría mostrar una foto de remitente en lugar de en el icono de la aplicación.

Este es un ejemplo de una notificación de Android 5.0 básica &ndash; muestra sólo el icono pequeño de aplicación:

![Notificación normal de ejemplo](local-notifications-images/13-sample-notification.png)

Y aquí es una captura de pantalla de la notificación después de modificarlo para mostrar un icono grande &ndash; utiliza un icono creado a partir de una imagen de un mono de código de Xamarin:

![Notificación de iconos grandes de ejemplo](local-notifications-images/14-large-icon-sample.png)

Tenga en cuenta que, cuando una notificación se presenta en formato de iconos grandes, el icono pequeño de aplicación se muestra como un distintivo en la esquina inferior derecha del icono grande.

Para utilizar una imagen como un icono grande en una notificación, llame el generador de notificación [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) método y pase un mapa de bits de la imagen. A diferencia de `SetSmallIcon`, `SetLargeIcon` solo acepta un mapa de bits. Para convertir un archivo de imagen en un mapa de bits, utilice la [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) clase. Por ejemplo:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Este código de ejemplo abre el archivo de imagen **Resources/drawable/monkey_icon.png**, lo convierte en un mapa de bits y pasa el mapa de bits resultante `Notification.Builder`. Normalmente, la resolución de imagen de origen es mayor que el icono pequeño &ndash; pero no mucho más grande. Una imagen que es demasiado grande puede dar lugar a operaciones de cambio de tamaño innecesarias que podrían retrasar el envío de la notificación.
Para obtener más información acerca de los tamaños de icono de notificación en Android, consulte [iconos de notificación](http://developer.android.com/design/style/iconography.html#notification).


### <a name="big-text-style"></a>Estilo de texto grande

El *texto grande* estilo es una plantilla de diseño expandida que usa para mostrar mensajes largos en las notificaciones. Al igual que todas las notificaciones de diseño expandida, la notificación de texto grande se muestra inicialmente en un formato de presentación compacta:

![Notificación de texto grande de ejemplo](local-notifications-images/15-big-text-notification.png)

En este formato, se muestra solo un extracto del mensaje, termina con dos puntos. Cuando el usuario arrastra hacia abajo en la notificación, se expande para mostrar el mensaje de notificación completo:

![Notificación de texto grande expandido](local-notifications-images/16-big-text-expanded.png)

Este formato expandido de diseño también incluye el texto de resumen en la parte inferior de la notificación. El alto máximo de la *texto grande* notificación es 256 dp.

Para crear un *texto grande* notificación, se crea una instancia una `Notification.Builder` objeto, como antes y, a continuación, crear una instancia y se agrega un [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) el objeto a la `Notification.Builder` objeto. Por ejemplo:

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

En este ejemplo, el texto del mensaje y el texto de resumen se almacenan en la `BigTextStyle` objeto (`textStyle`) antes de pasarlo a `Notification.Builder.`


### <a name="image-style"></a>Estilo de imagen

El *imagen* estilo (también denominado el *panorama* estilo) es un formato de notificación expandida que puede usar para mostrar una imagen en el cuerpo de una notificación. Por ejemplo, puede usar una aplicación de captura de pantalla o una aplicación de fotografía el *imagen* notificación de estilo que se va a proporcionar al usuario una notificación de la última imagen que se capturaron. Tenga en cuenta que el alto máximo de la *imagen* notificación es 256 dp &ndash; Android cambiará el tamaño de la imagen para ajustarla a esta restricción de alto máximo, dentro de los límites de memoria disponible.

Como ocurre con todas expande las notificaciones de diseño, *imagen* las notificaciones se muestran por primera vez en un formato compacto que muestra un extracto del texto del mensaje que lo acompaña:

![Notificación de imagen Compact no muestra ninguna imagen](local-notifications-images/17-image-compact.png)

Cuando el usuario arrastra hacia abajo la *imagen* notificación, se expande para mostrar una imagen. Por ejemplo, esta es la versión expandida de la notificación anterior:

![Imagen de muestra de notificación de imagen expandida](local-notifications-images/18-image-expanded.png)

Observe que, cuando la notificación se muestra en formato compacto, muestra el texto de notificación (el texto que se pasa al generador de la notificación `SetContentText` método, como se muestra anteriormente). Sin embargo, cuando la notificación se expande para mostrar la imagen, muestra el texto de resumen sobre la imagen.

Para crear un *imagen* notificación, se crea una instancia una `Notification.Builder` objeto como antes y, a continuación, crear e insertar un [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) objeto en el `Notification.Builder` objeto. Por ejemplo:

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

Como el `SetLargeIcon` método `Notification.Builder`, el [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) método `BigPictureStyle` requiere un mapa de bits de la imagen que desea que aparezca en el cuerpo de la notificación. En este ejemplo, el [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) método `BitmapFactory` lee el archivo de imagen se encuentra en **Resources/drawable/x_bldg.png** y lo convierte en un mapa de bits.

También puede mostrar las imágenes que no se empaquetan como un recurso. Por ejemplo, el código de ejemplo siguiente se carga una imagen de la tarjeta SD local y lo muestra en un *imagen* notificación:

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

En este ejemplo, el archivo de imagen está ubicada en **/sdcard/Pictures/my-tshirt.jpg** es cargar, cambiar de tamaño a la mitad de su tamaño original y, a continuación, se convierte en un mapa de bits para su uso en la notificación:

![Imagen de la camiseta de ejemplo de notificación](local-notifications-images/19-tshirt-notification.png)

Si no conoce de antemano el tamaño del archivo de imagen, es una buena idea que incluya la llamada a [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) en un controlador de excepciones &ndash; un `OutOfMemoryError` excepción se puede producir si la imagen es demasiado grande para Android para cambiar el tamaño.

Para obtener más información sobre cómo cargar y descodificar imágenes de mapa de bits grande, consulte [carga eficazmente los mapas de bits grandes](https://developer.xamarin.com/recipes/android/resources/general/load_large_bitmaps_efficiently).


### <a name="inbox-style"></a>Estilo de bandeja de entrada

El *Bandeja de entrada* formato es una plantilla de diseño expandido diseñada para mostrar diferentes líneas de texto (por ejemplo, una bandeja de entrada de correo electrónico resumen) en el cuerpo de la notificación. El *Bandeja de entrada* formato notificación aparece por primera vez en un formato compacto:

![Notificación de compact de la Bandeja de entrada de ejemplo](local-notifications-images/20-inbox-compact.png)

Cuando el usuario arrastra hacia abajo en la notificación, se expande para mostrar un resumen de correo electrónico tal como se muestra en la captura de pantalla siguiente:

![Notificación de bandeja de entrada de ejemplo expandido](local-notifications-images/21-inbox-expanded.png)

Para crear un *Bandeja de entrada* notificación, se crea una instancia una `Notification.Builder` objeto, como antes y agregue un [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) el objeto a la `Notification.Builder`. Por ejemplo:

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

Para agregar nuevas líneas de texto al cuerpo de la notificación, llame a la [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) método de la `InboxStyle` objeto (el alto máximo de la *Bandeja de entrada* notificación es 256 dp). Tenga en cuenta que, a diferencia de *texto grande* estilo, el *Bandeja de entrada* estilo admite líneas individuales de texto en el cuerpo de la notificación.

También puede usar el *Bandeja de entrada* estilo para las notificaciones que necesita para mostrar las líneas individuales de texto en un formato expandido. Por ejemplo, el *Bandeja de entrada* estilo de notificación se puede usar para combinar varias notificaciones pendientes en una notificación de resumen &ndash; puede actualizar un único *Bandeja de entrada* estilo notificación con new líneas de contenido de la notificación (consulte [actualizar una notificación](#updating-a-notification) anteriormente), en lugar de generar un flujo continuo de nuevo, de forma similares en su mayor parte de las notificaciones. Para obtener más información acerca de este enfoque, consulte [resumir las notificaciones](http://developer.android.com/design/patterns/notifications.html#summarize_your_notifications).


## <a name="configuring-metadata"></a>Configuración de metadatos

`Notification.Builder` incluye métodos que se pueden llamar para establecer los metadatos acerca de la notificación, por ejemplo, prioridad, la visibilidad y la categoría. Android usa esta información &mdash; junto con la configuración de preferencias de usuario &mdash; para determinar cómo y cuándo quiere mostrar las notificaciones.


### <a name="priority-settings"></a>Configuración de prioridad

El valor de prioridad de una notificación determina dos resultados cuando se publica la notificación:

-   Donde aparezca la notificación en relación con otras notificaciones.
    Por ejemplo, las notificaciones de prioridad alta se presentan por encima de las notificaciones de prioridad inferior en el cajón de notificaciones, sin tener en cuenta cuando se publica cada notificación.

-   Si la notificación se muestra en el formato de notificación de pantalla de datos (Android 5.0 y versiones posteriores). Solo *alta* y *máximo* las notificaciones de prioridad se muestran como notificaciones de aviso.

Xamarin.Android define las siguientes enumeraciones para establecer la prioridad de notificación:

-   `NotificationPriority.Max` &ndash; Alerta de una condición crítica o urgente (por ejemplo, una llamada entrante, giro por instrucciones o una alerta de emergencia). En Android 5.0 y versiones posteriores, las notificaciones de prioridad máximo se muestran en formato de aviso.

-   `NotificationPriority.High` &ndash; Informa al usuario de eventos importantes (como mensajes de correo electrónico importantes o la llegada de mensajes de chat en tiempo real). En Android 5.0 y versiones posteriores, las notificaciones de prioridad alta se muestran en formato de aviso.

-   `NotificationPriority.Default` &ndash; Notifica al usuario de condiciones que tienen un nivel medio de importancia.

-   `NotificationPriority.Low` &ndash; Para obtener información no urgentes que el usuario debe ser informado de (por ejemplo, recordatorios de actualización de software o actualizaciones de red social).

-   `NotificationPriority.Min` &ndash; Para obtener información general que el usuario observa solo cuando ver notificaciones (por ejemplo, información de ubicación o el tiempo).

Para establecer la prioridad de una notificación, llame a la [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) método de la `Notification.Builder` objeto, que se pasa en el nivel de prioridad. Por ejemplo:

```csharp
builder.SetPriority (NotificationPriority.High);
```

En el ejemplo siguiente, la notificación de alta prioridad, "Un mensaje importante!" aparece en la parte superior del cajón de notificaciones:

![Notificación de alta prioridad de ejemplo](local-notifications-images/22-hi-priority-drawer.png)

Dado que esta es una notificación de alta prioridad, también se muestra como una notificación de aviso por encima de la pantalla del usuario actual actividad en Android 5.0:

![Notificación de aviso de ejemplo](local-notifications-images/23-heads-up-example.png)

En el ejemplo siguiente, se muestra la notificación de "Considerar del día" de prioridad baja en una notificación de nivel de batería de mayor prioridad:

![Notificación de prioridad baja de ejemplo](local-notifications-images/24-lo-priority-drawer.png)

Dado que la notificación "Pensamiento del día" es una notificación de prioridad baja, Android no mostrará, en formato de pantalla de datos.


### <a name="visibility-settings"></a>Configuración de visibilidad

A partir de Android 5.0, el *visibilidad* configuración está disponible para controlar cuánto contenido de la notificación que aparece en la pantalla de bloqueo seguro.
Xamarin.Android define las siguientes enumeraciones para establecer la visibilidad de notificación:

-   `NotificationVisibility.Public` &ndash; Todo el contenido de la notificación se muestra en la pantalla de bloqueo seguro.

-   `NotificationVisibility.Private` &ndash; Información esencial solo se muestra en la pantalla de bloqueo seguro (por ejemplo, el icono de notificación y el nombre de la aplicación que se publican), pero el resto de los detalles de la notificación están ocultos. De forma predeterminada todas las notificaciones `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; No se mostrará en la pantalla de bloqueo seguro, ni siquiera en el icono de notificación. El contenido de la notificación está disponible sólo después de que el usuario desbloquea el dispositivo.

Para establecer la visibilidad de una notificación, aplicaciones de la llamada la `SetVisibility` método de la `Notification.Builder` objeto, que se pasa en la configuración de visibilidad. Por ejemplo, esta llamada a `SetVisibility` hace que la notificación `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Cuando un `Private` notificación se ha registrado, solo el nombre y el icono de la aplicación se muestra en la pantalla de bloqueo seguro. En lugar del mensaje de notificación, el usuario ve "Desbloquear el dispositivo para ver esta notificación":

![Desbloquear el mensaje de notificación de dispositivo](local-notifications-images/25-lockscreen-private.png)

En este ejemplo, **NotificationsLab** es el nombre de la aplicación de origen. Esta versión de la notificación redactada aparece sólo si la pantalla de bloqueo es segura (es decir, se protegen mediante PIN, el patrón o contraseña) &ndash; si la pantalla de bloqueo no es segura, todo el contenido de la notificación está disponible en la pantalla de bloqueo.


### <a name="category-settings"></a>Configuración de las categorías

A partir de Android 5.0, las categorías predefinidas están disponibles para clasificar y filtrar las notificaciones. Xamarin.Android proporciona las siguientes enumeraciones para estas categorías:

-   `Notification.CategoryCall` &ndash; Llamada telefónica entrante.

-   `Notification.CategoryMessage` &ndash; Mensaje de texto entrante.

-   `Notification.CategoryAlarm` &ndash; Una alarma condición o temporizador expira.

-   `Notification.CategoryEmail` &ndash; Mensaje de correo electrónico entrante.

-   `Notification.CategoryEvent` &ndash; Un evento de calendario.

-   `Notification.CategoryPromo` &ndash; Mensaje promocional o anuncio.

-   `Notification.CategoryProgress` &ndash; El progreso de una operación en segundo plano.

-   `Notification.CategorySocial` &ndash; Actualización de redes sociales.

-   `Notification.CategoryError` &ndash; Error de una operación o la autenticación de proceso en segundo plano.

-   `Notification.CategoryTransport` &ndash; Actualización de la reproducción de medios.

-   `Notification.CategorySystem` &ndash; Reservado para uso del sistema (estado del sistema o del dispositivo).

-   `Notification.CategoryService` &ndash; Indica que se está ejecutando un servicio en segundo plano.

-   `Notification.CategoryRecommendation` &ndash; Un mensaje de recomendación relacionado con la aplicación que se está ejecutando.

-   `Notification.CategoryStatus` &ndash; Información sobre el dispositivo.

Cuando se ordenan las notificaciones, prioridad de la notificación tiene prioridad sobre el valor de la categoría. Por ejemplo, se mostrará una notificación de alta prioridad como aviso, incluso si pertenece a la `Promo` categoría. Para establecer la categoría de una notificación, se llama a la `SetCategory` método de la `Notification.Builder` objeto, pasando el valor de la categoría. Por ejemplo:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

El *no molestar* característica (nuevo en Android 5.0) filtra las notificaciones basadas en categorías. Por ejemplo, el *no molestar* pantalla **configuración** permite al usuario exentos notificaciones para llamadas telefónicas y mensajes:

![No molestar conmutadores de pantalla](local-notifications-images/26-do-not-disturb.png)

Cuando el usuario configura *no molestar* para bloquear todas las interrupciones excepto para las llamadas de teléfono (como se muestra en la captura de pantalla anterior), Android permite notificaciones con un valor de la categoría de `Notification.CategoryCall` debe presentarse mientras el dispositivo se encuentra en *no molestar* modo. Tenga en cuenta que `Notification.CategoryAlarm` notificaciones nunca se bloquean *no molestar* modo.


<a name="compatibility" />

## <a name="compatibility"></a>Compatibilidad

Si va a crear una aplicación que también se ejecutan en versiones anteriores de Android (tan pronto como el nivel de API 4), usará el [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html) clase en lugar de `Notification.Builder`. Cuando se crea notificaciones con `NotificationCompat.Builder`, Android garantiza que el contenido de notificaciones básico se muestra correctamente en los dispositivos más antiguos. Sin embargo, dado que algunas características avanzadas de notificación no están disponibles en versiones anteriores de Android, el código debe controlar explícitamente los problemas de compatibilidad para los estilos de notificación expandida, categorías y niveles de visibilidad tal como se explica a continuación.

Usar `NotificationCompat.Builder` en la aplicación, debe incluir el [biblioteca de compatibilidad de Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet en el proyecto.

El ejemplo de código siguiente muestra cómo crear una notificación básico mediante `NotificationCompat.Builder`:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();
```

Como se muestra en este ejemplo, llamadas a métodos de opciones de notificación esenciales son idénticas a las de `Notification.Builder`. Sin embargo, el código tiene que administrar los problemas de compatibilidad de forma descendente para las notificaciones más complejos (que se describe en la sección siguiente).

El [LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) ejemplo muestra cómo utilizar `NotificationCompat.Builder` para iniciar una segunda actividad desde una notificación. Este código de ejemplo se explica en la [Using Local Notifications en Xamarin.Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) tutorial.


### <a name="notification-styles"></a>Estilos de notificación

Para crear *texto grande*, *imagen*, o *Bandeja de entrada* estilo notificaciones con `NotificationCompat.Builder`, la aplicación debe utilizar las versiones de compatibilidad de estos estilos. Por ejemplo, para usar el *texto grande* estilo, crear una instancia de `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

De forma similar, puede usar su aplicación `NotificationCompat.InboxStyle` y `NotificationCompat.BigPictureStyle` para *Bandeja de entrada* y *imagen* estilos, respectivamente.


### <a name="notification-priority-and-category"></a>Categoría y la prioridad de notificación

`NotificationCompat.Builder` admite la `SetPriority` método (disponible a partir de Android 4.1). Sin embargo, el `SetCategory` método es *no* admite `NotificationCompat.Builder` porque categorías forman parte del nuevo sistema de metadatos de notificación que se introdujo en Android 5.0.

Compatibilidad con versiones anteriores de Android, dónde `SetCategory` es no disponible, el código puede comprobar el nivel de API en tiempo de ejecución a fin de llamar condicionalmente `SetCategory` cuando el nivel de API es igual o mayor que Android 5.0 (API nivel 21):

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

En del este ejemplo, la aplicación **.NET Framework de destino** está establecido en Android 5.0 y **mínimo Android versión** está establecido en **Android 4.1 (API nivel 16)**. Dado que `SetCategory` está disponible en el nivel de API 21 y versiones posterior, llamará a este código de ejemplo `SetCategory` sólo cuando está disponible &ndash; no llamará a `SetCategory` cuando el nivel de API es menor que
21.


### <a name="lockscreen-visibility"></a>Visibilidad de la pantalla de bloqueo

Dado que Android no eran compatibles con notificaciones de pantalla de bloqueo antes de Android 5.0 (API nivel 21), `NotificationCompat.Builder` no admite el `SetVisibility` método. Como se explicó anteriormente para `SetCategory`, el código puede comprobar el nivel de API en tiempo de ejecución y llame al método `SetVisiblity` solo cuando está disponible:

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>Resumen

Este artículo explica cómo crear notificaciones locales en Android. Describe la Anatomía de una notificación, explica cómo usar `Notification.Builder` para crear las notificaciones, cómo las notificaciones de estilo en iconos grandes, *texto grande*, *imagen* y *Bandeja de entrada*  formatos, cómo establecer la notificación de configuración de los metadatos como prioridad, la visibilidad y la categoría y cómo iniciar una actividad desde una notificación. En este artículo también se describe cómo funcionan estas configuraciones de notificación con el nuevo aviso, pantalla de bloqueo, y *no molestar* características introducidas en Android 5.0. Por último, aprendió cómo usar `NotificationCompat.Builder` para mantener la compatibilidad de notificación con versiones anteriores de Android.

Para obtener instrucciones acerca de las notificaciones de diseño para Android, vea [notificaciones](http://developer.android.com/preview/notifications.html).


## <a name="related-links"></a>Vínculos relacionados

- [NotificationsLab (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (ejemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notificaciones locales en el tutorial de Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notificaciones](http://developer.android.com/design/patterns/notifications.html)
- [Notificar al usuario](http://developer.android.com/training/notify-user/index.html)
- [Notification](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
