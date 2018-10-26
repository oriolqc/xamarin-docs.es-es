---
title: Introducción a OpenTK en Xamarin.Mac
description: En este artículo se proporciona una introducción a trabajar con OpenTK en una aplicación de Xamarin.Mac. Tratan de crear y mantener una ventana de juego, representar un objeto simple y mostrar el objeto al usuario.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 835b8cd0f2e689c4d7d4cace1d846543863b7393
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107721"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Introducción a OpenTK en Xamarin.Mac

OpenTK (el Kit de herramientas abierto) es una avanzada, bajo nivel biblioteca de C# que facilita el trabajo con OpenGL y OpenCL, OpenAL. OpenTK puede usarse para juegos, aplicaciones científicas u otros proyectos que requieren gráficos 3D, la funcionalidad de audio o de cálculo. En este artículo se ofrece una breve introducción al uso de OpenTK en una aplicación de Xamarin.Mac.

[![](opentk-images/intro01.png "Ejecutar una aplicación de ejemplo")](opentk-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos de OpenTK en una aplicación de Xamarin.Mac. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` comandos se usa para conexión de seguridad de las clases de C# para objetos de Objective-C y elementos de interfaz de usuario.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>Acerca de OpenTK

Como se indicó anteriormente, OpenTK (el Kit de herramientas abierto) es una avanzada, bajo nivel C# biblioteca que facilita el trabajo con OpenGL y OpenCL, OpenAL. Uso de OpenTK en una aplicación de Xamarin.Mac proporciona las siguientes características:

- **Desarrollo rápido** -OpenTK proporciona tipos de datos segura y la documentación en línea para mejorar el flujo de trabajo de codificación y detectar los errores más fácil y rápidamente.
- **Fácil integración** -OpenTK se diseñó para integrar fácilmente con aplicaciones. NET.
- **Licencia permisiva** -OpenTK se distribuye según las licencias MIT/X11 y es totalmente gratuita.
- **Enriquecido, los enlaces de seguridad de tipos** -OpenTK es compatible con las versiones más recientes de OpenGL, OpenGL | ES, OpenAL y OpenCL con la carga automática de extensión, documentación de error de comprobación y en línea.
- **Opciones flexibles de GUI** -OpenTK proporciona nativo, la ventana de juego de alto rendimiento diseñado específicamente para juegos y Xamarin.Mac.
- **Totalmente administrado y código conforme a CLS** -OpenTK es compatible con versiones de 32 bits y 64 bits de macOS sin bibliotecas no administradas.
- **Kit de herramientas de matemáticas 3D** OpenTK proporciona `Vector`, `Matrix`, `Quaternion` y `Bezier` structs a través de su kit de herramientas de matemáticas 3D.

OpenTK puede usarse para juegos, aplicaciones científicas u otros proyectos que requieren gráficos 3D, la funcionalidad de audio o de cálculo.

Para obtener más información, consulte [el Kit de herramientas abierto](http://www.opentk.com) sitio Web.

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Guía de inicio rápido de OpenTK

Como una introducción rápida al uso de OpenTK en una aplicación de Xamarin.Mac, vamos a crear una aplicación simple que abre una vista de juego, representa un triángulo en esa vista y la vista de juego attachs simple en la ventana principal de la aplicación de Mac para mostrar el triángulo al usuario.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Iniciar un nuevo proyecto

Inicie Visual Studio para Mac y crear una nueva solución de Xamarin.Mac. Seleccione **Mac** > **aplicación** > **General** > **Cocoa App**:

[![](opentk-images/sample01.png "Agregar una nueva aplicación de Cocoa")](opentk-images/sample01.png#lightbox)

Escriba `MacOpenTK` para el **nombre del proyecto**:

[![](opentk-images/sample02.png "Establecer el nombre del proyecto")](opentk-images/sample02.png#lightbox)

Haga clic en el **crear** para crear el nuevo proyecto.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Incluyendo OpenTK

Para poder usar TK abierta en una aplicación de Xamarin.Mac, deberá incluir una referencia al ensamblado OpenTK. En el **el Explorador de soluciones**, haga clic en el **referencias** carpeta y seleccione **editar referencias...** .

Coloque una marca de verificación por `OpenTK` y haga clic en el **Aceptar** botón:

[![](opentk-images/sample03.png "Edición de las referencias del proyecto")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Uso de OpenTK

Con el nuevo proyecto creado, haga doble clic en el `MainWindow.cs` de archivos en el **el Explorador de soluciones** para abrirlo y editarlo. Realice la `MainWindow` clase aspecto parecido al siguiente:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

Vamos a explicar este código en detalle a continuación.

<a name="Required_APIs" />

### <a name="required-apis"></a>API necesarias

Varias referencias deben usar OpenTK en una clase de Xamarin.Mac. Hemos incluido lo siguiente al principio de la definición de `using` instrucciones:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```
Este conjunto mínimo será necesario para cualquier clase utilizando OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Agregar la vista de juego

A continuación, necesitamos crear una vista de juego para contener todos de la interacción con OpenTK y mostrar los resultados. Hemos usado el código siguiente:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Aquí hemos tomado la vista de juego del mismo tamaño que nuestra ventana principal de Mac y reemplaza la vista de contenido de la ventana con el nuevo `MonoMacGameView`. Dado que se reemplaza el contenido de la ventana existente, nuestra vista dio cambiará de tamaño automáticamente cuando se cambia el tamaño de la principal de Windows.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Respuesta a eventos

Hay varios eventos de forma predeterminada que debe responder cada vista de juego a. En esta sección tratará los principales eventos necesarios.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>El evento de carga

El `Load` eventos es el lugar para cargar los recursos de disco como imágenes, las texturas o música. Para nuestra sencilla aplicación de prueba, no estamos usando el `Load` eventos, pero se ha incluido como referencia:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>El evento de cambio de tamaño

El `Resize` eventos deben llamarse cada vez que se cambia el tamaño de la vista de juego. Para nuestra aplicación de ejemplo, estamos realizando la contabilidad de ventanilla del mismo tamaño que nuestra vista de juego (que es automática, cambiar el tamaño de la ventana principal de Mac) con el código siguiente:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>El evento UpdateFrame

El `UpdateFrame` eventos se usan para controlar la entrada de usuario, actualizar las posiciones de objeto, ejecución física o cálculos de inteligencia artificial. Para nuestra sencilla aplicación de prueba, no estamos usando el `UpdateFrame` eventos, pero se ha incluido como referencia: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> La aplicación de Xamarin.Mac de OpenTK no incluye el `Input API`, por lo que deberá usar el Apple proporciona compatibilidad con las API para agregar el teclado y mouse (ratón). Opcionalmente, puede crear una instancia personalizada de la `MonoMacGameView` e invalidar la `KeyDown` y `KeyUp` métodos.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>El evento RenderFrame

El `RenderFrame` evento contiene el código que se usa para representar (draw) los gráficos. Para nuestra aplicación de ejemplo, nos estamos rellenando la vista de juego con un triángulo simple: 

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

Normalmente, el código de representación hará que es con una llamada a `GL.Clear` para quitar todos los elementos existentes antes de dibujar los nuevos elementos.

> [!IMPORTANT]
> Para la versión de Xamarin.Mac de OpenTK **no** llamar a la `SwapBuffers` método de su `MonoMacGameView` instancia al final de su código de representación. Si lo hace, hará que la vista de juego para strobe rápidamente, en lugar de mostrar la vista representada.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Ejecución de la vista de juego

Con todos los eventos necesarios definir y la vista de juego asociados a la ventana principal de Mac de nuestra aplicación, estamos leemos para ejecutar la vista de juego y mostrar nuestros gráficos. Utilice el código siguiente:

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Se pasa en la velocidad de fotograma deseada que se va a actualizar en la vista de juego, en nuestro ejemplo hemos elegido `60` fotogramas por segundo (la misma frecuencia de actualización como Televisor normal).

Vamos a ejecutar nuestra aplicación y ver la salida:

[![](opentk-images/intro01.png "Un ejemplo de la salida de aplicaciones")](opentk-images/intro01.png#lightbox)

Si se cambia el tamaño de la ventana, la vista de juego también será residen y se cambia el tamaño del triángulo y también actualiza en tiempo real.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>¿Qué hacer después?

Con los aspectos básicos de trabajar con OpenTk en una aplicación de Xamarin.mac hecha, estas son algunas sugerencias de lo que pruebe el siguiente:

- Pruebe a cambiar el color del triángulo y el color de fondo de la vista de juego en el `Load` y `RenderFrame` eventos.
- Asegúrese de cambiar el color cuando el usuario presione una tecla en el triángulo el `UpdateFrame` y `RenderFrame` eventos o realizar su propia personalizada `MonoMacGameView` clase e invalidar la `KeyUp` y `KeyDown` métodos.
- Asegúrese de mover a través de la pantalla con las claves de cuenta en el triángulo el `UpdateFrame` eventos. Sugerencia: utilice el `Matrix4.CreateTranslation` método para crear una matriz de traslación y llame a la `GL.LoadMatrix` método para cargarlo en el `RenderFrame` eventos.
- Use un `for` bucle para representar varios triángulos en el `RenderFrame` eventos.
- Girar la cámara para proporcionar una vista diferente del triángulo en el espacio 3D. Sugerencia: utilice el `Matrix4.CreateTranslation` método para crear una matriz de traslación y llame a la `GL.LoadMatrix` método para cargarlo. También puede usar el `Vector2`, `Vector3`, `Vector4` y `Matrix4` clases para las manipulaciones de cámara.

Para obtener más ejemplos, vea el [OpenTK ejemplos de GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples) repositorio. Contiene una lista de ejemplos del uso de OpenTK oficial. Tendrá que adaptar estos ejemplos para usar con la versión de Xamarin.Mac de OpenTK.

Para obtener un ejemplo de Xamarin.Mac más complejo de una implementación OpenTK, consulte nuestra [MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/) ejemplo.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado un vistazo rápido a trabajar con OpenTK en una aplicación de Xamarin.Mac. Hemos visto cómo crear una ventana de juego, cómo asociar la ventana de juego a una ventana de Mac y cómo representar una forma simple en la ventana de juego.

## <a name="related-links"></a>Vínculos relacionados

- [MacOpenTK (ejemplo)](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView (ejemplo)](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [El Kit de herramientas abierto](http://www.opentk.com)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
