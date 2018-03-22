---
title: Notificaciones de usuario mejorada
description: "Este artículo explican todas las formas que se han mejorado la notificación a los usuarios mediante iOS 10 y cómo utilizarlos en una aplicación de Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 50553cb1dc5f7ea782c0f13e32f60d7b6ce3e181
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="enhanced-user-notifications"></a>Notificaciones de usuario mejorada

_Este artículo explican todas las formas que se han mejorado la notificación a los usuarios mediante iOS 10 y cómo utilizarlos en una aplicación de Xamarin.iOS._

Nuevo en 10, la notificación al usuario framework permite la entrega y el manejo de notificaciones locales y remotas de iOS. Con este marco de trabajo, una aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

## <a name="about-user-notifications"></a>Acerca de las notificaciones de usuario

Como se mencionó anteriormente, el nuevo marco de notificación de usuario permite la entrega y el manejo de notificaciones locales y remotas. Con este marco de trabajo, una aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o la extensión puede recibir (y potencialmente modificar) las notificaciones locales y remotas que se entregan al dispositivo de iOS del usuario.

El nuevo marco de interfaz de usuario de notificación de usuario permite que una aplicación o extensión de la aplicación para personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Este marco de trabajo proporciona las siguientes formas que una aplicación puede entregar notificaciones a un usuario:

- **Alertas visuales** : donde la notificación resume hacia abajo de la parte superior de la pantalla como una pancarta.
- **Sonido y vibración** -puede asociarse con una notificación.
- **Icono de aplicación distintivos** - donde el icono de la aplicación muestra un distintivo que muestra que el nuevo contenido está disponible, como el número de mensajes de correo electrónico no leído.

Además, según el contexto del usuario actual, hay distintas maneras en que se mostrará una notificación:

- Si el dispositivo se desbloquea, la notificación deberá escribirse hacia abajo desde la parte superior de la pantalla como una pancarta.
- Si el dispositivo está bloqueado, la notificación se mostrará en la pantalla de bloqueo del usuario.
- Si el usuario ha perdido una notificación, puede abrir el centro de notificaciones y ver las notificaciones de espera está disponible, no existe.

Una aplicación de Xamarin.iOS tiene dos tipos de notificaciones de usuario que es capaz de enviar:

- **Notificaciones locales** -estos se envían por aplicaciones instaladas localmente en el dispositivo.
- **Notificaciones remotas** -se envían desde un equipo remoto server y, o bien presenta al usuario o activan una actualización en segundo plano de contenido de la aplicación.

### <a name="about-local-notifications"></a>Acerca de las notificaciones Local

Las notificaciones Local que puede enviar una aplicación de iOS tienen las siguientes características y atributos:

- Se envían por aplicaciones que son locales en el dispositivo del usuario. 
- Únicamente son puede configurarse usar la hora o la ubicación en función de desencadenadores. 
- La aplicación programa la notificación con el dispositivo del usuario y se muestra cuando se cumpla la condición desencadenadora.
- Cuando el usuario interactúa con una notificación, la aplicación recibirá una devolución de llamada.

Algunos ejemplos de notificaciones Local:

- Alertas del calendario
- Avisos
- Ubicación compatible con desencadenadores

