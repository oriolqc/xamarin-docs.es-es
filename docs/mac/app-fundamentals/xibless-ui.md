---
title: .Storyboard/.xib-less diseño de la interfaz de usuario
description: Este artículo trata la creación de interfaz de usuario de la aplicación Xamarin.Mac directamente desde el código C#, sin .storyboard archivos, archivos de .xib o el generador de interfaz.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 66725b02d3e351e74fa79ae5336a7db3a9f2b534
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="storyboardxib-less-user-interface-design"></a>.Storyboard/.xib-less diseño de la interfaz de usuario

_Este artículo trata la creación de interfaz de usuario de la aplicación Xamarin.Mac directamente desde el código C#, sin .storyboard archivos, archivos de .xib o el generador de interfaz._


## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tener acceso a los mismos elementos de interfaz de usuario y herramientas que un desarrollador que trabaja *Objective-C* y *Xcode* does. Normalmente, al crear una aplicación Xamarin.Mac, deberá usar el generador de interfaz de Xcode con .storyboard o .xib archivos para crear y mantener la interfaz de usuario de la aplicación.

También tiene la opción de creación de algunos o todos de interfaz de usuario de la aplicación Xamarin.Mac directamente en código C#. En este artículo, se tratarán los fundamentos de la creación de interfaces de usuario y los elementos de interfaz de usuario en código C#.

