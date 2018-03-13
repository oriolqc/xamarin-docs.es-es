---
title: Extensiones de aplicaciones avanzadas de mensajes
description: "Este artículo muestra técnicas avanzadas para trabajar con extensiones de mensajes de aplicación en una solución de Xamarin.iOS que se integra con la aplicación de mensajes y presenta nuevas funciones para el usuario."
ms.topic: article
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fcfd1fd2ec9271bb5e8d9e09b43b7dc4cf3b3f12
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="advanced-message-app-extensions"></a>Extensiones de aplicaciones avanzadas de mensajes

_Este artículo muestra técnicas avanzadas para trabajar con extensiones de mensajes de aplicación en una solución de Xamarin.iOS que se integra con la aplicación de mensajes y presenta nuevas funciones para el usuario._


Nuevo en 10 de iOS, una extensión de la aplicación de mensaje se integra con la **mensajes** aplicación y presenta nueva funcionalidad al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos.

## <a name="about-message-app-extensions"></a>Acerca de las extensiones de aplicación de mensaje

Como se mencionó anteriormente, una extensión de la aplicación de mensaje se integra con la **mensajes** aplicación y presenta nueva funcionalidad al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos. Existen dos tipos de extensión de la aplicación de mensaje:

- **Módulos de etiqueta** -contiene una colección de etiquetas que el usuario puede agregar a un mensaje. Módulos de etiqueta se pueden crear sin escribir ningún código.
- **iMessage aplicación** -puede presentar una interfaz de usuario personalizada dentro de la aplicación de mensajes para seleccionar adhesivos, escribir texto, incluidos los archivos de medios (con las conversiones de tipo opcional) y crear, modificar y enviar mensajes de interacción.

Extensiones de mensajes de aplicaciones proporciona tres tipos principales de contenido:

- **Mensajes interactivos** -son un tipo de contenido del mensaje personalizado que genera una aplicación, cuando el usuario puntea en el mensaje, la aplicación se iniciará en primer plano.
- **Adhesivos** -son imágenes generadas por la aplicación que puede incluirse en los mensajes enviados entre los usuarios. Consulte nuestro [el generador de helado](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) aplicación de ejemplo para una implementación de ejemplo de una aplicación de módulo de etiqueta.
- **Otro tipo de contenido admite** -la aplicación puede proporcionar contenido como fotos, vídeos, texto o vínculos del tipo que siempre ha sido respaldado por la aplicación de mensajes.

Nuevo en iOS 10, la aplicación de mensaje ahora incluye su propia tienda de aplicaciones dedicado e integradas. Las aplicaciones que incluyen extensiones de aplicaciones de mensajes se muestran y se promocionan en este almacén. El nuevo espacio de aplicación de mensajes se mostrarán las aplicaciones que se han descargado de la tienda de aplicaciones de mensajes para proporcionar un acceso rápido a los usuarios.

También nuevo en 10 de iOS, Apple agregada Inline App Attribution que permite al usuario detectar fácilmente una aplicación. Por ejemplo, si un usuario envía el contenido a otro desde una aplicación que el usuario 2 no tiene instalado (por ejemplo, una etiqueta por ejemplo), el nombre de la aplicación envío aparece en el contenido en el historial de mensajes. Si el usuario puntee en la aplicación el nombre, se puede abrir la tienda de aplicaciones de mensaje y la aplicación seleccionada en el almacén.

Extensiones de mensajes de aplicaciones son similares a las aplicaciones existentes de iOS que el programador esté familiarizado para crear y tendrán acceso a todas las características de una aplicación de iOS estándar y marcos de trabajo estándares. Por ejemplo:

- Tienen acceso a la compra de la aplicación.
- Tienen acceso a Apple Pay.
- Tienen acceso al hardware del dispositivo, como la cámara.

Solo se admiten extensiones de mensajes de aplicaciones en iOS 10, sin embargo, el contenido que estas extensiones de envío está visible en los dispositivos watchOS y macOS. El nuevo _Recents Page_ agregado a watchOS 3, mostrará etiquetas recientes que se hayan enviado desde el teléfono, los de extensiones de mensajes de aplicaciones, incluidos y permite al usuario enviar esas etiquetas desde la inspección.

## <a name="about-interactive-messages"></a>Acerca de los mensajes interactivos

