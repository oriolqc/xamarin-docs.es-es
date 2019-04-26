---
title: Creación de aplicaciones modernas de macOS
description: En este artículo se trata varias sugerencias, características y técnicas de que un desarrollador puede usar para crear una aplicación moderna de macOS en Xamarin.Mac.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 53bfc9f147b6cf369b8f5ce8d1257dbaf6b0f807
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032903"
---
# <a name="building-modern-macos-apps"></a>Creación de aplicaciones modernas de macOS

_En este artículo se trata varias sugerencias, características y técnicas de que un desarrollador puede usar para crear una aplicación moderna de macOS en Xamarin.Mac._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>Creación de aspecto moderno con vistas modernas

Un aspecto moderno incluirá una apariencia moderna de ventana y la barra de herramientas, como la aplicación de ejemplo que se muestra a continuación:

[![](modern-cocoa-apps-images/content08.png "Un ejemplo de una aplicación de Mac moderna interfaz de usuario")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Habilitar completo tamaño vistas de contenido

Para lograr este aspecto en una aplicación de Xamarin.Mac, el desarrollador desea utilizar un _vista completa de contenido de tamaño_, lo que significa que amplía el contenido de las áreas de la herramienta y la barra de título y macOS estará borrosa automáticamente.

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

Esta característica también se puede habilitar en Interface Builder de Xcode mediante la selección de la ventana y la comprobación **vista contenido completa tamaño**:

[![](modern-cocoa-apps-images/content01.png "Editar el guión gráfico principal en Interface Builder de Xcode")](modern-cocoa-apps-images/content01.png#lightbox)

Cuando se usa una vista de contenido de tamaño completo, el desarrollador que deba el contenido por debajo de las áreas de barra de título y la herramienta de desplazamiento para que no se desliza contenido específico (por ejemplo, etiquetas) debajo de ellos.

Para complicar este problema, las áreas de título y la barra de herramientas pueden tener una altura dinámica basándose en la acción que el usuario está realizando actualmente, la versión de macOS que el usuario ha instalado o el hardware de Mac que se está ejecutando la aplicación.

Como resultado, simplemente codificar el desplazamiento al diseñar la interfaz de usuario no funcionará. El desarrollador tendrá que adoptar un enfoque dinámico.

Apple ha incluido la [Observable de pares clave-valor](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` propiedad de la `NSWindow` clase para obtener el área de contenido actual en el código. El programador puede utilizar este valor para colocar los elementos necesarios de forma manual cuando cambia el área de contenido.

La mejor solución es usar el diseño automático y las clases de tamaño para colocar los elementos de interfaz de usuario en el código o Interface Builder.

Código similar al ejemplo siguiente puede utilizarse para colocar los elementos de interfaz de usuario con diseño automático y las clases de tamaño en el controlador de vista de la aplicación:

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

Este código crea el almacenamiento para una restricción superior que se aplicarán a una etiqueta (`ItemTitle`) para asegurarse de que no retrasar en el área de título y la barra de herramientas:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Reemplazando el controlador de vista `UpdateViewConstraints` método, el desarrollador puede probar para ver si ya se ha compilado la restricción necesaria y crearla si fuera necesario.

Si necesita una nueva restricción de generarse el `ContentLayoutGuide` propiedad de la ventana se tiene acceso y se convierte en el control que es necesario restringir un `NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

La propiedad TopAnchor de la `NSLayoutGuide` se obtiene acceso y si está disponible, se usa para crear una nueva restricción con la cantidad de desplazamiento deseada y la nueva restricción se activará para aplicarla:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Habilitación de las barras de herramientas simplificados

MacOS ventana normal incluye un estándar de la que barra de título se ejecuta a lo largo de hasta el borde superior de la ventana. Si la ventana también incluye una barra de herramientas, se mostrará en esta área de barra de título:

[![](modern-cocoa-apps-images/content02.png "Una barra de herramientas estándar de Mac")](modern-cocoa-apps-images/content02.png#lightbox)

Cuando usa una barra de herramientas simplificado, desaparece el área de título y la barra de herramientas se mueve hacia arriba en la posición de la barra de título, en línea con los botones de la ventana Cerrar, minimizar y maximizar:

[![](modern-cocoa-apps-images/content03.png "Una barra de herramientas simplificado de Mac")](modern-cocoa-apps-images/content03.png#lightbox)

Está habilitada la barra de herramientas simplificado invalidando el `ViewWillAppear` método de la `NSViewController` y haciendo que se parezca a lo siguiente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Este efecto se utiliza normalmente para _Shoebox aplicaciones_ (aplicaciones de una ventana), como mapas, calendario, notas y las preferencias del sistema. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>Uso de controladores de vista de accesorios

Dependiendo del diseño de la aplicación, el desarrollador también puede complementar la barra de título área con un controlador de vista de accesorio que aparece debajo del área de barra de título o herramienta para proporcionar ayuda contextual controla al usuario en función de la actividad son participa en:

[![](modern-cocoa-apps-images/content04.png "Un ejemplo de controlador de vista de accesorio")](modern-cocoa-apps-images/content04.png#lightbox)

El controlador de vista de accesorio automáticamente se difuminan mucho y cambiar de tamaño por el sistema sin intervención del programador.

Para agregar un controlador de vista de accesorio, haga lo siguiente:

1. Haga doble clic en el archivo `Main.storyboard` en el **Explorador de soluciones** para abrirlo para su edición.
2. Arrastre un **Custom View Controller** en la jerarquía de la ventana: 

    [![](modern-cocoa-apps-images/content05.png "Agregar un nuevo controlador de vista personalizado")](modern-cocoa-apps-images/content05.png#lightbox)
3. De diseño de la vista de accesorios interfaz de usuario: 

    [![](modern-cocoa-apps-images/content06.png "Diseño de la nueva vista")](modern-cocoa-apps-images/content06.png#lightbox)
4. Exponer la vista accesorio como un **toma** y cualquier otro **acciones** o **salidas** para su interfaz de usuario: 

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

Los puntos clave de este código son donde se establece la vista de la vista personalizada que se ha definido en el generador de interfaz y se exponen como un **toma**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

Y el `LayoutAttribute` que define _donde_ se mostrará el accesorio:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Dado que macOS ahora es totalmente localizado, el `Left` y `Right` `NSLayoutAttribute` propiedades han quedado en desuso y se debe reemplazar con `Leading` y `Trailing`.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>Uso de Windows con pestañas

Además, el sistema de macOS puede agregar controladores de vista de accesorio para la ventana de la aplicación. Por ejemplo, para crear pestañas Windows donde varios de Windows la aplicación se combinan en una ventana virtual:

[![](modern-cocoa-apps-images/content08.png "Un ejemplo de una ventana con pestañas de Mac")](modern-cocoa-apps-images/content08.png#lightbox)

Normalmente, el desarrollador tendrá que tomar el uso de una acción limitada por pestañas de Windows en sus aplicaciones de Xamarin.Mac, el sistema los controlará automáticamente del modo siguiente:

- Windows será automáticamente por fichas cuando el `OrderFront` se invoca el método.
- Windows automáticamente será Untabbed cuando el `OrderOut` se invoca el método.
- Sin embargo en el código todavía se consideran "visibles" todas las ventanas por pestañas, las pestañas que no sea superior están ocultos por el sistema mediante CoreGraphics.
- Use la `TabbingIdentifier` propiedad de `NSWindow` al grupo Windows en las pestañas.
- Si es un `NSDocument` basado en aplicación, varias de estas características se habilitarán automáticamente (por ejemplo, el botón del signo más que se va a agregar a la barra de pestañas) sin intervención del programador.
- Que no sean de`NSDocument` en función de las aplicaciones pueden habilitar el botón "más" en el grupo de pestañas para agregar un nuevo documento invalidando el `GetNewWindowForTab` método de la `NSWindowsController`.

Reúne todas las piezas, la `AppDelegate` de una aplicación que desea usar en función del sistema con fichas Windows podría ser similar al siguiente:

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

Donde el `NewDocumentNumber` propiedad realiza un seguimiento de la cantidad de nuevos documentos creados y el `NewDocument` método crea un nuevo documento y lo muestra.

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

Donde estático `App` propiedad proporciona un acceso directo para llegar a la `AppDelegate`. El `SetDefaultDocumentTitle` método establece un nuevo título de documentos en función del número de documentos nuevos creados.

El código siguiente, indica a macOS que prefiere utilizar fichas de la aplicación y proporciona una cadena que permite que de Windows la aplicación que se desea agrupar en pestañas:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

Y el método de invalidación siguiente agrega un botón del signo más a la barra de pestañas que va a crear un nuevo documento cuando el usuario hace clic en:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>Uso de la animación principal

Animación básica es un motor de representación de gráficos potente que está integrado en macOS. Animación básica se ha optimizado para aprovechar las ventajas de la GPU (unidad de procesamiento de gráficos) disponible en el hardware de modernas de macOS en lugar de ejecutar las operaciones de gráficos en la CPU, lo que podría ralentizar el equipo.

El `CALayer`, proporcionada por la animación principal, se puede usar para tareas como el desplazamiento rápido y fluido y animaciones. Interfaz de usuario de una aplicación debe estar compuesta de varias capas para sacar el máximo provecho de animación básica y subvistas.

Un `CALayer` objeto proporciona varias propiedades que permiten que el desarrollador puede controlar lo que se presenta en la pantalla para el usuario, como:

- `Content` -Puede ser un `NSImage` o `CGImage` que proporciona el contenido de la capa.
- `BackgroundColor` : Establece el color de fondo de la capa como un `CGColor`
- `BorderWidth` : Establece el ancho del borde.
- `BorderColor` : Establece el color del borde.

Para utilizar los gráficos esenciales en la interfaz de usuario de la aplicación, debe usar _capa de seguridad_ vistas, que Apple sugiere que el desarrollador siempre debe habilitar en Vista la ventana de contenido. De este modo, todas las vistas secundarias heredará automáticamente capa de seguridad también.

Además, Apple sugiere el uso de vistas de nivel de seguridad en lugar de agregar un nuevo `CALayer` como una subcapa porque el sistema controlará automáticamente algunos de los valores requeridos (como las necesarias en una pantalla retina).

Capa de seguridad puede ser habilitada configurando el `WantsLayer` de un `NSView` a `true` o dentro Interface Builder de Xcode en la **View Effects Inspector** activando **Core animación capa**:

[![](modern-cocoa-apps-images/content09.png "El Inspector de efectos de vista")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Volver a dibujar las vistas con capas

Otro paso importante al utilizar las vistas de capa de seguridad en una aplicación de Xamarin.Mac es establecer el `LayerContentsRedrawPolicy` de la `NSView` a `OnSetNeedsDisplay` en el `NSViewController`. Por ejemplo:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Si el desarrollador no establece esta propiedad, la vista se volverá a dibujar cada vez que cambia su origen de marco, que no es el deseado por motivos de rendimiento. Con esta propiedad establecida en `OnSetNeedsDisplay` el desarrollador manualmente tendrá que establecer `NeedsDisplay` a `true` para forzar que el contenido para volver a dibujar, sin embargo.

Cuando una vista se marca como modificada, el sistema comprueba el `WantsUpdateLayer` propiedad de la vista. Si devuelve `true` el `UpdateLayer` , else, se denomina método la `DrawRect` método de la vista se llama para actualizar el contenido de la vista.

Apple tiene las siguientes sugerencias para la actualización de contenido de las vistas cuando sea necesario:

- Apple prefiere usar `UpdateLater` sobre `DrawRect` siempre que sea posible ya que proporciona una mejora considerable del rendimiento.
- Usar el mismo `layer.Contents` para los elementos de interfaz de usuario con un aspecto similares.
- Apple también prefiere el desarrollador para crear su interfaz de usuario mediante las vistas estándar como `NSTextField`, nuevo siempre que sea posible.

Para usar `UpdateLayer`, cree una clase personalizada para el `NSView` y hacer que el código de aspecto similar al siguiente:

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

## <a name="using-modern-drag-and-drop"></a>Usar la característica moderna de arrastrar y colocar

Para presentar una experiencia moderna de arrastrar y colocar para el usuario, el desarrollador debe adoptar _arrastre aves_ en su aplicación, arrastre y coloque las operaciones. Arrastre Flocking es donde aparece cada archivo individual o un elemento arrastrado inicialmente como un elemento individual que se dirige (grupo bajo el cursor con un recuento del número de elementos) como el usuario continúa la operación de arrastre.

Si el usuario finaliza la operación de arrastre, los elementos individuales se Unflock y vuelva a sus ubicaciones originales.

Ejemplo de código siguiente permite arrastrar aves en una vista personalizada:

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

El efecto agrupado se logra mediante el envío de cada elemento que se arrastran a la `BeginDraggingSession` método de la `NSView` como un elemento independiente en una matriz.

Cuando se trabaja con un `NSTableView` o `NSOutlineView`, utilice el `PastboardWriterForRow` método de la `NSTableViewDataSource` clase para iniciar la operación de arrastrar:

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

Esto permite al desarrollador proporcionar un individuo `NSDraggingItem` para todos los elementos de la tabla que se está arrastrando el método anterior en lugar de `WriteRowsWith` que escribir todas las filas como un único grupo en la mesa de trabajo.

Cuando se trabaja con `NSCollectionViews`, volver a usar el `PasteboardWriterForItemAt` método en contraposición a la `WriteItemsAt` método al arrastrar comienza.

El desarrollador siempre debe evitar colocar archivos de gran tamaño en la mesa de trabajo. Nuevo en macOS Sierra, _archivo promesas_ permiten al programador colocar las referencias a dado archivos en la mesa de trabajo más adelante se cumplirá cuando el usuario termina la operación de colocar con el nuevo `NSFilePromiseProvider` y `NSFilePromiseReceiver` clases.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>Uso del seguimiento de eventos moderna

Para un elemento de interfaz de usuario (como un `NSButton`) que se ha agregado a un área de título o la barra de herramientas, el usuario debe ser capaz de hacer clic en el elemento y tiene desencadenar un evento como normal (por ejemplo, para mostrar una ventana emergente). Sin embargo, dado que el elemento también está en el área de título o la barra de herramientas, el usuario debe ser capaz de hacer clic y arrastrar el elemento para mover la ventana también.

Para lograr esto en el código, cree una clase personalizada para el elemento (como `NSButton`) e invalidar la `MouseDown` eventos como sigue:

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

Este código usa el `TrackEventsMatching` método de la `NSWindow` que está asociado el elemento de interfaz de usuario para interceptar el `LeftMouseUp` y `LeftMouseDragged` eventos. Para una `LeftMouseUp` el elemento de interfaz de usuario responde a eventos, como es habitual. Para el `LeftMouseDragged` evento, el evento se pasa a la `NSWindow`del `PerformWindowDrag` método para mover la ventana en pantalla.

Una llamada a la `PerformWindowDrag` método de la `NSWindow` clase proporciona las siguientes ventajas:

- Permite para que la ventana Mover, incluso si se ha bloqueado la aplicación (por ejemplo, cuando un profundo bucle de procesamiento).
- Cambiar espacio funcionará según lo previsto.
- Se mostrará la barra de espacios con normalidad.
- Ventana de ajuste y alineación funcionan con normalidad.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>Con los controles de vista de contenedor moderna

macOS Sierra proporciona muchas mejoras modernas a los controles de vista de contenedor existentes disponibles en versiones anteriores del sistema operativo.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Mejoras de la vista de tabla

El desarrollador siempre debe usar el nuevo `NSView` basada en la versión de vista de controles del contenedor como `NSTableView`. Por ejemplo:

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

Esto permite acciones de fila de tabla personalizado adjuntarlo a dado de filas de la tabla (por ejemplo, Deslizar el derecho a eliminar la fila). Para habilitar este comportamiento, invalidar el `RowActions` método de la `NSTableViewDelegate`:

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

Estático `NSTableViewRowAction.FromStyle` se usa para crear una nueva acción de fila de tabla de los estilos siguientes:

- `Regular` -Realiza una acción no destructiva estándar, como editar contenido de la fila.
- `Destructive` -Realiza una acción destructiva como eliminar la fila de la tabla. Estas acciones se representa con un fondo rojo.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Mejoras de la vista de desplazamiento 

Cuando se usa una vista de desplazamiento (`NSScrollView`) directamente, o como parte de otro control (como `NSTableView`), puede deslizar el contenido de la vista de desplazamiento en las áreas de título y la barra de herramientas en una aplicación de Xamarin.Mac con un aspecto moderno y vistas.

Como resultado, el primer elemento en el área de contenido de la vista de desplazamiento puede quede parcialmente tapado por el área de título y la barra de herramientas.

Para corregir este problema, Apple ha agregado dos nuevas propiedades a la `NSScrollView` clase:

- `ContentInsets` : Permite al desarrollador proporcionar un `NSEdgeInsets` objeto que define el desplazamiento que se aplicará a la parte superior de la vista de desplazamiento.
- `AutomaticallyAdjustsContentInsets` -If `true` controlará automáticamente la vista de desplazamiento del `ContentInsets` para el desarrollador.

Mediante el uso de la `ContentInsets` el desarrollador puede ajustar el inicio de la vista de desplazamiento para permitir la inclusión de accesorios, como:

- Un indicador de ordenación, como se muestra en la aplicación de correo.
- Un campo de búsqueda.
- Un botón de actualización o actualización.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Diseño automático y localización en las aplicaciones modernas

Apple ha incluido varias tecnologías en Xcode que permiten al desarrollador a crear fácilmente una aplicación internacionalizada de macOS. Xcode ahora permite al desarrollador separar el texto de cara al usuario desde el diseño de interfaz de usuario de la aplicación en sus archivos de guión gráfico y proporciona herramientas para mantener esta separación si cambia la interfaz de usuario.

Para obtener más información, consulte Apple [internacionalización y localización guía](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html).

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Implementación de Base de internacionalización 

Mediante la implementación de internacionalización de Base, el desarrollador puede proporcionar un único archivo de guión gráfico para representar la interfaz de usuario de la aplicación y separar todas las cadenas de cara al usuario. 

Cuando el programador crea el archivo de guión gráfico inicial (o archivos) que definen la interfaz de usuario de la aplicación, se compilará en la internacionalización de Base (el lenguaje que habla el desarrollador).

A continuación, el programador puede exportar localizaciones y las cadenas de internacionalización de Base (en el diseño de la interfaz de usuario de guión gráfico) que pueden traducirse en varios idiomas.

Posteriormente, se pueden importar estos localizaciones y Xcode generará los archivos de la cadena específica del lenguaje para el guión gráfico.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Implementar el diseño automático para admitir la localización

Dado que las versiones localizadas de cadena de valores pueden tener considerablemente distintos tamaños o dirección de lectura, debe usar el programador diseño automático para colocar y cambiar el tamaño de interfaz la aplicación de usuario en un archivo de guión gráfico.

Apple sugiere lo siguiente:

- **Quitar las restricciones de ancho fijo** -deben permitir todas las vistas basadas en texto para cambiar el tamaño en función de su contenido. Ancho fijo vista puede recortar su contenido en idiomas específicos.
- **Usar tamaños de contenido intrínseco** - al valor predeterminado basado en texto vistas will tamaño automático para ajustar su contenido. Para la vista de texto que no son de tamaño correctamente, selecciónelos en Interface Builder de Xcode, a continuación, elija **editar** > **tamaño para ajustarse a contenido**.
- **Aplicar iniciales y finales atributos** : ya puede cambiar la dirección del texto según el idioma del usuario, use la nueva `Leading` y `Trailing` atributos de las restricciones existentes en lugar de `Right` y `Left` atributos. `Leading` y `Trailing` ajustará automáticamente en función de la dirección de idiomas.
- **Vistas de PIN a vistas adyacentes** : Esto permite que las vistas para cambiar la posición y cambio de tamaño a medida que cambian las vistas en torno a ellas en la respuesta para el idioma seleccionado.
- **No establezca un mínimo de Windows o el tamaño máximo de** -permite Windows cambiar tamaño como el idioma seleccionado cambia el tamaño de sus áreas de contenido.
- **Probar los cambios de diseño constantemente** - durante el desarrollo de la aplicación se debe probar constantemente en diferentes idiomas. Consulte Apple [pruebas internacionalizar su aplicación](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) documentación para obtener más detalles.
- **Usar NSStackViews para Pin vistas conjuntamente**  -  `NSStackViews` permite que su contenido en la tecla MAYÚS y crecer de forma predecible y el contenido de cambian el tamaño según el idioma seleccionado.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Localizar en Interface Builder de Xcode

Apple ofrece varias características en Interface Builder de Xcode que el desarrollador puede usar al diseñar o modificar la interfaz de usuario de una aplicación para admitir la localización. El **dirección del texto** sección de la **Inspector de atributos** permite al desarrollador proporcionar sugerencias sobre cómo debe usarse y actualizada en una vista de texto, seleccione dirección (como `NSTextField`):

[![](modern-cocoa-apps-images/content10.png "Las opciones de dirección del texto")](modern-cocoa-apps-images/content10.png#lightbox)

Hay tres valores posibles para el **dirección del texto**:

- **Natural** -el diseño se basa en la cadena asignada al control.
- **De izquierda a derecha** -siempre se fuerza el diseño de izquierda a derecha.
- **De derecha a izquierda** -siempre se fuerza el diseño de derecha a izquierda.

Hay dos valores posibles para el **diseño**:

- **De izquierda a derecha** -el diseño es siempre de izquierda a derecha.
- **De derecha a izquierda** -siempre es el diseño de derecha a izquierda.

Normalmente, estos no deben cambiarse a menos que se requiere una alineación específica.

El **reflejado** propiedad indica al sistema que voltear propiedades de control específico (por ejemplo, la posición de la imagen de celda). Tiene tres valores posibles:

- **Automáticamente** -la posición cambia automáticamente según la dirección del idioma seleccionado.
- **En la interfaz de derecha a izquierda** -sólo se cambiará la posición de derecha a izquierda lenguajes basado en.
- **Nunca** -nunca cambiará la posición.

Si ha especificado el desarrollador **Center**, **Justify** o **completa** alineación del contenido de una vista basada en texto, nunca serán volteado idioma según seleccionado.

Antes de macOS Sierra, los controles creados en el código podrían no reflejarse automáticamente. El desarrollador tenía que usar código similar al siguiente para controlar la creación de reflejo:

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

Donde el `Alignment` y `ImagePosition` se establecen según la `UserInterfaceLayoutDirection` del control.

macOS Sierra agrega varios constructores comodidad nuevo (a través de estático `CreateButton` método) que toman varios parámetros (por ejemplo, título, imagen y acción) y automáticamente reflejará correctamente. Por ejemplo:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>Uso de los aspectos del sistema

Aplicaciones modernas de macOS pueden adoptar una nueva apariencia oscuro de interfaz que funciona bien para las aplicaciones de creación, edición o presentación de imagen:

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

Estático `GetAppearance` método de la `NSAppearance` clase se utiliza para obtener un aspecto con nombre del sistema (en este caso `NSAppearance.NameVibrantDark`).

Apple tiene las siguientes sugerencias para el uso de los aspectos del sistema:

- Preferir valores codificados en colores con nombre (tales como `LabelColor` y `SelectedControlColor`).
- Utilice el estilo de control estándar del sistema siempre que sea posible.

Una aplicación de Mac OS que usa las apariencias sistema automáticamente funcionará correctamente para los usuarios que han habilitado características de accesibilidad de la aplicación preferencias del sistema. Como resultado, Apple sugiere que el desarrollador siempre debe usar los aspectos del sistema en sus aplicaciones de Mac OS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Diseño de interfaces de usuario con guiones gráficos

Guiones gráficos permiten al desarrollador no solo para diseño de flujo de los elementos individuales que componen de una aplicación interfaz de usuario, pero para visualizar y diseñar la interfaz de usuario y la jerarquía de elementos especificados.

Los controladores permiten al desarrollador recopilar los elementos en una unidad de composición y los objetos Segue abstracta y quitar el típico "el código de adherencia" necesarios para mover a lo largo de la jerarquía de vistas:

[![](modern-cocoa-apps-images/content12.png "Edición de la interfaz de usuario en Interface Builder de Xcode")](modern-cocoa-apps-images/content12.png#lightbox)

Para obtener más información, consulte nuestra [Introducción a guiones gráficos](~/mac/platform/storyboards/index.md) documentación.

Existen muchas instancias donde la escena definida en un guión gráfico requerirá datos de una escena anterior en la jerarquía de vistas. Apple tiene las siguientes sugerencias para pasar información entre bastidores:

- Dependencias de datos siempre deben en cascada hacia abajo por la jerarquía.
- Evitar dependencias estructurales de la interfaz de usuario de codificar, porque esto limita la flexibilidad de la interfaz de usuario.
- Use C# Interfaces para proporcionar las dependencias de datos genéricos.

El controlador de vista que actúa como origen de Segue, puede invalidar el `PrepareForSegue` se ejecuta el método y lleve a cabo cualquier inicialización necesaria (por ejemplo, para pasar datos) antes de Segue para mostrar el controlador de vista de destino. Por ejemplo:

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

Para obtener más información, consulte nuestra [objetos Segue](~/mac/platform/storyboards/indepth.md#Segues) documentación.

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>Propagación de acciones

Según el diseño de la aplicación macOS, puede haber ocasiones cuando el mejor controlador para una acción en un control de interfaz de usuario podría ser en otra parte de la jerarquía de la interfaz de usuario. Esto ocurre normalmente de los menús y elementos de menú que se encuentran en su propios escena, independiente del resto de la interfaz de usuario de la aplicación.

Para controlar esta situación, el desarrollador puede crear una acción personalizada y pasar la acción de la cadena de servicio de respuesta. Para obtener más información, consulte nuestra [trabajar con acciones de ventana personalizada](~/mac/user-interface/menu.md) documentación.

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Características modernas de Mac

Apple ha incluido varias características orientadas al usuario en macOS Sierra que permiten al desarrollador a aprovechar al máximo la plataforma Mac, como:

- **NSUserActivity** : Esto permite a describir la actividad que el usuario está ocupado actualmente en la aplicación. `NSUserActivity` se creó inicialmente para admitir la entrega, donde podrían ser recoge y continúa en otro dispositivo actividades iniciadas en uno de los dispositivos del usuario. `NSUserActivity` Por tanto, funciona igual en macOS, tal como se hace en iOS, consulte nuestra [Introducción a la entrega](~/ios/platform/handoff.md) iOS documentación para obtener más detalles.
- **Siri en Mac** -siri lo usa la actividad actual (`NSUserActivity`) para proporcionar contexto para los comandos de un usuario puede emitir.
- **Restauración de estado** : cuando el usuario se cierra una aplicación en Mac OS y versiones posteriores, a continuación, vuelva a iniciarse, la aplicación no se realizará automáticamente devolverse al estado anterior. El programador puede utilizar la API de restauración de estado para codificar y restaurar el estado transitorio de la interfaz de usuario antes de la interfaz de usuario se muestre al usuario. Si la aplicación es `NSDocument` basado, restauración de estado se controla automáticamente. Para habilitar la restauración del estado para que no sean de`NSDocument` en función de las aplicaciones, establecer el `Restorable` de la `NSWindow` clase `true`.
- **Documentos en la nube** -antes de macOS Sierra, una aplicación debía explícitamente participar en trabajar con documentos en iCloud del usuario en la unidad. En macOS Sierra el usuario **Desktop** y **documentos** carpetas pueden sincronizarse con su unidad de iCloud automáticamente por el sistema. Como resultado, es posible que se puede eliminar copias locales de documentos para liberar espacio en el equipo del usuario. `NSDocument` en función de las aplicaciones controlará automáticamente este cambio. Todos los demás tipos de aplicación debe usar un `NSFileCoordinator` para sincronizar la lectura y escritura de documentos.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha cubierto varias sugerencias, características y técnicas de que un desarrollador puede usar para crear una aplicación moderna de macOS en Xamarin.Mac.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de macOS](https://developer.xamarin.com/samples/mac/)
