---
title: diseño de interfaz de usuario de.Storyboard/.xib-less en Xamarin.Mac
description: En este artículo se describe la creación de interfaz de usuario de la aplicación de Xamarin.Mac directamente desde C# código sin .storyboard archivos, archivos .xib o Interface Builder.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 076c6464359a58c2b36d157d9620673b0644cd4a
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459842"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>diseño de interfaz de usuario de.Storyboard/.xib-less en Xamarin.Mac

_En este artículo se describe la creación de interfaz de usuario de la aplicación de Xamarin.Mac directamente desde C# código sin .storyboard archivos, archivos .xib o Interface Builder._

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, podrá tener acceso a los mismos elementos de interfaz de usuario y herramientas que un desarrollador que trabaja *Objective-C* y *Xcode* does. Normalmente, al crear una aplicación de Xamarin.Mac, usará Interface Builder de Xcode con archivos .storyboard o .xib para crear y mantener la interfaz de usuario de la aplicación.

También tiene la opción de crear algunos o todos de la interfaz de usuario de la aplicación de Xamarin.Mac directamente en C# código. En este artículo, trataremos los aspectos básicos de la creación de interfaces de usuario y elementos de interfaz de usuario de C# código.

[![El editor Visual Studio para Mac código](xibless-ui-images/intro01.png "Visual Studio para el editor de código de Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Cambio de una ventana para utilizar código

Cuando se crea una nueva aplicación de Xamarin.Mac Cocoa, obtendrá una ventana en blanco, estándar de forma predeterminada. Este windows se define en un **Main.storyboard** (o tradicionalmente una **MainWindow.xib**) se incluyen automáticamente en el proyecto de archivo. Esto también incluye un **ViewController.cs** archivos que administra la vista principal de la aplicación (o volver a tradicionalmente una **MainWindow.cs** y un **MainWindowController.cs** archivo).

Para cambiar a una ventana Xibless para una aplicación, realice lo siguiente:

1. Abra la aplicación que desea dejar de usar `.storyboard` o archivos .xib para definir la interfaz de usuario en Visual Studio para Mac.
2. En el **panel de solución**, haga doble clic en el **Main.storyboard** o **MainWindow.xib** de archivo y seleccione **quitar**: 

    ![Quitar el guión gráfico principal o la ventana](xibless-ui-images/switch01.png "quitar el guión gráfico principal o la ventana")
3. Desde el **cuadro de diálogo Quitar**, haga clic en el **eliminar** botón para quitar el .storyboard o .xib completamente desde el proyecto: 

    ![Confirmar la eliminación](xibless-ui-images/switch02.png "confirmar la eliminación")

Ahora necesitamos modificar la **MainWindow.cs** archivo para definir el diseño de ventana y modificar el **ViewController.cs** o **MainWindowController.cs** archivo para crear un instancia de nuestro `MainWindow` clase puesto que ya no estamos usando el archivo .storyboard o .xib.

Aplicaciones modernas de Xamarin.Mac que los guiones gráficos de uso de la interfaz de usuario no puede incluir automáticamente la **MainWindow.cs**, **ViewController.cs** o **MainWindowController.cs** archivos. Según sea necesario, basta con agregar una nueva y vacía C# clase al proyecto (**agregar** > **nuevo archivo...**   >  **General** > **clase vacía**) y asígnele el mismo que el archivo que falta. 


### <a name="defining-the-window-in-code"></a>Definición de la ventana de código

A continuación, edite el **MainWindow.cs** de archivo y dele un aspecto similar al siguiente:

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

Hablemos sobre algunos de los elementos clave.

En primer lugar, hemos agregado algunas _propiedades calculadas_ que actuará como salidas (como si se creó la ventana en un archivo .storyboard o .xib):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Estos nos proporcionará acceso a los elementos de interfaz de usuario que se van a mostrar en la ventana. Puesto que la ventana no es que se inflan desde un archivo .storyboard o .xib, necesitamos una manera de crear instancias del mismo (como veremos más adelante en el `MainWindowController` clase). Eso es lo que hace este nuevo método de constructor:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Esto es donde agregaremos el diseño de la ventana y colocar los elementos de interfaz de usuario necesarios para crear la interfaz de usuario necesarios. Antes de que podemos agregar cualquier elemento de interfaz de usuario a una ventana, necesita un _vista contenido_ para contener los elementos:

```csharp
ContentView = new NSView (Frame);
```

Esto crea una vista de contenido que va a rellenar la ventana. Ahora agregaremos nuestro primer elemento de interfaz de usuario, un `NSButton`, en la ventana:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

Lo primero que tener en cuenta aquí es que, a diferencia de iOS, macOS usa una notación matemática para definir su sistema de coordenadas de la ventana. Por lo que el punto de origen está en la esquina inferior izquierda de la ventana, con valores aumento de la derecha y hacia la esquina superior derecha de la ventana. Cuando se crea el nuevo `NSButton`, nos tenerlo en cuenta cuando se definen su posición y tamaño en pantalla.

El `AutoresizingMask = NSViewResizingMask.MinYMargin` propiedad indica al botón que queremos que permanezca en la misma ubicación desde la parte superior de la ventana cuando la ventana se redimensiona verticalmente. Nuevamente, esto es necesario porque (0,0) se encuentra en la parte inferior izquierda de la ventana.

Por último, el `ContentView.AddSubview (ClickMeButton)` método agrega el `NSButton` a la vista de contenido por lo que TI se mostrarán en pantalla cuando la aplicación se ejecute y la ventana muestra.

A continuación se agrega una etiqueta a la ventana que se mostrará el número de veces que el `NSButton` se ha hecho clic: 

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

Dado que macOS no tiene un determinado _etiqueta_ elemento de interfaz de usuario, hemos agregado un especialmente estilo, no se puede modificar `NSTextField` para que actúe como una etiqueta. Al igual que el botón antes, el tamaño y la ubicación tiene en cuenta que (0,0) es la parte inferior izquierda de la ventana. El `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` propiedad usa el **o** operador para combinar dos `NSViewResizingMask` características. Esto hará que la etiqueta permanecen en la misma ubicación desde la parte superior de la ventana cuando la ventana se redimensiona verticalmente y reducir y crecen en ancho como la ventana se redimensiona horizontalmente.

Nuevamente, el `ContentView.AddSubview (ClickMeLabel)` método agrega el `NSTextField` a la vista de contenido por lo que TI se mostrarán en pantalla cuando se ejecuta la aplicación y abre la ventana.


### <a name="adjusting-the-window-controller"></a>Ajuste el controlador de ventana

Desde el diseño de la `MainWindow` ya no se carga desde un archivo .storyboard o .xib, necesitamos realizar algunos ajustes en el controlador de ventana. Editar el **MainWindowController.cs** de archivos y darle un aspecto similar al siguiente:

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

En primer lugar, definimos una nueva instancia de la `MainWindow` clase y se asigna a la del controlador de ventana base `Window` propiedad:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Se definen la ubicación de la ventana de la pantalla con un `CGRect`. Al igual que el sistema de coordenadas de la ventana, la pantalla define (0,0) como la esquina inferior izquierda. A continuación, definimos el estilo de la ventana mediante el uso de la **o** operador para combinar dos o más `NSWindowStyle` características:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

La siguiente `NSWindowStyle` características están disponibles:

- **Sin bordes** -la ventana no tendrá borde.
- **Titulada** : la ventana tendrá una barra de título.
- **Puede cerrar** -tiene un botón de cierre y se puede cerrar la ventana.
- **Miniaturizable** -la ventana tiene un botón Miniaturize y se puede minimizar.
- **Puede cambiar el tamaño** -se tienen un botón para cambiar el tamaño de la ventana y se puede cambiar el tamaño.
- **Utilidad** -es una ventana de estilo de utilidad (panel).
- **DocModal** -si la ventana es un Panel, será Modal de documento en lugar del sistema Modal. 
- **NonactivatingPanel** -si la ventana es un Panel, no se realizará la ventana principal.
- **TexturedBackground** -la ventana tendrá un fondo de textura.
- **Sin escala** -no se escalará la ventana.
- **UnifiedTitleAndToolbar** -áreas de título y la barra de herramientas de la ventana que se van a unir.
- **HUD** -la ventana se mostrará como una visualización sobreimpresa del Panel.
- **FullScreenWindow** -la ventana puede entrar en modo de pantalla completa.
- **FullSizeContentView** -vista de contenido de la ventana está detrás del título y el área de la barra de herramientas.

Las dos últimas propiedades definen la _tipo de almacenamiento en búfer_ para la ventana y si se aplaza el dibujo de la ventana. Para obtener más información sobre `NSWindows`, consulte Apple [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) documentación.

Por último, dado que la ventana no es que se inflan desde un archivo .storyboard o .xib, debemos simular en nuestra **MainWindowController.cs** mediante una llamada a las ventanas `AwakeFromNib` método:

```csharp
Window.AwakeFromNib ();
```

Esto permitirá que escribir código basado en la ventana simplemente como una ventana estándar cargada desde un archivo .storyboard o .xib.


### <a name="displaying-the-window"></a>Mostrar la ventana

Los archivos .xib .storyboard quitado y la **MainWindow.cs** y **MainWindowController.cs** archivos modificados, que va a usar la ventana tal como lo haría con cualquier ventana normal que se había creado en Interface Builder de Xcode con un archivo .xib.

El siguiente creará una nueva instancia de la ventana y su controlador y mostrar la ventana en pantalla:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

En este momento, si se ejecuta la aplicación y un par de veces clic en el botón, el siguiente se mostrará:

![Ejecutar una aplicación de ejemplo](xibless-ui-images/run01.png "una ejecución de la aplicación de ejemplo")


## <a name="adding-a-code-only-window"></a>Adición de una única ventana de código

Si desea agregar un solo código, la ventana de xibless a una aplicación de Xamarin.Mac existente, haga doble clic en el proyecto en el **panel de solución** y seleccione **agregar** > **nuevo archivo...** . En el **nuevo archivo** cuadro de diálogo Elegir **Xamarin.Mac** > **ventana de Cocoa con controlador**, tal y como se muestra a continuación:

![Agregar un nuevo controlador de ventana](xibless-ui-images/add01.png "agregando un nuevo controlador de ventana") 

Igual que antes, borraremos el archivo de .storyboard o .xib predeterminado del proyecto (en este caso **SecondWindow.xib**) y siga los pasos descritos en la [cambiar una ventana para utilizar código](#Switching_a_Window_to_use_Code) sección anterior para cubrir el definición de la ventana al código.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Adición de un elemento de interfaz de usuario a una ventana de código

Si una ventana se ha creado en el código o cargado desde un archivo .storyboard o .xib, puede haber ocasiones donde queremos agregar un elemento de interfaz de usuario a una ventana de código. Por ejemplo:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

El código anterior crea un nuevo `NSButton` y lo agrega a la `MyWindow` instancia de la ventana para su presentación. Básicamente cualquier elemento de interfaz de usuario que se puede definir en Interface Builder de Xcode en un archivo .storyboard o .xib puede creado en el código y se muestra en una ventana.


## <a name="defining-the-menu-bar-in-code"></a>Definición de la barra de menús en código

Debido a las limitaciones actuales de Xamarin.Mac, se recomienda no crear barra de menú de: la aplicación de Xamarin.Mac`NSMenuBar`– en código pero seguir usando el **Main.storyboard** o **MainMenu.xib** archivo definirla. Dicho esto, puede agregar y quitar los menús y elementos de menú en C# código.

Por ejemplo, modifique el **AppDelegate.cs** y realice la `DidFinishLaunching` método aspecto parecido al siguiente:

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

Lo anterior crea un menú de barra de estado desde el código y muestra cuando se inicia la aplicación. Para obtener más información sobre cómo trabajar con menús, vea nuestra [menús](~/mac/user-interface/menu.md) documentación.


## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de la creación de la interfaz de usuario de la aplicación de Xamarin.Mac en C# código en lugar de usar Interface Builder de Xcode con archivos .storyboard o .xib.



## <a name="related-links"></a>Vínculos relacionados

- [MacXibless (ejemplo)](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [Menús](~/mac/user-interface/menu.md)
- [Directrices de la interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introducción a Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
