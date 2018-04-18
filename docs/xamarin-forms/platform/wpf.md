---
title: Configuración de la plataforma WPF
description: Xamarin.Forms tiene ahora compatibilidad con vista previa para la plataforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 04/05/2018
ms.openlocfilehash: 51aad1643709a96c56ccad8187a53f47a65a9dac
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="wpf-platform-setup"></a>Configuración de la plataforma WPF

![Vista previa](~/media/shared/preview.png)

Xamarin.Forms tiene ahora compatibilidad de vista previa de Windows Presentation Foundation (WPF). En este artículo se muestra cómo agregar un proyecto de WPF a una solución de Xamarin.Forms.

Antes de empezar, cree una nueva solución de Xamarin.Forms en Visual Studio de 2017 o usar una solución Xamarin.Forms, por ejemplo, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/). Solo puede agregar aplicaciones de WPF a una solución de Xamarin.Forms en Windows.

## <a name="adding-a-wpf-app"></a>Agregar una aplicación WPF

Siga estas instrucciones para agregar una aplicación WPF que se ejecutará en los escritorios de Windows 7, 8 y 10:

1. En Visual Studio de 2017, haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y elija **Agregar > Nuevo proyecto...** .

2. En el **nuevo proyecto** ventana, en la izquierda, seleccione **Visual C#** y **escritorio clásico de Windows**. En la lista de tipos de proyecto, elija **aplicación de WPF (.NET Framework)**. 

3. Escriba un nombre para el proyecto con un **WPF** extensión, por ejemplo, **BoxViewClock.WPF**. Haga clic en el **examinar** botón, seleccione la **BoxViewClock** carpeta y presione **seleccionar la carpeta**. Esto colocará el proyecto de WPF en el mismo directorio que los otros proyectos de la solución.

    ![Agregue un nuevo proyecto WPF](wpf-images/add-new-project.png "agregar un nuevo proyecto WPF")

    Haga clic en Aceptar para crear el proyecto.

4. En el **el Explorador de soluciones**, derecha haga clic en el nuevo **BoxViewClock.WPF** de proyecto y seleccione **administrar paquetes de NuGet**. Seleccione el **examinar** , haga clic en el **incluir versión preliminar** casilla de verificación y busque **Xamarin.Forms**.

    ![Seleccione el paquete de NuGet](wpf-images/select-nuget-package.png "seleccione el paquete de NuGet")

    Seleccione paquete y haga clic en el **instalar** botón.

5. Ahora busque **Xamarin.Forms.Platform.WPF** empaquetar e instalar dicho uno así. Asegúrese de que el paquete es de Microsoft.

6. Haga clic con el nombre de la solución en el **el Explorador de soluciones** y seleccione **administrar paquetes de NuGet para la solución**. Seleccione el **actualización** ficha y **Xamarin.Forms** paquete. Seleccione todos los proyectos y actualizarlos a la misma versión de Xamarin.Forms:

    ![Actualizar el paquete de NuGet](wpf-images/update-nuget-package.png "actualizar el paquete de NuGet") 

7. En el proyecto WPF, haga doble clic en **referencias**. En el **Administrador de referencias** cuadro de diálogo, seleccione **proyectos** a la izquierda y activar la casilla de verificación junto a la **BoxViewClock** proyecto:

    ![Hacer referencia al proyecto compartido](wpf-images/reference-shared-project.png "hagan referencia al proyecto compartido")

8. Editar la **MainWindow.xaml** archivo del proyecto WPF. En el `Window` etiqueta, agregue una declaración de espacio de nombres XML para el **Xamarin.Forms.Platform.WPF** ensamblado y espacio de nombres:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Ahora, cambie la `Window` etiqueta a `wpf:FormsApplcationPage`. Cambiar el `Title` si se establece en el nombre de la aplicación, por ejemplo, **BoxViewClock**. El archivo XAML completado debería tener este aspecto:

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>
        
        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. Editar la **MainWindow.xaml.cs** archivo del proyecto WPF. Agregue dos nuevas `using` directivas:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Cambie la clase base de `MainWindow` de `Window` a `FormsApplicationPage`. Después de la `InitializeComponent` llamar, agregue las dos instrucciones siguientes:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    Excepto para comentarios y sin usar `using` directivas, la sección completa **MainWindows.xaml.cs** archivo debería ser similar al siguiente:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

10. Haga clic en el proyecto WPF en el **el Explorador de soluciones** y seleccione **establecer como proyecto de inicio**. Presione F5 para ejecutar el programa con el depurador de Visual Studio en el escritorio de Windows:

    ![Reloj WPF BoxView](wpf-images/wpf-boxviewclock.png "WPF BoxView reloj" )

## <a name="next-steps"></a>Pasos siguientes

### <a name="platform-specifics"></a>Características específicas de plataforma

Puede determinar qué plataforma se ejecuta la aplicación de Xamarin.Forms código o en XAML. Esto le permite cambiar las características del programa cuando se ejecuta en WPF. En el código, compare el valor de `Device.RuntimePlatform` con el `Device.WPF` constante (que es igual a la cadena "WPF"). Si se encuentra una coincidencia, la aplicación se ejecuta en WPF.

En XAML, puede usar el `OnPlatform` etiqueta para seleccionar un valor de propiedad específico de la plataforma:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="window-size"></a>Tamaño de ventana

Puede ajustar el tamaño inicial de la ventana de WPF **MainWindow.xaml** archivo:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problemas

Se trata de una vista previa, por lo que se debe esperar a que no todo sea listos para producción. No todos los paquetes de NuGet para Xamarin.Forms están listos para WPF y algunas características podrían no estar totalmente funcional.

