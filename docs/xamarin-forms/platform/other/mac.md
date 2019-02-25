---
title: Configuración de la plataforma Mac
description: En este artículo se explica cómo agregar un proyecto de Mac a un proyecto de Xamarin.Forms, que generará una aplicación capaz de ejecutarse en macOS Sierra y El capitán de macOS.
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: 3a488b3a9f729da5d4bee8c1262190b15c2e9240
ms.sourcegitcommit: 0044d04990faa0b144b8626a4fceea0fdff95cfe
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2019
ms.locfileid: "56666901"
---
# <a name="mac-platform-setup"></a>Configuración de la plataforma Mac

![Vista previa](~/media/shared/preview.png)

Antes de empezar, cree (o usar una existente) proyecto de Xamarin.Forms. Solo puede agregar aplicaciones de Mac con Visual Studio para Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**Agregar un proyecto de macOS a Xamarin.Forms, por [Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-mac-app"></a>Adición de una aplicación de Mac

Siga estas instrucciones para agregar una aplicación de Mac que se ejecutará en macOS Sierra y El capitán de macOS:

1. En Visual Studio para Mac, haga doble clic en la solución existente de Xamarin.Forms y elija **Agregar > Agregar nuevo proyecto...**

2. En el **nuevo proyecto** ventana Elegir **Mac > aplicación > aplicación Cocoa** y presione **siguiente**.

3. Tipo de un **nombre de la aplicación** (y, opcionalmente, elija un nombre diferente para el elemento del Dock), a continuación, presione **siguiente**.

4. Revise la configuración y presione **crear**. En estos pasos se muestran a continuación:

    ![Instrucciones animadas que muestra cómo agregar una aplicación Cocoa](mac-images/add-macos-proj.gif)

5. En el proyecto de Mac, haga doble clic en **paquetes > Agregar paquetes...**  para agregar la [Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/) NuGet. También debe actualizar los otros proyectos para usar la misma versión del paquete Xamarin.Forms NuGet.

6. En el proyecto de Mac, haga doble clic en **referencias** y agregue una referencia al proyecto de Xamarin.Forms (proyecto de biblioteca de proyecto compartido o .NET Standard).

    ![Agregue una referencia al proyecto de código compartido de Xamarin.Forms](mac-images/references-sml.png)

7. Actualización **Main.cs** para inicializar el `AppDelegate`:

    ```csharp
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            NSApplication.SharedApplication.Delegate = new AppDelegate(); // add this line
            NSApplication.Main(args);
        }
    }
    ```

8. Actualización `AppDelegate` para inicializar Xamarin.Forms, crear una ventana y cargar la aplicación de Xamarin.Forms (sin olvidarse de definir un adecuado `Title`). _Si tiene otras dependencias que deben inicializarse, hacerlo aquí también._

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.MacOS;
    // also add a using for the Xamarin.Forms project, if the namespace is different to this file
    ...
    [Register("AppDelegate")]
    public class AppDelegate : FormsApplicationDelegate
    {
        NSWindow window;
        public AppDelegate()
        {
            var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

            var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
            window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            window.Title = "Xamarin.Forms on Mac!"; // choose your own Title here
            window.TitleVisibility = NSWindowTitleVisibility.Hidden;
        }

        public override NSWindow MainWindow
        {
            get { return window; }
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            Forms.Init();
            LoadApplication(new App());
            base.DidFinishLaunching(notification);
        }
    }
    ```

9. Haga doble clic en **Main.storyboard** para editar en Xcode. Seleccione el **ventana** y _desactive_ el **controlador inicial es** casilla (Esto es porque el código anterior crea una ventana):

    [![Desactive la casilla de controlador inicial está en Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    Puede editar el sistema de menú en el guión gráfico para quitar elementos no deseados.

10. Por último, agregue cualquier recurso local (p ej. archivos de imagen) de los proyectos existentes de plataforma que son necesarios.

11. El proyecto de Mac ahora debe ejecutar el código de Xamarin.Forms en macOS.

## <a name="next-steps"></a>Pasos siguientes

### <a name="styling"></a>Aplicación de estilos

Con los cambios recientes realizados en `OnPlatform` ahora puede definir cualquier número de plataformas. Esto incluye macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Tenga en cuenta también es posible que duplica en plataformas como esta: `<On Platform="iOS, macOS" ...>`.

### <a name="window-size-and-position"></a>Posición y tamaño de ventana

Puede ajustar el tamaño inicial y la ubicación de la ventana en la `AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemas conocidos

Se trata de una vista previa, por lo que debe esperar que no todo está lista para producción. A continuación se muestran algunas cosas que puede encontrarse con que agregue macOS a los proyectos:

### <a name="not-all-nugets-are-ready-for-macos"></a>No todos los paquetes de NuGet están listos para macOS

Es posible que algunas de las bibliotecas que usa aún no admite macOS. En este caso, deberá enviar una solicitud al mantenedor del proyecto para agregarlo. Hasta que tengan soporte técnico, deberá buscar otras alternativas.

### <a name="missing-xamarinforms-features"></a>Características de Xamarin.Forms que faltan

No todas las características de Xamarin.Forms son completadas en esta versión preliminar. Para obtener más información, consulte [compatibilidad con la plataforma macOS estado](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status) en el [Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms) repositorio de GitHub.

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Mac](~/mac/index.yml)
