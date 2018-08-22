---
title: Copie y pegue en Xamarin.Mac
description: En este artículo se explica cómo trabajar con la mesa de trabajo para proporcionar copiar y pegar en una aplicación de Xamarin.Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados dentro de una aplicación determinada.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 728e0264f7da8f3adfef360dd473772dd7e28a11
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2018
ms.locfileid: "40250950"
---
# <a name="copy-and-paste-in-xamarinmac"></a>Copie y pegue en Xamarin.Mac

_En este artículo se explica cómo trabajar con la mesa de trabajo para proporcionar copiar y pegar en una aplicación de Xamarin.Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados dentro de una aplicación determinada._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, tener acceso a la misma mesa de trabajo (copiar y pegar) compatibilidad con un desarrollador que trabaja en Objective-C.

En este artículo se cubren las dos formas principales para usar la mesa de trabajo en una aplicación de Xamarin.Mac lo siguiente:

1. **Tipos de datos estándar** -puesto que normalmente se llevan a cabo las operaciones de la mesa de trabajo entre dos aplicaciones no relacionadas, ninguna aplicación reconoce los tipos de datos compatibles con la otra. Para maximizar el potencial para el uso compartido, la mesa de trabajo puede contener varias representaciones de un elemento determinado (mediante un conjunto estándar de tipos de datos comunes), esto permite que la aplicación consumidora elegir la versión que es más adecuada para sus necesidades.
2. **Datos personalizados** - para admitir la copia y pegado de datos complejos dentro de su Xamarin.Mac puede definir un tipo de datos personalizado que será procesado por la mesa de trabajo. Por ejemplo, una aplicación de dibujo vectorial que permite al usuario copiar y pegar formas complejas que se componen de varios tipos de datos y puntos.