[![Visual Studio para el editor de código Mac](xibless-ui-images/intro01.png "Visual Studio para el editor de código de Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Cambiar una ventana para usar código

Cuando se crea una nueva aplicación de Xamarin.Mac cacao, obtendrá una ventana en blanco, estándar de forma predeterminada. Esta ventana se define en un **Main.storyboard** (o tradicionalmente una **MainWindow.xib**) incluida automáticamente en el proyecto de archivo. Esto también incluye un **ViewController.cs** archivo que administra la vista principal de la aplicación (o nuevo tradicionalmente una **MainWindow.cs** y un **MainWindowController.cs** archivo).

Para cambiar a una ventana de Xibless para una aplicación, haga lo siguiente:

1. Abra la aplicación que desea dejar de usar `.stroyboard` o .xib archivos para definir la interfaz de usuario en Visual Studio para Mac.
2. En el **solución Pad**, haga doble clic en el **Main.storyboard** o **MainWindow.xib** de archivos y seleccione **quitar**: 

    ![Quitar el guión gráfico principal o la ventana](xibless-ui-images/switch01.png "quitar el guión gráfico principal o la ventana")
3. Desde el **cuadro de diálogo Quitar**, haga clic en el **eliminar** botón para quitar el .storyboard o .xib completamente en el proyecto: 

    ![Confirmar la eliminación](xibless-ui-images/switch02.png "confirmar la eliminación")

Ahora debemos modificar el **MainWindow.cs** archivo para definir el diseño de la ventana y modificar la **ViewController.cs** o **MainWindowController.cs** archivo para crear un instancia de nuestro `MainWindow` clase puesto que ya no se está usando el archivo .storyboard o .xib.

Las aplicaciones modernas de Xamarin.Mac que utilicen guiones gráficos para la interfaz de usuario no puede incluir automáticamente la **MainWindow.cs**, **ViewController.cs** o **MainWindowController.cs** archivos. Según sea necesario, basta con agregar una nueva vacía clase C# al proyecto (**agregar** > **New File...**   >  **General** > **clase vacía**) y asígnele el mismo que el archivo que falta. 


### <a name="defining-the-window-in-code"></a>Definir la ventana en el código

A continuación, edite la **MainWindow.cs** de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

Analicemos algunos de los elementos clave.

En primer lugar, hemos agregado algunas _propiedades calculadas_ que actuará como salidas (como si se creó la ventana en un archivo .storyboard o .xib):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Estos nos ofrecerá acceso a los elementos de interfaz de usuario que se va a mostrar en la ventana. Puesto que no se aumenta la ventana desde un archivo .storyboard o .xib, necesitamos una manera de crear instancias de él (como veremos más adelante en el `MainWindowController` clase). Es lo que hace este nuevo método de constructor:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Esto es donde se cree el diseño de la ventana y colocar los elementos de interfaz de usuario necesarios para crear la interfaz de usuario necesarios. Antes de que podemos agregar los elementos de interfaz de usuario a una ventana, necesita un _vista de contenido_ para incluir los elementos:

```csharp
ContentView = new NSView (Frame);
```

Esto crea una vista de contenido que va a rellenar la ventana. Ahora que agregar el primer elemento de interfaz de usuario, un `NSButton`, en la ventana:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

Lo primero que hay que destacar aquí es que, a diferencia de iOS, macOS usa una notación matemática para definir su sistema de coordenadas de ventana. Por lo que el punto de origen está en la esquina izquierda inferior de la ventana, con valores cada vez mayores y derecha hacia la esquina superior derecha de la ventana. Cuando se crea el nuevo `NSButton`, nos tenerlo en cuenta cuando se definen su posición y el tamaño de pantalla.

El `AutoresizingMask = NSViewResizingMask.MinYMargin` propiedad indica al botón que deseamos que permanecer en la misma ubicación desde la parte superior de la ventana cuando la ventana se redimensiona verticalmente. Una vez más, esto es necesario porque (0,0) está en la parte inferior izquierda de la ventana.

Por último, el `ContentView.AddSubview (ClickMeButton)` método agrega la `NSButton` a la vista de contenido por lo que TI se mostrará en la pantalla cuando la aplicación es la ejecución y la ventana que aparece.

A continuación se agrega una etiqueta a la ventana que muestra el número de veces que el `NSButton` se ha hecho clic: 

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
``` 

Porque no dispone de un determinado macOS _etiqueta_ elemento de interfaz de usuario, hemos agregado un especialmente estilo, no se puede modificar `NSTextField` para actuar como una etiqueta. Igual que el botón antes, el tamaño y la ubicación se toman en cuenta esa (0,0) se encuentra en la parte inferior izquierda de la ventana. El `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` propiedad es mediante el **o** operador para combinar dos `NSViewResizingMask` características. Esto hará que la etiqueta permanecen en la misma ubicación desde la parte superior de la ventana cuando la ventana se redimensiona verticalmente y reducir y crecer en ancho según la ventana se redimensiona horizontalmente.

Una vez más, la `ContentView.AddSubview (ClickMeLabel)` método agrega la `NSTextField` a la vista de contenido por lo que TI se mostrará en la pantalla cuando se ejecuta la aplicación y abra la ventana.


### <a name="adjusting-the-window-controller"></a>Ajustar el controlador de ventana

Desde el diseño de la `MainWindow` ya no se carga desde un archivo .storyboard o .xib, necesitamos realizar algunos ajustes en el controlador de la ventana. Editar la **MainWindowController.cs** de archivos y darle un aspecto similar al siguiente:

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

Permiten analizar los elementos clave de esta modificación.

En primer lugar, definimos una nueva instancia de la `MainWindow` clase y asígnela al controlador de ventana base `Window` propiedad:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Se define la ubicación de la ventana de la pantalla con un `CGRect`. Al igual que el sistema de coordenadas de la ventana, en la pantalla se define (0,0) como la esquina inferior izquierda. A continuación, definimos el estilo de la ventana utilizando la **o** operador para combinar dos o más `NSWindowStyle` características:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

El siguiente `NSWindowStyle` características están disponibles:

- **Sin márgenes** -la ventana no tendrá ningún borde.
- **Titulada** -la ventana tendrá una barra de título.
- **Puede cerrar** -tiene un botón de cierre y se puede cerrar la ventana.
- **Miniaturizable** -la ventana tiene un botón Miniaturize y se puede minimizar.
- **Puede cambiar el tamaño** -se tienen un botón para cambiar el tamaño de la ventana y se puede cambiar el tamaño.
- **Utilidad** -la ventana es una ventana de estilo de la utilidad (panel).
- **DocModal** -si la ventana es un Panel, será Modal de documento en lugar de sistema Modal. 
- **NonactivatingPanel** -si la ventana es un Panel, no se realizará la ventana principal.
- **TexturedBackground** -la ventana tendrá un fondo de textura.
- **Sin ajuste de escala** -no se ampliará la ventana.
- **UnifiedTitleAndToolbar** -áreas de título y la barra de herramientas de la ventana se combinarán.
- **HUD** -la ventana se mostrará como una pantalla de aviso de Panel.
- **FullScreenWindow** -la ventana puede entrar en modo de pantalla completa.
- **FullSizeContentView** -vista de contenido de la ventana está detrás de la barra de herramientas del área y el título.

Las dos últimas propiedades definen la _tipo de almacenamiento en búfer_ de la ventana y si se aplaza el dibujo de la ventana. Para obtener más información sobre `NSWindows`, vea de Apple [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) documentación.

Por último, puesto que no se aumenta la ventana desde un archivo .storyboard o .xib, es necesario simular en nuestro **MainWindowController.cs** mediante una llamada a las ventanas `AwakeFromNib` método:

```csharp
Window.AwakeFromNib ();
```

Esto le permitirá que escribir código basado en la ventana sólo desee una ventana estándar cargada desde un archivo .storyboard o .xib.


### <a name="displaying-the-window"></a>Mostrar la ventana

Con el archivo .storyboard o .xib quitado y la **MainWindow.cs** y **MainWindowController.cs** archivos modificados que vamos a usar la ventana como haría con cualquier ventana normal que se había creado en Generador de interfaz de Xcode con un archivo .xib.

A continuación creará una nueva instancia de la ventana y su controlador y mostrar la ventana en la pantalla:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

En este momento, si se ejecuta la aplicación y un par de veces clic en el botón, éste se mostrará:

![Ejecutar una aplicación de ejemplo](xibless-ui-images/run01.png "ejecutar una aplicación de ejemplo")


## <a name="adding-a-code-only-window"></a>Agregar una única ventana de código

Si desea agregar un solo código, la ventana de xibless a una aplicación Xamarin.Mac existente, haga doble clic en el proyecto en el **solución Pad** y seleccione **agregar** > **nuevo archivo...** . En el **nuevo archivo** cuadro de diálogo Elegir **Xamarin.Mac** > **ventana cacao con controlador**, como se muestra a continuación:

![Agregar un nuevo controlador de ventana](xibless-ui-images/add01.png "agregando un nuevo controlador de ventana") 

Al igual que antes, se eliminará el archivo predeterminado de .storyboard o .xib desde el proyecto (en este caso **SecondWindow.xib**) y siga los pasos descritos en la [cambiar una ventana para usar código](#Switching_a_Window_to_use_Code) sección anterior para cubrir el definición de la ventana al código.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Adición de un elemento de interfaz de usuario a una ventana de código

Si una ventana se creó en el código o se carga desde un archivo .storyboard o .xib, puede haber ocasiones donde desea agregar un elemento de interfaz de usuario en una ventana de código. Por ejemplo:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

El código anterior crea un nuevo `NSButton` y lo agrega a la `MyWindow` instancia de la ventana para su presentación. Básicamente cualquier elemento de interfaz de usuario que se pueden definir en el generador de interfaz de Xcode en un archivo .storyboard o .xib puede creado en código y se muestra en una ventana.


## <a name="defining-the-menu-bar-in-code"></a>Definir la barra de menús en el código

Debido a las limitaciones actuales de Xamarin.Mac, se recomienda no crear barra de menú de: la aplicación Xamarin.Mac`NSMenuBar`– en código pero seguir usando el **Main.storyboard** o **MainMenu.xib** archivo para definirlo. Es decir, puede agregar y quitar elementos de menú y menús en código C#.

Por ejemplo, editar la **AppDelegate.cs** de archivos y realice la `DidFinishLaunching` método aspecto similar al siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

Los pasos anteriores crea un menú de barra de estado desde el código y muestra cuando se inicia la aplicación. Para obtener más información sobre cómo trabajar con menús, vea nuestra [menús](~/mac/user-interface/menu.md) documentación.


## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de crear la interfaz de usuario de la aplicación de Xamarin.Mac en código C# en lugar de utilizar el generador de interfaz de Xcode con .storyboard o .xib archivos.



## <a name="related-links"></a>Vínculos relacionados

- [MacXibless (ejemplo)](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [Menús](~/mac/user-interface/menu.md)
- [macOS directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introducción a Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
