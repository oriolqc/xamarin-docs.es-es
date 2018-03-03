---
title: "Conceptos básicos de extensión de la aplicación de mensaje"
description: "En este artículo se muestra cómo incluir una extensión de la aplicación de mensaje en una solución de Xamarin.iOS que se integra con la aplicación de mensajes y presenta nuevas funciones para el usuario."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7da0f52816d4efc102e830411589e0ac38a73574
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="message-app-extension-basics"></a>Conceptos básicos de extensión de la aplicación de mensaje

_En este artículo se muestra cómo incluir una extensión de la aplicación de mensaje en una solución de Xamarin.iOS que se integra con la aplicación de mensajes y presenta nuevas funciones para el usuario._

Nuevo en 10 de iOS, una extensión de la aplicación de mensaje se integra con la **mensajes** aplicación y presenta nueva funcionalidad al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos.

## <a name="about-message-app-extensions"></a>Acerca de las extensiones de aplicación de mensaje

Como se mencionó anteriormente, una extensión de la aplicación de mensaje se integra con la **mensajes** aplicación y presenta nueva funcionalidad al usuario. La extensión puede enviar mensajes de texto, etiquetas, archivos multimedia e interactivos. Existen dos tipos de extensión de la aplicación de mensaje:

- **Módulos de etiqueta** -contiene una colección de etiquetas que el usuario puede agregar a un mensaje. Módulos de etiqueta se pueden crear sin escribir ningún código.
- **iMessage aplicación** -puede presentar una interfaz de usuario personalizada dentro de la aplicación de mensajes para seleccionar adhesivos, escribir texto, incluidos los archivos de medios (con las conversiones de tipo opcional) y crear, modificar y enviar mensajes de interacción.

Extensiones de mensajes de aplicaciones proporciona tres tipos principales de contenido:

- **Mensajes interactivos** -son un tipo de contenido del mensaje personalizado que genera una aplicación, cuando el usuario puntea en el mensaje, la aplicación se iniciará en primer plano.
- **Adhesivos** -son imágenes generadas por la aplicación que puede incluirse en los mensajes enviados entre los usuarios.
- **Otro tipo de contenido admite** : la aplicación puede proporcionar contenido como fotos, vídeos, texto o tipos de vínculos a cualquier otro tipo de contenido que siempre han admitido por la aplicación de mensajes.

Nuevo en iOS 10, la aplicación de mensaje ahora incluye su propia tienda de aplicaciones dedicado e integradas. Las aplicaciones que incluyen extensiones de aplicaciones de mensajes se muestran y se promocionan en este almacén. El nuevo espacio de aplicación de mensajes se mostrarán las aplicaciones que se han descargado de la tienda de aplicaciones de mensajes para proporcionar un acceso rápido a los usuarios.

También nuevo en 10 de iOS, Apple agregada Inline App Attribution que permite al usuario detectar fácilmente una aplicación. Por ejemplo, si un usuario envía el contenido a otro desde una aplicación que el usuario 2 no tiene instalado (por ejemplo, una etiqueta por ejemplo), el nombre de la aplicación envío aparece en el contenido en el historial de mensajes. Si el usuario puntee en la aplicación el nombre, se puede abrir la tienda de aplicaciones de mensaje y la aplicación seleccionada en el almacén.

Extensiones de mensajes de aplicaciones son similares a las aplicaciones existentes de iOS que el desarrollador está familiarizado con la creación y tendrán acceso a todas las características de una aplicación de iOS estándar y marcos de trabajo estándares. Por ejemplo:

- Tienen acceso a la compra de la aplicación.
- Tienen acceso a Apple Pay.
- Tienen acceso al hardware del dispositivo, como la cámara.

Solo se admiten extensiones de mensajes de aplicaciones en iOS 10, sin embargo, el contenido que estas extensiones de envío está visible en los dispositivos watchOS y macOS. El nuevo _Recents Page_ agregado a watchOS 3, mostrará etiquetas recientes que se hayan enviado desde el teléfono, los de extensiones de mensajes de aplicaciones, incluidos y permite al usuario enviar esas etiquetas desde la inspección.

## <a name="about-the-messages-framework"></a>Acerca de cómo el marco de trabajo de mensajes

Nuevo en iOS 10, el marco de trabajo de mensajes proporciona la interfaz entre la extensión de aplicaciones de mensajes y la aplicación de mensaje en el dispositivo del usuario iOS. Cuando el usuario inicia una aplicación desde dentro de la aplicación de mensajes, este marco de trabajo permite que la aplicación para que se detecten y proporciona el contexto necesario para mostrar su interfaz de usuario y los datos.

