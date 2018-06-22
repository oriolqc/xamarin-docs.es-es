---
title: Introducción a OpenTK en Xamarin.Mac
description: Este artículo proporciona una introducción a trabajar con OpenTK en una aplicación Xamarin.Mac. Describe cómo crear y mantener una ventana de juego, representar un objeto simple y mostrar el objeto al usuario.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 448b8bdba8ccedbb732a73a265d0ce76bb589190
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792611"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Introducción a OpenTK en Xamarin.Mac

OpenTK (el Kit de herramientas abierta) es una avanzada, bajo nivel C# biblioteca que facilita el trabajo con OpenGL, OpenCL y OpenAL. OpenTK puede utilizarse para juegos, aplicaciones científicas u otros proyectos que requieren gráficos 3D, la funcionalidad de audio o de cálculo. Este artículo proporciona una breve introducción al uso de OpenTK en una aplicación Xamarin.Mac.

[![](opentk-images/intro01.png "Ejecutar una aplicación de ejemplo")](opentk-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos de OpenTK en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>Acerca de OpenTK

Como se mencionó anteriormente, OpenTK (el Kit de herramientas abierta) es una avanzada, bajo nivel C# biblioteca que facilita el trabajo con OpenGL, OpenCL y OpenAL. El uso de OpenTK en una aplicación Xamarin.Mac ofrece las siguientes características:

- **Desarrollo rápido de** -OpenTK proporciona tipos de datos segura y documentación en línea para mejorar el flujo de trabajo de codificación y detectar errores de forma más sencilla y más pronto.
- **Una integración fácil** -OpenTK se ha diseñado para integrarse fácilmente con aplicaciones. NET.
- **Licencia permisiva** -OpenTK se distribuye según las licencias MIT/X11 y es totalmente gratuita.
- **Enriquecido, los enlaces de seguridad de tipos** -OpenTK es compatible con las versiones más recientes de OpenGL, OpenGL | ES, OpenAL y OpenCL con carga automáticas de extensión, documentación de error de comprobación y en línea.
- **Opciones flexibles de GUI** -OpenTK proporciona nativo, la ventana de juego de alto rendimiento diseñado específicamente para juegos y Xamarin.Mac.
- **Completamente administrado, código conforme a CLS** -OpenTK admite versiones de 32 bits y 64 bits de macOS con ningún bibliotecas no administradas.
- **Kit de herramientas de matemáticas 3D** OpenTK proporciona `Vector`, `Matrix`, `Quaternion` y `Bezier` structs a través de su kit de herramientas de matemáticas 3D.

OpenTK puede utilizarse para juegos, aplicaciones científicas u otros proyectos que requieren gráficos 3D, la funcionalidad de audio o de cálculo.

Para obtener más información, vea [el Kit de herramientas abierta](http://www.opentk.com) sitio Web.

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Inicio rápido de OpenTK

Como una introducción rápida al uso de OpenTK en una aplicación Xamarin.Mac, vamos a crear una aplicación simple que abre una vista de juego, representa un triángulo simple en esa vista y attachs la vista de juego a la ventana principal de la aplicación de Mac para mostrar el triángulo al usuario.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Inicie un nuevo proyecto

Inicie Visual Studio para Mac y crear una nueva solución de Xamarin.Mac. Seleccione **Mac** > **aplicación** > **General** > **cacao aplicación**:

[![](opentk-images/sample01.png "Agregar una nueva aplicación de cacao")](opentk-images/sample01.png#lightbox)

Escriba `MacOpenTK` para el **nombre de proyecto**:

[![](opentk-images/sample02.png "Establecer el nombre del proyecto")](opentk-images/sample02.png#lightbox)

Haga clic en el **crear** para crear el nuevo proyecto.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Incluidos OpenTK

Antes de poder usar TK abierto en una aplicación Xamarin.Mac, deberá incluir una referencia al ensamblado OpenTK. En el **el Explorador de soluciones**, haga clic en el **referencias** carpeta y seleccione **editar referencias...** .

Colocar una marca de verificación por `OpenTK` y haga clic en el **Aceptar** botón:

[![](opentk-images/sample03.png "Editar las referencias del proyecto")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Usar OpenTK

Con el nuevo proyecto creado, haga doble clic en el `MainWindow.cs` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo. Realizar la `MainWindow` clase aspecto similar al siguiente:

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

Veamos este código en detalle a continuación.

<a name="Required_APIs" />

### <a name="required-apis"></a>API necesarias

Varias referencias son necesarios para usar OpenTK en una clase Xamarin.Mac. Hemos incluido lo siguiente al principio de la definición de `using` instrucciones:

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
Este conjunto mínimo será necesario para cualquier clase mediante OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Agregar la vista de juego

A continuación se necesita crear una vista de juego para contener todos nuestra interacción con OpenTK y mostrar los resultados. Hemos usado el código siguiente:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Aquí hemos simplificado la vista de juego el mismo tamaño que la ventana principal de Mac y reemplazan la vista de contenido de la ventana con el nuevo `MonoMacGameView`. Dado que se reemplaza el contenido de la ventana existente, nuestra vista dio cambiará automáticamente de tamaño cuando se cambia el tamaño de las ventanas principales.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Respuesta a eventos

Hay varios eventos de predeterminado que debe responder cada vista de juego. En esta sección explica los eventos principales necesarios.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>El evento de carga

El `Load` eventos es el lugar para cargar los recursos de disco como imágenes, texturas o música. Para nuestro simple, la aplicación de prueba, no estamos utilizando el `Load` eventos, pero incluyeran como referencia:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>El evento de cambio de tamaño

El `Resize` debe llamarse evento cada vez que se cambia el tamaño de la vista de juego. Para nuestra aplicación de ejemplo, estamos realizando la ventanilla GL el mismo tamaño que la vista de juego (que es ser automáticamente al cambiar el tamaño de la ventana principal de Mac) con el código siguiente:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>El evento UpdateFrame

El `UpdateFrame` eventos se usan para controlar los proporcionados por el usuario, actualice las posiciones de objeto, ejecución física o cálculos de AI. Para nuestro simple, la aplicación de prueba, no estamos utilizando el `UpdateFrame` eventos, pero incluyeran como referencia: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> La implementación de Xamarin.Mac de OpenTK no incluye el `Input API`, por lo que deberá usar el Apple proporcionaba soportan API para agregar el teclado y mouse (ratón). Opcionalmente, puede crear una instancia personalizada de la `MonoMacGameView` e invalide el `KeyDown` y `KeyUp` métodos.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>El evento RenderFrame

El `RenderFrame` evento contiene el código que se usa para representar (draw) los gráficos. Para nuestra aplicación de ejemplo, se está rellenando la vista de juego con un triángulo simple: 

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

Normalmente el código de representación hará lo que es con una llamada a `GL.Clear` para quitar todos los elementos existentes antes de dibujar los nuevos elementos.

> [!IMPORTANT]
> Para la versión de Xamarin.Mac de OpenTK **no** llamar a la `SwapBuffers` método de su `MonoMacGameView` instancia al final de su código de representación. Si lo hace, provocará la vista de juego a strobe rápidamente en lugar de mostrar la vista representada.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Ejecutar la vista de juego

Con todos los eventos necesarios definir y la vista de juego se adjunta a la ventana principal de Mac de nuestra aplicación, estamos leemos para ejecutar la vista de juego y mostrar el gráfico. Utilice el código siguiente:

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Se pasa en la velocidad de fotogramas deseado que queremos que la vista de juego para actualizar a, en nuestro ejemplo que hemos elegido `60` fotogramas por segundo (la misma frecuencia de actualización como TV normal).

Vamos a ejecutar la aplicación y ver el resultado:

[![](opentk-images/intro01.png "Un ejemplo de la salida de aplicaciones")](opentk-images/intro01.png#lightbox)

Si se cambia el tamaño de la ventana, la vista de juego también será residen y el triángulo que se cambia el tamaño y también actualiza en tiempo real.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>¿Dónde siguiente?

Con los conceptos básicos sobre cómo trabajar con OpenTk en una aplicación Xamarin.mac realizada, éstas son algunas sugerencias de lo que se debe probar los siguiente:

- Pruebe a cambiar el color del triángulo y el color de fondo de la vista de juego en el `Load` y `RenderFrame` eventos.
- Asegúrese de cambiar el color cuando el usuario presiona una tecla en el triángulo la `UpdateFrame` y `RenderFrame` eventos o realizar su propio personalizado `MonoMacGameView` clase e invalidar el `KeyUp` y `KeyDown` métodos.
- Asegúrese de mover a través de la pantalla mediante las teclas en cuenta en el triángulo el `UpdateFrame` eventos. Sugerencia: utilice el `Matrix4.CreateTranslation` método para crear una matriz de traslación y llame el `GL.LoadMatrix` método para cargarlo en el `RenderFrame` eventos.
- Use un `for` bucle para representar varios triángulos en el `RenderFrame` eventos.
- Gire la cámara para proporcionar una vista diferente del triángulo en el espacio 3D. Sugerencia: utilice el `Matrix4.CreateTranslation` método para crear una matriz de traslación y llame el `GL.LoadMatrix` método para cargar el recurso. También puede usar el `Vector2`, `Vector3`, `Vector4` y `Matrix4` clases de manipulaciones de la cámara.

Para obtener más ejemplos, vea el [OpenTK ejemplos de GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples) repositorio. Contiene una lista de ejemplos de uso OpenTK oficial. Tendrá que adaptar estos ejemplos para usar con la versión de Xamarin.Mac de OpenTK.

Para obtener un ejemplo más complejo de Xamarin.Mac de una implementación de OpenTK, visite nuestro [MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/) ejemplo.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado un vistazo al trabajar con OpenTK en una aplicación Xamarin.Mac. Hemos visto cómo crear una ventana de juego, cómo adjuntar la ventana de juego en una ventana de Mac y cómo representar una forma simple en la ventana de juego.

## <a name="related-links"></a>Vínculos relacionados

- [MacOpenTK (ejemplo)](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView (ejemplo)](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con ventanas](~/mac/user-interface/window.md)
- [El Kit de herramientas abierta](http://www.opentk.com)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
