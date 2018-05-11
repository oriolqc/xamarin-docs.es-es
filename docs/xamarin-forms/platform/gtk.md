---
title: 'Configuración de la plataforma GTK #'
description: 'Xamarin.Forms tiene ahora compatibilidad con vista previa para la plataforma GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 275ec851a2fd8e96adecfeca5daf6a66add7bd92
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="gtk-platform-setup"></a>Configuración de la plataforma GTK #

![Vista previa](~/media/shared/preview.png)

Xamarin.Forms ahora es compatible con vista previa aplicaciones GTK #. GTK # es un Kit de herramientas de interfaz gráfica de usuario que se vincula el Kit de herramientas GTK + y una gran variedad de bibliotecas GNOME, lo que permite el desarrollo de aplicaciones de gráficos GNONE totalmente nativas mediante Mono y. NET. En este artículo se muestra cómo agregar un proyecto GTK # a una solución de Xamarin.Forms.

Antes de empezar, cree una nueva solución de Xamarin.Forms o usar una solución Xamarin.Forms, por ejemplo, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/).

> [!NOTE]
> Aunque en este artículo se centra en cómo agregar una aplicación GTK # a una solución de Xamarin.Forms en VS2017 y Visual Studio para Mac, también se pueden realizar en [MonoDevelop](http://www.monodevelop.com/) para Linux.

## <a name="adding-a-gtk-app"></a>Agregar una aplicación GTK #

GTK # para Mac OS y Linux se instalan como parte de [Mono](http://www.mono-project.com/download/stable/). GTK # para .NET puede instalarse en Windows con el [GTK # instalador](http://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Siga estas instrucciones para agregar una aplicación GTK # que se ejecutará en el escritorio de Windows:

1. En Visual Studio de 2017, haga doble clic en el nombre de la solución en **el Explorador de soluciones** y elija **Agregar > Nuevo proyecto...** .

2. En el **nuevo proyecto** ventana, en la izquierda, seleccione **Visual C#** y **escritorio clásico de Windows**. En la lista de tipos de proyecto, elija **biblioteca de clases (.NET Framework)** y asegúrese de que el **Framework** desplegable se establece en un mínimo de .NET Framework 4.7.

3. Escriba un nombre para el proyecto con un **GTK** extensión, por ejemplo **GameOfLife.GTK**. Haga clic en el **examinar** botón, seleccione la carpeta que contiene la plataforma de otra proyectos y presione **seleccionar la carpeta**. Esto colocará el proyecto GTK en el mismo directorio que los otros proyectos de la solución.

    ![Agregue un nuevo proyecto GTK](gtk-images/win/add-new-project.png "agregar un nuevo proyecto GTK")

    Presione el **Aceptar** botón para crear el proyecto.

4. En el **el Explorador de soluciones**, haga clic en el nuevo proyecto GTK y seleccione **administrar paquetes de NuGet**. Seleccione el **examinar** , haga clic en el **versión preliminar de inclusión** casilla de verificación y busque **Xamarin.Forms** 3.0 o superior.

    ![Seleccione el paquete Xamarin.Forms NuGet](gtk-images/win/select-forms-nuget-package.png "seleccione el paquete Xamarin.Forms NuGet")

    Seleccione el paquete y haga clic en el **instalar** botón.

5. Ahora busque la **Xamarin.Forms.Platform.GTK** paquete 3.0 o superior.

    ![Seleccione el paquete de Xamarin.Forms.Platform.GTK NuGet](gtk-images/win/select-forms-platform-nuget-package.png "seleccione el paquete de Xamarin.Forms.Platform.GTK NuGet")

    Seleccione el paquete y haga clic en el **instalar** botón.

6. En el **el Explorador de soluciones**, haga clic en el nombre de la solución y seleccione **administrar paquetes de NuGet para la solución**. Seleccione el **actualización** ficha y **Xamarin.Forms** paquete. Seleccione todos los proyectos y actualizarlos a la misma versión de Xamarin.Forms utilizados por el proyecto GTK.

7. En el **el Explorador de soluciones**, haga doble clic en **referencias** en el proyecto GTK. En el **Administrador de referencias** cuadro de diálogo, seleccione **proyectos** a la izquierda y activar la casilla de verificación junto al proyecto de .NET estándar o compartido:

    ![Hacer referencia al proyecto compartido](gtk-images/win/reference-shared-project.png "hagan referencia al proyecto compartido")

