---
title: Configuración de la plataforma WPF
description: Xamarin.Forms tiene ahora compatibilidad con la versión preliminar para la plataforma WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: cdf115c4ea6d6613a1da2d0d2cfa14ed500086f8
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209190"
---
# <a name="wpf-platform-setup"></a>Configuración de la plataforma WPF

![Vista previa](~/media/shared/preview.png)

Xamarin.Forms tiene ahora la compatibilidad de versión preliminar de Windows Presentation Foundation (WPF). En este artículo se muestra cómo agregar un proyecto de WPF a una solución de Xamarin.Forms.

Antes de empezar, cree una nueva solución de Xamarin.Forms en Visual Studio 2017 o usar una solución de Xamarin.Forms existente, por ejemplo, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/). Las aplicaciones de WPF solo se pueden agregar a una solución de Xamarin.Forms en Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Agregar un proyecto de WPF a una aplicación de Xamarin.Forms con Xamarin.University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 la compatibilidad de WPF, por [Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>Agregar una aplicación WPF

Siga estas instrucciones para agregar una aplicación WPF que se ejecutará en los escritorios de Windows 7, 8 y 10:

1. En Visual Studio 2017, haga doble clic en el nombre de la solución en el **el Explorador de soluciones** y elija **Agregar > Nuevo proyecto...** .

2. En el **nuevo proyecto** ventana a la izquierda, seleccione **Visual C#** y **escritorio clásico de Windows**. En la lista de tipos de proyecto, elija **aplicación de WPF (.NET Framework)**. 

3. Escriba un nombre para el proyecto con un **WPF** extensión, por ejemplo, **BoxViewClock.WPF**. Haga clic en el **examinar** botón, seleccione el **BoxViewClock** carpeta y presione **seleccionar la carpeta**. Esto colocará el proyecto de WPF en el mismo directorio que los otros proyectos de la solución.

    ![Agregue un nuevo proyecto WPF](wpf-images/add-new-project.png "agregar un nuevo proyecto WPF")

    Haga clic en Aceptar para crear el proyecto.

4. En el **el Explorador de soluciones**, derecha, haga clic en el nuevo **BoxViewClock.WPF** del proyecto y seleccione **administrar paquetes de NuGet**. Seleccione el **examinar** pestaña, haga clic en el **incluir versión preliminar** casilla de verificación y busque **Xamarin.Forms**.

    ![Seleccione el paquete NuGet](wpf-images/select-nuget-package.png "seleccione el paquete de NuGet")

    Seleccione paquete y haga clic en el **instalar** botón.

5. Ahora busque **Xamarin.Forms.Platform.WPF** empaquetar e instálela que también. Asegúrese de que el paquete procede de Microsoft.

6. Haga clic en el nombre de la solución el **el Explorador de soluciones** y seleccione **administrar paquetes de NuGet para la solución**. Seleccione el **actualización** ficha y el **Xamarin.Forms** paquete. Seleccione todos los proyectos y actualizarlas con la misma versión de Xamarin.Forms:

    ![Actualizar el paquete NuGet](wpf-images/update-nuget-package.png "actualizar el paquete de NuGet") 

7. En el proyecto WPF, haga doble clic en **referencias**. En el **Administrador de referencias** cuadro de diálogo, seleccione **proyectos** a la izquierda y activar la casilla junto a la **BoxViewClock** proyecto:

    ![Hacer referencia al proyecto compartido](wpf-images/reference-shared-project.png "hacer referencia al proyecto compartido")

8. Editar el **MainWindow.xaml** archivo del proyecto WPF. En el `Window` etiqueta, agregue una declaración de espacio de nombres XML para el **Xamarin.Forms.Platform.WPF** ensamblado y espacio de nombres:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Ahora, cambie el `Window` etiquetar a `wpf:FormsApplicationPage`. Cambiar el `Title` establecer con el nombre de la aplicación, por ejemplo, **BoxViewClock**. El archivo XAML completado debería tener este aspecto:

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

9. Editar el **MainWindow.xaml.cs** archivo del proyecto WPF. Agregue dos nuevos `using` directivas:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Cambie la clase base de `MainWindow` desde `Window` a `FormsApplicationPage`. Siguiendo el `InitializeComponent` llamar, agregue las dos instrucciones siguientes:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    Excepto en los comentarios y sin usar `using` directivas, completo **MainWindows.xaml.cs** archivo debería tener este aspecto:

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

    ![Reloj de WPF BoxView](wpf-images/wpf-boxviewclock.png "reloj BoxView de WPF" )

## <a name="next-steps"></a>Pasos siguientes

### <a name="platform-specifics"></a>Características específicas de plataforma

Puede determinar qué plataforma se está ejecutando en la aplicación de Xamarin.Forms mediante código o XAML. Esto le permite cambiar las características del programa cuando se ejecuta en WPF. En el código, compare el valor de `Device.RuntimePlatform` con el `Device.WPF` constante (que es igual a la cadena "WPF"). Si hay una coincidencia, la aplicación se ejecuta en WPF.

En XAML, puede usar el `OnPlatform` para seleccionar un valor de propiedad específico de la plataforma:

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

Se trata de una vista previa, por lo que debe esperar que no todo está lista para producción. No todos los paquetes de NuGet para Xamarin.Forms están listos para WPF, y algunas características no ser totalmente operativo.

