---
title: Copiando y pegando
description: Este artículo explica cómo trabajar con la mesa de trabajo para proporcionar copiar y pegar en una aplicación Xamarin.Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados dentro de una aplicación determinada.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: cf81666403f687ce997e20f6f5f097dc9fcf1421
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="copy-and-paste"></a>Copiando y pegando

_Este artículo explica cómo trabajar con la mesa de trabajo para proporcionar copiar y pegar en una aplicación Xamarin.Mac. Muestra cómo trabajar con tipos de datos estándar que se pueden compartir entre varias aplicaciones y cómo admitir datos personalizados dentro de una aplicación determinada._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tener acceso a la misma mesa de trabajo (copiar y pegar) compatibilidad que un desarrollador que trabaja en Objective C tiene.

En este artículo se va a tratar las dos formas principales para utilizar la mesa de trabajo en una aplicación Xamarin.Mac:

1. **Tipos de datos estándar** -puesto que normalmente se llevan a cabo las operaciones de la mesa de trabajo entre las dos aplicaciones no relacionadas, ninguna aplicación conoce los tipos de datos que la otra admite. Para maximizar la posibilidad de uso compartido, la mesa de trabajo puede contener varias representaciones de un elemento determinado (con un conjunto estándar de tipos de datos comunes), esto permite que la aplicación consume la elección de la versión que se adapta mejor a sus necesidades.
2. **Datos personalizados** - para admitir la copia y pegado de datos complejos dentro de su Xamarin.Mac puede definir un tipo de datos personalizado que será procesado por la mesa de trabajo. Por ejemplo, una aplicación de dibujo vectorial que permite al usuario copiar y pegar formas complejas que se componen de varios tipos de datos y puntos.

