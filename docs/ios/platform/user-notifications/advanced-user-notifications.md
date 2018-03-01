---
title: Notificaciones de usuario avanzado
description: "En este artículo se toma una visión más profunda en el nuevo marco de las notificaciones de usuario y cómo aprovechar al máximo del mismo en una aplicación de Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4e1ff652-28f0-4566-b383-9d12664401a4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 6408f3b45f93413fa814e410f07e7b71179b7338
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="advanced-user-notifications"></a>Notificaciones de usuario avanzado

_En este artículo se toma una visión más profunda en el nuevo marco de las notificaciones de usuario y cómo aprovechar al máximo del mismo en una aplicación de Xamarin.iOS._

Nuevo en 10, la notificación al usuario framework permite la entrega y el manejo de notificaciones locales y remotas de iOS. Con este marco de trabajo, una aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

## <a name="about-user-notifications"></a>Acerca de las notificaciones de usuario

El nuevo marco de notificación de usuario permite la entrega y el manejo de notificaciones locales y remotas. Con este marco de trabajo, una aplicación o extensión de la aplicación puede programar la entrega de notificaciones locales mediante la especificación de un conjunto de condiciones como la ubicación o la hora del día.

Además, la aplicación o la extensión puede recibir (y potencialmente modificar) las notificaciones locales y remotas que se entregan al dispositivo de iOS del usuario.

El nuevo marco de interfaz de usuario de notificación de usuario permite que una aplicación o extensión de la aplicación para personalizar la apariencia de las notificaciones locales y remotas cuando se presentan al usuario.

Este marco de trabajo proporciona las siguientes formas que una aplicación puede entregar notificaciones a un usuario:

- **Alertas visuales** : donde la notificación resume hacia abajo de la parte superior de la pantalla como una pancarta.
- **Sonido y vibración** -puede asociarse con una notificación.
- **Icono de aplicación distintivos** - cuando el icono de la aplicación muestra un distintivo que muestra que el nuevo contenido está disponible. Por ejemplo, el número de mensajes de correo electrónico no leído.

Además, según el contexto del usuario actual, hay distintas maneras en que se mostrará una notificación:

- Si el dispositivo se desbloquea, la notificación deberá escribirse hacia abajo desde la parte superior de la pantalla como una pancarta.
- Si el dispositivo está bloqueado, la notificación se mostrará en la pantalla de bloqueo del usuario.
- Si el usuario ha perdido una notificación, puede abrir el centro de notificaciones y ver las notificaciones de espera está disponible, no existe.

Una aplicación de Xamarin.iOS tiene dos tipos de notificaciones de usuario que es capaz de enviar:

- **Notificaciones locales** -estos se envían por aplicaciones instaladas localmente en el dispositivo.
- **Notificaciones remotas** : se envían desde un servidor remoto y, o bien presenta al usuario o desencadena una actualización en segundo plano de contenido de la aplicación.

Para obtener más información, vea nuestra [mejorado de las notificaciones de usuario](~/ios/platform/user-notifications/enhanced-user-notifications.md) documentación.

## <a name="the-new-user-notification-interface"></a>La nueva interfaz de notificación de usuario

Las notificaciones de usuario en iOS 10 se muestran con un nuevo diseño de interfaz de usuario que proporciona más contenido como un título, subtítulo y datos adjuntos de multimedia opcionales que se pueden presentar en la pantalla de bloqueo, como una pancarta en la parte superior del dispositivo o en el centro de notificaciones.

Con independencia de donde se muestra una notificación de usuario en iOS 10, se presenta con la misma apariencia y funcionamiento y con las mismas características y funcionalidad.

En iOS 8, Apple incorporó procesables notificaciones del sistema donde el programador podría adjuntar acciones personalizadas a una notificación y permitir que el usuario realizar acciones en una notificación sin tener que iniciar la aplicación. En iOS 9, Apple mejorado procesables notificaciones con respuesta rápida que permite al usuario responder a una notificación con entrada de texto.

Dado que las notificaciones de usuario son una parte integral de más de la experiencia del usuario en iOS 10, Apple ha ampliado aún más las notificaciones a compatibles con 3D Touch, donde el usuario presiona una notificación y una interfaz de usuario personalizada es la pantalla para proporcionar interacción enriquecido con la notificación.