Para obtener más información, vea de Apple [Local y remoto Guía de programación de notificación](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentación.

### <a name="about-remote-notifications"></a>Acerca de las notificaciones remotas

Las notificaciones remoto que puede enviar una aplicación de iOS tienen las siguientes características y atributos:

- La aplicación tiene un componente de servidor que se comunica.
- El servicio de notificaciones Push de Apple (APN) se utiliza para transmitir una entrega de mejor esfuerzo de notificaciones remoto para el dispositivo del usuario de los servidores basados en la nube del desarrollador.
- Cuando la aplicación recibe la notificación remoto se mostrará al usuario.
- Cuando el usuario interactúa con la notificación, la aplicación recibirá una devolución de llamada.

Algunos ejemplos de notificaciones remoto son:

- Alertas de noticias
- Actualizaciones de deportes
- Mensajes de mensajería instantáneos

Hay dos tipos de notificaciones remoto disponibles para una aplicación de iOS:

- **A nivel de usuario** -se muestran al usuario en el dispositivo.
- **Las actualizaciones silenciosas** -proporcionan un mecanismo para actualizar el contenido de una aplicación de iOS en segundo plano. Cuando se recibe una actualización en modo silencioso, la aplicación puede llegar a la extracción de servidores de quitar el contenido más reciente.

Para obtener más información, vea de Apple [Local y remoto Guía de programación de notificación](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentación.

### <a name="about-the-existing-notifications-api"></a>Acerca de la API de notificaciones existente

Antes de iOS 10, podría usar una aplicación de iOS `UIApplication` para registrar una notificación con el sistema y para programar la notificación debe desencadenarse (ya sea por hora o ubicación).

Hay varias problema que un desarrollador puede encontrar al trabajar con la API de notificación existente:

- Hay diferentes devoluciones de llamada necesarias para Local o remota de las notificaciones que podría provocar la duplicación del código.
- La aplicación tenía un control limitado de la notificación después de se había programado con el sistema.
- Hay distintos niveles de compatibilidad en todas las plataformas existentes de Apple.

### <a name="about-the-new-user-notification-framework"></a>Sobre el nuevo marco de notificación de usuario

Con iOS 10, Apple incorporó el nuevo marco de notificación de usuario, que reemplaza las existentes `UIApplication` método indicados anteriormente.

El marco de trabajo de notificación de usuario ofrece lo siguiente:

- Una API familiar que incluye una paridad de características con los métodos anteriores, lo que facilita al código de puerto desde el marco de trabajo existente.
- Incluye un conjunto expandido de opciones de contenido que permite a las notificaciones más enriquecidas para enviarse al usuario.
- Local y remoto notificaciones pueden ser controladas por el mismo código y las devoluciones de llamada.
- Simplifica el proceso de controlar las devoluciones de llamada que se envían a una aplicación cuando el usuario interactúa con una notificación.
- Mejora de la administración de notificaciones pendientes y entregadas incluye la posibilidad de quitar o notificaciones de actualización.
- Agrega la capacidad de hacer en la aplicación de presentación de las notificaciones.
- Agrega la capacidad de programar y administrar las notificaciones desde dentro de las extensiones de aplicación.
- Agrega el nuevo punto de extensión para las notificaciones por sí mismos. 

El nuevo marco de notificación de usuario proporciona una notificación unificada API entre el múltiplo de las plataformas que Apple admite incluidos: 

- **iOS** -compatibilidad para administrar y programar las notificaciones completa.
- **tvOS** -agrega la capacidad de distintivo iconos de aplicación para notificaciones locales y remotas.
- **watchOS** : agrega la capacidad para enviar notificaciones desde el dispositivo del usuario iOS emparejados a sus Apple Watch y permite a aplicaciones de inspección para realizar las notificaciones locales directamente en el reloj propiamente dicho.

Para obtener más información, vea de Apple [UserNotifications Framework referencia](https://developer.apple.com/reference/usernotifications) y [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) documentación.

## <a name="preparing-for-notification-delivery"></a>Preparación para la entrega de notificación

Antes de un iOS aplicación puede enviar notificaciones al usuario que la aplicación debe registrarse con el sistema y, dado que una notificación es una interrupción para el usuario, una aplicación debe solicitar explícitamente permiso antes de enviarlos.

Hay tres niveles diferentes de solicitudes de notificación que el usuario puede aprobar para una aplicación:

- Muestra la pancarta.
- Alertas de sonido.
- Distintivos el icono de la aplicación.

Además, estos niveles de aprobación deben ser solicitados y establecer notificaciones locales y remotos.

Se debe solicitar permiso de notificación en cuanto se inicia la aplicación agregando el código siguiente a la `FinishedLaunching` método de la `AppDelegate` y estableciendo el tipo de notificación deseado (`UNAuthorizationOptions`):

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

Además, un usuario puede cambiar siempre los privilegios de notificación para una aplicación en cualquier momento mediante el **configuración** aplicación en el dispositivo. La aplicación debe comprobar los privilegios del usuario solicitada notificación antes de presentar una notificación con el código siguiente:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configurar el entorno remoto de notificaciones

Nuevo en iOS 10, el desarrollador debe informar al sistema operativo qué entorno notificaciones de inserción se ejecutan en como desarrollo o de producción. Error al proporcionar esta información puede dar lugar a la aplicación que se rechazan cuando se envían a la tienda de aplicaciones iTune con una notificación similar al siguiente:

> Derechos de notificaciones de inserción que falta - la aplicación incluyen una API para el servicio de notificaciones Push de Apple, pero la `aps-environment` derechos no está presente en la firma de la aplicación.

Para proporcionar los derechos necesarios, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en el `Entitlements.plist` un archivo en el **solución Pad** para abrirlo y editarlo.
2. Cambie a la **origen** vista: 

    [![](enhanced-user-notifications-images/setup01.png "La vista del origen")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Haga clic en el  **+**  botón para agregar una nueva clave.
4. Escriba `aps-environment` para el **propiedad**, deje el **tipo** como `String` y escribir cualquiera `development` o `production` para el **valor**: 

    [![](enhanced-user-notifications-images/setup02.png "La propiedad de entorno de aps")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Guarde los cambios en el archivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en el `Entitlements.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Haga clic en el  **+**  botón para agregar una nueva clave.
4. Escriba `aps-environment` para el **propiedad**, deje el **tipo** como `String` y escribir cualquiera `development` o `production` para el **valor**: 

    [![](enhanced-user-notifications-images/setup02w.png "La propiedad de entorno de aps")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Guarde los cambios en el archivo.

-----

### <a name="registering-for-remote-notifications"></a>Registrarse para recibir notificaciones remotas

Si la aplicación vaya a enviar y recibir notificaciones remoto, seguirá siendo necesario hacer _registro Token_ con los existentes `UIApplication` API. Este registro requiere el dispositivo para tener un acceso de conexión de red activa APN, lo que generará el token necesario que se enviará a la aplicación. La aplicación debe reenviar, a continuación, este token para la aplicación del lado de servidor del desarrollador registrarse para recibir notificaciones remotas:

[![](enhanced-user-notifications-images/token01.png "Información general de registro del token")](enhanced-user-notifications-images/token01.png#lightbox)

Utilice el siguiente código para inicializar el registro requiere:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

El símbolo (token) que se envía a la aplicación del lado servidor del desarrollador deberá incluirse como parte de la carga de notificación que get enviados desde el servidor a APN al enviar una notificación remoto:

[![](enhanced-user-notifications-images/token02.png "El token incluido como parte de la carga de notificación")](enhanced-user-notifications-images/token02.png#lightbox)

El token actúa como la clave que une la notificación y la aplicación se usa para abrir o responder a la notificación.

Para obtener más información, vea de Apple [Local y remoto Guía de programación de notificación](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentación.

## <a name="notification-delivery"></a>Entrega de notificaciones

Con la aplicación registrada totalmente y solicitan los permisos necesarios y concedido por el usuario, la aplicación ya está lista para enviar y recibir notificaciones. 

### <a name="providing-notification-content"></a>Proporciona contenido de la notificación

Nuevo en iOS 10, todas las notificaciones contienen un **título** y **subtítulo** siempre que se muestre con la **cuerpo** del contenido de notificación. También es nueva, la capacidad de agregar **datos adjuntos de multimedia** para el contenido de la notificación.

Para crear el contenido de una notificación Local, utilice el código siguiente:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Para las notificaciones remoto, el proceso es similar:

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

### <a name="scheduling-when-a-notification-is-sent"></a>Se envía a Notification cuando la programación

Con el contenido de la notificación que se creó, la aplicación necesita para programar cuándo la notificación se presentará al usuario estableciendo un *desencadenador*. iOS 10 proporciona cuatro tipos diferentes de desencadenador:

- **Las notificaciones de inserción** : se usa exclusivamente con notificaciones remoto y se desencadena cuando el paquete de APN envía una notificación a la aplicación que se ejecuta en el dispositivo.
- **Intervalo de tiempo** -permite una notificación Local programarse de un tiempo de intervalo comienzan por ahora y finalizar un algún momento futuro. Por ejemplo, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`.
- **Fecha del calendario** -permite notificaciones Local puede programarse para una fecha y hora específicas.
- **Basado en la ubicación** -permite Local las notificaciones pueden programarse cuando el dispositivo iOS entra o salir de una ubicación geográfica o está en una determinada proximidad con cualquier balizas de Bluetooth.

Cuando una notificación Local esté lista, la aplicación necesita llamar a la `Add` método de la `UNUserNotificationCenter` objeto que se va a programar su presentación al usuario. Para las notificaciones remoto, la aplicación de servidor envía una carga de notificación para el APNs, que, a continuación, envía el paquete en el dispositivo del usuario.

Reunir todas las partes, un ejemplo de notificación Local podría ser similar:

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

Nuevo en iOS 10, una aplicación puede controlar las notificaciones de forma diferente cuando se encuentra en primer plano y se desencadena una notificación. Proporcionando un `UNUserNotificationCenterDelegate` e implementar el `UserNotificationCenter` método, la aplicación puede asumir la responsabilidad para mostrar la notificación. Por ejemplo:

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

Este código simplemente está escribiendo el contenido de la `UNNotification` a la salida de la aplicación y pedir el sistema para mostrar la alerta estándar para la notificación. 

Si la aplicación desea mostrar la notificación de sí mismo cuando estaba en primer plano y no usa los valores predeterminados del sistema, pasar `None` al controlador de finalización. Ejemplo:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Con este código en su lugar, abra el `AppDelegate.cs` para su edición y cambie el `FinishedLaunching` método para buscar, como lo siguiente:

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

Este código está asociando personalizado `UNUserNotificationCenterDelegate` anterior al actual `UNUserNotificationCenter` por lo que la aplicación puede controlar la notificación mientras está activa y en primer plano.

## <a name="notification-management"></a>Administración de notificación

Nuevo en 10 de iOS, administración de notificación proporciona acceso a las notificaciones pendientes y entregadas y agrega la posibilidad de quitar, actualizar o promocionar estas notificaciones.

Es una parte importante de la administración de notificación de la _identificador de la solicitud_ que se asignó a la notificación cuando se creó y se programan con el sistema. Para las notificaciones remoto, se asigna a través del nuevo `apps-collapse-id` campo en el encabezado de solicitud HTTP.

El identificador de la solicitud se utiliza para seleccionar la notificación de que desea realizar la administración de notificación en la aplicación.

### <a name="removing-notifications"></a>Quitar las notificaciones

Para quitar una notificación pendiente desde el sistema, utilice el código siguiente:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Para quitar una notificación ya entregada, utilice el código siguiente:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>Actualizar una notificación existente

Para actualizar una notificación existente, basta con crear una nueva notificación con los parámetros que desee modificados (por ejemplo, una nueva hora de desencadenador) y agregarlo al sistema con el mismo identificador de solicitud que la notificación de que es necesario modificar. Ejemplo:


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

Para las notificaciones ya entregadas, obtendrá actualizada y promueve a la parte superior de la lista en las pantallas de inicio y de bloqueo y en el centro de notificaciones si ya se ha leído por el usuario la notificación existente.

## <a name="working-with-notification-actions"></a>Trabajar con acciones de notificación

En iOS 10, las notificaciones que se entregan al usuario que no son estáticas y proporcionan numerosos mecanismos que el usuario puede interactuar con ellos (de integrado para acciones personalizadas).

Hay tres tipos de acciones que puede responder una aplicación de iOS:

- **Acción predeterminada** -esto es cuando el usuario puntea una notificación para abrir la aplicación y mostrar los detalles de la notificación determinada.
- **Acciones personalizadas** -estos se agregaron en iOS 8 y proporciona una manera rápida para el usuario realizar una tarea personalizada directamente desde la notificación sin necesidad de iniciar la aplicación. Se puede mostrar como una lista de botones con títulos personalizables o un texto campo de entrada que se puede ejecutar en segundo plano (donde la aplicación tiene una pequeña cantidad de tiempo para satisfacer la solicitud) o el primer plano (donde se inicia la aplicación en primer plano fu lfill la solicitud). Acciones personalizadas están disponibles en iOS y watchOS.
- **Acción de descartar** -esta acción se envía a la aplicación cuando el usuario cierra una notificación determinada.

### <a name="creating-custom-actions"></a>Crear acciones personalizadas

Para crear y registrar una acción personalizada con el sistema, utilice el código siguiente:

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

Al crear un nuevo `UNNotificationAction`, se asigna un identificador único y el título que aparecerá en el botón. De forma predeterminada, la acción se creará como una acción en segundo plano, sin embargo, se pueden proporcionar opciones para ajustar el comportamiento de la acción (por ejemplo si se establece en una acción de primer plano).

Cada una de las acciones creadas deben estar asociados con una categoría. Al crear un nuevo `UNNotificationCategory`, se le asigna un identificador único, una lista de acciones que puede realizar una lista de identificadores de intención de proporcionar más información sobre la intención de las acciones en la categoría y algunas opciones para controlar el comportamiento de la categoría.

Por último, todas las categorías están registrados en el sistema mediante la `SetNotificationCategories` método.

### <a name="presenting-custom-actions"></a>Presentación de acciones personalizadas

Una vez que se han creado y registrado con el sistema de un conjunto de acciones personalizadas y categorías, pueden presentar en Local o remota de las notificaciones.

Para recibir notificaciones remoto, establezca un `category` en la carga remota de notificación que coincide con una de las categorías que creó anteriormente. Por ejemplo:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Para las notificaciones Local, establezca el `CategoryIdentifier` propiedad de la `UNMutableNotificationContent` objeto. Por ejemplo:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Una vez más, este identificador debe coincidir con una de las categorías que se creó anteriormente.

### <a name="handling-dismiss-actions"></a>Control de descartar acciones

Como se mencionó anteriormente, una acción de descartar pueden enviarse a la aplicación cuando el usuario cierra una notificación. Puesto que esto no es una acción estándar, una opción deberá establecerse cuando se crea la categoría. Por ejemplo:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Controlar las respuestas de acción

Cuando el usuario interactúa con las acciones personalizadas y las categorías que se crearon anteriormente, la aplicación necesita para completar la tarea solicitada. Esto se hace proporcionando un `UNUserNotificationCenterDelegate` e implementar el `UserNotificationCenter` método. Por ejemplo:

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

El valor en `UNNotificationResponse` clase tiene un `ActionIdentifier` propiedad que puede ser la acción predeterminada o la acción de descartar. Utilice `response.Notification.Request.Identifier` para comprobar la acciones personalizadas.

El `UserText` propiedad contiene el valor de cualquier usuario de entrada de texto. El `Notification` propiedad contiene la notificación de origen que incluye la solicitud con el desencadenador y contenido de la notificación. La aplicación puede decidir si es una variable Local o remoto notificación según el tipo de desencadenador.

## <a name="working-with-service-extensions"></a>Trabajar con las extensiones de servicio

Cuando se trabaja con notificaciones remoto, _extensiones de servicio_ proporcionan una manera de habilitar el cifrado de extremo a extremo dentro de la carga de notificación. Las extensiones de servicio son una extensión de interfaz que no son de usuario (disponible en iOS 10) que se ejecutan en segundo plano con el propósito principal de aumento o reemplazar el contenido visible de una notificación antes de que se presenta al usuario. 

[![](enhanced-user-notifications-images/extension01.png "Introducción a la extensión de servicio")](enhanced-user-notifications-images/extension01.png#lightbox)

Las extensiones de servicio están diseñadas para ejecutarse de forma rápida y solo tienen un corto período de tiempo de ejecución por el sistema. En caso de que la extensión de servicio no se puede completar su tarea en la cantidad de tiempo asignada, se llamará a un método de reserva. Si se produce un error en el recurso de reserva, el contenido de notificación original se mostrará al usuario.

Algunos usos posibles de extensiones de servicio son:

- Proporciona cifrado to-end del contenido notificación remoto.
- Agregar datos adjuntos a las notificaciones remoto para enriquecer ellos.

### <a name="implementing-a-service-extension"></a>Implementar una extensión de servicio

Para implementar una extensión de servicio en una aplicación Xamarin.iOS, realice lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga doble clic en el nombre de la solución en el **solución Pad** y seleccione **agregar** > **Agregar nuevo proyecto**.
3. Seleccione **iOS** > **extensiones** > **las extensiones de servicio de notificación** y haga clic en el **siguiente** botón: 

    [![](enhanced-user-notifications-images/extension02.png "Seleccione las extensiones de servicio de notificación")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el **siguiente** botón: 

    [![](enhanced-user-notifications-images/extension03.png "Escriba un nombre para la extensión")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Ajustar la **nombre del proyecto** o **nombre de la solución** si es necesario y haga clic en el **crear** botón: 

    [![](enhanced-user-notifications-images/extension04.png "Ajuste el nombre del proyecto o el nombre de la solución")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra la solución de la aplicación en Visual Studio.
2. Haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y seleccione **agregar** > **Agregar nuevo proyecto**.
3. Seleccione **iOS** > **extensiones** > **las extensiones de servicio de notificación**: 

    [![](enhanced-user-notifications-images/extension01w.png "Seleccione las extensiones de servicio de notificación")](enhanced-user-notifications-images/extension01w.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el **Aceptar** botón.

-----

> [!IMPORTANT]
> El identificador de paquete para la extensión de servicio debe coincidir con el identificador de paquete de la aplicación principal con `.appnameserviceextension` anexado al final. Por ejemplo, si la aplicación principal tiene un identificador de paquete de `com.xamarin.monkeynotify`, la extensión de servicio debe tener un identificador de paquete de `com.xamarin.monkeynotify.monkeynotifyserviceextension`. Esto se debe establecer automáticamente cuando la extensión se agrega a la solución. 

Hay una clase principal de la extensión de servicio de notificación que deberá modificarse para proporcionar la funcionalidad necesaria. Por ejemplo:

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

El primer método, `DidReceiveNotificationRequest`, se pasará el identificador de notificación, así como el contenido de notificación a través de la `request` objeto. El valor de `contentHandler` debe llamarse para presentar la notificación al usuario.

El segundo método, `TimeWillExpire`, se llamará justo antes de tiempo está a punto de agotarse el tiempo de espera para que la extensión de servicio procesar la solicitud. Si se produce un error en la extensión de servicio llamar a la `contentHandler` en el tiempo asignado, se mostrará el contenido original al usuario.

### <a name="triggering-a-service-extension"></a>Desencadenar una extensión de servicio

Con una extensión de servicio creado y entregado con la aplicación, puede activarse mediante la modificación de la carga remota de notificación enviado al dispositivo. Por ejemplo:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

El nuevo `mutable-content` clave especifica que la extensión de servicio debe iniciarse para actualizar el contenido de la notificación remoto. El `encrypted-content` clave contiene los datos cifrados que la extensión de servicio puede descifrar antes de presentar al usuario.

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

Este código descifra el contenido cifrado de la `encrypted-content` clave, se crea un nuevo `UNMutableNotificationContent`, Establece la `Body` propiedad para el descifrado contenido y usa el `contentHandler` para presentar la notificación al usuario.

## <a name="summary"></a>Resumen

En este artículo ha cubierto todas las formas que se han mejorado la notificación a los usuarios por iOS 10. Se han mostrado el nuevo marco de notificación al usuario y cómo utilizarlo en una aplicación de Xamarin.iOS o la extensión de la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Referencia de UserNotifications Framework](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guía de programación de notificación locales y remotos](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
