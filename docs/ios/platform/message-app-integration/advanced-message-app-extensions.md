---
title: Extensiones de aplicaciones avanzadas de mensajes en Xamarin.iOS
description: Este artículo muestra las técnicas avanzadas para trabajar con las extensiones de aplicación de mensaje en una solución de Xamarin.iOS que se integra con la aplicación de mensajes y presenta la nueva funcionalidad para el usuario.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: baceb59116dd907918b34eca4f44293051190954
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120533"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Extensiones de aplicaciones avanzadas de mensajes en Xamarin.iOS

_Este artículo muestra las técnicas avanzadas para trabajar con las extensiones de aplicación de mensaje en una solución de Xamarin.iOS que se integra con la aplicación de mensajes y presenta la nueva funcionalidad para el usuario._


Nuevo en iOS 10, una extensión de mensaje de la aplicación se integra con la **mensajes** funcionalidad nueva aplicación y presenta al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos.

## <a name="about-message-app-extensions"></a>Acerca de las extensiones de aplicación de mensaje

Como se indicó anteriormente, una extensión de mensaje de la aplicación se integra con la **mensajes** funcionalidad nueva aplicación y presenta al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos. Existen dos tipos de mensaje de extensión de la aplicación:

- **Módulos de adhesivo** -contiene una colección de etiquetas que el usuario puede agregar a un mensaje. Módulos de etiqueta se pueden crear sin escribir ningún código.
- **Aplicación iMessage** -puede presentar una interfaz de usuario personalizada en la aplicación mensajes para seleccionar adhesivos, escribir texto, incluidos los archivos de medios (con las conversiones de tipo opcional) y crear, editar y enviar mensajes de interacción.

Extensiones de aplicaciones de mensajes proporcionan tres tipos de contenido principales:

- **Los mensajes interactivos** -son un tipo de contenido del mensaje personalizado que genera una aplicación, cuando el usuario puntea en el mensaje, la aplicación se iniciará en primer plano.
- **Adhesivos** -son imágenes generadas por la aplicación que puede incluirse en los mensajes enviados entre los usuarios. Consulte nuestra [helado generador](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) aplicación de ejemplo para una implementación de ejemplo de una aplicación de módulo adhesivo.
- **Otro tipo de contenido admite** -la aplicación puede proporcionar contenido, como fotos, vídeos, texto o vínculos del tipo que siempre ha sido compatible con la aplicación mensajes.

Nuevo en iOS 10, la aplicación de mensajes ahora incluye su propio Store App dedicado e integradas. Las aplicaciones que incluyen extensiones de aplicaciones de mensajes se muestran y se promueve en este almacén. El cajón de aplicaciones de los mensajes nuevos se mostrarán todas las aplicaciones que se han descargado desde el Store de la aplicación de mensajes para proporcionar acceso rápido a los usuarios.

También nuevo en iOS 10, Apple ha agregado Inline App Attribution que permite al usuario a detectar fácilmente una aplicación. Por ejemplo, si un usuario envía el contenido a otro desde una aplicación que el usuario 2 no tiene instalado (por ejemplo, una etiqueta por ejemplo), el nombre de la aplicación envío aparece en el contenido en el historial de mensajes. Si el usuario pulsa la aplicación el nombre, el Store de la aplicación de mensaje se puede abrir y la aplicación seleccionada en el almacén.

Extensiones de mensajes de aplicaciones son similares a las aplicaciones iOS existentes que el desarrollador está familiarizado para crear y tendrán acceso a todas las características de una aplicación de iOS estándar y marcos de trabajo estándares. Por ejemplo:

- Tiene acceso a la compra en la aplicación.
- Tiene acceso a Apple Pay.
- Tiene acceso a hardware, como la cámara del dispositivo.

Solo se admiten extensiones de mensajes de aplicaciones en iOS 10, sin embargo, el contenido que enviar estas extensiones pueden verse en dispositivos para watchOS y macOS. El nuevo _Recents Page_ agregado a watchOS 3, mostrará los adhesivos recientes que se han enviado desde el teléfono, los de las extensiones de aplicaciones de mensaje, incluidos y permitir al usuario enviar esos adhesivos de la inspección.

