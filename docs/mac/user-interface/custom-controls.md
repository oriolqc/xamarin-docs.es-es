---
title: Creación de controles personalizados en Xamarin.Mac
description: Este documento describe cómo crear controles personalizados en Xamarin.Mac. Muestra cómo compilar el control personalizado, realizar un seguimiento de su estado, dibujar su interfaz, responder a entradas del usuario y usar el control en una aplicación.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 015c1e315b6070777542a8f8c5871c00cf336b5c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61236189"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Creación de controles personalizados en Xamarin.Mac

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, tiene acceso para el mismo usuario que controla un desarrollador que trabaja *Objective-C*, *Swift* y *Xcode*does. Ya que Xamarin.Mac se integra directamente con Xcode, puede usar Xcode _Interface Builder_ para crear y mantener sus controles de usuario (o bien, opcionalmente, crearlos directamente en código de C#).

Aunque macOS proporciona una gran variedad de controles de usuario integrados, puede que en ocasiones que necesita para crear un control personalizado para proporcionar funcionalidad no proporciona de fábrica o para que coincida con un tema de la interfaz de usuario personalizado (por ejemplo, una interfaz de juego).

[![](custom-controls-images/intro01.png "Ejemplo de un control personalizado de la interfaz de usuario")](custom-controls-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos de creación de un Control de interfaz de usuario personalizada reutilizable en una aplicación de Xamarin.Mac. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` comandos se usa para conexión de seguridad de las clases de C# para objetos de Objective-C y elementos de interfaz de usuario.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introducción a los controles personalizados

Como se indicó anteriormente, puede que en ocasiones cuando necesite crear reutilizable, Control de interfaz de usuario personalizado para proporcionar una funcionalidad única para la interfaz de usuario de la aplicación de Xamarin.Mac o para crear un tema personalizado de la interfaz de usuario (por ejemplo, una interfaz de juego).

En estas situaciones, fácilmente puede heredar de `NSControl` y crear una herramienta personalizada que se puede agregar a la interfaz de usuario de la aplicación a través de código de C# o a través de Interface Builder de Xcode. Al heredar de `NSControl` su control personalizado tendrán automáticamente todas las características estándares que tiene un Control de interfaz de usuario integrada (como `NSButton`).

Si el control de interfaz de usuario personalizado simplemente presenta información (por ejemplo, un gráficos personalizados y una herramienta gráfica), desea heredar `NSView` en lugar de `NSControl`.

Con independencia de qué clase base se usa, los pasos básicos para crear un control personalizado es el mismo.

En este artículo le, crear un componente de conmutador voltear personalizado que proporciona un tema de la interfaz de usuario único y un ejemplo de la creación de un Control de interfaz de usuario personalizado completamente funcional.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Crear el Control personalizado

Puesto que el control personalizado que estamos creando responderá a la entrada del usuario (clics del botón primario del mouse), vamos a heredar `NSControl`. De este modo, nuestro control personalizado automáticamente tiene todas las características estándares que tiene un Control de interfaz de usuario integrada y responden como un control estándar de macOS.

En Visual Studio para Mac, abra el proyecto de Xamarin.Mac que desea crear un Control de interfaz de usuario personalizada para (o cree uno nuevo). Agregue una nueva clase y llámelo `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Agregar una nueva clase")](custom-controls-images/custom01.png#lightbox)

A continuación, edite el `NSFlipSwitch.cs` clase y dele un aspecto similar al siguiente:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

Lo primero que debe tener en cuenta acerca de nuestra clase personalizada que estamos heredar `NSControl` y el uso de la **registrar** comando exponer esta clase en Objective-C y Xcode Interface Builder:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

En las secciones siguientes, echaremos un vistazo al resto del código anterior en detalle.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>El estado del Control de seguimiento

Puesto que nuestro Control personalizado es un conmutador, necesitamos una forma un seguimiento del estado de encendido y apagado del conmutador. Nosotros nos encargaremos de que con el siguiente código en `NSFlipSwitch`:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

Cuando se cambia el estado del conmutador, necesitamos una manera de actualizar la interfaz de usuario. Para ello, forzar el control vuelva a dibujar su interfaz de usuario con `NeedsDisplay = true`.

Si el control requiere más que un único estado (por ejemplo, un conmutador varios estado con 3 posiciones) activado o desactivado, podríamos haber usado un **Enum** un seguimiento del estado. En nuestro ejemplo, un simple **bool** llevará a cabo.

También hemos agregado un método auxiliar para intercambiar el estado del modificador entre activar y desactivar:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Más adelante, ampliaremos esta clase auxiliar para informar al llamador cuando ha cambiado el estado de los conmutadores.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>La interfaz del Control de dibujo

Vamos a usar las rutinas de dibujo Core gráficas para dibujar la interfaz de usuario de nuestro control personalizado en tiempo de ejecución. Antes de poder hacer esto, es necesario activar las capas para nuestro control. Para hacerlo con el método privado siguiente:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Este método se llama desde cada uno de los constructores del control para asegurarse de que el control está configurado correctamente. Por ejemplo:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

A continuación, se debe invalidar el `DrawRect` método y agregue las rutinas de Core gráfico para dibujar el control:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Se deberá ser el ajuste de la representación visual del control cuando cambia su estado (por ejemplo, pasar de **en** a **desactivar**). Cada vez que los cambios de estado, podemos utilizar el `NeedsDisplay = true` comando para forzar que el control vuelva a dibujar para el estado nueva.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Responder a la entrada de usuario

Hay dos formas básicas que podemos agregar entrada de usuario para nuestro control personalizado: **Invalidar las rutinas de control de Mouse** o **los reconocedores de gestos**. El método que use, se basará en la funcionalidad requerida por nuestro control.

> [!IMPORTANT]
> Para cualquier control personalizado que cree, debe usar **invalidar métodos** _o_ **los reconocedores de gestos**, pero no ambos al mismo tiempo que pueden entrar en conflicto entre sí.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Control de entrada del usuario con los métodos de invalidación

Los objetos que heredan de `NSControl` (o `NSView`) tener varios invalidar los métodos de control de mouse o entrada del teclado. Para el control de ejemplo, queremos voltear el estado del modificador entre **en** y **desactivar** cuando el usuario hace clic en el control con el botón primario del mouse. Podemos agregar lo siguiente invalidar métodos a la `NSFlipSwitch` clase para controlar esto:

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

En el código anterior, llamamos el `FlipSwitchState` método (definido anteriormente) para voltear el On/Off de estado del conmutador de la `MouseDown` método. También se forzará el control se vuelva a dibujar para reflejar el estado actual.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Control de entrada del usuario con los reconocedores de gestos

Si lo desea, puede usar los reconocedores de gestos para controlar el usuario interactúa con el control. Quitar las invalidaciones que agregó anteriormente, edite el `Initialize` método y darle un aspecto similar al siguiente:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

En este caso, vamos a crear un nuevo `NSClickGestureRecognizer` y llamar a nuestra `FlipSwitchState` método para cambiar el estado del modificador cuando el usuario hace clic en él con el botón primario del mouse. El `AddGestureRecognizer (click)` método agrega el reconocedor de gestos al control.

Nuevamente, el método que use depende de lo que intentamos alcanzar con nuestro control personalizado. Si es necesario el acceso de nivel bajo de la a la interacción del usuario, utilice los métodos de invalidación. Si necesitamos funcionalidad predefinida, como clics del mouse, use los reconocedores de gestos.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Responder a eventos de cambio de estado

Cuando el usuario cambia el estado de nuestro control personalizado, necesitamos una manera de responder al cambio de estado en el código (por ejemplo, hacer algo cuando hace clic en un botón personalizado). 

Para proporcionar esta funcionalidad, edite el `NSFlipSwitch` de clases y agregue el código siguiente:

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null) 
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

A continuación, edite el `FlipSwitchState` método y darle un aspecto similar al siguiente:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

En primer lugar, proporcionamos un `ValueChanged` eventos que podemos agregar un controlador en código C# para que se puede realizar una acción cuando el usuario cambia el estado del conmutador.

Segundo, porque nuestro control personalizado hereda de `NSControl`, automáticamente tiene un **acción** que se pueden asignar en Interface Builder de Xcode. Llamar a este método **acción** cuando cambia el estado, usamos el código siguiente:

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

En primer lugar, comprobamos si un **acción** se ha asignado al control. A continuación, llamamos el **acción** si se ha definido.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Utilizar el Control personalizado

Con nuestro control personalizado definido por completo, que podamos o agregarlo a la interfaz de usuario de nuestra aplicación de Xamarin.Mac con código C# o en Interface Builder de Xcode.

Para agregar el control mediante Interface Builder, primero realice una compilación limpia del proyecto de Xamarin.Mac y después haga doble clic en el `Main.storyboard` archivo para abrirlo en Interface Builder para su edición:

[![](custom-controls-images/custom02.png "Editar el guión gráfico en Xcode")](custom-controls-images/custom02.png#lightbox)

A continuación, arrastre un `Custom View` en el diseño de interfaz de usuario:

[![](custom-controls-images/custom03.png "Seleccionar una vista personalizada de la biblioteca")](custom-controls-images/custom03.png#lightbox)

Con la vista personalizada aún seleccionado, cambie a la **Inspector de identidad** y cambiar la vista **clase** a `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Configuración de la clase de la vista")](custom-controls-images/custom04.png#lightbox)

Cambie a la **Editor del asistente** y cree un **toma** para el control personalizado (asegurándose de que va a enlazar en el `ViewController.h` archivo y no el `.m` archivo):

[![](custom-controls-images/custom05.png "Configurar una nueva salida")](custom-controls-images/custom05.png#lightbox)

Guarde los cambios, vuelva a Visual Studio para Mac y permita que los cambios sincronizar. Editar el `ViewController.cs` y realice la `ViewDidLoad` método aspecto parecido al siguiente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
``` 

En este caso, respondemos a la `ValueChanged` eventos que definimos anteriormente en el `NSFlipSwitch` clase y escribir actual **valor** cuando el usuario hace clic en el control.

Si lo desea, podemos volver a Interface Builder y definir un **acción** en el control:

[![](custom-controls-images/custom06.png "Configurar una nueva acción")](custom-controls-images/custom06.png#lightbox)

De nuevo, editar la `ViewController.cs` archivo y agregue el siguiente método:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Se debe usar el **eventos** o definir una **acción** en Interface Builder, pero no debe usar ambos métodos al mismo tiempo o pueden entrar en conflicto entre sí.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de la creación de un Control de interfaz de usuario personalizada reutilizable en una aplicación de Xamarin.Mac. Hemos visto cómo dibujar los controles personalizados la interfaz de usuario, las dos formas principales para responder a la intervención del usuario y de mouse y cómo exponer el nuevo control a las acciones en Interface Builder de Xcode.

## <a name="related-links"></a>Vínculos relacionados

- [MacCustomControl (ejemplo)](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Control de eventos del Mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
