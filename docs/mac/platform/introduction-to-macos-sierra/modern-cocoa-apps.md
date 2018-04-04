---
title: Creación de macOS modernas aplicaciones
description: Este artículo tratan varias sugerencias, características y técnicas de que un desarrollador puede utilizar para compilar una aplicación moderna macOS en Xamarin.Mac.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 4eb4ff4a9e4784d816e2cbe8734e0422573cad92
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="building-modern-macos-apps"></a>Creación de macOS modernas aplicaciones

_Este artículo tratan varias sugerencias, características y técnicas de que un desarrollador puede utilizar para compilar una aplicación moderna macOS en Xamarin.Mac._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>Compilar modernas parece con vistas modernas

Un aspecto moderno incluirá un aspecto moderno de ventana y la barra de herramientas como la aplicación de ejemplo que se muestra a continuación:

[![](modern-cocoa-apps-images/content08.png "Un ejemplo de una aplicación de Mac moderna interfaz de usuario")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Habilitar completo tamaño vistas de contenido

Para lograr este aspecto en una aplicación Xamarin.Mac, el desarrollador desee usar un _tamaño completo (vista) contenido_, lo que significa que el contenido se extiende en las áreas de herramienta y la barra de título y se pueden difuminado automáticamente por macOS.

Para habilitar esta característica en el código, cree una clase personalizada para el `NSWindowController` y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

Esta característica también puede habilitarse en el generador de interfaz de Xcode, seleccione la ventana y la comprobación de **vista contenido completa tamaño**:

[![](modern-cocoa-apps-images/content01.png "Editar el guión gráfico principal en el generador de interfaz de Xcode")](modern-cocoa-apps-images/content01.png#lightbox)

Cuando utilice una vista de contenido de tamaño completo, el desarrollador que necesite desplazar el contenido por debajo de las áreas de barra de título y la herramienta para que el contenido específico (como etiquetas) no se desliza en ellos.

Para complicar este problema, las áreas de título y la barra de herramientas pueden tener una altura dinámica basándose en la acción que el usuario está realizando actualmente, la versión de macOS el usuario ha instalado o el hardware de Mac que se esté ejecutando la aplicación.

Como resultado, basta con codificación el desplazamiento al diseñar la interfaz de usuario no funcionará. El programador deberá adoptar un enfoque dinámico.

Apple ha incluido el [clave-valor Observable](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` propiedad de la `NSWindow` clase para obtener el área de contenido actual en el código. El programador puede utilizar este valor para colocar los elementos necesarios de forma manual cuando cambia el área de contenido.

La mejor solución es usar diseño automático y las clases de tamaño para colocar los elementos de interfaz de usuario en el código o el generador de interfaz.

Código similar al ejemplo siguiente puede usarse para colocar los elementos de interfaz de usuario usar Autodiseño y las clases de tamaño en el controlador de vista de la aplicación:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

Este código crea el almacenamiento para una restricción superior que se aplicarán a una etiqueta (`ItemTitle`) para asegurarse de que no pospone en el área de título y la barra de herramientas:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Reemplazando el controlador de vista `UpdateViewConstraints` método, el desarrollador puede probar para ver si ya se ha creado la restricción necesaria y crearla si es necesario.

Si una nueva restricción debe generarse, el `ContentLayoutGuide` propiedad de la ventana de control que debe restringirse se tiene acceso y convertir en un `NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

La propiedad TopAnchor de la `NSLayoutGuide` se tiene acceso a y, si está disponible, se utiliza para crear una nueva restricción con la cantidad de desplazamiento deseada y la nueva restricción estará activa para aplicarlo:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Habilitar las barras de herramientas simplificadas

Una ventana de macOS normal incluye un estándar de la que barra de título en se ejecuta al borde superior de la ventana. Si la ventana también incluye una barra de herramientas, se mostrará en esta área de la barra de título:

[![](modern-cocoa-apps-images/content02.png "Una barra de herramientas estándar de Mac")](modern-cocoa-apps-images/content02.png#lightbox)

Cuando se usa una barra de herramientas simplificado, desaparece el área de título y la barra de herramientas se desplaza hacia arriba en la posición de la barra de título, en línea con los botones de la ventana Cerrar, minimizar y maximizar:

[![](modern-cocoa-apps-images/content03.png "Una barra de herramientas de Mac simplificada")](modern-cocoa-apps-images/content03.png#lightbox)

Está habilitada la barra de herramientas simplificado invalidando el `ViewWillAppear` método de la `NSViewController` y lo que buscar, como los siguientes:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Este efecto se utiliza normalmente para _aplicaciones de caja de zapatos_ (aplicaciones de una ventana), como mapas, calendario, notas y las preferencias del sistema. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>Uso de controladores de la vista de accesorios

Dependiendo del diseño de la aplicación, el programador que le interese complementar la barra de título del área con un controlador de vista de accesorio que aparece derecho debajo de la barra de título y herramientas para proporcionar contextuales controla al usuario en función de la actividad son participa en:

[![](modern-cocoa-apps-images/content04.png "Un ejemplo accesorio View Controller")](modern-cocoa-apps-images/content04.png#lightbox)

Automáticamente se borrosa en el controlador de vista de accesorio y se cambia el tamaño por el sistema sin intervención del programador.

Para agregar un controlador de vista de accesorio, haga lo siguiente:

1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
2. Arrastre un **Custom View Controller** en la jerarquía de la ventana: 

    [![](modern-cocoa-apps-images/content05.png "Agregar un nuevo controlador de vista personalizada")](modern-cocoa-apps-images/content05.png#lightbox)
3. De diseño de la vista de accesorios interfaz de usuario: 

    [![](modern-cocoa-apps-images/content06.png "Diseñar la nueva vista")](modern-cocoa-apps-images/content06.png#lightbox)
4. Exponer la vista de accesorios como un **toma** y cualquier otro **acciones** o **tomas** para su interfaz de usuario: 

    [![](modern-cocoa-apps-images/content07.png "Agregar la salida necesaria")](modern-cocoa-apps-images/content07.png#lightbox)
5. Guarde los cambios.
6. Vuelva a Visual Studio para Mac sincronizar los cambios.

Editar el `NSWindowController` y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

Los puntos clave de este código son donde se establece la vista para la vista personalizada que se define en el generador de interfaz y se expone como un **toma**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

Y el `LayoutAttribute` que define _donde_ se mostrará el descriptor de acceso:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Dado que macOS ahora totalmente localizado, el `Left` y `Right` `NSLayoutAttribute` propiedades han quedado en desuso y se debe reemplazar con `Leading` y `Trailing`.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>Uso de ventanas con fichas

Además, el sistema macOS puede agregar controladores de la vista de descriptor de acceso a la ventana de la aplicación. Por ejemplo, para crear ventanas con fichas donde varios de Windows de la aplicación se combinan en una ventana virtual:

[![](modern-cocoa-apps-images/content08.png "Un ejemplo de una ventana con pestañas de Mac")](modern-cocoa-apps-images/content08.png#lightbox)

Normalmente, el desarrollador deben tener el uso de una acción limitada por pestañas de Windows en sus aplicaciones Xamarin.Mac, el sistema los controlará automáticamente del modo siguiente:

- Windows estarán automáticamente por pestañas cuando el `OrderFront` se invoca el método.
- Windows estarán automáticamente Untabbed cuando el `OrderOut` se invoca el método.
- En el código de todas las ventanas por pestañas todavía se consideran "visibles", sin embargo las fichas no superior están ocultos por el sistema mediante CoreGraphics.
- Use la `TabbingIdentifier` propiedad de `NSWindow` para agrupar ventanas en pestañas.
- Si es un `NSDocument` basado en aplicación, algunas de estas características se habilitarán automáticamente (por ejemplo, el botón de signo más que se va a agregar a la barra de pestañas) sin intervención del desarrollador.
- No es`NSDocument` aplicaciones pueden habilitar el botón "más" en el grupo de pestañas para agregar un nuevo documento invalidando el `GetNewWindowForTab` método de la `NSWindowsController`.

Reunir todas las piezas, la `AppDelegate` de una aplicación que desea usar en función del sistema por pestañas Windows podría ser similar al siguiente:

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        } 
        #endregion
    }
}
```

Donde la `NewDocumentNumber` propiedad mantiene un seguimiento del número de documentos nuevos creados y el `NewDocument` método crea un nuevo documento y lo muestra.

El `NSWindowController` , a continuación, podría ser similar:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

Donde el método estático `App` propiedad proporciona un acceso directo para ir a la `AppDelegate`. El `SetDefaultDocumentTitle` método establece un nuevo título de documentos en función del número de documentos nuevos creados.

El código siguiente, indica macOS que prefiere utilizar fichas de la aplicación y proporciona una cadena que permite a de Windows la aplicación que se desea agrupar en pestañas:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

Y el siguiente método de invalidación agrega un botón de signo más a la barra de pestañas que va a crear un nuevo documento al hacer clic en el usuario:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>Uso de animación de núcleo

Animación de núcleo es un motor de representación de gráficos de alto consumo que se integra en macOS. Animación de núcleo se ha optimizado para aprovechar las ventajas de la GPU (unidad de procesamiento de gráficos) disponible en el hardware de macOS moderna en lugar de ejecutar las operaciones de gráficos en la CPU, lo que podría ralentizar el equipo.

El `CALayer`, proporcionada por núcleo animación, que puede utilizarse para tareas como el desplazamiento rápida y fluida y las animaciones. Interfaz de usuario de una aplicación debe estar formado de varias capas para sacar el máximo provecho de animación de núcleo y subvistas.

Un `CALayer` objeto proporciona varias propiedades que permiten al desarrollador controlar lo que se presenta en la pantalla para el usuario, como:

- `Content` -Puede ser un `NSImage` o `CGImage` que proporciona el contenido de la capa.
- `BackgroundColor` : Establece el color de fondo de la capa como un `CGColor`
- `BorderWidth` : Establece el ancho del borde.
- `BorderColor` : Establece el color del borde.

Para utilizar los gráficos esenciales en la interfaz de usuario de la aplicación, debe contar con _capa de seguridad_ vistas, lo que Apple sugiere que el desarrollador debe habilitarla en la vista de contenido de la ventana. De esta manera, todas las vistas secundarias heredará automáticamente copia capa así.

Además, Apple sugiere utilizando vistas de nivel de seguridad en lugar de agregar un nuevo `CALayer` como una subcapa porque el sistema controlará automáticamente algunos de los parámetros necesarios (por ejemplo, las que necesitan una pantalla Retina Display).

Capa de copia se puede habilitar estableciendo la `WantsLayer` de un `NSView` a `true` o dentro del generador de interfaz de Xcode en el **Inspector de efectos de vista** activando **Core animación capa**:

[![](modern-cocoa-apps-images/content09.png "El Inspector de efectos de vista")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Volver a dibujar vistas con capas

Otro paso importante al utilizar vistas de nivel de seguridad en una aplicación de Xamarin.Mac es establecer el `LayerContentsRedrawPolicy` de la `NSView` a `OnSetNeedsDisplay` en el `NSViewController`. Por ejemplo:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Si el desarrollador no establece esta propiedad, la vista se volverá a dibujar cada vez que cambie su origen de marco, que no se desea por motivos de rendimiento. Con esta propiedad establecida en `OnSetNeedsDisplay` el desarrollador manualmente deben establecer `NeedsDisplay` a `true` para forzar que el contenido que se va a volver a dibujar, sin embargo.

Cuando una vista se marca como modificada, el sistema comprueba el `WantsUpdateLayer` propiedad de la vista. Si devuelve `true` la `UpdateLayer` método se llama, else el `DrawRect` se denomina método de la vista para actualizar el contenido de la vista.

Apple tiene las siguientes sugerencias para la actualización de un contenido de las vistas cuando sea necesario:

- Apple prefiere usar `UpdateLater` sobre `DrawRect` siempre que sea posible tal como proporciona una mejora significativa del rendimiento.
- Use el mismo `layer.Contents` para los elementos de interfaz de usuario que tengan un aspecto similar.
- Apple también preferir el desarrollador para crear su interfaz de usuario con vistas estándar como `NSTextField`, nuevo siempre que sea posible.

Usar `UpdateLayer`, cree una clase personalizada para el `NSView` y que el código de aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view 
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop" />

## <a name="using-modern-drag-and-drop"></a>Usar moderna arrastrar y colocar

Para presentar una experiencia moderna de arrastrar y colocar para el usuario, el desarrollador debe adoptar _flocado arrastre_ en su aplicación, arrastre y coloque las operaciones. Arrastre Flocking es donde cada archivo individual o un elemento arrastrado inicialmente aparece como un elemento individual que se dirige (el grupo bajo el cursor con un recuento del número de elementos) como el usuario continúa la operación de arrastre.

Si el usuario finaliza la operación de arrastre, los elementos individuales se Unflock y volver a sus ubicaciones originales.

Ejemplo de código siguiente permite arrastrar flocado en una vista personalizada:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

El efecto agrupado se logra mediante el envío de cada elemento que se está arrastrando a la `BeginDraggingSession` método de la `NSView` como un elemento independiente en una matriz.

Cuando se trabaja con un `NSTableView` o `NSOutlineView`, use la `PastboardWriterForRow` método de la `NSTableViewDataSource` clase para iniciar la operación de arrastrar:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...
            
            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

Esto permite al desarrollador proporcionar un individuo `NSDraggingItem` para todos los elementos de la tabla que se arrastra en lugar de al método anterior `WriteRowsWith` que escribir todas las filas como un único grupo en la mesa de trabajo.

Cuando se trabaja con `NSCollectionViews`, usar de nuevo el `PasteboardWriterForItemAt` método en contraposición a la `WriteItemsAt` método al arrastrar comienza.

El programador siempre se deben colocar los archivos de gran tamaño en la mesa de trabajo. Nuevo para macOS Sierra, _promesas de archivo_ permiten al desarrollador colocar las referencias a dado archivos en la mesa de trabajo que más adelante se habrán cumplido cuando el usuario finaliza la operación de colocar con el nuevo `NSFilePromiseProvider` y `NSFilePromiseReceiver` clases.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>Uso del seguimiento de eventos moderna

Para un elemento de interfaz de usuario (como un `NSButton`) que se ha agregado a un área de título o barra de herramientas, el usuario debe ser capaz de hacer clic en el elemento y desencadenar un evento como normal (por ejemplo, para mostrar una ventana emergente). Sin embargo, puesto que el elemento está también en el área de título o barra de herramientas, el usuario debe ser capaz de hacer clic y arrastrar el elemento para mover la ventana también.

Para lograr esto en el código, cree una clase personalizada para el elemento (como `NSButton`) e invalide el `MouseDown` evento como se indica a continuación:

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

Este código usa el `TrackEventsMatching` método de la `NSWindow` que se asocia el elemento de interfaz de usuario para interceptar las `LeftMouseUp` y `LeftMouseDragged` eventos. Para una `LeftMouseUp` el elemento de interfaz de usuario responde a eventos, como es habitual. Para el `LeftMouseDragged` eventos, el evento se pasa a la `NSWindow`del `PerformWindowDrag` método para mover la ventana en la pantalla.

Llamar a la `PerformWindowDrag` método de la `NSWindow` clase proporciona las siguientes ventajas:

- Permite usar de la ventana Mover, incluso si se ha producido un error en la aplicación (por ejemplo, cuando un profundo bucle de procesamiento).
- Cambio de espacio funcionará según lo esperado.
- La barra de espacios se mostrará con normalidad.
- Ventana de ajuste y alineación funcionan normalmente.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>Usar controles de vista de contenedor moderna

macOS Sierra proporciona muchas mejoras modernas a los controles de vista de contenedor existentes disponibles en versiones anteriores del sistema operativo.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Mejoras de la vista de tabla

El desarrollador debe utilizar siempre el nuevo `NSView` según la versión de controles de vista de contenedor como `NSTableView`. Por ejemplo:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

Esto permite para que las acciones personalizadas de fila de tabla adjuntarlo a especificado de filas de la tabla (por ejemplo, Deslizar rápidamente derecha para eliminar la fila). Para habilitar este comportamiento, invalidar la `RowActions` método de la `NSTableViewDelegate`:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

El método estático `NSTableViewRowAction.FromStyle` se utiliza para crear una nueva acción de fila de tabla de los estilos siguientes:

- `Regular` -Se realiza una acción no destructiva estándar, como editar contenido de la fila.
- `Destructive` -Realiza una acción destructiva como eliminar la fila de la tabla. Estas acciones se representa con un fondo rojo.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Mejoras de la vista de desplazamiento 

Cuando se utiliza una vista de desplazamiento (`NSScrollView`) directamente, o como parte de otro control (como `NSTableView`), puede deslizar el contenido de la vista de desplazamiento en las áreas de título y la barra de herramientas en una aplicación de Xamarin.Mac con un aspecto moderno y vistas.

Como resultado, el primer elemento en el área de contenido de la vista de desplazamiento puede quedar oculto parcial en el área de título y la barra de herramientas.

Para corregir este problema, Apple ha agregado dos nuevas propiedades a la `NSScrollView` clase:

- `ContentInsets` : Permite al desarrollador proporcionar un `NSEdgeInsets` objeto que define el desplazamiento que se aplicará a la parte superior de la vista de desplazamiento.
- `AutomaticallyAdjustsContentInsets` -If `true` la vista de desplazamiento controlará automáticamente el `ContentInsets` para el programador.

Mediante el uso de la `ContentInsets` el desarrollador puede ajustar el inicio de la vista de desplazamiento para permitir la inclusión de accesorios como:

- Un indicador de ordenación, como se muestra en la aplicación de correo electrónico.
- Un campo de búsqueda.
- Un botón de actualización o actualización.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Diseño automático y la localización de aplicaciones modernas

Apple ha incluido varias tecnologías en Xcode que permiten a los programadores crear fácilmente una aplicación de uso internacional macOS. Xcode ahora permite al desarrollador separar texto orientadas al usuario de diseño de la interfaz de usuario de la aplicación en sus archivos de guión gráfico y proporciona herramientas para mantener esta separación si cambia la interfaz de usuario.

Para obtener más información, vea de Apple [internacionalización y localización guía](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html).

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Implementación de Base de internacionalización 

Mediante la implementación de internacionalización de Base, el desarrollador puede proporcionar un único archivo de guión gráfico para representar la interfaz de usuario de la aplicación y apartar todas las cadenas de cara al usuario. 

Cuando el programador crea el archivo del guión gráfico inicial (o archivos) que definen la interfaz de usuario de la aplicación, se compilará en la sección de internacionalización de Base (el idioma que habla el desarrollador).

A continuación, el programador puede exportar adaptaciones y las cadenas de internacionalización de Base (en el diseño de la interfaz de usuario de guión gráfico) que se pueden traducir a varios idiomas.

Más adelante, se pueden importar estos adaptaciones y Xcode generará los archivos de la cadena específica del lenguaje para el guión gráfico.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Implementar el diseño automático para admitir la localización

Dado que las versiones localizadas de cadena de valores pueden tener tamaños muy diferentes o dirección de lectura, el desarrollador debe usar diseño automático para la posición y el tamaño de la interfaz de usuario de la aplicación en un archivo de guión gráfico.

Apple sugerir las acciones siguientes:

- **Quitar las restricciones de ancho fijo** -todas las vistas basadas en el texto deberían poder cambiar el tamaño basándose en su contenido. Ancho fijo vista puede recortar su contenido en idiomas específicos.
- **Usar tamaños de contenido intrínseco** - de predeterminado basado en texto vistas tendrán tamaño automático para ajustar su contenido. Para ver basado en texto que no son de tamaño correctamente, selecciónelos en el generador de interfaz de Xcode, a continuación, elija **editar** > **tamaño para ajustarse a contenido**.
- **Aplicar iniciales y finales atributos** : dado que puede cambiar la dirección del texto según el idioma del usuario, use la nueva `Leading` y `Trailing` atributos de las restricciones en lugar de la existente `Right` y `Left` atributos. `Leading` y `Trailing` se ajustará automáticamente en función de la dirección de idiomas.
- **Vistas de PIN a vistas adyacentes** -Esto permite que las vistas cambiar la posición y cambian de tamaño cuando las vistas alrededor de ellos cambian en respuesta al idioma seleccionado.
- **No configurar un mínimo de Windows o tamaños máximos** -permite Windows cambiar tamaño según el idioma seleccionado cambia el tamaño de sus áreas de contenido.
- **Probar cambios de diseño constantemente** : durante el desarrollo de la aplicación debe probarse constantemente en distintos idiomas. Vea de Apple [pruebas internacionalizados su aplicación](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) documentación para obtener más detalles.
- **Usar NSStackViews para Pin vistas conjuntamente**  -  `NSStackViews` permite que el contenido y su contenido para desplazar y aumentan de forma previsible cambian tamaño según el idioma seleccionado.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Localizar en el generador de interfaz de Xcode

Apple ha desarrollado varias características en el generador de interfaz de Xcode que el programador puede utilizar al diseñar o modificar la interfaz de usuario de una aplicación para admitir la localización. El **dirección del texto** sección de la **atributo Inspector** permite al desarrollador proporcionar sugerencias sobre cómo se debe usar y actualicen en una vista basada en texto, seleccione dirección (como `NSTextField`):

[![](modern-cocoa-apps-images/content10.png "Las opciones de dirección del texto")](modern-cocoa-apps-images/content10.png#lightbox)

Hay tres valores posibles para la **dirección del texto**:

- **Natural** -el diseño se basa en la cadena asignada al control.
- **De izquierda a derecha** -siempre se fuerza el diseño de izquierda a derecha.
- **De derecha a izquierda** -siempre se fuerza el diseño de derecha a izquierda.

Hay dos valores posibles para la **diseño**:

- **De izquierda a derecha** -el es siempre de izquierda a derecha.
- **De derecha a izquierda** -siempre es el diseño de derecha a izquierda.

Normalmente estos no se deben cambiar a menos que se requiere una alineación específica.

El **reflejado** propiedad indica al sistema que voltear propiedades de control específico (por ejemplo, la posición de la imagen de celda). Tiene tres valores posibles:

- **Automáticamente** -la posición cambiará automáticamente en función de la dirección del idioma seleccionado.
- **En la interfaz de derecha a izquierda** -sólo se cambiará la posición de derecha a izquierda lenguajes basados en.
- **Nunca** -nunca cambiará la posición.

Si el desarrollador ha especificado **Center**, **justificar** o **completa** alineación en el contenido de una vista basada en texto, nunca serán volteado idioma según seleccionado.

Antes de macOS Sierra, los controles creados en el código podrían no reflejar automáticamente. El desarrollador tenía que usar código similar al siguiente para controlar la creación de reflejo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

Donde la `Alignment` y `ImagePosition` se establecen en función de la `UserInterfaceLayoutDirection` del control.

macOS Sierra agrega varios constructores comodidad nuevo (mediante el método estático `CreateButton` método) que toman varios parámetros (por ejemplo, título, imagen y acción) y automáticamente reflejará correctamente. Por ejemplo:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>Uso de repeticiones del sistema

Aplicaciones modernas macOS pueden adoptar una nueva apariencia oscuro de interfaz que funciona bien para las aplicaciones de creación, edición o presentación de imagen:

[![](modern-cocoa-apps-images/content11.png "Un ejemplo de una interfaz de usuario de ventana Mac oscuro")](modern-cocoa-apps-images/content11.png#lightbox)

Esto puede hacerse agregando una línea de código antes de que aparezca la ventana. Por ejemplo:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...
    
        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

El método estático `GetAppearance` método de la `NSAppearance` clase se utiliza para obtener un aspecto con nombre del sistema (en este caso `NSAppearance.NameVibrantDark`).

Apple tiene las siguientes sugerencias para el uso de aspecto del sistema:

- Preferir valores codificados en colores con nombre (como `LabelColor` y `SelectedControlColor`).
- Usar el estilo de control estándar de sistema siempre que sea posible.

Una aplicación de Mac OS que use el aspecto del sistema automáticamente funcionará correctamente para los usuarios que han habilitado las características de accesibilidad de la aplicación de preferencias del sistema. Como resultado, Apple sugiere que el desarrollador debe utilizar siempre los aspectos del sistema en sus aplicaciones de Mac OS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Diseñar interfaces de usuario con guiones gráficos

Guiones gráficos permiten al desarrollador no solo diseño de flujo de los elementos individuales que componen la interfaz de usuario de una aplicación, pero, para visualizar y diseñar la interfaz de usuario y la jerarquía de los elementos especificados.

Los controladores permiten al desarrollador recopile los elementos en una unidad de composición y Segues abstracta y quitar el típico "búsqueda de código" necesarios para mover a lo largo de la jerarquía de vista:

[![](modern-cocoa-apps-images/content12.png "Edición de la interfaz de usuario en el generador de interfaz de Xcode")](modern-cocoa-apps-images/content12.png#lightbox)

Para obtener más información, vea nuestra [Introducción a guiones gráficos](~/mac/platform/storyboards/index.md) documentación.

Existen muchas instancias donde una escena determinada definida en un guión gráfico, requieren datos de una escena anterior en la jerarquía de vista. Apple tiene las siguientes sugerencias para pasar información entre bastidores:

- Dependencias de datos siempre deben cascada hacia abajo por la jerarquía.
- Evitar dependencias estructurales de interfaz de usuario de codificar, como esto limita la flexibilidad de la interfaz de usuario.
- Usar Interfaces de C# para proporcionar las dependencias de datos genéricos.

El controlador de vista que actúa como el origen de Segue, puede invalidar el `PrepareForSegue` método y lleve a cabo cualquier inicialización necesaria (por ejemplo, para pasar datos) antes de la Segue se ejecuta para mostrar el controlador de vista de destino. Por ejemplo:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Para obtener más información, vea nuestra [Segues](~/mac/platform/storyboards/indepth.md#Segues) documentación.

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>Propagación de acciones

En función del diseño de la aplicación de Mac OS, puede haber ocasiones cuando el mejor controlador para una acción en un control de interfaz de usuario podría estar en otra parte de la jerarquía de la interfaz de usuario. Esto ocurre normalmente de los menús y elementos de menú que se encuentran en su propios escena, independiente del resto de la interfaz de usuario de la aplicación.

Para controlar esta situación, el desarrollador puede crear una acción personalizada y pasar la acción de la cadena de respuesta. Para obtener más información, consulte nuestro [trabajar con acciones de ventana personalizado](~/mac/user-interface/menu.md) documentación.

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Características de Mac moderna

Apple ha incluido varias características orientadas al usuario en macOS Sierra que permiten a los programadores aprovechar al máximo la plataforma de Mac, como:

- **NSUserActivity** -Esto permite que describen la actividad que el usuario está ocupado actualmente en la aplicación. `NSUserActivity` se creó inicialmente para admitir la entrega, donde una actividad que se inició en uno de los dispositivos del usuario podría recoge y continúa en otro dispositivo. `NSUserActivity` por lo tanto, funciona igual en macOS tal como se hace en iOS, consulte nuestro [Introducción a la entrega](~/ios/platform/handoff.md) iOS documentación para obtener más detalles.
- **Siri Mac** -Siri usa la actividad actual (`NSUserActivity`) para proporcionar un contexto para los comandos que se puede emitir un usuario.
- **Estado de restauración** : cuando el usuario se cierra una aplicación en Mac OS y versiones posteriores, a continuación, relaunches de él, la aplicación no se realizará automáticamente se devuelve a su estado anterior. El programador puede utilizar la API de restauración de estado para codificar y restaurar los estados transitorios de interfaz de usuario antes de la interfaz de usuario se muestra al usuario. Si la aplicación es `NSDocument` según, restauración del estado se controla de forma automática. Para habilitar la restauración del estado de no -`NSDocument` aplicaciones, establecer el `Restorable` de la `NSWindow` clase a `true`.
- **Documentos en la nube** -antes de macOS Sierra, una aplicación tenía que explícitamente inscribirse para trabajar con documentos en iCloud del usuario unidad. En Mac OS Sierra el usuario **Desktop** y **documentos** carpetas pueden sincronizarse con su iCloud unidad automáticamente por el sistema. Como resultado, pueden eliminar las copias locales de documentos para liberar espacio en el equipo del usuario. `NSDocument` aplicaciones controlará automáticamente este cambio. Todos los demás tipos de aplicación debe utilizar un `NSFileCoordinator` para sincronizar la lectura y escritura de documentos.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata varias sugerencias, características y técnicas de que un desarrollador puede utilizar para compilar una aplicación moderna macOS en Xamarin.Mac.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de macOS](https://developer.xamarin.com/samples/mac/)