## <a name="about-interactive-messages"></a>Acerca de los mensajes interactivos

Mensajes interactivos presentan una burbuja de mensaje personalizado y se proporcionan mediante una extensión de aplicación de mensaje. Permiten al usuario crear mensaje contenido interactivo, insértelo en el campo de entrada de mensaje y enviarlo.

[![](advanced-message-app-extensions-images/interactive01.png "Creación de contenido de mensaje interactivo")](advanced-message-app-extensions-images/interactive01.png#lightbox)

El usuario receptor puede responder a un mensaje interactivo punteando en sus burbujas de mensaje en el historial de mensajes para cargar la extensión de aplicación de mensajes que lo creó. La extensión se inició de pantalla completa y permitir al usuario redactar una respuesta y devolverlo al usuario de origen.

[![](advanced-message-app-extensions-images/interactive02.png "La extensión inicia la pantalla completa")](advanced-message-app-extensions-images/interactive02.png#lightbox)


Los siguientes temas se tratarán en detalle a continuación:

- Introducción a la API de mensajes
- El ciclo de vida de la extensión
- Redactar un mensaje
- Enviar un mensaje

## <a name="messages-api-overview"></a>Introducción a la API de mensajes

Cuando se invoca por el usuario, una extensión de aplicación de mensaje se mostrará en la parte inferior del historial de mensajes en el modo de vista compacta:

[![](advanced-message-app-extensions-images/interactive03.png "Introducción a la API de mensajes")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. La `MSMessageAppViewController` objeto en la extensión de aplicación de mensajes es la clase principal que se llama cuando se muestre la vista de la extensión para el usuario.
2. La conversación se presenta al usuario como un `MSConversation` instancia del objeto.
3. La `MSMessage` clase representa una burbuja de mensaje determinado en la conversación.
4. `MSSession` controla cómo se envía un mensaje.
5. `MSMessageTemplateLayout` controla cómo se muestra el mensaje

## <a name="the-extension-lifecycle"></a>El ciclo de vida de la extensión

Eche un vistazo al proceso de una extensión de aplicación de mensaje de activación:

[![](advanced-message-app-extensions-images/interactive04.png "El proceso de una extensión de aplicación de mensaje de activación")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. Cuando se inicia una extensión (por ejemplo, desde el cajón de aplicaciones), la aplicación de mensaje iniciará un proceso.
2. El `DidBecomeActive` método se llama y se pasa un `MSConversation` que representa la conversación que se ejecuta la extensión de aplicación de mensajes en.
3. Dado que la extensión está basada fuera de un `UIViewController` ambos `ViewWillAppear` y `ViewDidAppear` se denominan.

A continuación, eche un vistazo al proceso de convertirse en desactiva una extensión de aplicación de mensaje:

[![](advanced-message-app-extensions-images/interactive05.png "El proceso de una extensión de aplicación de mensaje cada vez desactivado")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. Cuando se desactiva la extensión de aplicación de mensajes, el `ViewWillDisappear` primero se llamará al método.
2. El `ViewDidDisappear` se llamará al método.
3. El `WillResignActive` método se llama y se pasa un `MSConversation` que representa la conversación que se ejecuta la extensión de aplicación de mensajes en. En este momento, la conexión entre la aplicación de mensajes y la extensión está a punto de liberarse.
4. En algún momento posterior, el proceso termina con la aplicación mensajes.

Puesto que una extensión es un proceso de corta duración, se termina de forma agresiva por el sistema para conservar la energía de procesamiento y la batería. El desarrollador debe Téngalo en cuenta al diseñar e implementar una extensión de aplicación de mensaje.

## <a name="composing-a-message"></a>Redactar un mensaje

Una vez que la extensión de mensajes de aplicación se ejecuta en un proceso y se muestra la interfaz de usuario, el código siguiente puede utilizarse para redactar un mensaje nuevo:

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

Este código crea un nuevo `MSMessage` y establece varias propiedades (como `Url`). Aunque el mensaje solo se puede crear en iOS, pueden enviarse a iOS y macOS para mostrarse.

Si el usuario hace clic en el globo de mensaje en la conversación en macOS, el equipo Mac intentará abrir la dirección especificada en la dirección URL en el explorador web. Como resultado, el sitio Web del desarrollador debería poder mostrar equipos basados en alguna representación del mensaje en el explorador web en macOS.

El `AccessibilityLabel` propiedad se usa por los lectores de pantalla para leer la transcripción de la conversación para el usuario. El `Layout` propiedad especifica cómo el mensaje se mostrará, actualmente solo la `MSMessageTemplateLayout` es compatible y es similar al siguiente:

[![](advanced-message-app-extensions-images/interactive06.png "La plantilla MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

El `Image` propiedad de la `MSMessageTemplateLayout` proporciona contenido para el cuerpo principal de la MessageBubble en pantalla. El `MediaFileUrl` propiedad también proporciona el contenido del cuerpo de la burbuja de mensaje, pero permite para el contenido que no es compatible con `UIImage` (por ejemplo, un archivo de vídeo que haría un bucle en segundo plano). Si tanto el `Image` y `MediaFileUrl` se proporcionan propiedades, la `Image` propiedad tendrá prioridad. El `MediaFileUrl` admite el PNG, JPEG, GIF y vídeo (en cualquier formato que se puede reproducir el marco de trabajo de Media Player) formatos multimedia.

El tamaño medio recomendado es 300 x 300 píxeles en la resolución de 3 x. También se aceptan activos ligeramente más grandes y más pequeños y Apple sugiere las pruebas con unos tamaños diferentes para obtener los mejores resultados. La aplicación de mensaje se reducen y escalar este medio según sea necesario.

Cuando los recursos se envían al receptor, cualquier elemento multimedia adjunto será automáticamente transcodificados por la aplicación mensajes para la optimización de la transferencia a través de las redes. Por este motivo, Apple desaconseja incluido texto en el medio adjunto al mensaje porque se reducida y comprimir para la transmisión, media, por tanto, podría representar el texto ilegible.

El `ImageTitle` y `ImageSubtitle` propiedades proporcionan una descripción para el medio que se presenta en la burbuja de mensaje. Estas propiedades se enviarán como texto para el dispositivo receptor donde representará cualidad en la esquina inferior izquierda de la imagen.

El `Caption`, `SubCaption`, `TrailingCaption` y `TrailingSubcaption` propiedades describen la imagen más y se representará en una sección debajo de la imagen. Configuración de todas estas propiedades para `null` creará una burbuja de mensaje sin el área de título:

[![](advanced-message-app-extensions-images/interactive07.png "Una burbuja de mensaje sin el área de título")](advanced-message-app-extensions-images/interactive07.png#lightbox)

Lo último que tener en cuenta es que la aplicación mensajes dibujará el icono de la extensión de aplicación de mensaje en la esquina superior izquierda de la burbuja de mensaje.

## <a name="sending-a-message"></a>Enviar un mensaje

Una vez un `MSMessage` ha sido compuesta, el código siguiente puede utilizarse para enviar:

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

El `ActiveConversation` propiedad de la `MSMessagesAppViewController` contendrá la conversación actual que se lanzó la extensión de aplicación de mensajes.

Llame a la `InsertMessage` de la `MSConversation` para incluir el mensaje en la conversación y controlar los errores que pueden surgir. Si el mensaje se incluye correctamente, se mostrará el globo de mensaje en el campo de entrada.

Además, la extensión puede enviar distintos tipos de datos a la conversación, como:

- **Texto** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Datos adjuntos** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Adhesivos**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});` donde `sticker` es un `MSSticker`.

Una vez que el nuevo contenido en el campo de entrada, el usuario es capaz de enviar el mensaje, puntee en azul **enviar** botón (como si fueran cualquier mensaje típico). No hay ninguna manera de que la extensión de aplicación de mensajes enviar automáticamente el contenido, este proceso es completamente bajo el control del usuario.

## <a name="handling-the-compact-and-expanded-modes"></a>Control de los modos compacto y expandidos

Una extensión de aplicación de mensaje puede mostrarse en uno de dos modos de vista diferentes:

[![](advanced-message-app-extensions-images/interactive08.png "Una extensión de aplicación de mensaje que aparece en dos modos de vista diferentes: compacto y expandido")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -éste es el modo predeterminado donde la extensión de aplicación de mensajes ocupa el parte inferior del 25% de la vista de mensaje. En el modo compacto, la aplicación no tiene acceso al teclado, desplazamiento horizontal o los reconocedores de gestos de pasar el dedo. La aplicación tiene acceso al campo de entrada y las llamadas a `InsertMessage` al instante se mostrará al usuario no existe.
- **Expande** : la extensión de mensaje de la aplicación rellena toda la vista de mensaje. No tiene acceso al campo de entrada, pero tiene acceso al teclado, desplazamiento horizontal y los reconocedores de gestos de pasar el dedo.

Extensión de la aplicación de un mensaje se puede cambiar entre estos modos mediante programación o manualmente por el usuario en cualquier momento y debe ser al instante con capacidad de respuesta a cualquiera de la vista cambia el modo.

Eche un vistazo en el siguiente ejemplo de controlar el cambio entre los dos modos de vista diferente. Dos controladores de vista diferentes será necesarios para cada estado. El `StickerBrowserViewController` controla la **Compact** vista y el `AddStickerViewController` controlará la **expandido** vista:

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

El `DidTransition` método se invalida para atender el cambio entre los dos modos:

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

Si lo desea, podría haber usado la aplicación la `WillTransition` método para controlar el cambio de modo de vista antes de que se presenta al usuario (como se hace en el ejemplo del generador de helado anterior). Para obtener más información, consulte nuestra [más personalizaciones adhesivo](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) documentación.

## <a name="replying-to-a-message"></a>Responder a un mensaje

Hay dos casos que una extensión de mensaje de la aplicación deberá controlar al responder a un mensaje:

[![](advanced-message-app-extensions-images/interactive09.png "La extensión de aplicación de mensajes en los modos activos e inactivos")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **Extensión es inactivo** -hay una de las burbujas de mensajes de la extensión de aplicación de mensaje en la transcripción del mensaje que el usuario puede pulsar para activar las extensiones y continuar la conversación interactiva.
- **Extensión está activo** -el usuario puede pulsar la burbuja de mensaje de la extensión aplicación de mensajes en la transcripción del mensaje para especificar el modo de vista expandida y continuar el proceso interactivo desde donde lo dejó.

### <a name="the-extension-is-inactive"></a>La extensión está inactivo

Cuando se pulsa una burbuja de mensaje por el usuario en la transcripción del mensaje y la extensión de aplicación de mensajes está inactiva, se producirá el siguiente proceso:

[![](advanced-message-app-extensions-images/interactive10.png "Controlar una burbuja de mensaje inactiva")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. El usuario pulsa burbujas de mensaje de la extensión.
2. Cuando se inicia una extensión, la aplicación de mensaje iniciará un proceso.
3. El `DidBecomeActive` método se llama y se pasa un `MSConversation` que representa la conversación que se ejecuta la extensión de aplicación de mensajes en.
4. Dado que la extensión está basada fuera de un `UIViewController` ambos `ViewWillAppear` y `ViewDidAppear` se denominan.

Una vez completado el proceso, la extensión de la aplicación de mensaje aparecerá en el modo de vista expandida.

### <a name="the-extension-is-active"></a>La extensión está activo

Cuando se pulsa una burbuja de mensaje por el usuario en la transcripción del mensaje y la extensión de aplicación de mensajes está activa, se producirá el siguiente proceso:

[![](advanced-message-app-extensions-images/interactive11.png "Un activo burbujas de mensaje de control")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. El usuario pulsa burbujas de mensaje de la extensión.
2. Dado que la extensión de mensajes de aplicación ya está activa, el `WillTransition` método del `MSMessagesAppViewController` se llama para controlar el cambio de la compacto para el modo de vista expandida.
3. El `DidSelectMessage` método de la `MSMessagesAppViewController` y pase el `MSMessage` y `MSConversation` que pertenece la burbuja de mensaje.
4. El `DidTransition` método de la `MSMessagesAppViewController` se llama para controlar el cambio de la compacto para el modo de vista expandida.

Nuevamente, una vez completado el proceso, la extensión de la aplicación de mensaje aparecerá en el modo de vista expandida.

### <a name="accessing-the-selected-message"></a>Acceder al mensaje seleccionado

En cualquier caso, cuando el usuario pulsa una burbuja de mensajes que pertenecen a la extensión de la aplicación de mensaje, será necesario tener acceso a la `MSMessage` que se ha punteado con el `SelectedMessage` propiedad de la `MSConversation`.

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

Debe mostrarse el mensaje seleccionado en la interfaz de usuario de la extensión de aplicación de mensaje y el usuario debería poder redactar una respuesta.

## <a name="removing-partially-completed-messages"></a>Quitar parcialmente completado mensajes

En el proceso de envío de los diferentes pasos de una conversación interactiva entre dos del usuario en la conversación, puede iniciar las burbujas de mensaje parcialmente completada provocar un desorden en la transcripción del mensaje:

[![](advanced-message-app-extensions-images/interactive12.png "Las burbujas de mensaje completada parcialmente puede saturar la transcripción del mensaje")](advanced-message-app-extensions-images/interactive12.png#lightbox)

En su lugar, la extensión de aplicación de mensajes debe contraer las burbujas de mensaje anterior en un breve comentario en la transcripción del mensaje:

[![](advanced-message-app-extensions-images/interactive13.png "Contraer las burbujas de mensaje anterior en la transcripción del mensaje")](advanced-message-app-extensions-images/interactive13.png#lightbox)

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

Si el mensaje seleccionado ya tiene una salida `MSSession`, se usa en otro un nuevo `MSSession` se crea. El `SummaryText` propiedad de la `MSMessage` se usa para agregar un título a los pasos anteriores contraídos. Si el `SummaryText` propiedad está establecida en `null`, los pasos anteriores en la conversación se quitará por completo de la transcripción de la conversación.

## <a name="advanced-message-api-features"></a>Mensaje API características avanzadas

Con las características básicas de la nueva API de mensaje que se tratan detalladamente anterior, a continuación a examinar algunas de las características más avanzadas que Apple ha incorporado en el marco de trabajo.

En primer lugar, hay muchos otros métodos de invalidación en el `MSMessagesAppViewController` clases que proporcionan un mayor acceso a la conversación:

- `DidStartSendingMessage` -Se llama cuando el usuario pulsa el botón de envío. Esto no significa que el mensaje ha estado realmente entrega al destinatario, simplemente que se ha iniciado el proceso de envío.
- `DidCancelSendingMessage` -Esto sucede cuando el usuario pulsa el *X* situado en la esquina superior derecha de la burbuja de mensaje en la transcripción de la conversación.
- `DidReceiveMessage` : Este método se llama cuando la extensión de aplicación de mensajes está activa un recibió un mensaje nuevo de uno de los participantes en la conversación.

### <a name="group-conversations"></a>Conversaciones en grupo

Una extensión de aplicación de mensaje puede usarse mientras los usuarios están implicados en las conversaciones de grupo (con individuos 3 o más) y esto debe tenerse en cuenta al diseñar e implementar una extensión de aplicación de mensaje.

Eche un vistazo a la interacción siguiente en una conversación de grupo con tres usuarios:

[![](advanced-message-app-extensions-images/interactive14.png "Interacción de una conversación de grupo con tres usuarios")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. El usuario 1 envía un mensaje interactivo grupo preguntando usuario 2 y 3 de usuario para elegir un acompañamiento hamburguesa.
2. El usuario 2 elige tomates.
3. El usuario 3 elige pepinillos.
4. Opciones del usuario 2 y de 3 de usuario llegan a 1 usuario casi al mismo tiempo. Como resultado, la elección del usuario 2 se contrae en una línea de resumen y no está disponible. En este caso podría haber también ha volteado, con la elección del usuario 2 se muestra y el usuario 3 del que se va a contraído.

En cualquier caso, este comportamiento es no deseado como 1 usuario debería poder tener acceso a las opciones del usuario 2 y del usuario 3. Para controlar esta situación, Apple sugiere que la extensión de aplicación de mensajes almacena el estado del mensaje en la nube y use la propiedad de dirección URL de la `MSMessage` (que se envía entre los usuarios) para tener acceso a este estado.

Cuando el usuario envía un mensaje, un token de sesión se genera y se insertan en la nube con el estado actual del mensaje. Cuando un usuario pulsa una burbuja de mensaje en la transcripción de conversación, el token de sesión se usa para recuperar el estado de sesión actual de la nube.

### <a name="sender-identifiers"></a>Identificadores de remitente

Para tratar de obtener acceso al identificador del remitente de un mensaje, tome como ejemplo de una conversación de grupo proporcionado anteriormente:

[![](advanced-message-app-extensions-images/interactive15.png "Envío de los identificadores de conversación de grupo")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Nuevamente, 1 usuario envía un mensaje interactivo grupo preguntando usuario 2 y 3 de usuario para elegir un acompañamiento hamburguesa.
2. El usuario 3 elige pepinillos.
3. Elección del usuario de 3 llega al usuario 1 y 2 de usuario no ha respondido aún.
4. Dado que Apple está muy relacionado con la privacidad del usuario, la extensión de aplicación de mensajes solo sabe sobre un identificador único (como un `NSUUID`) que se asigna a cada participante en la conversación. En el dispositivo local, se conoce solo el identificador del usuario actual.
5. El `MSMessage` tiene un `SenderIdentifier` propiedad que coincide con uno de los usuarios en la lista de participantes conocida a la extensión.
6. Cada dispositivo de usuario tiene su propia copia de la lista de participantes que de nuevo, solo el identificador del usuario local se conoce.
7. Cuando se envía un mensaje, sus `SenderIdentifier` propiedad también se conoce como del usuario local.

Los identificadores de remitente se puede utilizar de las maneras siguientes:

- Examinando la lista de participantes la extensión puede obtener el número de usuarios en la conversación.
- Cuando la extensión recibe un mensaje de un usuario, puede realizar un seguimiento del identificador del remitente. Si recibe otro mensaje con el mismo identificador de remitente, la extensión sabe que es del mismo usuario.
- Se puede usar para ayudar a identificar un usuario específico de la conversación.

El identificador de remitente puede usarse en cualquiera de los campos de texto de la `MSMessageTemplateLayout` anteponiendo un signo de dólar (`$`). Por ejemplo:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Cuando la aplicación de mensajes muestra una burbuja de mensaje con este tipo de formato, reemplazará el `$uuid...` con el nombre de contacto del participante en la conversación que envió el mensaje.

El identificador del remitente es único en cada dispositivo, por lo que examinamos el diagrama anterior de nuevo, tenga en cuenta dispositivos del 1 de ese usuario y dispositivo del usuario 3 tienen un identificador de remitente único diferente para cada participante en la conversación.

Los identificadores de remitente se limitan a la instalación de la extensión de aplicación de mensajes. Por lo que si un usuario desinstala y vuelve a instalar la extensión de aplicación de mensajes nuevos identificadores de remitente se generará para la instalación nuevo.

Para obtener acceso a los identificadores de remitente, la extensión puede usar el código siguiente:

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

Los mensajes interactivos generados por una extensión de aplicación de mensajes se enviarán en las siguientes plataformas de Apple:

- watchOS 3
- macOS Sierra
- iOS 10

De las tres plataformas, iOS solo 10 permitirá al usuario generar un mensaje interactivo. En macOS Sierra, si el usuario hace clic en una burbuja de mensaje interactivo, la dirección URL adjunta a la `MSMessage` en Safari se abrirá y se debe mostrar una representación del mensaje no existe.

En watchOS, la aplicación de mensajes puede entrega un mensaje interactivo en un dispositivo iOS adjunto donde el usuario puede redactar una respuesta.

La nueva API de mensajes tiene soporte técnico para la reserva si se recibe el mensaje interactivo en plataformas de Apple anteriores:

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

Se entregarán en un formato de reserva como dos mensajes diferentes:

- Uno será la imagen proporcionada por el diseño de la plantilla.
- El otro será la dirección URL que se proporciona en el `MSMessage`.

## <a name="summary"></a>Resumen

En este artículo se presenta técnicas avanzadas para trabajar con las extensiones de aplicación de mensaje en una solución de Xamarin.iOS que se integra con la **mensajes** aplicación y la funcionalidad nueva presente al usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Generador de helado (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Referencia de mensajes](https://developer.apple.com/reference/messages)
- [Guía de programación de la extensión de la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
