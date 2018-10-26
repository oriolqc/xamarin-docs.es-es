---
title: Notificaciones de usuario mejorada en Xamarin.iOS
description: Este artículo describe el marco de trabajo de las notificaciones de usuario introducida en iOS 10. Describe notificaciones locales, las notificaciones remotas, administración de notificación, las acciones de notificación y mucho más.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: d1b1a59b432315532844f8fca3b613ff3392a7b5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108254"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Notificaciones de usuario mejorada en Xamarin.iOS

Nuevo en iOS 10, la notificación de usuario framework permite la entrega y el tratamiento de las notificaciones locales y remotas. Con este marco de trabajo, una aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

## <a name="about-user-notifications"></a>Acerca de las notificaciones de usuario

Como se indicó anteriormente, el nuevo marco de notificación de usuario permite la entrega y el tratamiento de las notificaciones locales y remotas. Con este marco de trabajo, una aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o extensión puede recibir (y posiblemente modificar) las notificaciones locales y remotas que se entregan a dispositivos iOS del usuario.

El nuevo marco de interfaz de usuario de notificación de usuario permite que una aplicación o extensión de la aplicación para personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Este marco de trabajo proporciona los siguientes métodos que una aplicación puede entregar notificaciones a un usuario:

- **Alertas visuales** : donde la notificación resume hacia abajo de la parte superior de la pantalla como un banner.
- **Sonido y vibraciones** -puede asociarse con una notificación.
- **Uso de distintivos de icono de aplicación** : icono de aplicación donde muestra un distintivo que muestra que el nuevo contenido está disponible, como el número de mensajes de correo electrónico no leídos.

Además, según el contexto del usuario actual, hay distintas maneras en que se mostrará una notificación:

- Si el dispositivo está desbloqueado, la notificación se desplegarán en la parte superior de la pantalla como un banner.
- Si el dispositivo está bloqueado, se mostrará la notificación en pantalla de bloqueo del usuario.
- Si el usuario ha perdido una notificación, puede abrir el centro de notificaciones y ver las notificaciones de espera está disponible, no existe.

Una aplicación Xamarin.iOS tiene dos tipos de notificaciones de usuario que es capaz de enviar:

- **Notificaciones locales** -estos se envían por las aplicaciones instaladas localmente en el dispositivo del usuario.
- **Notificaciones remotas** -se envían desde un equipo remoto server y, o bien presenta al usuario o desencadenará una actualización de fondo del contenido de la aplicación.

### <a name="about-local-notifications"></a>Acerca de las notificaciones locales

Las notificaciones locales que puede enviar una aplicación de iOS tienen las características y los atributos siguientes:

- Se envían por las aplicaciones que son locales en el dispositivo del usuario. 
- Son puede configurarse usar la hora o ubicación basada en desencadenadores. 
- La aplicación programa la notificación con el dispositivo del usuario y se muestra cuando se cumpla la condición del desencadenador.
- Cuando el usuario interactúa con una notificación, la aplicación recibirá una devolución de llamada.

Algunos ejemplos de notificaciones locales incluyen:

- Alertas de calendario
- Avisos
- Ubicación compatible con desencadenadores

