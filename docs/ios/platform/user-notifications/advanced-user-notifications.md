---
title: Notificaciones de usuario avanzadas en Xamarin.iOS
description: En este artículo echa un vistazo más profundo en el marco de las notificaciones de usuario, introducido en iOS 10. Describe las notificaciones de usuario, la interfaz de notificación de usuario, los archivos adjuntos multimedia, interfaces de usuario personalizadas y mucho más.
ms.prod: xamarin
ms.assetid: 4E0C60AE-6F54-4098-8FA0-AADF9AC86805
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 4472654064812142e3281374754ace0042b542bf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61089324"
---
# <a name="advanced-user-notifications-in-xamarinios"></a>Notificaciones de usuario avanzadas en Xamarin.iOS

Nuevo en iOS 10, la notificación de usuario framework permite la entrega y el tratamiento de las notificaciones locales y remotas. Con este marco de trabajo, una aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

## <a name="about-user-notifications"></a>Acerca de las notificaciones de usuario

El nuevo marco de notificación de usuario permite la entrega y el tratamiento de las notificaciones locales y remotas. Con este marco de trabajo, una aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o extensión puede recibir (y posiblemente modificar) las notificaciones locales y remotas que se entregan a dispositivos iOS del usuario.

El nuevo marco de interfaz de usuario de notificación de usuario permite que una aplicación o extensión de la aplicación para personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Este marco de trabajo proporciona los siguientes métodos que una aplicación puede entregar notificaciones a un usuario:

- **Alertas visuales** : donde la notificación resume hacia abajo de la parte superior de la pantalla como un banner.
- **Sonido y vibraciones** -puede asociarse con una notificación.
- **Uso de distintivos de icono de aplicación** - cuando el icono de la aplicación muestra un distintivo que muestra que el nuevo contenido está disponible. Por ejemplo, el número de mensajes de correo electrónico no leídos.

Además, según el contexto del usuario actual, hay distintas maneras en que se mostrará una notificación:

- Si el dispositivo está desbloqueado, la notificación se desplegarán en la parte superior de la pantalla como un banner.
- Si el dispositivo está bloqueado, se mostrará la notificación en pantalla de bloqueo del usuario.
- Si el usuario ha perdido una notificación, puede abrir el centro de notificaciones y ver las notificaciones de espera está disponible, no existe.

Una aplicación Xamarin.iOS tiene dos tipos de notificaciones de usuario que es capaz de enviar:

- **Notificaciones locales** -estos se envían por las aplicaciones instaladas localmente en el dispositivo del usuario.
- **Notificaciones remotas** : se envían desde un servidor remoto y bien presenta al usuario o desencadena una actualización en segundo plano de contenido de la aplicación.

