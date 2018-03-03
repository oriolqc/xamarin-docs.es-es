---
title: Crear controles personalizados
description: "Este artículo describe cómo crear controles personalizados y trabajar con ellos en el generador de interfaz."
ms.topic: article
ms.prod: xamarin
ms.assetid: 675B9405-D9A7-49F0-94AD-417F10A71D11
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: f3d6301bc2c0237a268669fff437801bfb2657d1
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="creating-custom-controls"></a>Creación de controles personalizados

_Este artículo describe cómo crear controles personalizados y trabajar con ellos en el generador de interfaz._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tener acceso a los mismos controles de usuario que un desarrollador que trabaja *Objective-C*, *Swift* y *Xcode* does . Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener los controles de usuario (o si lo desea crearlos directamente en código de C#).

Aunque macOS proporciona una gran variedad de controles de usuario integrados, habrá ocasiones en que necesite crear un control personalizado para proporcionar funcionalidad no ha proporcionado de serie o para que coincida con un tema de la interfaz de usuario personalizado (por ejemplo, una interfaz de juego).

[ ![](custom-controls-images/intro01.png "Ejemplo de un control personalizado de la interfaz de usuario")](custom-controls-images/intro01.png)

En este artículo, se tratarán los fundamentos de la creación de un Control personalizado de interfaz de usuario reutilizable en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introducción a los controles personalizados

Como se mencionó anteriormente, puede haber ocasiones en que deba crear reutilizable, Control de interfaz de usuario personalizado para proporcionar funcionalidad exclusiva para la interfaz de usuario de la aplicación Xamarin.Mac o para crear un tema de la interfaz de usuario personalizado (por ejemplo, una interfaz de juego).

En estas situaciones, se puede heredar fácilmente de `NSControl` y crear una herramienta personalizada que puede agregarse a la interfaz de usuario de la aplicación a través de código de C# o mediante el generador de interfaz de Xcode. Al heredar de `NSControl` el control personalizado tendrá automáticamente todas las características estándares que tiene un Control de interfaz de usuario integrados (como `NSButton`).

Si el control de interfaz de usuario personalizado simplemente muestra información (por ejemplo, un gráficos personalizados y una herramienta gráfica), debe heredar de `NSView` en lugar de `NSControl`.

Con independencia de qué clase base se utiliza, los pasos básicos para crear un control personalizado es el mismo.

En este artículo le, crear un componente de conmutador voltear personalizado que proporciona un tema de la interfaz de usuario único y un ejemplo de la creación de un Control de interfaz de usuario personalizado completamente funcional.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Generar un Control personalizado

Puesto que el control personalizado que vamos a crear responderán a proporcionados por el usuario (clics del botón primario del mouse), vamos a heredar de `NSControl`. De este modo, nuestro control personalizado automáticamente tiene todas las características estándares que tiene un Control de interfaz de usuario integrada y responden como un control de macOS estándar.

En Visual Studio para Mac, abra el proyecto Xamarin.Mac que desea crear un Control de interfaz de usuario personalizada para (o cree uno nuevo). Agregue una nueva clase y llámelo `NSFlipSwitch`:

[ ![](custom-controls-images/custom01.png "Agregar una nueva clase")](custom-controls-images/custom01.png)

A continuación, edite la `NSFlipSwitch.cs` clase y darle un aspecto similar al siguiente:

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

Lo primero que debe tener en cuenta acerca de la clase personalizada que estamos heredar `NSControl` y el uso de la **registrar** comando exponer esta clase para Objective-C y del Xcode generador de interfaz:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

En las siguientes secciones, echaremos un vistazo el resto del código anterior en detalle.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>El estado del Control de seguimiento

Puesto que nuestro Control personalizado es un conmutador, necesitamos una manera de un seguimiento del estado de encendido/apagado del conmutador. Se controlarán que con el código siguiente en `NSFlipSwitch`:

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

Cuando se cambia el estado del conmutador, necesitamos una manera de actualizar la interfaz de usuario. Para ello, al forzar el control vuelva a dibujar su interfaz de usuario con `NeedsDisplay = true`.

Si nuestro control requiere más que una sola activado/desactivado estado (por ejemplo, un conmutador varios estado con 3 posiciones), podríamos haber usado un **Enum** un seguimiento del estado. En nuestro ejemplo, una sencilla **bool** llevará a cabo.

También hemos agregado un método auxiliar para intercambiar el estado del modificador entre activar y desactivar:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Más adelante, se podrá expandir esta clase auxiliar para informar al llamador cuando ha cambiado el estado de conmutadores.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>La interfaz del Control de dibujo

Vamos a usar las rutinas de dibujo Core gráficas para dibujar la interfaz de usuario de nuestro control personalizado en tiempo de ejecución. Antes de que podemos hacer esto, es necesario activar las capas para nuestro control. Para hacerlo con el método privado siguiente:

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

A continuación, necesitamos invalidar el `DrawRect` método y agregue las rutinas Core gráfico para dibujar el control:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Se le puede ajustar la representación visual del control cuando cambia su estado (por ejemplo, desde la **en** a **desactivar**). Cada vez que los cambios de estado, se puede usar el `NeedsDisplay = true` comando para forzar que el control vuelva a dibujar para el nuevo estado.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Responder a las entradas de usuario

Hay dos formas básicas que podemos agregar proporcionados por el usuario en nuestro control personalizado: **invalidar rutinas de control de Mouse** o **gesto identificadores**. Método se use, se basará en la funcionalidad requerida por nuestro control.

> [!IMPORTANT]
> Para cualquier control personalizado que cree, debe utilizar **invalidar métodos** _o_ **identificadores de gestos**, pero no ambos al mismo tiempo pueden entrar en conflicto entre sí.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Control de entrada del usuario con los métodos de invalidación

Objetos que se heredan de `NSControl` (o `NSView`) dispone de varias invalidar métodos de control de mouse o teclado de entrada. Para nuestro ejemplo, el control, deseamos examinar el estado del modificador entre **en** y **desactivar** cuando el usuario hace clic en el control con el botón primario del mouse. Podemos agregar lo siguiente invalidar métodos para la `NSFliwSwitch` clase para controlar esto:

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

En el código anterior, llamamos a la `FlipSwitchState` método (definido anteriormente) para voltear el On/Off de estado del conmutador de la `MouseDown` método. También se forzará el control se vuelva a dibujar para reflejar el estado actual.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Control de entrada de usuario con identificadores de gestos

Si lo desea, puede usar identificadores de gestos para controlar el usuario interactúa con el control. Quite las invalidaciones que agregó anteriormente, edite el `Initialize` método y darle un aspecto similar al siguiente:

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

En este caso, vamos a crear un nuevo `NSClickGestureRecognizer` y llamar a nuestro `FlipSwitchState` método para cambiar el estado del modificador cuando el usuario hace clic en él con el botón primario del mouse. El `AddGestureRecognizer (click)` método agrega el reconocedor de movimientos para el control.

Una vez más, qué método se usa depende de lo que estamos intentando realizar con nuestro control personalizado. Si se necesita acceso de nivel bajo la a la interacción del usuario, use los métodos de invalidación. Si se necesita la funcionalidad predefinida, como clics del mouse, utilice identificadores de gestos.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Responder a eventos de cambio de estado

Cuando el usuario cambia el estado de nuestro control personalizado, necesitamos una manera de responder al cambio de estado en el código (por ejemplo, realiza una acción cuando se hace clic en un botón personalizado). 

Para proporcionar esta funcionalidad, edite la `NSFlipSwitch` clase y agregue el código siguiente:

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

Segundo, puesto que nuestro control personalizado hereda de `NSControl`, automáticamente tiene un **acción** que se pueden asignar en el generador de interfaz de Xcode. Para llamar a esta **acción** cuando cambia el estado, usamos el siguiente código:

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

En primer lugar, se comprueba para ver si hay un **acción** se ha asignado al control. A continuación, llamamos a la **acción** si se ha definido.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Utilizar el Control personalizado

Con nuestro control personalizado que se ha definido totalmente, se podemos o agregarlo a la interfaz de usuario de la aplicación de nuestros Xamarin.Mac mediante código C# o en el generador de interfaz de Xcode.

Para agregar el control mediante el generador de interfaz, en primer lugar realice una compilación limpia del proyecto Xamarin.Mac, a continuación, haga doble clic en el `Main.storyboard` archivo para abrirlo en el generador de interfaz para su edición:

[ ![](custom-controls-images/custom02.png "Editar el guión gráfico en Xcode.")](custom-controls-images/custom02.png)

A continuación, arrastre un `Custom View` en el diseño de la interfaz de usuario:

[ ![](custom-controls-images/custom03.png "Seleccionar una vista personalizada de la biblioteca")](custom-controls-images/custom03.png)

Con la vista personalizada aún seleccionado, cambiar a la **identidad Inspector** y cambiar la vista **clase** a `NSFlipSwitch`:

[ ![](custom-controls-images/custom04.png "Configuración de clase de la vista")](custom-controls-images/custom04.png)

Cambie a la **Ayudante para el Editor** y crear un **toma** del control personalizado (y asegúrese de que enlazar en el `ViewControler.h` archivo y no el `.m` archivo):

[ ![](custom-controls-images/custom05.png "Configurar una nueva salida")](custom-controls-images/custom05.png)

Guarde los cambios, vuelva a Visual Studio para Mac y permita que los cambios sincronizar. Editar la `ViewController.cs` de archivos y realice la `ViewDidLoad` método aspecto similar al siguiente:

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

En este caso, respondamos a la `ValueChanged` evento definimos anteriormente en el `NSFlipSwitch` clase y escribir actual **valor** cuando el usuario hace clic en el control.

Si lo desea, podríamos devolver al generador de interfaz y definir una **acción** en el control:

[ ![](custom-controls-images/custom06.png "Configurar una nueva acción")](custom-controls-images/custom06.png)

Una vez más, modifique la `ViewController.cs` de archivos y agregue el siguiente método:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Se debe usar el **eventos** o definir una **acción** en el generador de interfaz, pero no debe utilizar ambos métodos al mismo tiempo o pueden entrar en conflicto entre sí.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de crear un Control personalizado de interfaz de usuario reutilizable en una aplicación Xamarin.Mac. Hemos visto cómo dibujar los controles personalizados la interfaz de usuario, los dos métodos principales para responder a mouse (ratón) y la intervención del usuario y cómo exponer el nuevo control a las acciones en el generador de interfaz de Xcode.

## <a name="related-links"></a>Vínculos relacionados

- [MacCustomControl (ejemplo)](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Enlace de datos y el valor de clave de codificación](~/mac/app-fundamentals/databinding.md)
- [Directrices de interfaz de sistema operativo X humanos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Control de eventos del Mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