Cuando se muestra la interfaz de usuario de notificación de usuario personalizada, si el usuario interactúa con todas las acciones que se adjunta a la notificación, se puede actualizar al instante la interfaz de usuario personalizada para proporcionar comentarios sobre lo que se cambió.

Nuevo en iOS 10, la API de interfaz de usuario de notificación de usuario permite que una aplicación Xamarin.iOS aprovechar fácilmente las ventajas de estas nuevas características de interfaz de usuario de notificación de usuario.

## <a name="adding-media-attachments"></a>Agregar datos adjuntos de multimedia

Uno de los elementos más comunes que obtengan compartirse entre usuarios es fotos, por lo que iOS 10 agregó la capacidad de adjuntar un elemento multimedia (por ejemplo, una fotografía) directamente a una notificación, donde se vaya a presentar y estén disponibles para el usuario con el resto de Conté la notificación NT.

Sin embargo, debido a los tamaños implicados en el envío incluso una imagen pequeña, adjuntar a una carga de notificación remoto pasa a ser muy poco práctica. Para controlar esta situación, el programador puede utilizar la nueva extensión de servicio en iOS 10 para descargar la imagen de otro origen (por ejemplo, un almacén de datos CloudKit) y adjuntarlo al contenido de la notificación antes de que se muestre al usuario.

Para que una notificación remoto que va a modificar una extensión de servicio, su carga debe marcarse como mutable. Por ejemplo:

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

[ ![](advanced-user-notifications-images/extension02.png "Proceso de agregar datos adjuntos de multimedia")](advanced-user-notifications-images/extension02.png)

Una vez que se entrega la notificación remoto al dispositivo (a través de APN), la extensión de servicio, a continuación, puede descargar la imagen necesaria a través de cualquier medio deseado (como un `NSURLSession`) y después de recibir la imagen, puede modificar el contenido de la notificación y la presentación que el usuario.

El siguiente es un ejemplo de cómo puede controlar este proceso en el código:

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

Cuando se recibe la notificación de APNs, la dirección de la imagen personalizada se lee desde el contenido y se descarga el archivo desde el servidor. Un `UNNotificationAttachement` se crea con un identificador único y la ubicación local de la imagen (como un `NSUrl`). Se crea una copia modificable de contenido de notificación y se agregan los datos adjuntos de multimedia. Por último, se muestra la notificación al usuario mediante una llamada a la `contentHandler`.

Una vez que se ha agregado un archivo adjunto a una notificación, el sistema tiene sobre el movimiento y la administración del archivo.

Además de las notificaciones remoto presentado anteriormente, también se admiten los datos adjuntos de multimedia de notificaciones Local, donde el `UNNotificationAttachement` se crea y asocia a la notificación junto con su contenido.

Notificación de iOS 10 admite datos adjuntos de multimedia de imágenes (estático y formato GIF), audio o vídeo y el sistema, mostrará automáticamente la interfaz de usuario correcto para cada uno de estos tipos de datos adjuntos cuando se presenta la notificación al usuario.

> [!NOTE]
> **Nota:** debe tener cuidado para optimizar el tamaño medio y el tiempo necesario para descargar lo archivos multimedia desde el servidor remoto (o para ensamblar los medios para las notificaciones Local) como el sistema impone límites estrictos tanto al ejecutar el servicio de la aplicación Extensión. Por ejemplo, considere la posibilidad de enviar una versión escalada hacia abajo de la imagen o un clip pequeño de un vídeo se muestre en la notificación.

## <a name="creating-custom-user-interfaces"></a>Crear Interfaces de usuario personalizadas

Para crear una interfaz de usuario personalizada para sus notificaciones de usuario, el programador debe agregar una extensión de contenido de notificación (nuevo en iOS 10) a la solución de la aplicación.

La extensión de contenido de notificación permite al desarrollador agregar sus propias vistas de la interfaz de usuario de notificación y dibujar cualquier contenido que deseen. Sin embargo, los widgets de interfaz de usuario interactivos (como botones o campos de texto) son **no** compatible con la interfaz de usuario de notificación y nunca deben agregarse a un diseño de interfaz de usuario personalizado.