Mensajes interactivos presentan una burbuja de mensaje personalizado y se proporcionan con una extensión de la aplicación de mensaje. Permiten al usuario crear mensaje contenido interactivo, introdúzcalo en el campo de entrada de mensaje y enviarlo.

[![](advanced-message-app-extensions-images/interactive01.png "Creación de contenido de mensaje interactivo")](advanced-message-app-extensions-images/interactive01.png#lightbox)

El usuario que recibe puede responder a un mensaje interactivo punteando en sus burbujas de mensaje en el historial de mensajes para cargar la extensión de la aplicación de mensaje que lo creó. La extensión se ser iniciado pantalla completa y permitir al usuario crear una respuesta y enviar al usuario de origen.

[![](advanced-message-app-extensions-images/interactive02.png "La extensión inicia la pantalla completa")](advanced-message-app-extensions-images/interactive02.png#lightbox)


Los temas siguientes se explicará en detalle a continuación:

- Introducción a la API mensajes
- El ciclo de vida de la extensión
- Redactar un mensaje
- Enviar un mensaje

## <a name="messages-api-overview"></a>Introducción a la API mensajes

Cuando se invoca por el usuario, una extensión de la aplicación de mensaje se mostrará en la parte inferior del historial del mensaje en el modo de vista compacta:

[![](advanced-message-app-extensions-images/interactive03.png "Introducción a la API mensajes")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. La `MSMessageAppViewController` objeto en la extensión de la aplicación de mensaje es la clase principal que se llama cuando la vista de la extensión se muestra al usuario.
2. La conversación se presenta al usuario como un `MSConversation` instancia del objeto.
3. La `MSMessage` clase representa una burbuja de mensaje determinado en la conversación.
4. `MSSession` controla cómo se envía un mensaje.
5. `MSMessageTemplateLayout` controla cómo se muestra el mensaje

## <a name="the-extension-lifecycle"></a>El ciclo de vida de la extensión

Eche un vistazo en el proceso de una extensión de la aplicación de mensaje pasan a estar activos:

[![](advanced-message-app-extensions-images/interactive04.png "El proceso de una extensión de la aplicación de mensaje pasan a estar activos")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Cuando se inicia una extensión (por ejemplo, en el espacio de aplicación), la aplicación de mensaje iniciará un proceso.
2. El `DidBecomeActive` método se llama y se pasa un `MSConversation` que representa la conversación que se ejecuta la extensión de la aplicación de mensaje.
3. Dado que la extensión se basa en un `UIViewController` ambos `ViewWillAppear` y `ViewDidAppear` se denominan.

A continuación, eche un vistazo en el proceso de una extensión de la aplicación de mensaje pase a ser desactivado:

[![](advanced-message-app-extensions-images/interactive05.png "El proceso de una extensión de la aplicación de mensaje pase a ser desactivado")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Cuando se está desactivando la extensión de la aplicación de mensaje, el `ViewWillDisappear` se llama primero al método.
2. La `ViewDidDisappear` se llama al método.
3. El `WillResignActive` método se llama y se pasa un `MSConversation` que representa la conversación que se ejecuta la extensión de la aplicación de mensaje. En este momento, la conexión entre la aplicación de mensajes y la extensión está a punto de liberarse.
4. En algún momento posterior, se finaliza el proceso con la aplicación de mensajes.

Puesto que una extensión es un proceso de duración breve, se termina de forma agresiva por el sistema para ahorrar energía de la batería y procesamiento. El desarrollador debe tener esto en cuenta al diseñar e implementar una extensión de la aplicación de mensaje.

## <a name="composing-a-message"></a>Redactar un mensaje

Una vez que la extensión de la aplicación de mensaje se está ejecutando en un proceso y se muestra la interfaz de usuario, el código siguiente se puede utilizar para crear un nuevo mensaje:

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

Este código crea un nuevo `MSMessage` y establece varias propiedades (como `Url`). Mientras el mensaje solamente puede crearse en iOS, pueden enviarse a iOS y Mac OS que se mostrará.

Si el usuario hace clic en la burbuja de mensaje en la conversación en macOS, Mac intentará abrir la dirección especificada en la dirección URL en el explorador web. Como resultado, el sitio Web del desarrollador debería poder mostrar alguna representación del mensaje en el explorador web en macOS basadas en máquinas.

El `AccessibilityLabel` propiedad la usan los lectores de pantalla para leer la transcripción de la conversación para el usuario. El `Layout` propiedad especifica cómo el mensaje se mostrará, actualmente solo el `MSMessageTemplateLayout` se admite y es similar al siguiente:

[![](advanced-message-app-extensions-images/interactive06.png "La plantilla MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

El `Image` propiedad de la `MSMessageTemplateLayout` proporciona contenido para el cuerpo principal de la MessageBubble en pantalla. El `MediaFileUrl` propiedad también proporciona el contenido para el cuerpo de la burbuja de mensaje, pero permite para el contenido que no es compatible con `UIImage` (por ejemplo, un archivo de vídeo que haría un bucle en segundo plano). Si tanto la `Image` y `MediaFileUrl` se proporcionan propiedades, la `Image` propiedad tendrá prioridad. El `MediaFileUrl` es compatible con la PNG, JPEG, GIF y vídeo (en cualquier formato que puede reproducirse en el marco de Reproductor) formatos multimedia.

El tamaño del medio recomendado es 300 x 300 píxeles con la resolución de 3 x. También se aceptan activos ligeramente más grandes y más pequeños y Apple sugiere pruebas con unos tamaños diferentes para obtener los mejores resultados. La aplicación de mensaje se muestra abajo y escalar este medio según sea necesario.

Cuando los activos se envían al receptor, todos los medios adjuntados será automáticamente transcodifica por la aplicación de mensajes para optimizar de transferencia a través de las redes. Por este motivo, Apple no recomienda: incluido el texto en el medio que se adjunta al mensaje porque media se reducida y se comprimen para la transmisión, lo que podría representar el texto ilegible.

El `ImageTitle` y `ImageSubtitle` propiedades proporcionan una descripción para los medios que se presentan en la burbuja de mensaje. Estas propiedades se enviará como texto en el dispositivo receptor donde representarán con nitidez en la esquina inferior izquierda de la imagen.

El `Caption`, `SubCaption`, `TrailingCaption` y `TrailingSubcaption` propiedades más describen la imagen y se representará en una sección debajo de la imagen. La configuración de todas estas propiedades para `null` creará una burbuja de mensaje sin el área de título:

[![](advanced-message-app-extensions-images/interactive07.png "Una burbuja de mensaje sin el área de título")](advanced-message-app-extensions-images/interactive07.png#lightbox)

La última cosa que tener en cuenta es que la aplicación de mensajes dibujará el icono de la extensión de aplicación de mensaje en la esquina superior izquierda de la burbuja de mensaje.

## <a name="sending-a-message"></a>Enviar un mensaje

Una vez un `MSMessage` ha compuesto, el código siguiente puede utilizarse para enviar:

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

El `ActiveConversation` propiedad de la `MSMessagesAppViewController` contendrá la conversación actual que se inició la extensión de la aplicación de mensaje en.

Llame a la `InsertMessage` de la `MSConversation` para incluir el mensaje en la conversación y controlar los errores que pueden surgir. Si el mensaje se incluye correctamente, la burbuja de mensaje se mostrará en el campo de entrada.

Además, la extensión puede enviar distintos tipos de datos a la conversación como:

- **Text** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Datos adjuntos** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Adhesivos**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});` donde `sticker` es una `MSSticker`.

Una vez que el nuevo contenido está en el campo de entrada, el usuario es capaz de enviar el mensaje, puntee en el color azul **enviar** botón (como si fueran cualquier mensaje habitual). No hay ninguna manera de que la extensión de la aplicación de mensaje enviar automáticamente el contenido, este proceso es totalmente bajo el control del usuario.

## <a name="handling-the-compact-and-expanded-modes"></a>Control de los modos compacto y expandidos

Una extensión de la aplicación de mensaje puede mostrarse en uno de dos modos de vista diferentes:

[![](advanced-message-app-extensions-images/interactive08.png "Una extensión de la aplicación de mensaje muestran en dos modos de vista diferentes: compacto y expandido")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -éste es el modo predeterminado donde la extensión de la aplicación de mensaje ocupa el 25% de parte inferior de la vista de mensajes. En el modo compacto, la aplicación no tiene acceso al teclado, desplazamiento horizontal o identificadores de gesto de deslizar rápidamente. La aplicación tiene acceso al campo de entrada y llama a `InsertMessage` al instante se mostrará al usuario no existe.
- **Expandir** -la extensión de la aplicación de mensaje llena toda la vista de mensajes. No tiene acceso al campo de entrada, pero tienen acceso al teclado, desplazamiento horizontal e identificadores de gesto de deslizar rápidamente.

Una extensión de la aplicación de mensaje se puede cambiar entre estos modos mediante programación o manualmente por el usuario en cualquier momento y debe ser instantáneamente respondiendo a ninguna vista cambia el modo.

Eche un vistazo en el siguiente ejemplo de controlar el cambio entre los dos modos de vista diferentes. Dos controladores diferentes de la vista será necesarios para cada estado. El `StickerBrowserViewController` controla la **Compact** vista y el `AddStickerViewController` controlará la **expandida** vista:

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
}
```

El `DidTransition` método se invalida para controlar el cambio entre los dos modos:

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
```

Opcionalmente, la aplicación podría haber utilizado el `WillTransition` método para controlar el cambio de modo de vista antes de que se presenta al usuario (como se hace en el ejemplo de generador de Icecream anterior). Para obtener más información, vea nuestra [mayor personalización de etiqueta](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) documentación.

## <a name="replying-to-a-message"></a>Responder a un mensaje

Hay dos casos que una extensión de la aplicación de mensaje deberá tratar al responder a un mensaje:

[![](advanced-message-app-extensions-images/interactive09.png "La extensión de la aplicación de mensaje en los modos de inactivo y se activa")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **Extensión está inactivo** -hay una de las burbujas de mensajes de la extensión de aplicación de mensaje en la transcripción de mensaje que el usuario puede pulsar para activar las extensiones y continúe la conversación interactiva.
- **Extensión está activo** -el usuario puede pulsar la burbuja de mensaje de la extensión de aplicación de mensaje en la transcripción de mensaje para entrar en el modo de vista expandida y continuar el proceso interactivo desde donde lo dejó.

### <a name="the-extension-is-inactive"></a>La extensión está inactivo

Cuando una burbuja de mensaje que se derivan por el usuario en la transcripción del mensaje y la extensión de la aplicación de mensaje está inactiva, se producirá el siguiente proceso:

[![](advanced-message-app-extensions-images/interactive10.png "Control de una burbuja de mensaje inactiva")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. El usuario puntea burbujas de mensaje de la extensión.
2. Cuando se inicia una extensión, la aplicación de mensaje iniciará un proceso.
3. El `DidBecomeActive` método se llama y se pasa un `MSConversation` que representa la conversación que se ejecuta la extensión de la aplicación de mensaje.
4. Dado que la extensión se basa en un `UIViewController` ambos `ViewWillAppear` y `ViewDidAppear` se denominan.

Una vez completado el proceso, se presentará la extensión de la aplicación de mensaje en el modo de vista expandida.

### <a name="the-extension-is-active"></a>La extensión está activo

Cuando una burbuja de mensaje que se derivan por el usuario en la transcripción del mensaje y la extensión de la aplicación de mensaje está activa, se producirá el siguiente proceso:

[![](advanced-message-app-extensions-images/interactive11.png "Control de una burbuja de mensaje active")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. El usuario puntea burbujas de mensaje de la extensión.
2. Dado que la extensión de la aplicación de mensaje ya está activa, el `WillTransition` método de la `MSMessagesAppViewController` se llama para controlar el cambio en el proceso de compactación en el modo de vista expandida.
3. El `DidSelectMessage` método de la `MSMessagesAppViewController` se llama y se pasa el `MSMessage` y `MSConversation` que pertenece la burbuja de mensaje.
4. El `DidTransition` método de la `MSMessagesAppViewController` se llama para controlar el cambio en el proceso de compactación en el modo de vista expandida.

Una vez más, una vez completado el proceso, se presentará la extensión de la aplicación de mensaje en el modo de vista expandida.

### <a name="accessing-the-selected-message"></a>Tener acceso al mensaje seleccionado

En cualquier caso, cuando el usuario puntea una burbuja de mensaje que pertenecen a la extensión de la aplicación de mensaje, será necesario obtener acceso a la `MSMessage` que se puntea con el `SelectedMessage` propiedad de la `MSConversation`.

Por ejemplo:

```csharp
using System;
using Foundation;
using Messages;
using UIKit;


namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

El mensaje seleccionado debe mostrarse en la interfaz de usuario de la extensión de aplicación de mensaje y el usuario debería poder crear una respuesta.

## <a name="removing-partially-completed-messages"></a>Quitar parcialmente completado mensajes

En el proceso de envío de los diferentes pasos de una conversación interactiva entre dos del usuario en la conversación, puede iniciar las burbujas de mensaje completada parcialmente llenar la transcripción de mensaje:

[![](advanced-message-app-extensions-images/interactive12.png "Las burbujas de mensaje completada parcialmente puede saturar la transcripción de mensaje")](advanced-message-app-extensions-images/interactive12.png#lightbox)

En su lugar, la extensión de la aplicación de mensaje debe contraer las burbujas de mensaje anterior en un comentario conciso en la transcripción de mensaje:

[![](advanced-message-app-extensions-images/interactive13.png "Contraer las burbujas de mensaje anterior en la transcripción de mensaje")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Esto se controla mediante un `MSSession` para contraer todos los pasos existentes. Por lo que la `DidSelectMessage` método de la `MSMessagesAppViewController` clase podría modificarse para ser similar al siguiente:

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

Si el mensaje seleccionado ya tiene una salida `MSSession`, se usa en otro un nuevo `MSSession` se crea. El `SummaryText` propiedad de la `MSMessage` se utiliza para agregar un título a los pasos anteriores contraídos. Si el `SummaryText` propiedad está establecida en `null`, los pasos anteriores en la conversación se quitará por completo de la transcripción de conversación.

## <a name="advanced-message-api-features"></a>Mensaje API características avanzadas

Con las características básicas de la nueva API de mensaje describe en detalle anterior, junto a examinar algunas de las características más avanzadas que Apple ha incorporado en el marco de trabajo.

En primer lugar, hay varios otros métodos de invalidación de la `MSMessagesAppViewController` clase que proporcionan un acceso más detallado a la conversación:

- `DidStartSendingMessage` -Esto se llama cuando el usuario puntea el botón de envío. Esto no significa que el mensaje ha sido realmente entrega al destinatario, al igual que se ha iniciado el proceso de envío.
- `DidCancelSendingMessage` -Esto sucede cuando el usuario puntea el *X* situado en la esquina superior derecha de la burbuja de mensaje en la transcripción de conversación.
- `DidReceiveMessage` : Este método se llama cuando la extensión de la aplicación de mensaje está activa una recibió un mensaje nuevo de uno de los participantes en la conversación.

### <a name="group-conversations"></a>Conversaciones en grupo

Una extensión de la aplicación de mensaje puede usarse mientras los usuarios implicados en las conversaciones de grupo (con 3 o más personas) y esto debe tenerse en cuenta al diseñar e implementar una extensión de la aplicación de mensaje.

Eche un vistazo a la interacción siguiente en una conversación de grupo con tres usuarios:

[![](advanced-message-app-extensions-images/interactive14.png "Interacción de una conversación de grupo con tres usuarios")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. El usuario 1 envía un mensaje interactivo de grupo preguntando usuario 2 y 3 de usuario para elegir una recarga a fondo hamburguesa.
2. El usuario 2 elige tomatoes.
3. El usuario 3 elige pepinillos.
4. Las opciones de usuario 2 y el usuario 3 llegan al usuario 1 prácticamente al mismo tiempo. Como resultado, la elección del usuario 2 se contrae en una línea de resumen y no está disponible. Este caso podría haber también ha volteado, con la elección del usuario 2 del que se va a contraer que se muestran y usuario 3.

En cualquier caso, este comportamiento es no deseado como 1 usuario debería poder tener acceso a las opciones de usuario 2 y el usuario 3. Para controlar esta situación, Apple sugiere que la extensión de la aplicación de mensajes almacena el estado del mensaje en la nube y utilizar la propiedad dirección URL de la `MSMessage` (que se envía entre los usuarios) para tener acceso a este estado.

Cuando el usuario envía un mensaje, se genera un token de sesión y se inserta en la nube con el estado actual del mensaje. Cuando un usuario puntea en una burbuja de mensaje en la transcripción de conversación, el token de sesión se usa para recuperar el estado de sesión actual de la nube.

### <a name="sender-identifiers"></a>Identificadores de remitente

Para tratar de obtener acceso al identificador del remitente de un mensaje, tome el ejemplo de una conversación de grupo anterior:

[![](advanced-message-app-extensions-images/interactive15.png "Envío de identificadores de conversación de grupo")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Una vez más, 1 usuario envía un grupo de mensajes interactivo preguntando usuario 2 y 3 de usuario para elegir una recarga a fondo hamburguesa.
2. El usuario 3 toma pepinillos.
3. Elección del usuario del 3 llega al usuario 1 y 2 de usuario no ha respondido todavía.
4. Dado que Apple se preocupa la privacidad del usuario, la extensión de la aplicación de mensaje conoce solo un identificador único (como un `NSUUID`) que se asigna a cada participante en la conversación. En el dispositivo local, solo el identificador del usuario actual se conoce.
5. El `MSMessage` tiene un `SenderIdentifier` propiedad que coincide con uno de los usuarios en la lista de participantes conocida a la extensión.
6. Cada dispositivo de usuario tiene su propia copia de la lista de participantes que de nuevo, solo el identificador del usuario local se conoce.
7. Cuando se envía un mensaje, su `SenderIdentifier` propiedad también se conoce como que el usuario local.

Los identificadores de remitente se pueden utilizar de las maneras siguientes:

- Examinando la lista de participante de la extensión puede obtener el número de usuarios en la conversación.
- Cuando la extensión recibe un mensaje de un usuario, puede realizar un seguimiento de identificador del remitente. Si recibe otro mensaje con el mismo identificador de remitente, la extensión sabe que es del mismo usuario.
- Puede utilizar para ayudar a identificar un usuario específico de la conversación.

El identificador de remitente puede usarse en cualquiera de los campos de texto de la `MSMessageTemplateLayout` anteponiendo un signo de dólar (`$`). Por ejemplo:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Cuando la aplicación de mensajes muestra una burbuja de mensaje con este tipo de formato, se reemplazará la `$uuid...` con el nombre de contacto del participante en la conversación que envió el mensaje.

El identificador del remitente es único en cada dispositivo, por lo que buscando en el diagrama anterior una vez más, observe dispositivo del 1 de ese usuario y dispositivo del usuario 3 tiene un identificador de remitente único diferente para cada participante en la conversación.

Los identificadores de remitente se limitan a la instalación de la extensión de la aplicación de mensaje. Por lo que si un usuario desinstala y vuelve a instalar la extensión de la aplicación de mensaje se genera nuevos identificadores de remitente para la instalación de nuevo.

Para obtener acceso a los identificadores de remitente, la extensión puede utilizar el código siguiente:

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>Plataformas compatibles

En las siguientes plataformas de Apple se entregarán los mensajes interactivo generados por una extensión de la aplicación de mensaje:

- watchOS 3
- macOS Sierra
- iOS 10

De las tres plataformas iOS solo 10 permitirá al usuario generar un mensaje interactivo. En macOS Sierra, si el usuario hace clic en una burbuja de mensaje interactivo, la dirección URL adjunta a la `MSMessage` se abrirán en Safari y se debe mostrar una representación del mensaje no existe.

En watchOS, la aplicación de mensajes puede entrega un mensaje interactivo en un dispositivo iOS adjunto donde el usuario puede crear una respuesta.

La nueva API de mensajes tiene compatibilidad con la reserva si no se recibe el mensaje interactivo en plataformas antiguas de Apple:

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

Se entregarán en un formato de reserva como dos mensajes independientes:

- Uno será la imagen proporcionados por el diseño de la plantilla.
- El otro será la dirección URL como se indica en la `MSMessage`.

## <a name="summary"></a>Resumen

En este artículo se ha presentado técnicas avanzadas para trabajar con extensiones de mensajes de aplicación en una solución de Xamarin.iOS que se integra con la **mensajes** aplicación y presente nueva funcionalidad al usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Generador de helado (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Referencia de mensajes](https://developer.apple.com/reference/messages)
- [Guía de programación de la extensión de la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