Para obtener más información, consulte nuestra [notificaciones de usuario mejorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documentación.

## <a name="the-new-user-notification-interface"></a>La nueva interfaz de notificación de usuario

Las notificaciones de usuario en iOS 10 se le presentará un nuevo diseño de interfaz de usuario que proporciona contenido más como un título, subtítulo y archivos adjuntos multimedia opcional que se pueden presentar en la pantalla de bloqueo, como una pancarta en la parte superior del dispositivo o en el centro de notificaciones.

Independientemente de donde se muestra una notificación de usuario en iOS 10, se presenta con la misma apariencia y funcionamiento y con las mismas características y funcionalidad.

En iOS 8, Apple introdujo las notificaciones que requieren acción donde el desarrollador podría asociar acciones personalizadas a una notificación y permitir que el usuario realizar acciones en una notificación sin tener que iniciar la aplicación. En iOS 9, Apple mejorado las notificaciones que requieren acción con respuesta rápida, lo que permite al usuario que responda a una notificación con la entrada de texto.

Dado que las notificaciones de usuario son una parte más integral de la experiencia del usuario en iOS 10, Apple ha expandido aún más notificaciones accionable a compatibles con 3D Touch, donde el usuario presiona una notificación y una interfaz de usuario personalizada es la pantalla para proporcionar interacción enriquecido con la notificación.

Cuando se muestre la interfaz de usuario de notificación de usuario personalizada, si el usuario interactúa con todas las acciones que se adjunta a la notificación, se puede actualizar al instante la interfaz de usuario personalizada para proporcionar comentarios sobre lo que ha cambiado.

Nuevo iOS 10, la API de la interfaz de usuario de notificación de usuario permite que una aplicación de Xamarin.iOS para aprovecharse de estas nuevas características de interfaz de usuario de notificación de usuario.

## <a name="adding-media-attachments"></a>Agregar datos adjuntos de multimedia

Uno de los elementos más comunes que compartían entre los usuarios es fotos, por lo que iOS 10 agregó la capacidad de asociar un elemento multimedia (por ejemplo, una fotografía) directamente a una notificación, donde será presentada y estén disponibles para el usuario con el resto de conte la notificación NT.

Sin embargo, debido a los tamaños implicados en el envío de incluso una imagen pequeña, conectarlo a una carga de notificación remota se convierte en poco práctica. Para controlar esta situación, el programador puede utilizar la nueva extensión de servicio en iOS 10 para descargar la imagen de otro origen (por ejemplo, un almacén de datos de CloudKit) y se asocia al contenido de la notificación antes de que se muestre al usuario.

Para que una notificación remota a ser modificado por una extensión de servicio, su carga se debe marcar como mutable. Por ejemplo:

```csharp
{
    aps : {
        alert : "New Photo Available",
        mutable-content: 1
    },
    my-attachment : "https://example.com/photo.jpg"
}
```

Eche un vistazo a la información general siguiente del proceso:

[![](advanced-user-notifications-images/extension02.png "Proceso de agregar los archivos adjuntos multimedia")](advanced-user-notifications-images/extension02.png#lightbox)

Una vez que se entrega la notificación remota al dispositivo (a través de Apple Push Notification Service), la extensión de servicio, a continuación, puede descargar la imagen necesaria a través de cualquier medio deseado (como un `NSURLSession`) y después de recibir la imagen, puede modificar el contenido de la notificación y visualización que el usuario.

Este es un ejemplo de cómo puede controlar este proceso en el código:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyNotification
{
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Constructors
        public NotificationService ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            // Get file URL
            var attachementPath = request.Content.UserInfo.ObjectForKey (new NSString ("my-attachment"));
            var url = new NSUrl (attachementPath.ToString ());

            // Download the file
            var localURL = new NSUrl ("PathToLocalCopy");

            // Create attachment
            var attachmentID = "image";
            var options = new UNNotificationAttachmentOptions ();
            NSError err;
            var attachment = UNNotificationAttachment.FromIdentifier (attachmentID, localURL, options , out err);

            // Modify contents
            var content = request.Content.MutableCopy() as UNMutableNotificationContent;
            content.Attachments = new UNNotificationAttachment [] { attachment };

            // Display notification
            contentHandler (content);
        }

        public override void TimeWillExpire ()
        {
            // Handle service timing out
        }
        #endregion
    }
}
```

Cuando se recibe la notificación de APNs, la dirección de la imagen personalizada se lee desde el contenido y el archivo se descarga desde el servidor. Un `UNNotificationAttachement` se crea con un identificador único y la ubicación local de la imagen (como un `NSUrl`). Se crea una copia de la notificación de contenido mutable y se agregan los archivos adjuntos multimedia. Por último, la notificación se muestra al usuario mediante una llamada a la `contentHandler`.

Una vez que se ha agregado un archivo adjunto a una notificación, el sistema tiene sobre el movimiento y la administración del archivo.

Además de las notificaciones remotas presentada anteriormente, también se admiten los archivos adjuntos multimedia de notificaciones locales, donde el `UNNotificationAttachement` creado y conectado a la notificación junto con su contenido.

Notificación en iOS 10 admite datos adjuntos de multimedia de imágenes (estático y GIF), audio o vídeo y el sistema mostrará automáticamente la interfaz de usuario personalizada correcta para cada uno de estos tipos de datos adjuntos cuando se presenta la notificación al usuario.

> [!NOTE]
> Debe tener cuidado para optimizar el tamaño medio y el tiempo necesario para descargar los medios desde el servidor remoto (o para ensamblar los medios para las notificaciones locales) como el sistema impone límites estrictos en ambos cuando se ejecuta la extensión de la aplicación de servicio. Por ejemplo, considere la posibilidad de enviar una versión a escala hacia abajo de la imagen o un clip pequeño de un vídeo que se presentará en la notificación.

## <a name="creating-custom-user-interfaces"></a>Creación de interfaces de usuario personalizada

Para crear una interfaz de usuario personalizada para sus notificaciones de usuario, el desarrollador debe agregar una extensión de contenido de notificación (nuevo en iOS 10) a la solución de la aplicación.

La extensión de contenido de notificación permite al desarrollador agregar sus propias vistas a la interfaz de usuario de notificación y dibujar cualquier contenido que deseen. A partir de iOS 12, las extensiones de contenido de notificación admite controles de interfaz de usuario interactivos como controles deslizantes y botones. Para obtener más información, consulte el [interactivas notificaciones en iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) documentación.

Para admitir la interacción del usuario con una notificación de usuario, acciones personalizadas deben crearse, registradas con el sistema y conectadas a la notificación antes de programar con el sistema. La extensión de contenido de la notificación se llamará para controlar el procesamiento de estas acciones. Consulte la [trabajar con las acciones de notificación](~/ios/platform/user-notifications/enhanced-user-notifications.md) sección de la [notificaciones de usuario mejorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento para obtener más detalles sobre las acciones personalizadas.

Cuando se presenta una notificación de usuario con una interfaz de usuario personalizada para el usuario, tendrá los siguientes elementos:

[![](advanced-user-notifications-images/customui01.png "Una notificación de usuario con un elementos de interfaz de usuario personalizados")](advanced-user-notifications-images/customui01.png#lightbox)

Si el usuario interactúa con las acciones personalizadas (presentados a continuación de la notificación), se puede actualizar la interfaz de usuario para proporcionar al usuario información como qué sucedió cuando invoca una acción determinada.

### <a name="adding-a-notification-content-extension"></a>Agregar una extensión de contenido de notificación

Para implementar una interfaz de usuario de notificación de usuario personalizado en una aplicación de Xamarin.iOS, realice lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga doble clic en el nombre de la solución en el **panel de solución** y seleccione **agregar** > **Agregar nuevo proyecto**.
3. Seleccione **iOS** > **extensiones** > **las extensiones de contenido de notificación** y haga clic en el **siguiente** botón: 

    [![](advanced-user-notifications-images/notify01.png "Seleccione las extensiones de contenido de notificación")](advanced-user-notifications-images/notify01.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el **siguiente** botón: 

    [![](advanced-user-notifications-images/notify02.png "Escriba un nombre para la extensión")](advanced-user-notifications-images/notify02.png#lightbox)
5. Ajustar el **nombre del proyecto** o **nombre de la solución** si es necesario y haga clic en el **crear** botón: 

    [![](advanced-user-notifications-images/notify03.png "Ajuste el nombre del proyecto o el nombre de la solución")](advanced-user-notifications-images/notify03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y seleccione **Agregar > Nuevo proyecto...** .
3. Seleccione **Visual C# > extensiones de iOS > extensión de contenido de notificación**:

    [![](advanced-user-notifications-images/notify01.w157-sml.png "Seleccione las extensiones de contenido de notificación")](advanced-user-notifications-images/notify01.w157.png#lightbox)
4. Escriba un **nombre** para la extensión y haga clic en el **Aceptar** botón.

-----

Cuando se agrega la extensión de contenido de notificación a la solución, se crearán tres archivos en el proyecto de la extensión:

1. `NotificationViewController.cs` -Éste es el controlador de vista principal para la extensión de contenido de notificación.
2. `MainInterface.storyboard` -Donde el desarrollador diseña la interfaz de usuario visible para la extensión de contenido de notificación en el Diseñador de iOS.
3. `Info.plist` : Controla la configuración de la extensión de contenido de notificación.

El valor predeterminado `NotificationViewController.cs` archivo es similar al siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

        }
        #endregion
    }
}
```