[![Ejemplo de la aplicación en ejecución](copy-paste-images/intro01.png "ejemplo de la aplicación en ejecución")](copy-paste-images/intro01-large.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con la mesa de trabajo en una aplicación Xamarin.Mac admite copiar y pegar las operaciones. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` atributos se utiliza para conectarlo las clases de C# a los objetos de Objective-C y la interfaz de usuario de elementos.

## <a name="getting-started-with-the-pasteboard"></a>Introducción a la mesa de trabajo

La mesa de trabajo presenta un mecanismo estándar para el intercambio de datos dentro de una aplicación determinada o entre aplicaciones. El uso típico de una mesa de trabajo en una aplicación de Xamarin.Mac es controlar copiar y pegar las operaciones, sin embargo, también se admite un número de otras operaciones (como arrastrar y colocar y los servicios de aplicaciones).

Para comenzar, del suelo rápidamente, vamos a empezar con una introducción sencilla y práctica para usar mesas de trabajo en una aplicación Xamarin.Mac. Más adelante, le presentaremos una explicación detallada del funcionamiento de la mesa de trabajo y los métodos que se utilizan.

En este ejemplo, crearemos una aplicación simple documentos según que administra una ventana que contiene una vista de la imagen. El usuario podrá copiar y pegar imágenes entre los documentos en la aplicación y hacia o desde otras aplicaciones o varias ventanas dentro de la misma aplicación.

### <a name="creating-the-xamarin-project"></a>Crear el proyecto de Xamarin

En primer lugar, vamos a crear una nueva aplicación de Xamarin.Mac en función de documento que se se agregar copia y pega la compatibilidad con.

Haga lo siguiente:

1. Inicie Visual Studio para Mac y haga clic en el **nuevo proyecto...**  vínculo.
2. Seleccione **Mac** > **aplicación** > **aplicación cacao**, a continuación, haga clic en el **siguiente** botón: 

    [![Crear un nuevo proyecto de aplicación de cacao](copy-paste-images/sample01.png "crear un nuevo proyecto de aplicación de cacao")](copy-paste-images/sample01-large.png#lightbox)
3. Escriba `MacCopyPaste` para el **nombre de proyecto** y todo lo demás como valor predeterminado mantener. Haga clic en siguiente: 

    [![Establecer el nombre del proyecto](copy-paste-images/sample01a.png "establecer el nombre del proyecto")](copy-paste-images/sample01a-large.png#lightbox)

4. Haga clic en el **crear** botón: 

    [![Confirmar la nueva configuración de proyecto](copy-paste-images/sample02.png "confirmar la nueva configuración de proyecto")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Agregar un NSDocument

A continuación, agregaremos personalizado `NSDocument` clase que actuará como el almacenamiento de fondo para la interfaz de usuario de la aplicación. Lo contendrá una sola vista de imagen y saber cómo copiar una imagen de la vista en la mesa de trabajo de forma predeterminada y cómo utilizar una imagen de la mesa de trabajo de forma predeterminada y mostrarlo en la vista de la imagen.

Haga doble clic en el proyecto Xamarin.Mac en la **solución Pad** y seleccione **agregar** > **nuevo archivo...** :

![Agregar un NSDocument al proyecto](copy-paste-images/sample03.png "agregar un NSDocument al proyecto")

Escriba `ImageDocument` para el **Nombre** y haga clic en el botón **Nuevo**. Editar la **ImageDocument.cs** clase y darle un aspecto similar al siguiente:

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

¡Eche un vistazo a la parte del código en detalle a continuación.

El código siguiente proporciona una propiedad que se va a probar la existencia de datos de la imagen en la mesa de trabajo de forma predeterminada, si está disponible, una imagen `true` se devuelve else `false`:

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

El código siguiente copia una imagen de la vista de imagen adjunto a la mesa de trabajo de forma predeterminada:

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

Y el código siguiente pega una imagen de la mesa de trabajo de forma predeterminada y lo muestra en la vista de imagen adjunto (si la mesa de trabajo contiene una imagen válida):

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

Con este documento en su lugar, vamos a crear la interfaz de usuario de la aplicación Xamarin.Mac.

### <a name="building-the-user-interface"></a>Creación de la interfaz de usuario

Haga doble clic en el **Main.storyboard** archivo para abrirlo en Xcode. A continuación, agregar una barra de herramientas y una imagen bien y configurarlos como sigue:

[![La barra de herramientas de edición](copy-paste-images/sample04.png "la barra de herramientas de edición")](copy-paste-images/sample04-large.png#lightbox)

Agregue una copia y pega **elemento de la barra de herramientas de imagen** a la izquierda de la barra de herramientas. Usaremos estos archivos como accesos directos para copiar y pegar en el menú Edición. A continuación, agregue cuatro **elementos de la barra de herramientas de imagen** a la derecha de la barra de herramientas. Vamos a usar estos para rellenar la imagen también con algunas imágenes de forma predeterminada.

Para obtener más información sobre cómo trabajar con las barras de herramientas, visite nuestro [las barras de herramientas](~/mac/user-interface/toolbar.md) documentación.

A continuación, vamos a exponer las siguientes salidas y las acciones de los elementos de la barra de herramientas y la imagen también:

[![Crear acciones y las salidas](copy-paste-images/sample05.png "crear acciones y las salidas")](copy-paste-images/sample05-large.png#lightbox)

Para obtener más información sobre cómo trabajar con las salidas y las acciones, consulte la [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) sección de nuestro [Hola, Mac](~/mac/get-started/hello-mac.md) documentación.

### <a name="enabling-the-user-interface"></a>Habilitación de la interfaz de usuario

Con la interfaz de usuario creada en Xcode y nuestro elemento de interfaz de usuario que se expone a través de distribuidores y acciones, tenemos que agregar el código para habilitar la interfaz de usuario. Haga doble clic en el **ImageWindow.cs** un archivo en el **solución Pad** y darle un aspecto similar al siguiente:

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

¡Eche un vistazo a este código en detalle a continuación.

En primer lugar, se expone una instancia de la `ImageDocument` clase que hemos creado anteriormente:

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

Mediante el uso de `Export`, `WillChangeValue` y `DidChangeValue`, se ha configurado la `Document` propiedad para permitir la codificación de clave-valor y enlace de datos en Xcode.

También se expone la imagen de la imagen que también se agrega a la interfaz de usuario en Xcode con la siguiente propiedad:

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

Cuando se carga y se muestra la ventana principal, creamos una instancia de nuestro `ImageDocument` clase y adjuntar la imagen de la interfaz de usuario también a él con el código siguiente:

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

Por último, en respuesta al usuario al hacer clic en los elementos de barra de herramientas de copiar y pegar, llamamos a la instancia de la `ImageDocument` clase para realizar el trabajo real:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Habilitar los menús archivo y edición

Lo último que debemos hacer es habilitar la **New** elemento de menú de la **archivo** menú (para crear nuevas instancias de la ventana principal) y para permitir que el **cortar**, **copia**  y **pegar** elementos de menú de la **editar** menú.

Para habilitar la **New** menú Editar elementos, el **AppDelegate.cs** de archivos y agregue el código siguiente:

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

Para obtener más información, consulte el [trabajar con varias ventanas](~/mac/user-interface/window.md) sección de nuestro [Windows](~/mac/user-interface/window.md) documentación.

Para habilitar la **cortar**, **copia** y **pegar** editar elementos de menú, el **AppDelegate.cs** de archivos y agregue el código siguiente:

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

Para cada elemento de menú, se obtener la ventana actual, superior, key y conviértalo a nuestro `ImageWindow` clase:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

Desde allí se llame a la `ImageDocument` instancia de la clase de esa ventana para controlar la copia y pega acciones. Por ejemplo: 

```csharp
window.Document.CopyImage (sender);
```

Solamente queremos **cortar**, **copia** y **pegar** elementos de menú a estar accesible si no hay datos en la mesa de trabajo de forma predeterminada o en la imagen y de la ventana activa actual de la imagen.

Ahora agreguemos una **EditMenuDelegate.cs** al proyecto Xamarin.Mac y darle un aspecto similar al siguiente:

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

De nuevo, se obtenga la ventana de nivel superior, actual y utilice su `ImageDocument` instancia de la clase para ver si contiene los datos de imagen necesarios. A continuación, usamos el `MenuWillHighlightItem` método para habilitar o deshabilitar cada elemento en función de este estado.

Editar la **AppDelegate.cs** de archivos y realice la `DidFinishLaunching` método aspecto similar al siguiente:
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

En primer lugar, se deshabilita la habilitación automática y deshabilitar elementos de menú en el menú Edición. A continuación, adjuntamos una instancia de la `EditMenuDelegate` clase que hemos creado anteriormente.

Para obtener más información, vea nuestra [menús](~/mac/user-interface/menu.md) documentación.

### <a name="testing-the-app"></a>Probar la aplicación

Con todo el contenido en su lugar, estamos preparados para probar la aplicación. Compilar y ejecutar la aplicación y se muestra la interfaz principal:

![Ejecutar la aplicación](copy-paste-images/run01.png "ejecutando la aplicación")

Si abre el menú Edición, tenga en cuenta que **cortar**, **copia** y **pegar** están deshabilitadas porque no hay ninguna imagen en la imagen también o en la mesa de trabajo de forma predeterminada:

![Abrir el menú Edición](copy-paste-images/run02.png "abriendo el menú Edición")

Si agrega una imagen a la imagen también y vuelva a abrir el menú Edición, ahora se habilitarán los elementos:

![Mostrar los elementos de menú de edición se habilitan](copy-paste-images/run03.png "que muestra los elementos de menú de edición están habilitados")

Si copia la imagen y seleccione **New** en el menú archivo, puede pegar esa imagen en la ventana nueva:

![Pegar una imagen en una nueva ventana](copy-paste-images/run04.png "pegar una imagen en una nueva ventana")

En las siguientes secciones, echaremos un vistazo detallado a trabajar con la mesa de trabajo en una aplicación Xamarin.Mac.

## <a name="about-the-pasteboard"></a>Acerca de la mesa de trabajo

En Mac OS (anteriormente conocido como OS X) la mesa de trabajo (`NSPasteboard`) proporcionan compatibilidad para los procesos de varios servidores, como copiar y pegar, arrastrar y colocar y servicios de aplicación. En las siguientes secciones, se echará un vistazo más de cerca a varios conceptos clave de la mesa de trabajo.

### <a name="what-is-a-pasteboard"></a>¿Qué es una mesa de trabajo?

La `NSPasteboard` clase proporciona un mecanismo estándar para intercambiar información entre aplicaciones o dentro de una aplicación determinada. La función principal de una mesa de trabajo se utiliza para administrar las operaciones de copiar y pegar:

1. Cuando el usuario selecciona un elemento en una aplicación y utiliza el **cortar** o **copia** elemento de menú, no se aplica una o varias representaciones del elemento seleccionado en la mesa de trabajo.
2. Cuando el usuario utiliza el **pegar** elemento de menú (dentro de la misma aplicación o uno diferente), la versión de los datos que puede administrar se copian de la mesa de trabajo y agregada a la aplicación.

Menos obvia usa mesa de trabajo incluye la búsqueda, arrastrar, arrastrar y colocar y las operaciones de servicios de aplicaciones:

- Cuando el usuario inicia una operación de arrastre, los datos de arrastre se copian en la mesa de trabajo. Si la operación de arrastre termina con una caída en otra aplicación, esa aplicación copia los datos de la mesa de trabajo.
- Servicios de traducción, los datos que se deben traducir se copian en la mesa de trabajo con la aplicación solicitante. El servicio de aplicación, se recupera los datos de la mesa de trabajo, se hace la traducción, a continuación, pega realizar copias de los datos en la mesa de trabajo.

En su forma más sencilla, mesas de trabajo se utilizan para mover datos dentro de una aplicación determinada o entre las aplicaciones y, en consecuencia, existen en un área especial de la memoria global fuera de proceso de la aplicación. Aunque los conceptos de las mesas de trabajo son fácilmente extrae, hay varios detalles más complejos que deben tenerse en cuenta. Se explicará en detalle a continuación.

### <a name="named-pasteboards"></a>Mesas de trabajo con nombre

Una mesa de trabajo puede ser público o privado y se puede usar para una variedad de propósitos dentro de una aplicación o entre varias aplicaciones. macOS proporciona varias estándares mesas de trabajo, cada uno con un uso específico, bien definido:

- `NSGeneralPboard` -La mesa de trabajo predeterminada para **cortar**, **copia** y **pegar** operaciones.
- `NSRulerPboard` -Admite **cortar**, **copia** y **pegar** operaciones en **reglas**.
- `NSFontPboard` -Admite **cortar**, **copia** y **pegar** operaciones en `NSFont` objetos.
- `NSFindPboard` -Admite específica de la aplicación buscar paneles que pueden compartir el texto de búsqueda.
- `NSDragPboard` -Admite **arrastrar y colocar** operaciones.

Para la mayoría de los casos, deberá usar una de las mesas de trabajo definidos por el sistema. Pero puede haber situaciones que requieren que se creen sus propios mesas de trabajo. En estas situaciones, puede usar el `FromName (string name)` método de la `NSPasteboard` clase para crear una mesa de trabajo personalizado con el nombre especificado.

Si lo desea, puede llamar a la `CreateWithUniqueName` método de la `NSPasteboard` clase para crear una mesa de trabajo con nombre único.

### <a name="pasteboard-items"></a>Elementos de la mesa de trabajo

Cada fragmento de datos que una aplicación escribe en una mesa de trabajo se considera un _elemento de la mesa de trabajo_ y una mesa de trabajo puede contener varios elementos al mismo tiempo. De esta manera, puede escribir una aplicación pueden leer varias versiones de los datos se copien en una mesa de trabajo (por ejemplo, texto sin formato y texto con formato) y la aplicación al recuperar solo los datos que puede procesar (por ejemplo, solo el texto sin formato).

### <a name="data-representations-and-uniform-type-identifiers"></a>Representaciones de datos y los identificadores de tipo uniforme

Las operaciones de la mesa de trabajo suelen tardar entre dos (o más) las aplicaciones que no tienen conocimiento de ellos o los tipos de datos que puede controlar cada uno de ellos. Como se mencionó en la sección anterior, para maximizar la posibilidad de compartir información, una mesa de trabajo puede contener varias representaciones de los datos que se va a copiar y pegar.

Cada representación se identifica a través de un identificador de tipo uniforme (UTI), que es nada más que una cadena simple que identifica de forma única el tipo de fecha que se presentan (para obtener más información, vea de Apple [uniforme de información general de los identificadores de tipo ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentación). 

Si va a crear un tipo de datos personalizado (por ejemplo, un objeto de dibujo en un aplicación de dibujo vectorial), puede crear su propios UTI para identificar en una copia y operaciones de pegado.

Cuando una aplicación se prepara para pegar datos copiados de una mesa de trabajo, debe buscar la representación que mejor se adapte a sus capacidades (si existe). Normalmente será el tipo numerosas disponible (por ejemplo con formato texto para una aplicación de procesamiento de texto), en los formularios más sencillos disponibles como necesario (texto sin formato para un editor de texto simple).

<a name="Promised_Data" />

### <a name="promised-data"></a>Datos prometidos

Por lo general, debe proporcionar tantos representaciones de los datos se copian como sea posible para maximizar el uso compartido entre las aplicaciones. Sin embargo, debido a restricciones de tiempo o memoria, podría no resultar práctico para escribir realmente cada tipo de datos en la mesa de trabajo.

En esta situación, puede colocar la primera representación de datos en la mesa de trabajo y la aplicación receptora puede solicitar una representación diferente, que puede ser generado sobre la marcha justo antes de la operación de pegado.

Cuando se coloca el elemento inicial en la mesa de trabajo, deberá especificar que una o varias de las representaciones disponibles proceden de un objeto que se ajusta a la `NSPasteboardItemDataProvider` interfaz. Estos objetos proporcionará las representaciones adicionales a petición, cuando se le solicite la aplicación receptora.

### <a name="change-count"></a>Número de cambios

Cada mesa de trabajo se mantiene un _Nº de cambios de_ que incrementa cada vez un nuevo propietario se declara. Una aplicación puede determinar si el contenido de la mesa de trabajo ha cambiado desde la última vez que examinan comprobando el valor de recuento de cambio.

Use la `ChangeCount` y `ClearContents` métodos de la `NSPasteboard` clase para modificar el número de cambios de una mesa de trabajo determinado.

## <a name="copying-data-to-a-pasteboard"></a>Copiar datos a una mesa de trabajo

Realizar una operación de copia si obtiene acceso a una mesa de trabajo primero, elimina cualquier contenido existente y escribir tantos representaciones de los datos ya son necesarios para la mesa de trabajo.

Por ejemplo:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

Por lo general, solo que escriba en la mesa de trabajo general, como lo hemos hecho en el ejemplo anterior. Cualquier objeto que se envía a la `WriteObjects` método *debe* se ajustan a la `INSPasteboardWriting` interfaz. Varios, clases integradas (como `NSString`, `NSImage`, `NSURL`, `NSColor`, `NSAttributedString`, y `NSPasteboardItem`) ajustarse automáticamente a esta interfaz.

Si está escribiendo una clase de datos personalizados a la mesa de trabajo debe ajustarse a la `INSPasteboardWriting` interfaz o incluirse en una instancia de la `NSPasteboardItem` clase (vea la [tipos de datos personalizados](#Custom_Data_Types) sección más adelante).

## <a name="reading-data-from-a-pasteboard"></a>Leer datos de una mesa de trabajo

Como se mencionó anteriormente, para maximizar el potencial para compartir datos entre aplicaciones, varias representaciones de los datos copiados se puede escribir en la mesa de trabajo. Depende de la aplicación receptora para seleccionar la versión más completa posible que sus capacidades (si existe).

### <a name="simple-paste-operation"></a>Operación de pegado simple

Leer datos de la mesa de trabajo, utilizando el `ReadObjectsForClasses` método. Requiere dos parámetros:

1. Una matriz de `NSObject` según los tipos de clase que se desea leer de la mesa de trabajo. Debe solicitar esto con el tipo de datos más deseado en primer lugar, con los tipos restantes en decreciente preferencias.
2. Un diccionario que contiene las restricciones adicionales (como la limitación para determinados tipos de contenido de dirección URL) o un diccionario vacío si no hay ninguna restricción adicional es necesaria.

El método devuelve una matriz de elementos que cumplen los criterios que se pasan y, por tanto, a lo sumo contiene el mismo número de tipos de datos que se solicitan. También es posible que ninguno de los tipos solicitados están presente y se devolverá una matriz vacía.

Por ejemplo, el código siguiente comprueba si un `NSImage` existe en la mesa de trabajo general y lo muestra en una imagen y si es así:

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

Según el tipo de aplicación de Xamarin.Mac que se está creando, puede ser capaz de controlar varias representaciones de los datos que se va a pegar. En este caso, hay dos escenarios para recuperar datos de la mesa de trabajo:

1. Realizar una llamada única a la `ReadObjectsForClasses` método y proporcionar una matriz de todas las representaciones que desee (en el orden preferido).
2. Realizar varias llamadas a la `ReadObjectsForClasses` método piden otra matriz de tipos cada vez.

Consulte la **Simple operación de pegar** sección anterior para obtener más información sobre cómo recuperar datos de una mesa de trabajo.

### <a name="checking-for-existing-data-types"></a>Comprobación de tipos de datos existentes

Hay ocasiones en que desea comprobar si una mesa de trabajo contiene una representación de datos determinado sin realmente de leer los datos de la mesa de trabajo (como la habilitación de la **pegar** elemento de menú sólo cuando existen datos válidos).

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

En función de la función de una aplicación Xamarin.Mac determinada, puede ser necesario para leer las direcciones URL de una mesa de trabajo, pero solo si cumplen un determinado conjunto de criterios (por ejemplo, que señala a los archivos o las direcciones URL de un tipo de datos específico). En esta situación, puede especificar criterios de búsqueda adicionales mediante el segundo parámetro de la `CanReadObjectForClasses` o `ReadObjectsForClasses` métodos.

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>Tipos de datos personalizados

Hay ocasiones cuando necesite guardar sus propios tipos personalizados en la mesa de trabajo desde una aplicación de Xamarin.Mac. Por ejemplo, un dibujo vectorial aplicación que permite al usuario copiar y pegar objetos de dibujo.

En esta situación, debe diseñar la clase personalizada de datos para que herede de `NSObject` y se ajusta a algunas interfaces (`INSCoding`, `INSPasteboardWriting` y `INSPasteboardReading`). Si lo desea, puede usar una `NSPasteboardItem` para encapsular los datos que se va a copiar o pegar.

Ambas opciones se explicará en detalle a continuación.

### <a name="using-a-custom-class"></a>Usar una clase personalizada

En esta sección se le analizando la aplicación de ejemplo sencillo que se creó al principio de este documento y agregar una clase personalizada para realizar el seguimiento de información sobre la imagen que estamos copiando y pegando entre las ventanas.

Agregue una nueva clase al proyecto y llámelo **ImageInfo.cs**. Edite el archivo y darle un aspecto similar al siguiente:

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

En las secciones siguientes redirigirse a una visión detallada de esta clase.

#### <a name="inheritance-and-interfaces"></a>Herencia y sus interfaces

Antes de que una clase de datos personalizados se puede escribir o leer desde una mesa de trabajo, debe ajustarse a la `INSPastebaordWriting` y `INSPasteboardReading` interfaces. Además, debe heredar de `NSObject` y también se ajustan a la `INSCoding` interfaz:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

La clase también debe estar expuesta a Objective-C mediante la `Register` directiva y deben exponer las propiedades necesarias o métodos mediante `Export`. Por ejemplo:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Se están exponiendo los dos campos de datos que va a contener esta clase - nombre de la imagen y su tipo (jpg, png, etcetera). 

Para obtener más información, consulte el [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documentación, se explica la `Register` y `Export` atributos se utiliza para conectarlo las clases de C# a los objetos de Objective-C y la interfaz de usuario de elementos.

#### <a name="constructors"></a>Constructores

Dos constructores (correctamente expuestos para Objective-C) será necesarios para la clase de datos personalizada para que se puede leer desde una mesa de trabajo:

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

En primer lugar, se exponen los _vacía_ constructor en el método de C objetivo predeterminado de `init`.

A continuación, se exponen un `NSCoding` constructor conforme a que se usará para crear una nueva instancia del objeto de la mesa de trabajo cuando se pegue en el nombre exportado de `initWithCoder`.

Este constructor toma un `NSCoder` (tal como lo creó una `NSKeyedArchiver` cuando escribe en la mesa de trabajo), extrae los datos de clave/valor emparejada y lo guarda en los campos de propiedades de la clase de datos.

#### <a name="writing-to-the-pasteboard"></a>Escribir en la mesa de trabajo

Mediante la conformidad con la `INSPasteboardWriting` interfaz, es necesario exponer dos métodos y, opcionalmente, un tercer método, por lo que la clase se puede escribir en la mesa de trabajo.

En primer lugar, es necesario indicar el tipo de datos de la mesa de trabajo representaciones de la clase personalizada puede escribirse en:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Cada representación se identifica a través de un identificador de tipo uniforme (UTI), que es nada más que una cadena simple que identifica de forma única el tipo de datos que se presentan (para obtener más información, vea de Apple [uniforme de información general de los identificadores de tipo ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentación).

En nuestro formato personalizado, vamos a crear nuestra propia UTI: "com.xamarin.image-info" (tenga en cuenta que se encuentra en la notación inversa como un identificador de la aplicación). Nuestra clase también es capaz de escribir una cadena estándar en la mesa de trabajo (`public.text`). 

A continuación, necesitamos crear el objeto en el formato solicitado que obtiene escrito realmente en la mesa de trabajo:

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

Para el `public.text` tipo, se está devolviendo un sencillo, con el formato `NSString` objeto. Personalizada de `com.xamarin.image-info` tipo, estamos usando un `NSKeyedArchiver` y `NSCoder` interfaz para codificar la clase de datos personalizados a un archivo de clave/valor emparejado. Se necesita implementar el método siguiente para controlar realmente la codificación:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Los pares clave/valor individuales se escriben en el codificador y se puede descodificar con el segundo constructor que hemos agregado anteriormente.

Si lo desea, podemos incluimos el método siguiente para definir las opciones al escribir datos en la mesa de trabajo:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Actualmente, solo el `WritingPromised` opción está disponible y debe utilizarse cuando un tipo determinado es solo prometido y no se escribe realmente a la mesa de trabajo. Para obtener más información, consulte el [datos prometidos](#Promised_Data) sección anterior.

Con estos métodos en su lugar, el código siguiente puede utilizarse para escribir la clase personalizada en la mesa de trabajo:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>Lectura de la mesa de trabajo

Mediante la conformidad con la `INSPasteboardReading` interfaz, es necesario exponer tres métodos para que la clase de datos personalizados se puede leer de la mesa de trabajo.

En primer lugar, es necesario indicar el tipo de datos de la mesa de trabajo representaciones de la clase personalizada puede leer desde el Portapapeles:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Una vez más, estos se definen como UTIs simples y son los mismos tipos que se definen en el **escribir en la mesa de trabajo** sección anterior.

A continuación, es necesario indicar a la mesa de trabajo _cómo_ cada uno de los tipos UTI leerá usando el método siguiente:

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

Para el `com.xamarin.image-info` tipo, se está indicando la mesa de trabajo para descodificar el par clave/valor que se creó con la `NSKeyedArchiver` cuando se escribe en la clase a la mesa de trabajo mediante una llamada a la `initWithCoder:` constructor al que se agrega a la clase.

Por último, tenemos que agregar el siguiente método para leer el otras representaciones de datos UTI de la mesa de trabajo:

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

Puede haber ocasiones en necesite escribir elementos personalizados en la mesa de trabajo que no requieran la creación de una clase personalizada o desea proporcionar los datos en un formato común, sólo según sea necesario. En estas situaciones, puede usar un `NSPasteboardItem`.

Un `NSPasteboardItem` proporciona un control minucioso sobre los datos que se escriben en la mesa de trabajo y está diseñados para tener acceso temporal - debe eliminarse después de se ha escrito en la mesa de trabajo.

#### <a name="writing-data"></a>Escribir datos

Para escribir los datos personalizados a un `NSPasteboardItem` debe proporcionar un personalizado `NSPasteboardItemDataProvider`. Agregue una nueva clase al proyecto y llámelo **ImageInfoDataProvider.cs**. Edite el archivo y darle un aspecto similar al siguiente:

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

Igual que hicimos con la clase de datos personalizada, es necesario usar el `Register` y `Export` directivas para exponerlo a objetivo-C. La clase debe heredar de `NSPasteboardItemDataProvider` y debe implementar la `FinishedWithDataProvider` y `ProvideDataForType` métodos.

Use la `ProvideDataForType` método para proporcionar los datos que se encapsulará en el `NSPasteboardItem` como se indica a continuación:

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

En este caso, estamos almacenar dos piezas de información sobre la imagen (nombre y ImageType) y escribirlos en una cadena simple (`public.text`).

Tipo de escribir los datos en la mesa de trabajo, utilice el código siguiente:

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

#### <a name="reading-data"></a>Leer datos

Para leer los datos de la mesa de trabajo, utilice el código siguiente:

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

En este artículo ha tomado una visión detallada de trabajar con la mesa de trabajo en una aplicación Xamarin.Mac admite copiar y pegar las operaciones. En primer lugar, introdujo un ejemplo sencillo para familiarizarse con las operaciones de mesas de trabajo estándar. A continuación, que tardó una visión detallada de la mesa de trabajo y cómo leer y escribir datos de él. Por último, buscan en el uso de un tipo de datos personalizado para admitir la copia y pegado de tipos de datos complejos dentro de una aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [MacCopyPaste (ejemplo)](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guía de programación de la mesa de trabajo](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