Una vez que se inicia la aplicación, el usuario interactúa con él para crear nuevo contenido para compartir a través de un mensaje. A continuación, la aplicación utiliza el marco de trabajo de mensajes para transferir el contenido recién creado para la aplicación de mensajes para su procesamiento.

El marco de mensajes y las extensiones de aplicaciones de mensaje se basan en tecnologías de extensiones de aplicación de iOS preexistente. Para obtener más información acerca de las extensiones de aplicación, vea de Apple [Guía de programación de la extensión en la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

A diferencia de otros puntos de extensión que Apple ha proporcionado en todo el sistema, el desarrollador no necesita proporcionar una aplicación de host para las extensiones de aplicaciones de mensaje ya que la propia aplicación de mensaje actúa como contenedor. Sin embargo, el programador tiene la opción de incluida la extensión de aplicaciones de mensaje dentro de una aplicación nueva o existente de iOS y enviarlo junto con la agrupación.

Si las extensiones de aplicaciones de mensaje se incluye en el paquete de una aplicación de iOS, se mostrará el icono de la aplicación en la pantalla del dispositivo principal y en el espacio de aplicación de mensaje desde dentro de la aplicación de mensajes. Si no se incluye en un paquete de aplicación, la extensión de aplicaciones de mensajes solo se mostrará en el espacio de la aplicación de mensaje.

Aunque las extensiones de aplicaciones de mensaje no se incluye en un paquete de aplicación de host, el programador deberá proporcionar un icono de la aplicación en el paquete de la extensión de aplicaciones de mensaje, puesto que éste es el icono que se mostrará en otras partes del sistema como la configuración o el cajón de aplicación de mensaje , para la extensión.

## <a name="about-stickers"></a>Acerca de etiquetas

Apple diseñada adhesivos como una nueva forma para que los usuarios iMessage comunicarse permitiendo adhesivos que se envían en línea como cualquier otro contenido de mensaje o que pueden asociarse a las burbujas de mensaje anterior dentro de la conversación.

¿Cuáles son adhesivos?

- Son imágenes que proporciona la extensión de aplicaciones de mensajes.
- Pueden ser imágenes animadas o estáticas.
- Proporcionan una nueva manera de compartir el contenido de imagen desde dentro de una aplicación.

Hay dos maneras de crear etiquetas:

1. Una etiqueta de módulo mensaje aplicaciones extensiones pueden crearse desde dentro de Xcode sin incluir ningún código. Todo lo que se necesita es los activos de las etiquetas y los iconos de la aplicación.
2. Mediante la creación de una extensión de aplicaciones estándar de mensaje que proporciona adhesivos desde el código mediante el marco de trabajo de mensajes.

### <a name="creating-sticker-packs"></a>Creación de módulos de etiqueta

Módulos de etiqueta se crean a partir de una plantilla especial dentro de Xcode y basta con proporcionan un conjunto estático de activos de imagen que puede usarse como adhesivos. Como se mencionó anteriormente, no requieren ningún código, el desarrollador simplemente arrastra archivos de imagen en la carpeta del módulo de etiqueta en el catálogo de activos de etiquetas.

Para que una imagen que se incluirá en un módulo de etiqueta, debe cumplir los siguientes requisitos:

- Imágenes deben estar en un formato PNG, APNG, GIF o JPEG. Apple sugiere utilizando los formatos PNG y APNG al proporcionar activos de etiqueta.
- Adhesivos animados solo admiten los formatos APNG y GIF.
- Imágenes de etiqueta deben proporcionar un fondo transparente ya que puede colocarse sobre burbujas de mensaje en la conversación por el usuario.
- Los archivos de imagen individuales deben ser inferior a 500kb.
- Imágenes no pueden ser mayor o menor que 100 x 100 puntos que apunta 206 x 206.

> [!IMPORTANT]
> **Nota:** imágenes de etiqueta siempre deben proporcionarse en el `@3x` resolución en el intervalo de 300 x 300 a 618 x 618 píxeles. El sistema generará automáticamente la `@2x` y `@1x` versiones en tiempo de ejecución según sea necesario.

Apple sugiere probar los activos de imagen de etiqueta en varios fondos color diferentes (por ejemplo, blanco, negro, rojo, amarillo y múltiples en color) y sobre fotos, para garantizar que se muestran los mejores en todas las situaciones posibles.

Módulos de etiqueta pueden proporcionar adhesivos en uno de tres tamaños disponibles:

- **Pequeño** : 100 x 100 puntos.
- **Medio** : 136 x 136 puntos. Este es el tamaño predeterminado.
- **Gran** - 206 x 206 puntos.

Usar Inspector del Xcode de atributos para establecer el tamaño de todo el paquete de etiqueta y solo proporcionan activos de imagen que coincida con el tamaño solicitado, para obtener los mejores resultados en el Explorador de etiqueta dentro de la aplicación de mensajes.

Para obtener más información, vea nuestra [el generador de helado](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) aplicación y de Apple [referencia de mensajes](https://developer.apple.com/reference/messages).

## <a name="creating-a-custom-sticker-experience"></a>Crear una experiencia de etiqueta personalizada

Si la aplicación necesita más control o flexibilidad que la proporcionada por un módulo de etiqueta, puede incluir una extensión de la aplicación de mensaje y se proporcionan las etiquetas mediante el marco de trabajo de mensajes para una experiencia de etiqueta personalizada.

¿Cuáles son las ventajas de crear una experiencia de etiqueta personalizada?

1. Permite que la aplicación personalizar cómo se muestran las etiquetas a los usuarios de la aplicación. Por ejemplo, para adhesivos presentes en un formato que no sea el diseño de cuadrícula estándar o en un fondo color diferente.
2. Permite adhesivos creará dinámicamente desde el código en lugar de que se incluye como activos de imagen estática.
3. Permite a los activos de imagen de etiqueta descargar dinámicamente de servidor web del desarrollador sin tener que crear una nueva versión a la tienda de aplicaciones.
4. Permite el acceso de la cámara del dispositivo para crear etiquetas sobre la marcha.
5. Permite las compras de la aplicación para que el usuario puede adquirir más adhesivos desde dentro de la aplicación.

Para crear una experiencia de etiqueta personalizada, haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie Visual Studio para Mac.
2. Abra la solución para agregar una extensión de la aplicación de mensaje a. 
3. Seleccione **iOS** > **extensiones** > **iMessage extensión** y haga clic en el **siguiente** botón: 

    [ ![](intro-to-message-app-extensions-images/message01.png "Seleccione iMessage extensión")](intro-to-message-app-extensions-images/message01.png)
4. Escriba un **nombre de la extensión** y haga clic en el **siguiente** botón: 

    [ ![](intro-to-message-app-extensions-images/message02.png "Escriba un nombre de extensión")](intro-to-message-app-extensions-images/message02.png)
5. Haga clic en el **crear** botón para crear la extensión: 

    [ ![](intro-to-message-app-extensions-images/message03.png "Haga clic en el botón Crear")](intro-to-message-app-extensions-images/message03.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie Visual Studio.
2. Abra la solución para agregar una extensión de la aplicación de mensaje a. 
3. Seleccione **iOS** > **extensiones** > **iMessage extensión** y haga clic en el **siguiente** botón: 

    [ ![](intro-to-message-app-extensions-images/message01w.png "Seleccione iMessage extensión")](intro-to-message-app-extensions-images/message01.png)
4. Escriba un **nombre de la extensión** y haga clic en el **Aceptar** botón

-----

De forma predeterminada, la `MessagesViewController.cs` archivo se agregará a la solución. Este es el punto de entrada principal a la extensión y hereda de la `MSMessageAppViewController` clase.

El marco de trabajo de mensajes proporciona clases para presentar adhesivos disponibles para el usuario:

- `MSStickerBrowserViewController` : Controla la vista que se presentan en las etiquetas. También se ajusta a la `IMSStickerBrowserViewDataSource` interfaz para devolver el número de etiqueta y la etiqueta para un índice determinado de explorador.
- `MSStickerBrowserView` -Esta es la vista que se mostrará las etiquetas disponibles en.
- `MSStickerSize` -Decide los tamaños de celda individual de la cuadrícula de etiquetas que se presentan en la vista de explorador.

### <a name="creating-a-custom-sticker-browser"></a>Creación de un explorador de etiqueta personalizada

El desarrollador puede personalizar aún más la experiencia de etiqueta para el usuario proporcionando un explorador de etiqueta personalizada (`MSMessageAppBrowserViewController`) en la extensión de la aplicación de mensaje. El Explorador de etiqueta personalizado cambia cómo adhesivos van a mostrar al usuario cuando selecciona una etiqueta que se incluirán en la secuencia de mensajes.

Haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **solución Pad**, haga doble clic en el nombre del proyecto de la extensión y seleccione **agregar** > **nuevo archivo...**   >  **iOS | Apple Watch** > **controlador de la interfaz**.
2. Escriba `StickerBrowserViewController` para el **nombre** y haga clic en el **New** botón: 

    [ ![](intro-to-message-app-extensions-images/browser01.png "Escriba StickerBrowserViewController para el nombre")](intro-to-message-app-extensions-images/browser01.png)
3. Abra el `StickerBrowserViewController.cs` archivo para su edición.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el **el Explorador de soluciones**, haga doble clic en el nombre del proyecto de la extensión y seleccione **agregar** > **nuevo archivo...**   >  **iOS | Apple Watch** > **controlador de la interfaz**.
2. Escriba `StickerBrowserViewController` para el **nombre** y haga clic en el **New** botón: 

    [ ![](intro-to-message-app-extensions-images/browser01w.png "Escriba StickerBrowserViewController para el nombre")](intro-to-message-app-extensions-images/browser01.png)
3. Abra el `StickerBrowserViewController.cs` archivo para su edición.

-----

Realizar el `StickerBrowserViewController.cs` el siguiente aspecto:

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

Eche un vistazo en el código anterior en detalle. Almacenamiento se crea para las etiquetas que proporciona la extensión:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Y reemplaza los dos métodos de la `MSStickerBrowserViewController` clase para proporcionar datos para el Explorador de este almacén de datos:

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

El `CreateSticker` método obtiene la ruta de acceso de un recurso de imagen de agrupación de la extensión y lo utiliza para crear una nueva instancia de un `MSSticker` de este recurso, que agrega a la colección:

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

El `LoadSticker` método se llama desde `ViewDidLoad` para crear una etiqueta desde el recurso de imagen con el nombre (incluido en el paquete de la aplicación) y agregarlo a la colección de etiquetas.

Para implementar el Explorador de etiqueta personalizado, edite la `MessagesViewController.cs` de archivos y darle un aspecto similar al siguiente:

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

Y en el `ViewDidLoad` método, se crea y configura una nueva ventana del explorador:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

A continuación, agrega el explorador a la vista para mostrarla:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Mayor personalización de etiqueta

Mayor personalización de la etiqueta es posible mediante la inclusión de dos clases en la extensión de la aplicación de mensaje:

- `MSStickerView`
- `MSSticker`

Mediante los métodos anteriores, la extensión puede admiten la selección de etiqueta que no se basa en el método de etiqueta explorador estándar. Además, se puede cambiar la presentación de la etiqueta situada entre dos modos de vista diferentes:

- **Compact** -éste es el modo predeterminado donde la vista de etiqueta ocupa el 25% de parte inferior de la vista de mensajes.
- **Expandir** -vista de la etiqueta llena toda la vista de mensajes.

Esta vista de la etiqueta se puede cambiar entre estos modos mediante programación o manualmente por el usuario.

Eche un vistazo en el siguiente ejemplo de controlar el cambio entre los dos modos de vista diferentes. Dos controladores diferentes de la vista será necesarios para cada estado. El `StickerBrowserViewController` controla la **Compact** vista y es similar a lo siguiente:

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

El `AddStickerViewController` controlará la **expandida** vista adhesivo y aspecto similar al siguiente:

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

El `MessageViewController` implementa estos controladores de la vista para cambiar el estado solicitado:

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

Cuando el usuario solicita para agregar una nueva etiqueta a su colección disponibles, un nuevo `AddStickerViewController` se realiza el controlador visible y la vista de etiqueta entra en el **expandida** vista:

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

Cuando el usuario elige una etiqueta para agregar, se agrega a su colección disponibles y la **Compact** se solicita la vista:

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
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

## <a name="summary"></a>Resumen

En este artículo ha cubierto incluir una extensión de la aplicación de mensaje en una solución de Xamarin.iOS que se integra con la **mensajes** aplicación y presente nueva funcionalidad al usuario. Tratan con la extensión para enviar mensajes de texto, etiquetas, archivos multimedia e interactivos.



## <a name="related-links"></a>Vínculos relacionados

- [Generador de helado (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Referencia de mensajes](https://developer.apple.com/reference/messages)
- [Guía de programación de la extensión de la aplicación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