[![Ejemplo de la aplicación en ejecución](copy-paste-images/intro01.png "ejemplo de la aplicación en ejecución")](copy-paste-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos del uso de la mesa de trabajo en una aplicación de Xamarin.Mac admite copiar y pegar operaciones. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` atributos se utiliza para conectar las clases de C# para objetos de Objective-C y la interfaz de usuario de elementos.

## <a name="getting-started-with-the-pasteboard"></a>Introducción a la mesa de trabajo

La mesa de trabajo presenta un mecanismo estándar para el intercambio de datos en una determinada aplicación o entre aplicaciones. El uso típico de una mesa de trabajo en una aplicación de Xamarin.Mac es controlar copie y pegue las operaciones, pero también se admite un número de otras operaciones (por ejemplo, arrastrar y colocar y servicios de aplicación).

Para ponerse en marcha rápidamente, vamos a comenzar con una introducción simple y práctica para usar mesas de trabajo en una aplicación de Xamarin.Mac. Más adelante, proporcionaremos una explicación detallada del funcionamiento de la mesa de trabajo y los métodos usados.

En este ejemplo, crearemos una aplicación en función de documento simple que administra una ventana que contiene una vista de imagen. El usuario podrá copiar y pegar imágenes entre documentos en la aplicación y a o desde otras aplicaciones o varias ventanas dentro de la misma aplicación.

### <a name="creating-the-xamarin-project"></a>Crear el proyecto de Xamarin

En primer lugar, vamos a crear una nueva aplicación de Xamarin.Mac en función de documento que se agregar una copia y se pega la compatibilidad con.

Haga lo siguiente:

1. Inicie Visual Studio para Mac y haga clic en el **nuevo proyecto...**  vínculo.
2. Seleccione **Mac** > **aplicación** > **aplicación Cocoa**, a continuación, haga clic en el **siguiente** botón: 

    [![Crear un nuevo proyecto de aplicación de Cocoa](copy-paste-images/sample01.png "crear un nuevo proyecto de aplicación de Cocoa")](copy-paste-images/sample01-large.png#lightbox)
3. Escriba `MacCopyPaste` para el **nombre del proyecto** y todo lo demás como valor predeterminado deje. Haga clic en siguiente: 

    [![Establecer el nombre del proyecto](copy-paste-images/sample01a.png "establecer el nombre del proyecto")](copy-paste-images/sample01a-large.png#lightbox)

4. Haga clic en el **crear** botón: 

    [![Confirmar la nueva configuración del proyecto](copy-paste-images/sample02.png "confirmar la nueva configuración del proyecto")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Agregar un NSDocument.

A continuación, agregaremos personalizado `NSDocument` clase que actuará como el almacenamiento en segundo plano para la interfaz de usuario de la aplicación. Lo contendrá una sola vista de imagen y saber cómo copiar una imagen de la vista en la mesa de trabajo predeterminada y cómo tomar una imagen de la mesa de trabajo predeterminada y lo muestra en la vista de imagen.

Haga doble clic en el proyecto de Xamarin.Mac en la **panel de solución** y seleccione **agregar** > **nuevo archivo...** :

![Agregar un NSDocument al proyecto](copy-paste-images/sample03.png "agregando un NSDocument al proyecto")

Escriba `ImageDocument` para el **Nombre** y haga clic en el botón **Nuevo**. Editar el **ImageDocument.cs** clase y dele un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

Echemos un vistazo a la parte del código en detalle a continuación.

El código siguiente proporciona una propiedad para probar la existencia de datos de la imagen en la mesa de trabajo de forma predeterminada, si está disponible, una imagen `true` se devuelve otra `false`:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

El siguiente código copia una imagen de la vista de la imagen adjunta en la mesa de trabajo predeterminado:

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

Y el siguiente código pega una imagen de la mesa de trabajo predeterminada y lo muestra en la vista de la imagen adjunta (si la mesa de trabajo contiene una imagen válida):

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

Con este documento en su lugar, vamos a crear la interfaz de usuario para la aplicación de Xamarin.Mac.

### <a name="building-the-user-interface"></a>Creación de la interfaz de usuario

Haga doble clic en el **Main.storyboard** archivo para abrirlo en Xcode. A continuación, agregar una barra de herramientas y una imagen también y configúrelas como sigue:

[![La barra de herramientas de edición](copy-paste-images/sample04.png "la barra de herramientas de edición")](copy-paste-images/sample04-large.png#lightbox)

Agregar una copia y pega **elemento de barra de herramientas de imagen** al lado izquierdo de la barra de herramientas. Vamos a usar como accesos directos para copiar y pegar en el menú Edición. A continuación, agregue cuatro **elementos de la barra de herramientas de imagen** a la derecha de la barra de herramientas. Vamos a usar estos para rellenar la imagen bien con algunas imágenes de forma predeterminada.

Para obtener más información sobre cómo trabajar con las barras de herramientas, consulte nuestra [las barras de herramientas](~/mac/user-interface/toolbar.md) documentación.

A continuación, vamos a exponer las salidas y acciones para nuestros elementos de la barra de herramientas y la imagen siguiente bien:

[![Creación de salidas y acciones](copy-paste-images/sample05.png "crear salidas y acciones")](copy-paste-images/sample05-large.png#lightbox)

Para obtener más información sobre cómo trabajar con las salidas y acciones, vea el [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) sección de nuestra [Hello, Mac](~/mac/get-started/hello-mac.md) documentación.

### <a name="enabling-the-user-interface"></a>Habilitación de la interfaz de usuario

Con nuestra interfaz de usuario creada en Xcode y nuestro elemento de interfaz de usuario que se expone a través de las salidas y acciones, necesitamos agregar el código para habilitar la interfaz de usuario. Haga doble clic en el **ImageWindow.cs** de archivos en el **panel de solución** y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

Echemos un vistazo a este código en detalle a continuación.

En primer lugar, se expone una instancia de la `ImageDocument` clase que creamos anteriormente:

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

Mediante el uso de `Export`, `WillChangeValue` y `DidChangeValue`, hemos configurado la `Document` propiedad para permitir la codificación de clave-valor y enlace de datos en Xcode.

También se expone la imagen de la imagen que también se ha agregado a la interfaz de usuario en Xcode con la siguiente propiedad:

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

Cuando se carga y se muestra la ventana principal, creamos una instancia de nuestra `ImageDocument` clase y adjuntar la imagen de la interfaz de usuario bien a ella con el código siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

Por último, en respuesta al usuario al hacer clic en la barra de herramientas de copiar y pegar elementos, llamamos a la instancia de la `ImageDocument` clase para realizar el trabajo real:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Habilitación de los menús archivo y edición

Lo último que debemos hacer es habilitar el **New** elemento de menú de la **archivo** menú (para crear nuevas instancias de la ventana principal) y para habilitar la **cortar**, **copia**  y **pegar** los elementos de menú de la **editar** menú.

Para habilitar el **New** menú Editar elementos, el **AppDelegate.cs** archivo y agregue el código siguiente:

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Para obtener más información, consulte el [trabajar con varios Windows](~/mac/user-interface/window.md) sección de nuestra [Windows](~/mac/user-interface/window.md) documentación.

Para habilitar el **cortar**, **copia** y **pegar** editar elementos de menú, el **AppDelegate.cs** archivo y agregue el código siguiente:

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

Para cada elemento de menú, hacer que la ventana actual, superior, clave y conviértalo a nuestro `ImageWindow` clase:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

A partir de ahí que llamamos el `ImageDocument` instancia de la clase de esa ventana para controlar la copia y acciones de pegar. Por ejemplo: 

```csharp
window.Document.CopyImage (sender);
```

Sólo queremos **cortar**, **copia** y **pegar** elementos de menú sea accesible si no hay datos en la mesa de trabajo predeterminada o en la imagen también de la ventana activa actual de la imagen.

Vamos a agregar un **EditMenuDelegate.cs** de archivos al proyecto de Xamarin.Mac y darle un aspecto similar al siguiente:

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

De nuevo, se obtiene la ventana de nivel superior actual y usar su `ImageDocument` para ver los datos de imagen requiere la existencia de instancia de la clase. A continuación, usamos el `MenuWillHighlightItem` método para habilitar o deshabilitar cada elemento basada en este estado.

Editar el **AppDelegate.cs** y realice la `DidFinishLaunching` método aspecto parecido al siguiente:
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

En primer lugar, se deshabilita la habilitación automática y la deshabilitación de elementos de menú en el menú Edición. A continuación, se adjunte una instancia de la `EditMenuDelegate` clase que se crearon anteriormente.

Para obtener más información, consulte nuestra [menús](~/mac/user-interface/menu.md) documentación.

### <a name="testing-the-app"></a>Probar la aplicación

Con todo lo que en su lugar, estamos preparados para probar la aplicación. Compilar y ejecutar la aplicación y se muestra la interfaz principal:

![Ejecutar la aplicación](copy-paste-images/run01.png "ejecutando la aplicación")

Si abre el menú Edición, tenga en cuenta que **cortar**, **copia** y **pegar** están deshabilitadas porque no hay ninguna imagen en la imagen también o en la mesa de trabajo predeterminado:

![Abra el menú Edición](copy-paste-images/run02.png "abriendo el menú Edición")

Si agregar también una imagen a la imagen y vuelva a abrir el menú Edición, los elementos estará habilitados:

![Se muestran los elementos de menú de edición se habilitan](copy-paste-images/run03.png "se muestran los elementos de menú de edición están habilitados")

Si copia la imagen y seleccione **New** en el menú archivo, puede pegar esa imagen en la nueva ventana:

![Pegar una imagen en una nueva ventana](copy-paste-images/run04.png "pegar una imagen en una nueva ventana")

En las secciones siguientes, echaremos una visión detallada de trabajar con la mesa de trabajo en una aplicación de Xamarin.Mac.

## <a name="about-the-pasteboard"></a>Acerca de la mesa de trabajo

En macOS (antes conocido como OS X) la mesa de trabajo (`NSPasteboard`) proporcionan soporte técnico para los procesos de varios servidores, como copiar y pegar, arrastrar y colocar y servicios de aplicación. En las secciones siguientes, echaremos un vistazo a varios conceptos clave de mesa de trabajo.

### <a name="what-is-a-pasteboard"></a>¿Qué es una mesa de trabajo?

La `NSPasteboard` clase proporciona un mecanismo estándar para intercambiar información entre aplicaciones o dentro de una aplicación determinada. La función principal de una mesa de trabajo se utiliza para administrar las operaciones de copiar y pegar:

1. Cuando el usuario selecciona un elemento en una aplicación y utiliza el **cortar** o **copia** elemento de menú, se colocan uno o más representaciones del elemento seleccionado en la mesa de trabajo.
2. Cuando el usuario utiliza el **pegar** elemento de menú (dentro de la misma aplicación o uno diferente), la versión de los datos que puede controlar es copiada de la mesa de trabajo y agregada a la aplicación.

Los usos de mesa de trabajo menos obvios incluyen find, arrastre, arrastrar y colocar, y las operaciones de servicios de aplicación:

- Cuando el usuario inicia una operación de arrastre, los datos de arrastre se copian en la mesa de trabajo. Si la operación de arrastre termina con un descenso en otra aplicación, esa aplicación copia los datos de la mesa de trabajo.
- Servicios de traducción, los datos que se deben traducir se copian en la mesa de trabajo por la aplicación solicitante. El servicio de aplicación, se recupera los datos de la mesa de trabajo, se hace la traducción, a continuación, realice una copia de los datos de pega en la mesa de trabajo.

En su forma más simple, mesas de trabajo se usan para mover los datos dentro de una aplicación determinada o entre aplicaciones y, por tanto, existen en un área especial de la memoria global fuera del proceso de la aplicación. Si bien los conceptos de las mesas de trabajo son fácilmente extrae, hay varios detalles más complejos que deben tenerse en cuenta. Se tratarán en detalle a continuación.

### <a name="named-pasteboards"></a>Mesas de trabajo con nombre

Una mesa de trabajo puede ser público o privado y puede usarse para una variedad de propósitos dentro de una aplicación o entre varias aplicaciones. macOS proporciona varias estándares mesas de trabajo, cada uno con un uso específico, bien definido:

- `NSGeneralPboard` -La mesa de trabajo predeterminada para **cortar**, **copia** y **pegar** operaciones.
- `NSRulerPboard` -Admite **cortar**, **copia** y **pegar** operaciones en **reglas**.
- `NSFontPboard` -Admite **cortar**, **copia** y **pegar** operaciones en `NSFont` objetos.
- `NSFindPboard` -Admite específicos de la aplicación buscar paneles que pueden compartir el texto de búsqueda.
- `NSDragPboard` -Admite **arrastrar y colocar** operaciones.

Para la mayoría de los casos, usará una de las mesas de trabajo definido por el sistema. Pero puede haber situaciones que requieren que se va a crear sus propio mesas de trabajo. En estas situaciones, puede usar el `FromName (string name)` método de la `NSPasteboard` clase para crear una mesa de trabajo personalizado con el nombre especificado.

Si lo desea, puede llamar a la `CreateWithUniqueName` método de la `NSPasteboard` clase para crear una mesa de trabajo con nombre único.

### <a name="pasteboard-items"></a>Elementos de la mesa de trabajo

Cada fragmento de datos que una aplicación escribe en una mesa de trabajo se considera un _elemento de la mesa de trabajo_ y una mesa de trabajo puede contener varios elementos al mismo tiempo. De esta manera, una aplicación puede escribir pueden leer varias versiones de los datos que se copian en una mesa de trabajo (por ejemplo, texto sin formato y texto con formato) y la aplicación al recuperar solo los datos que puede procesar (por ejemplo, solo el texto sin formato).

### <a name="data-representations-and-uniform-type-identifiers"></a>Las representaciones de datos y los identificadores de tipo uniforme

Las operaciones de la mesa de trabajo normalmente tienen lugar entre dos (o más) las aplicaciones que no tienen conocimiento de ellos o los tipos de datos que puede controlar cada uno. Como se indicó en la sección anterior, para maximizar el potencial para compartir información, una mesa de trabajo puede contener varias representaciones de los datos que se va a copiar y pegar.

Cada representación se identifica a través de un identificador de tipo uniforme (UTI), que no es nada más que una simple cadena que identifica el tipo de fecha que se presentan (para obtener más información, consulte Apple [uniforme de información general de los identificadores de tipo ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentación). 

Si va a crear un tipo de datos personalizado (por ejemplo, un objeto de dibujo en un aplicación de dibujo vectorial), puede crear su propios UTI para identificarlo en la copia de forma exclusiva y operaciones de pegado.

Cuando una aplicación se prepara para pegar datos copiados de una mesa de trabajo, debe encontrar la representación que mejor se adapte a sus capacidades (si existe). Normalmente será el tipo más completa disponible (por ejemplo con formato texto para una aplicación de procesamiento de texto), recurriendo a los formularios más simple disponibles como necesario (texto sin formato para un editor de texto simple).

<a name="Promised_Data" />

### <a name="promised-data"></a>Datos prometidos

Por lo general, debe proporcionar tantos representaciones de los datos copiados como sea posible para maximizar el uso compartido entre aplicaciones. Sin embargo, debido a restricciones de tiempo o memoria, podría no resultar práctico para escribir realmente cada tipo de datos en la mesa de trabajo.

En esta situación, puede colocar la primera representación de datos en la mesa de trabajo y la aplicación receptora puede solicitar una representación diferente, que puede ser generado sobre la marcha justo antes de la operación de pegado.

Cuando se coloca el elemento inicial en la mesa de trabajo, deberá especificar que una o varias de las representaciones disponibles proceden de un objeto que se ajusta a la `NSPasteboardItemDataProvider` interfaz. Estos objetos proporcionará las representaciones adicionales a petición, según lo solicitó la aplicación receptora.

### <a name="change-count"></a>Número de cambios

Cada área de trabajo mantiene una _Nº de cambios_ que aumenta cada vez que un nuevo propietario se declara. Una aplicación puede determinar si el contenido de la mesa de trabajo ha cambiado desde la última vez que examina al comprobar el valor del número de cambios.

Use la `ChangeCount` y `ClearContents` métodos de la `NSPasteboard` clase para modificar el número de cambios de una mesa de trabajo determinado.

## <a name="copying-data-to-a-pasteboard"></a>Copiar datos en una mesa de trabajo

Realizar una operación de copia por primer acceso a una mesa de trabajo, borrar cualquier contenido existente y escribir tantos representaciones de los datos ya son necesarios para la mesa de trabajo.

Por ejemplo:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

Normalmente, solo que escriba a la mesa de trabajo general, como se ha hecho en el ejemplo anterior. Cualquier objeto que se envía a la `WriteObjects` método *debe* se ajustan a la `INSPasteboardWriting` interfaz. Varios, las clases integradas (como `NSString`, `NSImage`, `NSURL`, `NSColor`, `NSAttributedString`, y `NSPasteboardItem`) cumplir automáticamente con esta interfaz.

Si está escribiendo una clase de datos personalizados a la mesa de trabajo debe ajustarse a la `INSPasteboardWriting` interfaz o se encapsula en una instancia de la `NSPasteboardItem` clase (vea la [tipos de datos personalizados](#Custom_Data_Types) sección más adelante).

## <a name="reading-data-from-a-pasteboard"></a>Leer datos de una mesa de trabajo

Como se indicó anteriormente, para maximizar la posibilidad de compartir datos entre aplicaciones, varias representaciones de los datos copiados pueden escribirse en la mesa de trabajo. Es responsabilidad de la aplicación receptora para seleccionar la versión más completa posible para sus capacidades (si existe).

### <a name="simple-paste-operation"></a>Operación de pegar simple

Leer datos de la mesa de trabajo mediante el `ReadObjectsForClasses` método. Requiere dos parámetros:

1. Una matriz de `NSObject` en función de los tipos de clase que desea leer de la mesa de trabajo. Debe solicitar esto con el tipo de datos más deseado en primer lugar, con los tipos restantes en descendente de preferencia.
2. Un diccionario que contiene las restricciones adicionales (como la limitación para determinados tipos de contenido de dirección URL) o un diccionario vacío si no hay restricciones siguientes son necesarios.

El método devuelve una matriz de elementos que cumplen los criterios que se pasan y por lo tanto, a lo sumo contiene el mismo número de tipos de datos que se solicitan. También es posible que ninguno de los tipos solicitados están presente y se devolverá una matriz vacía.

Por ejemplo, el código siguiente comprueba si un `NSImage` existe en la mesa de trabajo general y lo muestra en una imagen bien si es así:

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>Solicitar varios tipos de datos

Según el tipo de aplicación de Xamarin.Mac que se está creando, puede ser capaz de controlar varias representaciones de los datos que se va a pegar. En esta situación, hay dos escenarios para recuperar datos de la mesa de trabajo:

1. Realizar una llamada única a la `ReadObjectsForClasses` método y proporcionar una matriz de todas las representaciones que desee (en el orden preferido).
2. Realizar varias llamadas a la `ReadObjectsForClasses` método, piden otra matriz de tipos de cada vez.

Consulte la **Simple operación de pegar** sección anterior para obtener más información sobre cómo recuperar datos de una mesa de trabajo.

### <a name="checking-for-existing-data-types"></a>Comprobación de tipos de datos existentes

Hay ocasiones en que desea comprobar si una mesa de trabajo contiene una representación de datos determinado sin leer realmente los datos de la mesa de trabajo (como la habilitación de la **pegar** elemento de menú sólo cuando hay datos válidos).

Llame a la `CanReadObjectForClasses` método de la mesa de trabajo para ver si contiene un tipo determinado.

Por ejemplo, el código siguiente determina si la mesa de trabajo general contiene un `NSImage` instancia:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>Leer las direcciones URL de la mesa de trabajo

En función de la función de una determinada aplicación de Xamarin.Mac, puede ser necesario para leer las direcciones URL de una mesa de trabajo, pero solo si cumplen un determinado conjunto de criterios (por ejemplo, para que apunte a archivos o las direcciones URL de un tipo de datos específica). En esta situación, puede especificar criterios de búsqueda adicionales mediante el segundo parámetro de la `CanReadObjectForClasses` o `ReadObjectsForClasses` métodos.

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>Tipos de datos personalizados

Hay veces cuando necesite guardar sus propios tipos personalizados en la mesa de trabajo desde una aplicación de Xamarin.Mac. Por ejemplo, un dibujo vectorial aplicación que permite al usuario copiar y pegar objetos de dibujo.

En esta situación, debe diseñar la clase de datos personalizada para que herede de `NSObject` y se ajusta a algunas interfaces (`INSCoding`, `INSPasteboardWriting` y `INSPasteboardReading`). Si lo desea, puede usar un `NSPasteboardItem` para encapsular los datos que se va a copiar o pegar.

Ambas opciones se tratarán en detalle a continuación.

### <a name="using-a-custom-class"></a>Usar una clase personalizada

En esta sección se se expandir en la aplicación de ejemplo sencillo que se creó al principio de este documento y agregar una clase personalizada para realizar un seguimiento de información sobre la imagen que estamos copiando y pegando entre ventanas.

Agregue una nueva clase al proyecto y llámelo **ImageInfo.cs**. Edite el archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

En las secciones siguientes, echaremos una visión detallada de esta clase.

#### <a name="inheritance-and-interfaces"></a>Herencia y las interfaces

Antes de una clase de datos personalizada se puede escribir o leer desde una mesa de trabajo, debe ajustarse a la `INSPastebaordWriting` y `INSPasteboardReading` interfaces. Además, debe heredar `NSObject` y también se ajustan a la `INSCoding` interfaz:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

La clase también debe estar expuesta a Objective-C mediante la `Register` directiva y deben exponer cualquier precisan propiedades o métodos mediante `Export`. Por ejemplo:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Nos estamos exponiendo los dos campos de datos que va a contener esta clase - nombre de la imagen y su tipo (jpg, png, etcetera.). 

Para obtener más información, consulte el [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentación, explica la `Register` y `Export` atributos se utiliza para conectar las clases de C# para objetos de Objective-C y la interfaz de usuario de elementos.

#### <a name="constructors"></a>Constructores

Dos constructores (expuestos correctamente y Objective-C) será necesarios para nuestra clase de datos personalizada para que se puede leer desde una mesa de trabajo:

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

En primer lugar, se exponen los _vacía_ constructor en el método de Objective-C predeterminado de `init`.

A continuación, exponemos un `NSCoding` constructor conforme que se usará para crear una nueva instancia del objeto de la mesa de trabajo cuando se pegue en el nombre exportado de `initWithCoder`.

Este constructor toma un `NSCoder` (tal como lo creó un `NSKeyedArchiver` cuando se escriben en la mesa de trabajo), extrae los datos de clave-valor emparejado y lo guarda en los campos de propiedades de la clase de datos.

#### <a name="writing-to-the-pasteboard"></a>Escribir en la mesa de trabajo

Mediante la conformidad con la `INSPasteboardWriting` interfaz, es necesario exponer dos métodos y, opcionalmente, un tercer método para que la clase se puede escribir en la mesa de trabajo.

En primer lugar, es necesario indicar el tipo de datos de la mesa de trabajo representaciones de la clase personalizada se puede escribir en:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Cada representación se identifica a través de un identificador de tipo uniforme (UTI), que no es nada más que una simple cadena que identifica el tipo de datos que se presentan (para obtener más información, consulte Apple [uniforme de información general de los identificadores de tipo ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentación).

Para nuestro formato personalizado, vamos a crear nuestro propio UTI: "com.xamarin.image-info" (tenga en cuenta que se encuentra en la notación inversa como un identificador de aplicación). Nuestra clase también es capaz de escribir una cadena estándar en la mesa de trabajo (`public.text`). 

A continuación, se debe crear el objeto en el formato solicitado que obtiene escrito realmente en la mesa de trabajo:

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

Para el `public.text` tipo, nos estamos devolviendo un simple, con el formato `NSString` objeto. Personalizada de `com.xamarin.image-info` tipo, estamos usando un `NSKeyedArchiver` y `NSCoder` interfaz para codificar la clase de datos personalizados a un archivo de clave/valor emparejado. Deberá implementar el método siguiente para controlar realmente la codificación:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Los pares clave/valor individuales se escriben en el codificador y se decodificará con el segundo constructor que agregamos anteriormente.

Si lo desea, podemos incluir el método siguiente para definir las opciones al escribir datos en la mesa de trabajo:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Actualmente, solo el `WritingPromised` opción está disponible y debe usarse cuando un tipo determinado es sólo prometido y realmente no se escriben en la mesa de trabajo. Para obtener más información, consulte el [datos prometidos](#Promised_Data) sección anterior.

Con estos métodos en su lugar, el código siguiente puede utilizarse para escribir nuestra clase personalizada en la mesa de trabajo:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>Lectura de la mesa de trabajo

Mediante la conformidad con la `INSPasteboardReading` interfaz, es necesario exponer los tres métodos para que la clase de datos personalizados se puede leer de la mesa de trabajo.

En primer lugar, es necesario indicar el tipo de datos de la mesa de trabajo representaciones de la clase personalizada puede leer el Portapapeles:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Nuevamente, estas se definen como UTI simple y son los mismos tipos que se definen en el **escribir en la mesa de trabajo** sección anterior.

A continuación, necesitamos indicar a la mesa de trabajo _cómo_ cada uno de los tipos UTI leerá utilizando el método siguiente:

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

Para el `com.xamarin.image-info` tipo, indicamos a la mesa de trabajo para descodificar el par clave/valor que se creó con la `NSKeyedArchiver` cuando se escribe en la clase a la mesa de trabajo mediante una llamada a la `initWithCoder:` constructor que se han agregado a la clase.

Por último, necesitamos agregar el siguiente método para leer las otras representaciones de datos UTI de la mesa de trabajo:

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

Con todos estos métodos en su lugar, la clase de datos personalizados se puede leer de la mesa de trabajo utilizando el código siguiente:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>Uso de un NSPasteboardItem

Puede que en ocasiones cuando necesite escribir elementos personalizados en la mesa de trabajo que no requieran la creación de una clase personalizada o quiere proporcionar los datos en un formato común, sólo si es necesario. Para estas situaciones, puede usar un `NSPasteboardItem`.

Un `NSPasteboardItem` proporciona un mayor control sobre los datos que se escriben en la mesa de trabajo y está diseñados para tener acceso temporal: se debe desechar después de que se ha escrito en la mesa de trabajo.

#### <a name="writing-data"></a>Escritura de datos

Para escribir los datos personalizados a un `NSPasteboardItem` , deberá proporcionar una personalizada `NSPasteboardItemDataProvider`. Agregue una nueva clase al proyecto y llámelo **ImageInfoDataProvider.cs**. Edite el archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

Igual que hicimos con la clase de datos personalizada, debemos usar la `Register` y `Export` directivas para exponerlo a Objective-C. La clase debe heredar de `NSPasteboardItemDataProvider` y debe implementar la `FinishedWithDataProvider` y `ProvideDataForType` métodos.

Use la `ProvideDataForType` método para proporcionar los datos que se ajustará el `NSPasteboardItem` como sigue:

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

En este caso, estamos almacenar dos piezas de información sobre nuestra imagen (nombre y tipo de imagen) y escribir en una cadena sencilla (`public.text`).

Tipo de escribir los datos en la mesa de trabajo, use el código siguiente:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>Lectura de datos

Para leer los datos de la mesa de trabajo, use el código siguiente:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con la mesa de trabajo en una aplicación de Xamarin.Mac admite copiar y pegar operaciones. En primer lugar, introdujo un ejemplo sencillo para familiarizarse con las operaciones de mesas de trabajo estándar. A continuación, se tardó una visión detallada de la mesa de trabajo y cómo leer y escribir datos de él. Por último, examinamos con un tipo de datos personalizado para admitir la copia y pegado de tipos de datos complejos dentro de una aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [MacCopyPaste (ejemplo)](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guía de programación de la mesa de trabajo](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [Directrices de interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