8. En el **Administrador de referencias** cuadro de diálogo, presione la **examinar** botón y vaya a la **C:\Program Files (x86)\GtkSharp\2.12\lib** carpeta y seleccione el  **ATK sharp.dll**, **gdk sharp.dll**, **glade sharp.dll**, **glib sharp.dll**, **gtk-dotnet.dll**, **sharp.dll gtk** archivos.

    ![Hacer referencia a las bibliotecas GTK #](gtk-images/win/reference-gtk-libraries.png "hacen referencia a las bibliotecas GTK #")

    Presione el **Aceptar** botón para agregar las referencias.

9. En el proyecto GTK, cambie el nombre **Class1.cs** a **Program.cs**.

10. En el proyecto GTK, editar la **Program.cs** de archivos para que lo es similar al código siguiente:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Este código inicializa GTK # y Xamarin.Forms, crea una ventana de aplicación y ejecuta la aplicación.

11. En el **el Explorador de soluciones**, haga clic en el proyecto GTK y seleccione **propiedades**.

12. En el **propiedades** ventana, seleccione la **aplicación** pestaña y cambie el **tipo de salida** lista desplegable- **aplicación de Windows**.

    ![Cambiar el tipo de salida del proyecto](gtk-images/win/change-project-output-type.png "cambiar el tipo de salida del proyecto")

13. En el **el Explorador de soluciones**, haga clic en el proyecto WPF y seleccione **establecer como proyecto de inicio**. Presione F5 para ejecutar el programa con el depurador de Visual Studio en el escritorio de Windows:

    ![Juego GTK # del ciclo de vida](gtk-images/win/gtk-gameoflife.png "GTK # juego del ciclo de vida")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Siga estas instrucciones para agregar una aplicación GTK # que se ejecutará en el escritorio de Mac:

1. En Visual Studio para Mac, haga doble clic en la solución de Xamarin.Forms y elija **Agregar > Agregar nuevo proyecto...** .

2. En el **nuevo proyecto** ventana Elegir **otros > .NET > Gtk # 2.0 proyecto** y presione **siguiente**.

3. Escriba un nombre para el proyecto con un **GTK** extensión, por ejemplo **GameOfLife.GTK**y presione **crear**.

4. En el **solución Pad**, haga doble clic en **paquetes > Agregar paquetes...**  para el GTK del proyecto y agregue el paquete de NuGet de versión preliminar de Xamarin.Forms 3.0 o superior.

    ![Seleccione el paquete Xamarin.Forms NuGet](gtk-images/mac/select-forms-nuget-package.png "seleccione el paquete Xamarin.Forms NuGet")

5. En el **solución Pad**, haga doble clic en **paquetes > Agregar paquetes...**  para el GTK del proyecto y agregue el paquete de NuGet de versión preliminar de Xamarin.Forms.Platform.GTK 3.0 o superior.

    ![Seleccione el paquete de Xamarin.Forms.Platform.GTK NuGet](gtk-images/mac/select-forms-platform-nuget-package.png "seleccione el paquete de Xamarin.Forms.Platform.GTK NuGet")

6. Actualizar los otros proyectos de plataforma para usar la misma versión de Xamarin.Forms que usó el proyecto GTK.

7. En el **solución Pad**, haga doble clic en **referencias > Editar referencias...**  para el GTK del proyecto y agregue una referencia al proyecto de Xamarin.Forms (estándar de .NET o proyecto compartido).

    ![Hacer referencia al proyecto compartido](gtk-images/mac/reference-shared-project.png "hagan referencia al proyecto compartido")

8. Editar la **Program.cs** archivo del proyecto GTK por lo que TI es similar al código siguiente:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Este código inicializa GTK # y Xamarin.Forms, crea una ventana de aplicación y ejecuta la aplicación.

9. En el **solución Pad**, haga clic en el proyecto GTK y seleccione **establecer como proyecto de inicio**.

10. En Visual Studio para la barra de herramientas de Mac, presione el **iniciar** botón (el botón triangular que es similar a un botón de reproducción) para iniciar la aplicación.

    ![Juego GTK # del ciclo de vida](gtk-images/mac/gtk-gameoflife.png "GTK # juego del ciclo de vida")

-----

## <a name="next-steps"></a>Pasos siguientes

### <a name="platform-specifics"></a>Características específicas de plataforma

Puede determinar qué plataforma se ejecuta la aplicación de Xamarin.Forms XAML o en código. Esto le permite cambiar las características del programa cuando se ejecuta en GTK #. En el código, compare el valor de `Device.RuntimePlatform` con el `Device.GTK` constante (que es igual a la cadena "GTK"). Si hay una coincidencia, la aplicación se ejecuta en GTK #.

En XAML, puede usar el `OnPlatform` etiqueta para seleccionar un valor de propiedad específico de la plataforma:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>Icono de aplicación

Puede establecer el icono de la aplicación en el inicio:

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>Temas

Hay una amplia variedad de temas disponibles para GTK #, y pueden usarse desde una aplicación de Xamarin.Forms:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Formularios nativos

Formularios nativos permite Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado páginas a ser consumidos por los proyectos nativos, incluidos los proyectos GTK #. Esto puede realizarse mediante la creación de una instancia de la [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivados de página y convertirla en el nativo GTK # tipo usando el `CreateContainer` método de extensión:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Para obtener más información acerca de los formularios nativo, vea [formularios nativos](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problemas

Se trata de una vista previa, por lo que se debe esperar a que no todo sea listos para producción. Para el estado actual de la implementación, consulte [estado](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)y para los problemas conocidos actuales, consulte [problemas conocidos y respuestas pendientes](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
