---
title: Conceptos básicos del mensaje extensión de la aplicación de Xamarin.iOS
description: En este artículo se muestra cómo incluir una extensión de aplicación de mensaje en una solución de Xamarin.iOS que se integra con la aplicación de mensajes y presenta la nueva funcionalidad para el usuario.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7bd4a87843852e940da96f688371ddbecbf7e0b4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105784"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Conceptos básicos del mensaje extensión de la aplicación de Xamarin.iOS

_En este artículo se muestra cómo incluir una extensión de aplicación de mensaje en una solución de Xamarin.iOS que se integra con la aplicación de mensajes y presenta la nueva funcionalidad para el usuario._

Nuevo en iOS 10, una extensión de mensaje de la aplicación se integra con la **mensajes** funcionalidad nueva aplicación y presenta al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos.

## <a name="about-message-app-extensions"></a>Acerca de las extensiones de aplicación de mensaje

Como se indicó anteriormente, una extensión de mensaje de la aplicación se integra con la **mensajes** funcionalidad nueva aplicación y presenta al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos. Existen dos tipos de mensaje de extensión de la aplicación:

- **Módulos de adhesivo** -contiene una colección de etiquetas que el usuario puede agregar a un mensaje. Módulos de etiqueta se pueden crear sin escribir ningún código.
- **Aplicación iMessage** -puede presentar una interfaz de usuario personalizada en la aplicación mensajes para seleccionar adhesivos, escribir texto, incluidos los archivos de medios (con las conversiones de tipo opcional) y crear, editar y enviar mensajes de interacción.

Extensiones de aplicaciones de mensajes proporcionan tres tipos de contenido principales:

- **Los mensajes interactivos** -son un tipo de contenido del mensaje personalizado que genera una aplicación, cuando el usuario puntea en el mensaje, la aplicación se iniciará en primer plano.
- **Adhesivos** -son imágenes generadas por la aplicación que puede incluirse en los mensajes enviados entre los usuarios.
- **Otro tipo de contenido admite** : la aplicación puede proporcionar contenido, como fotos, vídeos, texto o tipos de vínculos a cualquier otro contenido que siempre han admitido por la aplicación mensajes.

Nuevo en iOS 10, la aplicación de mensajes ahora incluye su propio Store App dedicado e integradas. Las aplicaciones que incluyen extensiones de aplicaciones de mensajes se muestran y se promueve en este almacén. El cajón de aplicaciones de los mensajes nuevos se mostrarán todas las aplicaciones que se han descargado desde el Store de la aplicación de mensajes para proporcionar acceso rápido a los usuarios.

También nuevo en iOS 10, Apple ha agregado Inline App Attribution que permite al usuario a detectar fácilmente una aplicación. Por ejemplo, si un usuario envía el contenido a otro desde una aplicación que el usuario 2 no tiene instalado (por ejemplo, una etiqueta por ejemplo), el nombre de la aplicación envío aparece en el contenido en el historial de mensajes. Si el usuario pulsa la aplicación el nombre, el Store de la aplicación de mensaje se puede abrir y la aplicación seleccionada en el almacén.

Extensiones de mensajes de aplicaciones son similares a las aplicaciones iOS existentes que el desarrollador está familiarizado con la creación y tendrán acceso a todas las características de una aplicación de iOS estándar y marcos de trabajo estándares. Por ejemplo:

- Tiene acceso a la compra en la aplicación.
- Tiene acceso a Apple Pay.
- Tiene acceso a hardware, como la cámara del dispositivo.

Solo se admiten extensiones de mensajes de aplicaciones en iOS 10, sin embargo, el contenido que enviar estas extensiones pueden verse en dispositivos para watchOS y macOS. El nuevo _Recents Page_ agregado a watchOS 3, mostrará los adhesivos recientes que se han enviado desde el teléfono, los de las extensiones de aplicaciones de mensaje, incluidos y permitir al usuario enviar esos adhesivos de la inspección.

## <a name="about-the-messages-framework"></a>Acerca del marco de mensajes

Nuevo en iOS 10, el marco de trabajo de mensajes proporciona la interfaz entre la extensión de aplicaciones de mensajes y la aplicación de mensaje en el dispositivo del usuario de iOS. Cuando el usuario inicia una aplicación desde dentro de la aplicación de mensajes, este marco de trabajo permite que la aplicación para que se detecten y proporciona el contexto necesario para diseñar su interfaz de usuario y los datos.