Para obtener más información, consulte Apple [Local y remoto Notification Programming Guide](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentación.

### <a name="about-remote-notifications"></a>Acerca de las notificaciones remotas

Las notificaciones remotas que puede enviar una aplicación de iOS tienen las características y los atributos siguientes:

- La aplicación tiene un componente de servidor que se comunica.
- Apple Push Notification Service (APNs) se usa para transmitir una entrega de mejor esfuerzo de las notificaciones remotas en el dispositivo del usuario desde los servidores basados en la nube del desarrollador.
- Cuando la aplicación recibe la notificación remota se mostrará al usuario.
- Cuando el usuario interactúa con la notificación, la aplicación recibirá una devolución de llamada.

Algunos ejemplos de las notificaciones remotas:

- Alertas de noticias
- Actualizaciones de deportes
- Mensajes de mensajería instantáneos

Hay dos tipos de notificaciones remotas disponibles para una aplicación de iOS:

- **Cara al usuario** -se muestran al usuario en el dispositivo.
- **Las actualizaciones silenciosas** -proporcionan un mecanismo para actualizar el contenido de una aplicación de iOS en segundo plano. Cuando se recibe una actualización en modo silencioso, la aplicación puede llegar a la extracción de los servidores de quitar el contenido más reciente.

Para obtener más información, consulte Apple [Local y remoto Notification Programming Guide](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentación.

### <a name="about-the-existing-notifications-api"></a>Acerca de las API de notificaciones existentes

Antes de iOS 10, usaría una aplicación iOS `UIApplication` para registrar una notificación con el sistema y programar cómo debe activarse dicha notificación (ya sea por hora o ubicación).

Hay varias problema que un desarrollador que puede surgir al trabajar con la API de notificación existente:

- Se han producido diferentes devoluciones de llamada necesarias para Local o remota de las notificaciones que podría provocar la duplicación de código.
- La aplicación tenía un control limitado de la notificación después de había se ha programado con el sistema.
- Se han producido los distintos niveles de compatibilidad en todas las plataformas existentes de Apple.

### <a name="about-the-new-user-notification-framework"></a>Sobre el nuevo marco de notificación de usuario

Con iOS 10, Apple ha introducido el nuevo marco de notificación de usuario, que reemplaza la existente `UIApplication` método se ha indicado anteriormente.

El marco de trabajo de notificación de usuario ofrece lo siguiente:

- Una API familiar que incluye paridad de características con los métodos anteriores, lo que facilita al código del puerto desde el marco de trabajo existente.
- Incluye un conjunto expandido de las opciones de contenido que permite a las notificaciones más completas para enviarse al usuario.
- Tanto locales como notificaciones remotas pueden controlarse mediante el mismo código y las devoluciones de llamada.
- Simplifica el proceso de controlar las devoluciones de llamada que se envían a una aplicación cuando el usuario interactúa con una notificación.
- Administración mejorada de notificaciones pendientes y entregadas, incluida la capacidad para quitar o notificaciones de actualización.
- Agrega la capacidad de hacer la presentación de la aplicación de las notificaciones.
- Agrega la capacidad de programar y controlar las notificaciones desde dentro de las extensiones de aplicación.
- Agrega el nuevo punto de extensión para las notificaciones mismas. 

El nuevo marco de notificación de usuario proporciona una notificación unificada API entre las varias de las plataformas que Apple admite incluidos: 

- **iOS** -compatibilidad para administrar y programar las notificaciones completa.
- **tvOS** -agrega la capacidad de distintivo de iconos de aplicación para las notificaciones locales y remotos.
- **watchOS** : agrega la capacidad de reenviar las notificaciones desde el dispositivo del usuario iOS emparejados a su Apple Watch y permite a las aplicaciones de watch para realizar notificaciones locales directamente en el mismo watch.

Para obtener más información, consulte Apple [referencia de marco de UserNotifications](https://developer.apple.com/reference/usernotifications) y [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) documentación.

## <a name="preparing-for-notification-delivery"></a>Preparación para la entrega de notificaciones

Antes de un iOS app puede enviar notificaciones al usuario que la aplicación debe registrarse con el sistema y, dado que una notificación es una interrupción para el usuario, una aplicación debe solicitar explícitamente el permiso antes de enviarlos.

Hay tres niveles distintos de las solicitudes de notificación que el usuario puede aprobar para una aplicación:

- Banner de muestra.
- Alertas de sonido.
- Uso de distintivos el icono de la aplicación.

Además, estos niveles de aprobación deben solicitar y establecer para las notificaciones locales y remotas.

Se debe solicitar permiso de notificación en cuanto se inicia la aplicación agregando el código siguiente a la `FinishedLaunching` método de la `AppDelegate` y establecer el tipo de notificación deseada (`UNAuthorizationOptions`):

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

Además, un usuario puede cambiar siempre los privilegios de notificación para una aplicación en cualquier momento mediante el **configuración** aplicación del dispositivo. La aplicación debe comprobar para los privilegios del usuario solicitado notificación antes de presentar una notificación con el código siguiente:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configurar el entorno de notificaciones remotas

Nuevo en iOS 10, el desarrollador debe informar al sistema operativo qué notificaciones Push de entorno se ejecutan en desarrollo o producción. Error al proporcionar esta información puede dar lugar a la aplicación se rechaza cuando envía a la aplicación Store iTune con una notificación similar al siguiente:

> Derechos de notificación de inserción que falta - la aplicación incluyen una API para el servicio de notificaciones Push de Apple, pero la `aps-environment` derecho no está presente en la firma de la aplicación.

Para proporcionar los derechos necesarios, realice lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en el `Entitlements.plist` de archivos en el **panel de solución** para abrirlo y editarlo.
2. Cambie a la **origen** vista: 

    [![](enhanced-user-notifications-images/setup01.png "La vista del origen")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Haga clic en el **+** para agregar una nueva clave.
4. Escriba `aps-environment` para el **propiedad**, deje el **tipo** como `String` y escriba `development` o `production` para el **valor**: 

    [![](enhanced-user-notifications-images/setup02.png "La propiedad de entorno de aps")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Guarde los cambios en el archivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en el `Entitlements.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Haga clic en el **+** para agregar una nueva clave.
4. Escriba `aps-environment` para el **propiedad**, deje el **tipo** como `String` y escriba `development` o `production` para el **valor**: 

    [![](enhanced-user-notifications-images/setup02w.png "La propiedad de entorno de aps")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Guarde los cambios en el archivo.

-----

### <a name="registering-for-remote-notifications"></a>Registrar las notificaciones remotas

Si la aplicación se pueden enviar y recibir notificaciones remotas, todavía será necesario hacer _registro Token_ con los existentes `UIApplication` API. Este registro requiere que el dispositivo para tener un acceso de conexión de red activa APNs, lo que generará el token necesario que se enviará a la aplicación. La aplicación debe reenviar, a continuación, este token para la aplicación de lado servidor del desarrollador para registrar las notificaciones remotas:

[![](enhanced-user-notifications-images/token01.png "Introducción al registro de token")](enhanced-user-notifications-images/token01.png#lightbox)

Utilice el siguiente código para inicializar el registro necesaria:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

El token que se envía a la aplicación del lado de servidor del desarrollador deberá incluirse como parte de la carga de notificación que get enviados desde el servidor para APNs al enviar una notificación remota:

[![](enhanced-user-notifications-images/token02.png "El token incluido como parte de la carga de notificación")](enhanced-user-notifications-images/token02.png#lightbox)

El token actúa como la clave que une a la notificación y la aplicación se usa para abrir o responder a la notificación.

Para obtener más información, consulte Apple [Local y remoto Notification Programming Guide](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentación.

## <a name="notification-delivery"></a>Entrega de notificaciones

Con la aplicación totalmente registrada y solicitan los permisos necesarios y concedido por el usuario, la aplicación ya está lista para enviar y recibir notificaciones. 

### <a name="providing-notification-content"></a>Proporcionar el contenido de la notificación

Nuevo en iOS 10, todas las notificaciones contienen un **título** y **subtítulo** que siempre se mostrará con el **cuerpo** del contenido de notificación. También es nueva, la capacidad de agregar **archivos adjuntos multimedia** para el contenido de la notificación.

Para crear el contenido de una notificación Local, use el código siguiente:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Las notificaciones remotas, el proceso es similar:

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>Se envía la notificación unas cuando la programación

Con el contenido de la notificación creado, la aplicación necesita programar cuándo se le presentará la notificación al usuario estableciendo un *desencadenador*. iOS 10 proporciona cuatro tipos diferentes de desencadenadores:

- **Notificación de inserción** : se usa exclusivamente con las notificaciones remotas y se desencadena cuando el paquete de Apple Push Notification Service envía una notificación a la aplicación que se ejecuta en el dispositivo.
- **Intervalo de tiempo** -permite que una notificación Local deben programarse de un tiempo de intervalo se inicia con ahora y el final de una en algún momento futuro. Por ejemplo, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`.
- **Fecha del calendario** -permite notificaciones locales para programar una fecha y hora específicas.
- **Basado en ubicación** -notificaciones locales permite programar cuando el dispositivo iOS entra o salir de una ubicación geográfica específica o está en una determinada proximidad a cualquier balizas Bluetooth.

Cuando una notificación Local esté lista, la aplicación necesita llamar a la `Add` método de la `UNUserNotificationCenter` objeto para programar su presentación al usuario. Las notificaciones remotas, la aplicación de servidor envía una notificación de carga a los APNs, que, a continuación, envía el paquete de la sesión en el dispositivo del usuario.

Reúne todas las piezas, un notificación Local de ejemplo podría ser similar:

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>Controlar las notificaciones de la aplicación de primer plano

Nuevo en iOS 10, una aplicación puede controlar las notificaciones de forma distinta cuando está en primer plano y se desencadena una notificación. Proporcionando un `UNUserNotificationCenterDelegate` e implementar el `UserNotificationCenter` método, la aplicación puede asumir la responsabilidad para mostrar la notificación. Por ejemplo:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

Este código simplemente escribe el contenido de la `UNNotification` a la salida de la aplicación y pide al sistema para mostrar la alerta estándar para la notificación. 

Si la aplicación desea mostrar la notificación cuando estaba en primer plano y no usa los valores predeterminados del sistema, pase `None` al controlador de finalización. Ejemplo:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Con este código en su lugar, abra el `AppDelegate.cs` para la edición de archivos y cambiar el `FinishedLaunching` método Buscar similar al siguiente:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

Este código está asociando personalizado `UNUserNotificationCenterDelegate` anterior al actual `UNUserNotificationCenter` por lo que la aplicación puede controlar la notificación cuando está activo y en primer plano.

## <a name="notification-management"></a>Administración de notificación

Nuevo IOS 10, notificación de administración proporciona acceso a las notificaciones pendientes y entregadas y agrega la capacidad de quitar, actualizar o promocionar estas notificaciones.

Es una parte importante de la administración de la notificación de la _identificador de la solicitud_ que se asignó a la notificación al que se creó o programado con el sistema. Las notificaciones remotas, se asigna a través del nuevo `apps-collapse-id` campo en el encabezado de solicitud HTTP.

El identificador de la solicitud se utiliza para seleccionar la notificación de que desea realizar la administración de notificación en la aplicación.

### <a name="removing-notifications"></a>Quitar notificaciones

Para quitar una notificación pendiente desde el sistema, use el siguiente código:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Para quitar una notificación ya entregada, utilice el siguiente código:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>Actualización de una notificación existente

Para actualizar una notificación existente, crear una nueva notificación con los parámetros deseados modificados (por ejemplo, una nueva hora de desencadenador) y agréguelo al sistema con el mismo identificador de solicitud que la notificación de que se debe modificar. Ejemplo:


```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

Para obtener notificaciones ya entregadas, obtendrá actualizada y promueve a la parte superior de la lista de las pantallas principal y de bloqueo y en el centro de notificaciones si ya se ha leído por el usuario la notificación existente.

## <a name="working-with-notification-actions"></a>Trabajar con las acciones de notificación

En iOS 10, las notificaciones que se entregan al usuario no son estáticas y ofrecen varias formas de que el usuario puede interactuar con ellos (desde integrado para acciones personalizadas).

Hay tres tipos de acciones que puede responder una aplicación de iOS:

- **Acción predeterminada** -esto es cuando el usuario pulsa una notificación para abrir la aplicación y mostrar los detalles de la notificación especificada.
- **Acciones personalizadas** -estos se agregaron en iOS 8 y proporciona una manera rápida para el usuario realizar una tarea personalizada directamente desde la notificación sin necesidad de iniciar la aplicación. Se puede presentar como una lista de botones con títulos personalizables o un entrada campo de texto que se puede ejecutar en primer plano o en segundo plano (donde la aplicación tiene una pequeña cantidad de tiempo para satisfacer la solicitud) (donde se inicia la aplicación en primer plano fu lfill la solicitud). Acciones personalizadas están disponibles en iOS y watchOS.
- **Acción de descartar** : esta acción se envía a la aplicación cuando el usuario cierra una notificación determinada.

### <a name="creating-custom-actions"></a>Creación de acciones personalizadas

Para crear y registrar una acción personalizada con el sistema, use el código siguiente:

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

Al crear un nuevo `UNNotificationAction`, se le asigna un identificador único y el título que aparecerá en el botón. De forma predeterminada, la acción se creará como una acción en segundo plano, sin embargo, se pueden proporcionar opciones para ajustar el comportamiento de la acción (por ejemplo si se establece en una acción de primer plano).

Cada una de las acciones que se crea debe estar asociado a una categoría. Al crear un nuevo `UNNotificationCategory`, se asigna un identificador único, una lista de acciones que puede realizar una lista de identificadores de intención de proporcionar más información sobre la intención de las acciones en la categoría y algunas opciones para controlar el comportamiento de la categoría.

Por último, todas las categorías registradas con el sistema mediante la `SetNotificationCategories` método.

### <a name="presenting-custom-actions"></a>Presentación de las acciones personalizadas

Una vez que se han creado y registrado con el sistema de un conjunto de acciones personalizadas y categorías, se puede presentar desde Local o remota de las notificaciones.

Para recibir notificaciones remotas, establezca un `category` en la carga remota de notificación que coincide con una de las categorías que ha creado anteriormente. Por ejemplo:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Para las notificaciones locales, establezca el `CategoryIdentifier` propiedad de la `UNMutableNotificationContent` objeto. Por ejemplo:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

De nuevo, este identificador debe coincidir con una de las categorías que se creó anteriormente.

### <a name="handling-dismiss-actions"></a>Control de descartar las acciones

Como se indicó anteriormente, una acción de descartar pueden enviarse a la aplicación cuando el usuario cierra una notificación. Puesto que esto no es una acción estándar, una opción debe establecerse cuando se crea la categoría. Por ejemplo:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Controlar las respuestas de acción

Cuando el usuario interactúa con las acciones personalizadas y categorías que se crearon anteriormente, la aplicación necesita para completar la tarea solicitada. Esto se hace proporcionando un `UNUserNotificationCenterDelegate` e implementar el `UserNotificationCenter` método. Por ejemplo:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

Ha pasado en `UNNotificationResponse` clase tiene un `ActionIdentifier` propiedad que puede ser la acción predeterminada o la acción de descartar. Use `response.Notification.Request.Identifier` para probar todas las acciones personalizadas.

El `UserText` propiedad contiene el valor de cualquier entrada de texto del usuario. El `Notification` propiedad contiene la notificación de origen que incluye la solicitud con el desencadenador y notificación de contenido. La aplicación puede decidir si era una variable Local o remoto notificación según el tipo de desencadenador.

> [!NOTE]
> iOS 12 hace posible para una notificación personalizada de la interfaz de usuario modificar sus botones de acción en tiempo de ejecución. Para obtener más información, eche un vistazo a la [botones de acción de notificación dinámico](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) documentación.

## <a name="working-with-service-extensions"></a>Trabajar con las extensiones de servicio

Cuando se trabaja con notificaciones remotas, _las extensiones de servicio_ proporcionan una manera de habilitar el cifrado de extremo a otro dentro de la carga de notificación. Las extensiones de servicio son una extensión no sea de interfaz de usuario (disponible en iOS 10) que se ejecutan en segundo plano con el propósito principal de aumento o reemplazar el contenido visible de una notificación antes de presentarse al usuario. 

[![](enhanced-user-notifications-images/extension01.png "Introducción a la extensión de servicio")](enhanced-user-notifications-images/extension01.png#lightbox)

Las extensiones de servicio están diseñadas para ejecutarse rápidamente y sólo se le concede un breve período de tiempo de ejecución por el sistema. En caso de que la extensión de servicio no se puede completar su tarea en el intervalo de tiempo asignado, se llamará a un método de reserva. Si se produce un error en la reserva, el contenido de notificación original se mostrará al usuario.

Algunos usos posibles de las extensiones de servicio incluyen:

- Proporciona cifrado de extremo a otro del contenido de notificación remota.
- Agregar datos adjuntos a las notificaciones remotas para enriquecer ellos.

### <a name="implementing-a-service-extension"></a>Implementar una extensión de servicio

Para implementar una extensión de servicio en una aplicación de Xamarin.iOS, realice lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga doble clic en el nombre de la solución en el **panel de solución** y seleccione **agregar** > **Agregar nuevo proyecto**.
3. Seleccione **iOS** > **extensiones** > **las extensiones de servicio de notificación** y haga clic en el **siguiente** botón: 

    [![](enhanced-user-notifications-images/extension02.png "Seleccione las extensiones de servicio de notificación")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el **siguiente** botón: 

    [![](enhanced-user-notifications-images/extension03.png "Escriba un nombre para la extensión")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Ajustar el **nombre del proyecto** o **nombre de la solución** si es necesario y haga clic en el **crear** botón: 

    [![](enhanced-user-notifications-images/extension04.png "Ajuste el nombre del proyecto o el nombre de la solución")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra la solución de la aplicación en Visual Studio.
2. Haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y seleccione **Agregar > Nuevo proyecto...** .
3. Seleccione **Visual C# > extensiones de iOS > extensión de servicio de notificación**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "Seleccione las extensiones de servicio de notificación")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el **Aceptar** botón.

-----

> [!IMPORTANT]
> El identificador de paquete para la extensión de servicio debe coincidir con el identificador de paquete de la aplicación principal con `.appnameserviceextension` anexado al final. Por ejemplo, si la aplicación principal tenía un identificador de lote de `com.xamarin.monkeynotify`, la extensión de servicio debe tener un identificador de lote de `com.xamarin.monkeynotify.monkeynotifyserviceextension`. Esto se debe establecer automáticamente cuando la extensión se agrega a la solución. 

Hay una clase principal de la extensión de servicio de notificación que deberán modificarse para proporcionar la funcionalidad necesaria. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

El primer método, `DidReceiveNotificationRequest`, se pasará el identificador de notificación, así como el contenido de notificación a través de la `request` objeto. Ha pasado en `contentHandler` debe llamarse para presentar la notificación al usuario.

El segundo método, `TimeWillExpire`, se llamará antes de tiempo está a punto de agotarse el tiempo de espera para que la extensión de servicio procesar la solicitud. Si se produce un error en la extensión de servicio llamar a la `contentHandler` en el intervalo de tiempo asignado, se mostrará el contenido original al usuario.

### <a name="triggering-a-service-extension"></a>Desencadenar una extensión de servicio

Con una extensión de servicio crea y entrega con la aplicación, puede activarse mediante la modificación de la carga remota de notificación enviado al dispositivo. Por ejemplo:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

El nuevo `mutable-content` clave especifica que la extensión de servicio debe iniciarse para actualizar el contenido de notificación remota. El `encrypted-content` clave contiene los datos cifrados que se puede descifrar la extensión de servicio antes de presentar al usuario.

Eche un vistazo en el siguiente ejemplo de extensión de servicio:

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

Este código descifra el contenido cifrado de la `encrypted-content` clave, se crea un nuevo `UNMutableNotificationContent`, Establece la `Body` propiedad para el descifrado contenido y utiliza el `contentHandler` para presentar la notificación al usuario.

## <a name="summary"></a>Resumen

En este artículo ha cubierto todas las formas que se han mejorado la notificación a los usuarios mediante iOS 10. Se han mostrado el nuevo marco de notificación de usuario y cómo usarlo en una aplicación de Xamarin.iOS o extensión de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Referencia de marco de Usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guía de programación de notificaciones locales y remotos](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