Para admitir la interacción del usuario con una notificación de usuario, acciones personalizadas deben crearse, registradas con el sistema y asociadas a la notificación antes de que se ha programado con el sistema. La extensión de contenido de notificación se llamará para controlar el procesamiento de estas acciones. Consulte la [trabajar con acciones de notificación](~/ios/platform/user-notifications/enhanced-user-notifications.md) sección de la [mejorado de las notificaciones de usuario](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento para obtener más información sobre acciones personalizadas.

Cuando se presenta una notificación de usuario con una interfaz de usuario personalizada para el usuario, tendrá los siguientes elementos:

[ ![](advanced-user-notifications-images/customui01.png "Una notificación de usuario con los elementos de interfaz de usuario personalizados")](advanced-user-notifications-images/customui01.png)

Si el usuario interactúa con las acciones personalizadas (presentados a continuación de la notificación), se puede actualizar la interfaz de usuario para proporcionar información al usuario como el qué ocurrió cuando invoca una acción determinada.

### <a name="adding-a-notification-content-extension"></a>Agregar una extensión de contenido de notificación

Para implementar una interfaz de usuario de notificación de usuario personalizado en una aplicación Xamarin.iOS, realice lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga doble clic en el nombre de la solución en el **solución Pad** y seleccione **agregar** > **Agregar nuevo proyecto**.
3. Seleccione **iOS** > **extensiones** > **las extensiones de contenido de notificación** y haga clic en el **siguiente** botón: 

    [ ![](advanced-user-notifications-images/notify01.png "Seleccione las extensiones de contenido de notificación")](advanced-user-notifications-images/notify01.png)
4. Escriba un **nombre** para la extensión y haga clic en el **siguiente** botón: 

    [ ![](advanced-user-notifications-images/notify02.png "Escriba un nombre para la extensión")](advanced-user-notifications-images/notify02.png)
5. Ajustar la **nombre del proyecto** o **nombre de la solución** si es necesario y haga clic en el **crear** botón: 

    [ ![](advanced-user-notifications-images/notify03.png "Ajuste el nombre del proyecto o el nombre de la solución")](advanced-user-notifications-images/notify03.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra la solución de la aplicación en Visual Studio para Mac.
2. Haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y seleccione **agregar** > **Agregar nuevo proyecto**.
3. Seleccione **iOS** > **extensiones** > **las extensiones de contenido de notificación**: 

    [ ![](advanced-user-notifications-images/notify01w.png "Seleccione las extensiones de contenido de notificación")](advanced-user-notifications-images/notify01w.png)
4. Escriba un **nombre** para la extensión y haga clic en el **Aceptar** botón.

-----

Cuando la extensión de contenido de notificación se agrega a la solución, se creará tres archivos de proyecto de la extensión:

1. `NotificationViewController.cs` -Éste es el controlador de vista principal para la extensión de contenido de notificación.
2. `MainInterface.storyboard` -Donde el desarrollador establece la interfaz de usuario visible para la extensión de contenido de notificación en el Diseñador de iOS.
3. `Info.plist` : Controla la configuración de la extensión de contenido de notificación.

El valor predeterminado `NotificationViewController.cs` archivo tiene un aspecto similar al siguiente:

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

El `DidReceiveNotification` método se llama cuando se expande la notificación por el usuario para que la extensión de contenido de notificación puede rellenar la interfaz de usuario personalizada con el contenido de la `UNNotification`. En el ejemplo anterior, se ha agregado una etiqueta a la vista, que se exponen al código con el nombre `label` y se usa para mostrar el cuerpo de la notificación.

### <a name="setting-the-notification-content-extensions-categories"></a>Establecer categorías la extensión de contenido notificación

El sistema debe ser informado sobre cómo encontrar la extensión de contenido de notificación de la aplicación se basa en las categorías específicas a que responde. Haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Haga doble clic en la extensión `Info.plist` un archivo en el **solución Pad** para abrirlo y editarlo.
2. Cambie a la **origen** vista.
3. Expanda el `NSExtension` clave.
4. Agregar el `UNNotificationExtensionCategory` clave como tipo **cadena** con el valor de la categoría de la extensión pertenece a (en este ejemplo ' evento invitación): 

    [ ![](advanced-user-notifications-images/customui02.png "Agregar la clave de UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02.png)
5. Guarde los cambios.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Haga doble clic en la extensión `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
3. Expanda el `NSExtension` clave.
4. Agregar el `UNNotificationExtensionCategory` clave como tipo **cadena** con el valor de la categoría de la extensión pertenece a (en este ejemplo ' evento invitación): 

    [ ![](advanced-user-notifications-images/customui02w.png "Agregar la clave de UNNotificationExtensionCategory")](advanced-user-notifications-images/customui02w.png)
5. Guarde los cambios.

-----

Categorías de extensión de contenido de notificación (`UNNotificationExtensionCategory`) usan los mismos valores de categoría que se usan para registrar las acciones de notificación. En el caso de que la aplicación usará la misma interfaz de usuario para varias categorías, cambie la `UNNotificationExtensionCategory` al tipo **matriz** y ofrecen todas las categorías necesarias. Por ejemplo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[ ![](advanced-user-notifications-images/customui03.png "Categorías de extensión de contenido de notificaciones")](advanced-user-notifications-images/customui03.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![](advanced-user-notifications-images/customui03w.png "Categorías de extensión de contenido de notificaciones")](advanced-user-notifications-images/customui03w.png)

-----

### <a name="hiding-the-default-notification-content"></a>Ocultar el contenido de notificación predeterminado

En el caso de que la interfaz de usuario de notificación personalizada va a mostrar el mismo contenido como el valor predeterminado de notificación (título, subtítulo y cuerpo aparecerá automáticamente en la parte inferior de la interfaz de usuario de notificación), se puede ocultar esta información de forma predeterminada mediante la adición de la `UNNotificationExtensionDefaultContentHidden`clave para la `NSExtensionAttributes` clave como tipo **booleano** con un valor de `YES` en la extensión `Info.plist` archivo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[ ![](advanced-user-notifications-images/customui04.png "Buscar información de forma predeterminada")](advanced-user-notifications-images/customui04.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![](advanced-user-notifications-images/customui04w.png "Buscar información de forma predeterminada")](advanced-user-notifications-images/customui04w.png)

-----

### <a name="designing-the-custom-ui"></a>Diseñar la interfaz de usuario personalizada

Para diseñar la interfaz de usuario personalizada de la extensión de contenido notificación, haga doble clic en el `MainInterface.storyboard` arrastre el archivo que se abre para su edición en el Diseñador de iOS en los elementos que necesita para compilar la interfaz deseada (como `UILabels` y `UIImageViews`).

> [!NOTE]
> **Nota:** does de la interfaz de usuario de notificación _no_ admite controles interactivos como campos de texto o botones en una extensión de contenido de notificación. Mientras se pueden agregar al guión gráfico, el usuario no podrá interactuar con ellos. Para agregar la interacción del usuario a una interfaz de usuario de notificación personalizada, utilice en su lugar las acciones personalizadas.

Una vez que se ha diseñado la interfaz de usuario y los controles necesarios se exponen al código de C#, abra el `NotificationViewController.cs` para su edición y modificar el `DidReceiveNotification` método para rellenar la interfaz de usuario cuando el usuario expande la notificación. Por ejemplo:

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

Para ajustar el tamaño del área de contenido muestra al usuario, el código siguiente establece el `PreferredContentSize` propiedad en el `ViewDidLoad` método hasta obtener el tamaño deseado. Este tamaño también podría ser ajustado por aplicar restricciones a la vista en el Diseñador de iOS, se deja al programador para elegir el método más adecuado para ellos.

Porque la notificación de sistema ya se está ejecutando antes de la notificación se invoca la extensión de contenido, el área de contenido se iniciará completo el tamaño y animar hasta el tamaño solicitado cuando se presentan al usuario.

Para eliminar este efecto, edite la `Info.plist` archivo para la extensión y establezca el `UNNotificationExtensionInitialContentSizeRatio` clave de la `NSExtensionAttributes` clave para escribir **número** con un valor que representa la relación deseada. Por ejemplo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[ ![](advanced-user-notifications-images/customui05.png "La clave de UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![](advanced-user-notifications-images/customui05w.png "La clave de UNNotificationExtensionInitialContentSizeRatio")](advanced-user-notifications-images/customui05w.png)

-----

### <a name="using-media-attachments-in-custom-ui"></a>Usar los datos adjuntos de multimedia en la interfaz de usuario personalizada

Dado que los datos adjuntos de multimedia (tal como se muestra en el [agregar datos adjuntos de multimedia](#Adding-Media-Attachments) sección anterior) forman parte de la carga de notificación, se pueden obtener acceso a y aparece en la extensión de contenido de la notificación al igual que estarían en el valor predeterminado Notificación de interfaz de usuario.

Por ejemplo, si la interfaz de usuario personalizada anterior incluye un `UIImageView` que se expuso al código de C#, el código siguiente podría usarse para rellenarla con los datos adjuntos de multimedia:

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

Dado que los datos adjuntos de multimedia está administrado por el sistema, está fuera de espacio aislado de la aplicación. La extensión debe informar al sistema que quiere que se acceda al archivo mediante una llamada a la `StartAccessingSecurityScopedResource` método. Cuando se realiza la extensión con el archivo, debe llamar a la `StopAccessingSecurityScopedResource` para liberar la conexión.

### <a name="adding-custom-actions-to-a-custom-ui"></a>Agregar acciones personalizadas en un interfaz de usuario personalizado

Como se mencionó anteriormente, la interfaz de usuario de notificación no admite la interacción del usuario para que controles como botones y campos de texto no se admiten en el diseño de interfaz de usuario.

En su lugar, se utiliza el mecanismo estándar de acciones personalizadas para agregar interactividad a una interfaz de usuario de notificación personalizada. Consulte la [trabajar con acciones de notificación](~/ios/platform/user-notifications/enhanced-user-notifications.md) sección de la [mejorado de las notificaciones de usuario](~/ios/platform/user-notifications/enhanced-user-notifications.md) documento para obtener más información sobre acciones personalizadas.

Además de las acciones personalizadas, la extensión de contenido de notificación puede responder a las acciones integradas siguientes:

- **Acción predeterminada** -esto es cuando el usuario puntea una notificación para abrir la aplicación y mostrar los detalles de la notificación determinada.
- **Acción de descartar** -esta acción se envía a la aplicación cuando el usuario cierra una notificación determinada.

Las extensiones de contenido de notificación también tiene la capacidad de actualizar su interfaz de usuario cuando el usuario invoca una de las acciones personalizadas, como mostrar una fecha como aceptado cuando el usuario puntea el **Accept** botón de acción personalizada. Además, las extensiones de contenido de notificación puede indicar al sistema para retrasar el despido de la interfaz de usuario de notificación para que el usuario pueda ver el efecto de su acción antes de cerrar la notificación.

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

Agregando el `Server.PostEventResponse` controlador para la `DidReceiveNotification` método de la extensión de contenido de notificación, la extensión *debe* controlar todas las acciones personalizadas. La extensión de reenvío también puede realizar las acciones personalizadas en la aplicación contenedora cambiando el `UNNotificationContentExtensionResponseOption`. Por ejemplo:

```csharp
// Close Notification
completionHandler (UNNotificationContentExtensionResponseOption.DismissAndForwardAction);
```

### <a name="working-with-the-text-input-action-in-custom-ui"></a>Trabajar con la acción de entrada de texto en la interfaz de usuario personalizada

Dependiendo del diseño de la aplicación y la notificación, puede haber ocasiones que requieren que el usuario escribir texto en la notificación (por ejemplo, para responder a un mensaje). Una extensión de contenido de notificación tiene acceso a la acción de entrada de texto integrado simplemente como una notificación estándar hace.

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

Este código crea una nueva acción de entrada de texto y lo agrega a la categoría de la extensión (en la `MakeExtensionCategory`) método. En el `DidReceive` invalidar el método controla al usuario escribir texto con el código siguiente:

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

Cuando se desencadena la acción de comentario por el usuario, el controlador de vista y el campo de entrada de texto personalizado tienen que ser activados:

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

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tomado un vistazo avanzada de mediante el nuevo marco de notificación de usuario en una aplicación de Xamarin.iOS. Lo cubre la adición de datos adjuntos de multimedia Local y remoto de notificación y lo cubre con la nueva interfaz de usuario de notificación de usuario para crear interfaces de usuario de notificación personalizada.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Referencia de UserNotifications Framework](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guía de programación de notificación locales y remotos](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)
