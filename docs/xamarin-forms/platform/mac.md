---
title: "Configuración de la plataforma de Mac"
description: Xamarin.Forms tiene ahora compatibilidad con vista previa para la plataforma de Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: bda207796d1019f8188176acce055d782cb9e32d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="mac-platform-setup"></a>Configuración de la plataforma de Mac

![Vista previa](~/media/shared/preview.png)

Antes de empezar, cree (o usar una existente) proyecto Xamarin.Forms.
Solo puede agregar aplicaciones de Mac con Visual Studio para Mac.

## <a name="adding-a-mac-app"></a>Agregar una aplicación de Mac

Siga estas instrucciones para agregar una aplicación de Mac que se ejecutará en macOS Sierra y Mac OS X El capitán:

1. En Visual Studio para Mac, haga doble clic en la solución de Xamarin.Forms existente y elija **Agregar > Agregar nuevo proyecto...**

2. En el **nuevo proyecto** ventana Elegir **Mac > aplicaciones > aplicación cacao** y presione **siguiente**.

3. Tipo de un **nombre de la aplicación** (y, opcionalmente, elija un nombre diferente para el elemento de acoplamiento), a continuación, presione **siguiente**.

4. Revise la configuración y presione **crear**. En estos pasos se muestran a continuación:

  ![Instrucciones animadas que muestra cómo agregar una aplicación de cacao](mac-images/add-macos-proj.gif)

5. En el proyecto de Mac, haga doble clic en **paquetes > Agregar paquetes...**  para agregar la [Xamarin.Forms/2.3.5.235-pre2](https://www.nuget.org/packages/Xamarin.Forms/2.3.5.235-pre2) NuGet. También debe actualizar los otros proyectos a esta versión.

6. En el proyecto de Mac, haga doble clic en **referencias** y agregue una referencia al proyecto de Xamarin.Forms (proyecto compartido o PCL).

  ![Agregue una referencia al proyecto de código compartido Xamarin.Forms](mac-images/references-sml.png)

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

  [ ![Desactive la casilla de verificación de controlador inicial está en Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png)

  Puede modificar el sistema de menú en el guión gráfico para quitar elementos no deseados.

10. Por último, agregue los recursos locales (p. ej. archivos de imagen) de los proyectos existentes de plataforma que son necesarios.

11. ¡El proyecto de Mac ahora debe ejecutar el código de Xamarin.Forms en macOS!

## <a name="next-steps"></a>Pasos siguientes

### <a name="styling"></a>Aplicación de estilos

Con cambios recientes realizados en `OnPlatform` ahora puede tener como destino cualquier número de plataformas. Esto incluye macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Tenga en cuenta también puede duplicar en plataformas similar al siguiente: `<On Platform="iOS, macOS" ...>`.

### <a name="window-size-and-position"></a>Posición y el tamaño de ventana

Puede ajustar el tamaño inicial y la ubicación de la ventana en el `AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemas conocidos

Se trata de una vista previa, por lo que se debe esperar a que no todo sea listos para producción. A continuación se muestran algunas cosas que puede encontrar cuando agregue macOS a los proyectos:

### <a name="not-all-nugets-are-ready-for-macos"></a>No todos los NuGets están listos para macOS

Los paquetes deben tener como destino "xamarinmac20" para que funcione en un proyecto de macOS. Es posible que algunas de las bibliotecas que usas aún no admiten macOS.

En este caso, debe enviar una solicitud al mantenedor del proyecto para agregarlo. Hasta que tengan compatibilidad, debe buscar alternativas.

### <a name="missing-xamarinforms-features"></a>Características que faltan de Xamarin.Forms

No todas las características de Xamarin.Forms están completos en esta vista previa; Esta es una lista de algunas de las funciones que no se ha implementado:

* Pie de página
* Imagen: aspecto
* ListView: ScrollTo, UnevenRows permiten, actualizar, SeparatorColor, SeparatorVisibility
* MasterDetailPage – BackgroundColor
* Navegación: InsertPageBefore
* OpenGLRenderer
* Selector: implementación de enlazable/Observable
* TabbedPage – BarBackgroundColor, BarTextColor
* TableView – UnevenRows
* ForceUpdateSize ViewCell – IsEnabled,
* WebView – WebNavigationEvents mayoría


## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Mac](~/mac/index.yml)