Una vez que se inicia la aplicación, el usuario interactúa con él para crear nuevo contenido para compartir a través de un mensaje. La aplicación, a continuación, usa el marco de trabajo de los mensajes para transferir el contenido recién creado a la aplicación de mensajes para su procesamiento.

El marco de mensajes y extensiones de mensajes de las aplicaciones se basan en tecnologías de extensiones de aplicación de iOS ya existentes. Para obtener más información acerca de las extensiones de aplicación, consulte Apple [Guía de programación de extensión de aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

A diferencia de otros puntos de extensión que Apple ha proporcionado en todo el sistema, el desarrollador no necesita proporcionar una aplicación host para las extensiones de aplicaciones de mensaje dado que la propia aplicación de mensajes actúa como contenedor. Sin embargo, el desarrollador tiene la opción de incluir la extensión de aplicaciones de mensajes dentro de una aplicación iOS nueva o existente y trasvase de registros junto con la agrupación.

Si las extensiones de aplicaciones de mensaje se incluye en el paquete de aplicación de iOS, se mostrará el icono de la aplicación en la pantalla principal del dispositivo y en el cajón de aplicaciones de mensaje de la aplicación mensajes. Si no se incluye en un lote de aplicaciones, la extensión de aplicaciones de mensajes solo se mostrará en el cajón de aplicaciones de mensaje.

Incluso si las extensiones de aplicaciones de mensaje no se incluye en un lote de aplicaciones de host, el desarrollador tendrá proporcionar un icono de la aplicación en paquete de la extensión de aplicaciones de mensaje, ya que es el icono que se mostrará en otras partes del sistema como la configuración o el cajón de aplicaciones de mensaje , para la extensión.

## <a name="about-stickers"></a>Acerca de adhesivos

Apple diseñado adhesivos como una nueva forma para que los usuarios iMessage comunicarse permitiendo pegatinas para enviarse insertadas como cualquier otro contenido de mensaje o se puede vincular a las burbujas de mensaje anterior dentro de la conversación.

¿Cuáles son los adhesivos?

- Son imágenes que proporciona la extensión de aplicaciones de mensajes.
- Pueden ser imágenes animadas o estáticas.
- Proporcionan una nueva forma de compartir el contenido de imagen desde dentro de una aplicación.

Hay dos maneras de crear etiquetas:

1. Un adhesivo Pack Message aplicaciones Extensions pueden crearse desde dentro de Xcode sin incluir ningún código. Todo lo necesario es que los recursos para los adhesivos y los iconos de aplicación.
2. Mediante la creación de una extensión de aplicaciones de mensaje estándar que proporciona adhesivos de código mediante el marco de trabajo de los mensajes.

### <a name="creating-sticker-packs"></a>Crear módulos de etiqueta

Módulos de etiqueta se crean a partir de una plantilla especial dentro de Xcode y basta con proporcionan un conjunto estático de activos de imagen que se pueden usar como adhesivos. Como se indicó anteriormente, no requieren ningún código, el desarrollador simplemente arrastra archivos de imagen en la carpeta del módulo de la etiqueta dentro del catálogo de activos adhesivos.

Para que una imagen que se incluirán en un módulo de etiqueta, debe cumplir los siguientes requisitos:

- Las imágenes deben ser en un formato PNG, APNG, GIF o JPEG. Apple sugiere el uso de los formatos PNG y APNG al proporcionar recursos de la etiqueta.
- Adhesivos animados solo admiten los formatos APNG y GIF.
- Imágenes adhesivo deben proporcionar un fondo transparente, ya que puede colocarse a través de las burbujas de mensaje de la conversación por el usuario.
- Los archivos de imagen individuales deben ser inferior a 500kb.
- Las imágenes no pueden ser mayor o menor que 100 x 100 puntos que apunta 206 x 206.

> [!IMPORTANT]
> Las imágenes de la etiqueta siempre deben proporcionarse en el `@3x` resolución en el intervalo de 300 x 300 a 618 x 618 píxeles. El sistema generará automáticamente el `@2x` y `@1x` versiones en tiempo de ejecución según sea necesario.

Apple sugiere las pruebas de los activos de imagen de etiqueta en distintos fondos color diferentes (por ejemplo, en blanco, negro, rojo, amarillo y multicolores) y a través de fotos, para garantizar que se muestran lo mejor en todas las situaciones posibles.

Los módulos de la etiqueta pueden proporcionar adhesivos en uno de tres tamaños disponibles:

- **Pequeño** : 100 x 100 puntos.
- **Medio** : 136 x 136 puntos. Este es el tamaño predeterminado.
- **Grandes** : 206 x 206 puntos.

Usar Inspector de atributos de Xcode para establecer el tamaño de todo el paquete de etiqueta y solo se proporcionan recursos de imagen que coincidan con el tamaño solicitado, para obtener los mejores resultados en el Explorador de la etiqueta dentro de la aplicación de mensajes.

Para obtener más información, consulte nuestra [helado generador](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) app y Apple [mensajes referencia](https://developer.apple.com/reference/messages).

## <a name="creating-a-custom-sticker-experience"></a>Creación de una experiencia de la etiqueta personalizada

Si la aplicación necesita más control o flexibilidad que la proporcionada por un módulo de etiqueta, puede incluir una extensión de mensajes de aplicación y proporcionar los adhesivos mediante el marco de trabajo de los mensajes para una experiencia de la etiqueta personalizada.

¿Cuáles son las ventajas de la creación de una experiencia de etiqueta personalizado?

1. Permite que la aplicación personalizar cómo se muestran las etiquetas a los usuarios de la aplicación. Por ejemplo, para adhesivos presentes en un formato que no sea el diseño de cuadrícula estándar o en un fondo de color diferente.
2. Permite pegatinas para crearse dinámicamente desde el código en lugar de que se incluye como activos de imagen estática.
3. Permite que los activos de imagen de etiqueta descargar dinámicamente de servidor web del desarrollador sin tener que crear una nueva versión a la aplicación de Store.
4. Permite el acceso de la cámara del dispositivo para crear etiquetas sobre la marcha.
5. Permite para compras de la aplicación, por lo que el usuario puede adquirir más adhesivos desde dentro de la aplicación.

Para crear una experiencia de la etiqueta personalizada, realice lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie Visual Studio para Mac.
2. Abra la solución para agregar una extensión de aplicación de mensaje. 
3. Seleccione **iOS** > **extensiones** > **extensión de iMessage** y haga clic en el **siguiente** botón: 

    [![](intro-to-message-app-extensions-images/message01.png "Seleccione la extensión de iMessage")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Escriba un **nombre de la extensión** y haga clic en el **siguiente** botón: 

    [![](intro-to-message-app-extensions-images/message02.png "Escriba un nombre de extensión")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Haga clic en el **crear** para crear la extensión: 

    [![](intro-to-message-app-extensions-images/message03.png "Haga clic en el botón Crear")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie Visual Studio.
2. Abra la solución para agregar una extensión de aplicación de mensaje.
3. Seleccione ** extensiones de iOS > (iOS) de la extensión de iMessage ** y haga clic en el **siguiente** botón:

    [![Seleccione (iOS) de la extensión de iMessage](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Escriba un **nombre** y haga clic en el **Aceptar** botón

-----

De forma predeterminada, el `MessagesViewController.cs` archivo se agregará a la solución. Este es el punto de entrada principal a la extensión y se hereda de la `MSMessageAppViewController` clase.

El marco de trabajo de mensajes proporciona clases para presentar etiquetas disponibles para el usuario:

- `MSStickerBrowserViewController` -Controla la vista que se presentan en los adhesivos. También se ajusta a la `IMSStickerBrowserViewDataSource` interfaz para devolver el número de etiqueta y la etiqueta para un índice determinado de explorador.
- `MSStickerBrowserView` -Esta es la vista que se mostrarán los adhesivos disponibles en.
- `MSStickerSize` -Decide los tamaños de celda individual de la cuadrícula de etiquetas que se presentan en la vista de explorador.

### <a name="creating-a-custom-sticker-browser"></a>Creación de un explorador de la etiqueta personalizada

El desarrollador puede personalizar aún más la experiencia de etiqueta para el usuario proporcionando un explorador de la etiqueta personalizada (`MSMessageAppBrowserViewController`) en la extensión de aplicación de mensajes. El Explorador de la etiqueta personalizada cambia cómo se presentan al usuario los adhesivos cuando seleccionan una etiqueta que se va a incluir en la secuencia de mensajes.

Haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **panel de solución**, haga doble clic en el nombre de la extensión del proyecto y seleccione **agregar** > **nuevo archivo...**   >  **iOS | Apple Watch** > **controlador de interfaz**.
2. Escriba `StickerBrowserViewController` para el **nombre** y haga clic en el **New** botón: 

    [![](intro-to-message-app-extensions-images/browser01.png "Escriba el nombre StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Abra el `StickerBrowserViewController.cs` archivo para editarlo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **el Explorador de soluciones**, haga doble clic en el nombre de la extensión del proyecto y seleccione **agregar** > **nuevo archivo...**   >  **iOS | Apple Watch** > **controlador de interfaz**.
2. Escriba `StickerBrowserViewController` para el **nombre** y haga clic en el **New** botón: 

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Escriba el nombre StickerBrowserViewController")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Abra el `StickerBrowserViewController.cs` archivo para editarlo.

-----

Realizar el `StickerBrowserViewController.cs` el aspecto siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

Eche un vistazo en el código anterior en detalle. Crea el almacenamiento para los adhesivos que proporciona la extensión:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Y reemplaza dos métodos de la `MSStickerBrowserViewController` clase para proporcionar datos para el Explorador de este almacén de datos:

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

El `CreateSticker` método obtiene la ruta de acceso de un recurso de imagen de agrupación de la extensión y lo usa para crear una nueva instancia de un `MSSticker` desde este recurso, lo que agrega a la colección:

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

El `LoadSticker` se llama al método desde `ViewDidLoad` para crear una etiqueta del activo de imagen con nombre (incluida en el lote de la aplicación) y agregarlo a la colección de etiquetas.

Para implementar el Explorador de etiqueta personalizado, edite el `MessagesViewController.cs` de archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();


            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

Echar un vistazo a este código en detalle, se crea el almacenamiento para el explorador personalizado:

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

Y, en el `ViewDidLoad` método, se crea y configura un nuevo explorador:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

A continuación, el explorador agrega a la vista para que aparezca:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Personalización de etiqueta adicional

Mayor personalización de la etiqueta es posible mediante la inclusión de dos clases en la extensión de aplicación de mensajes:

- `MSStickerView`
- `MSSticker`

Mediante los métodos anteriores, la extensión puede admitir selección de etiqueta que no se basa en el método adhesivo explorador estándar. Además, se puede cambiar la presentación de la etiqueta entre dos modos de vista diferentes:

- **Compact** -éste es el modo predeterminado donde la vista de adhesivo ocupa el parte inferior del 25% de la vista de mensaje.
- **Expande** -la etiqueta vista rellena toda la vista de mensaje.

Esta vista de la etiqueta se puede cambiar entre estos modos mediante programación o manualmente por el usuario.

Eche un vistazo en el siguiente ejemplo de controlar el cambio entre los dos modos de vista diferente. Dos controladores de vista diferentes será necesarios para cada estado. El `StickerBrowserViewController` controla la **Compact** vista y tiene un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

El `AddStickerViewController` controlará la **expandido** adhesivo vista y se parecen a lo siguiente:

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);
            
            ...
        }
        #endregion
    }
}
```

El `MessageViewController` implementa estos controladores de vista para cambiar el estado solicitado:

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

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
        #endregion
    }
}
```

Cuando el usuario solicita para agregar una nueva etiqueta a su colección disponible, un nuevo `AddStickerViewController` se realiza el controlador visible y la vista adhesivo entra en el **expandido** vista:

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Cuando el usuario, se elige un adhesivo para agregar, se agrega a su colección disponible y el **Compact** se solicita la vista:

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>Resumen

En este artículo ha cubierto incluir una extensión de aplicación de mensaje en una solución de Xamarin.iOS que se integra con la **mensajes** aplicación y la funcionalidad nueva presente al usuario. Se ha descrito con la extensión para enviar mensajes de texto, etiquetas, archivos multimedia e interactivos.



## <a name="related-links"></a>Vínculos relacionados

- [Generador de helado (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Referencia de mensajes](https://developer.apple.com/reference/messages)
- [Guía de programación de la extensión de la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