El `DidReceiveNotification` se llama al método cuando se expande la notificación por el usuario para que la extensión de contenido de la notificación puede rellenar la interfaz de usuario personalizada con el contenido de la `UNNotification`. En el ejemplo anterior, se ha agregado una etiqueta a la vista, que se exponen al código con el nombre `label` y se usa para mostrar el cuerpo de la notificación.

### <a name="setting-the-notification-content-extensions-categories"></a>Establecimiento de las categorías de la extensión contenido de notificación

El sistema necesita para mantenerse informado sobre cómo encontrar la extensión de contenido de notificación de la aplicación según las categorías específicas a que responde. Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga doble clic en la extensión `Info.plist` de archivos en el **panel de solución** para abrirlo y editarlo.
2. Cambie a la **origen** vista.
3. Expanda el `NSExtension` clave.
4. Agregar el `UNNotificationExtensionCategory` clave como tipo **cadena** con el valor de la categoría a la que pertenece la extensión (en este ejemplo ' invitación a un evento): 

    [![](advanced-user-notifications-images/customui02.png "Agregue la clave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02.png#lightbox)
5. Guarde los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Haga doble clic en la extensión `Info.plist` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Expanda el `NSExtension` clave.
4. Agregar el `UNNotificationExtensionCategory` clave como tipo **cadena** con el valor de la categoría a la que pertenece la extensión (en este ejemplo ' invitación a un evento): 

    [![](advanced-user-notifications-images/customui02w.png "Agregue la clave UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02w.png#lightbox)
5. Guarde los cambios.

-----

Categorías de extensión de contenido de notificación (`UNNotificationExtensionCategory`) utilizan los mismos valores de categoría que se usan para registrar las acciones de notificación. En la situación donde la aplicación usará la misma interfaz de usuario para varias categorías, cambie el `UNNotificationExtensionCategory` al tipo **matriz** y proporcionar todas las categorías necesarias. Por ejemplo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](advanced-user-notifications-images/customui03.png "Categorías de extensión de contenido de notificaciones")](advanced-user-notifications-images/customui03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui03w.png "Categorías de extensión de contenido de notificaciones")](advanced-user-notifications-images/customui03w.png#lightbox)

-----

### <a name="hiding-the-default-notification-content"></a>Ocultar el contenido de notificación predeterminado

En el caso donde la interfaz de usuario de la notificación personalizada va a mostrar el mismo contenido como el valor predeterminado de notificación (título, subtítulo y cuerpo aparece automáticamente en la parte inferior de la interfaz de usuario de notificación), se puede ocultar esta información de forma predeterminada mediante la adición de la `UNNotificationExtensionDefaultContentHidden`clave para el `NSExtensionAttributes` clave como tipo **booleano** con un valor de `YES` en la extensión `Info.plist` archivo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](advanced-user-notifications-images/customui04.png "Buscar información de forma predeterminada")](advanced-user-notifications-images/customui04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui04w.png "Buscar información de forma predeterminada")](advanced-user-notifications-images/customui04w.png#lightbox)

-----

### <a name="designing-the-custom-ui"></a>Diseñar la interfaz de usuario personalizada

Para diseñar la interfaz de usuario personalizada de la extensión contenido de notificación, haga doble clic en el `MainInterface.storyboard` archivo para abrirlo y editarlo en iOS Designer, arrastre los elementos que necesita para compilar la interfaz deseada (como `UILabels` y `UIImageViews`).

> [!NOTE]
> A partir de iOS 12, una extensión de contenido de la notificación puede incluir controles interactivos como botones y los campos de texto. Para obtener más información, consulte el [interactivas notificaciones en iOS 12](~/ios/platform/introduction-to-ios12/notifications/interactive.md) documentación.

Una vez que se ha diseñado la interfaz de usuario y los controles necesarios se exponen a C# código, abra el `NotificationViewController.cs` para editarlo y modifique el `DidReceiveNotification` método para rellenar la interfaz de usuario cuando el usuario expande la notificación. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

        }
        #endregion
    }
}
```

### <a name="setting-the-content-area-size"></a>Establecer el tamaño del área de contenido

Para ajustar el tamaño del área de contenido muestra al usuario, el código siguiente establece la `PreferredContentSize` propiedad en el `ViewDidLoad` método hasta el tamaño deseado. También se podría ajustar este tamaño aplicando las restricciones a la vista en el Diseñador de iOS, se deja al programador para elegir el método que mejor funcione para ellos.

Dado que empezara la notificación de sistema ya se está ejecutando antes de la notificación se invoca la extensión de contenido, el área de contenido completo tamaño y se anima hasta el tamaño solicitado cuando se presentan al usuario.

Para eliminar este efecto, edite el `Info.plist` archivo para la extensión y establezca el `UNNotificationExtensionInitialContentSizeRatio` clave de la `NSExtensionAttributes` clave al tipo **número** con un valor que representa la relación deseada. Por ejemplo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](advanced-user-notifications-images/customui05.png "La clave UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](advanced-user-notifications-images/customui05w.png "La clave UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05w.png#lightbox)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Uso de los archivos adjuntos multimedia en la interfaz de usuario personalizada

Dado que los archivos adjuntos multimedia (tal como se muestra en el [agregar datos adjuntos de multimedia](#adding-media-attachments) sección anterior) forman parte de la carga de notificación, puede obtener acceso y que se muestra en la extensión de contenido de notificación, tal como lo estarían en el valor predeterminado Notificación de la interfaz de usuario.

Por ejemplo, si la interfaz de usuario personalizada anterior incluye un `UIImageView` que expuso a C# , el siguiente código código podría usarse para rellenarla con los datos adjuntos de multimedia:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;

namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }
        #endregion
    }
}
```

Dado que los datos adjuntos de multimedia está administrado por el sistema, está fuera del recinto de seguridad de la aplicación. La extensión debe informar al sistema que desea tener acceso al archivo mediante una llamada a la `StartAccessingSecurityScopedResource` método. Cuando la extensión se realiza con el archivo, debe llamar a la `StopAccessingSecurityScopedResource` para liberar su conexión.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Agregar acciones personalizadas a una interfaz de usuario personalizada

Botones de acción personalizada se pueden usar para agregar interactividad a una interfaz de usuario de la notificación personalizada. Consulte la [trabajar con las acciones de notificación](~/ios/platform/user-notifications/enhanced-user-notifications.md) sección de la [notificaciones de usuario mejorada](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento para obtener más detalles sobre las acciones personalizadas.

Además de las acciones personalizadas, la extensión de contenido de la notificación puede responder a las acciones integradas siguientes:

- **Acción predeterminada** -esto es cuando el usuario pulsa una notificación para abrir la aplicación y mostrar los detalles de la notificación especificada.
- **Acción de descartar** : esta acción se envía a la aplicación cuando el usuario cierra una notificación determinada.

Las extensiones de contenido de notificación también tiene la capacidad de actualizar su interfaz de usuario cuando el usuario invoca una de las acciones personalizadas, como mostrar una fecha como aceptado cuando el usuario pulsa el **Accept** botón de acción personalizada. Además, las extensiones de contenido de notificación puede indicar al sistema que se va a retrasar el despido de la interfaz de usuario de notificación para que el usuario pueda ver el efecto de su acción antes de cerrar la notificación.

Esto se hace implementando una segunda versión de la `DidReceiveNotification` método que incluye un controlador de finalización. Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;
using CoreGraphics;

namespace myApp {
    public class NotificationViewController : UIViewController, UNNotificationContentExtension {

        public override void ViewDidLoad() {
            base.ViewDidLoad();

            // Adjust the size of the content area
            var size = View.Bounds.Size
            PreferredContentSize = new CGSize(size.Width, size.Width/2);
        }

        public void DidReceiveNotification(UNNotification notification) {

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = Content.UserInfo["location"] as string;
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments[0];
                if (attachment.Url.StartAccessingSecurityScopedResource()) {
                    EventImage.Image = UIImage.FromFile(attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch(response.ActionIdentifier){
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
    }
}
```

Agregando el `Server.PostEventResponse` controlador para el `DidReceiveNotification` método de la extensión de contenido de notificación, la extensión *debe* controlar todas las acciones personalizadas. La extensión también puede reenviar las acciones personalizadas en la aplicación contenedora cambiando el `UNNotificationContentExtensionResponseOption`. Por ejemplo:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Trabajar con la acción de entrada de texto en la interfaz de usuario personalizada

Dependiendo del diseño de la aplicación y la notificación, puede que en ocasiones requiere que el usuario escribir texto en la notificación (por ejemplo, para responder a un mensaje). Una extensión de contenido de notificación tiene acceso a la acción de entrada de texto integrado simplemente como una notificación estándar.

Por ejemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;
using UserNotificationsUI;


namespace MonkeyChatNotifyExtension
{
    public partial class NotificationViewController : UIViewController, IUNNotificationContentExtension
    {
        #region Computed Properties
        // Allow to take input
        public override bool CanBecomeFirstResponder {
            get { return true; }
        }

        // Return the custom created text input view with the
        // required buttons and return here
        public override UIView InputAccessoryView {
            get { return InputView; }
        }
        #endregion

        #region Constructors
        protected NotificationViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }
        #endregion

        #region Private Methods
        private UNNotificationCategory MakeExtensionCategory ()
        {

            // Create Accept Action
            ...

            // Create decline Action
            ...

            // Create Text Input Action
            var commentID = "comment";
            var commentTitle = "Comment";
            var textInputButtonTitle = "Send";
            var textInputPlaceholder = "Enter comment here...";
            var commentAction = UNTextInputNotificationAction.FromIdentifier (commentID, commentTitle, UNNotificationActionOptions.None, textInputButtonTitle, textInputPlaceholder);

            // Create category
            var categoryID = "event-invite";
            var actions = new UNNotificationAction [] { acceptAction, declineAction, commentAction };
            var intentIDs = new string [] { };
            var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);

            // Return new category
            return category;

        }
        #endregion

        #region Public Methods
        [Export ("didReceiveNotification:")]
        public void DidReceiveNotification (UNNotification notification)
        {
            label.Text = notification.Request.Content.Body;

            // Grab content
            var content = notification.Request.Content;

            // Display content in the UILabels
            EventTitle.Text = content.Title;
            EventDate.Text = content.Subtitle;
            EventMessage.Text = content.Body;

            // Get location and display
            var location = content.UserInfo ["location"].ToString ();
            if (location != null) {
                Event.Location.Text = location;
            }

            // Get Media Attachment
            if (content.Attachements.Length > 1) {
                var attachment = content.Attachments [0];
                if (attachment.Url.StartAccessingSecurityScopedResource ()) {
                    EventImage.Image = UIImage.FromFile (attachment.Url.Path);
                    attachment.Url.StopAccessingSecurityScopedResource ();
                }
            }
        }

        [Export ("didReceiveNotificationResponse:completionHandler:")]
        public void DidReceiveNotification (UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
        {

            // Is text input?
            if (response is UNTextInputNotificationResponse) {
                var textResponse = response as UNTextInputNotificationResponse;
                Server.Send (textResponse.UserText, () => {
                    // Close Notification
                    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
                });
            }

            // Update UI when the user interacts with the
            // Notification
            Server.PostEventResponse += (response) {
                // Take action based on the response
                switch (response.ActionIdentifier) {
                case "accept":
                    EventResponse.Text = "Going!";
                    EventResponse.TextColor = UIColor.Green;
                    break;
                case "decline":
                    EventResponse.Text = "Not Going.";
                    EventResponse.TextColor = UIColor.Red;
                    break;
                }

                // Close Notification
                completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
            };
        }
        #endregion
    }
}
```

Este código crea una nueva acción de entrada de texto y lo agrega a la categoría de la extensión (en el `MakeExtensionCategory`) método. En el `DidReceive` invalidar el método, administre al usuario escribir texto con el código siguiente:

```csharp
// Is text input?
if (response is UNTextInputNotificationResponse) {
    var textResponse = response as UNTextInputNotificationResponse;
    Server.Send (textResponse.UserText, () => {
        // Close Notification
        completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);
    });
}
```

Si el diseño requiere agregar botones personalizados para el campo de entrada de texto, agregue el código siguiente para incluirlos:

```csharp
// Allow to take input
public override bool CanBecomeFirstResponder {
    get {return true;}
}

// Return the custom created text input view with the
// required buttons and return here
public override UIView InputAccessoryView {
    get {return InputView;}
}
```

Cuando se desencadena la acción de comentario por el usuario, el controlador de vista y el campo de entrada de texto personalizado que se puede activar:

```csharp
// Update UI when the user interacts with the
// Notification
Server.PostEventResponse += (response) {
    // Take action based on the response
    switch(response.ActionIdentifier){
    ...
    case "comment":
        BecomeFirstResponder();
        TextField.BecomeFirstResponder();
        break;
    }

    // Close Notification
    completionHandler (UNNotificationContentExtensionResponseOption.Dismiss);

};
```

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión avanzada de usar el nuevo marco de notificación de usuario en una aplicación de Xamarin.iOS. Trata de agregar datos adjuntos de multimedia en Local y remota de notificación y lo cubre mediante la nueva interfaz de usuario de notificación de usuario para crear interfaces de usuario de notificación personalizada.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Referencia de marco de Usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guía de programación de notificaciones locales y remotos](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
