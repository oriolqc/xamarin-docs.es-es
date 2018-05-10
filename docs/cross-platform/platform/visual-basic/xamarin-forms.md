---
title: Xamarin.Forms con Visual Basic.NET
description: Plantilla de proyecto de PCL Xamarin.Forms se puede modificar para utilizar Visual Basic para el ensamblado principal, eficazmente lo que permite crear aplicaciones móviles multiplataforma con VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b858e26de95d2abbc23917b1ed5a1de65105cd8d
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin.Forms con Visual Basic.NET

Xamarin no admite Visual Basic directamente, siga las instrucciones que aparecen en esta página para crear una solución de C# Xamarin.Forms PCL y, a continuación, reemplace el proyecto de PCL código comunes con Visual Basic.

[![](xamarin-forms-images/hero-sml.png "Crear una solución de Xamarin.Forms PCL y, a continuación, reemplace el proyecto de PCL código comunes con Visual Basic")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Debe utilizar Visual Studio en Windows al programa con Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin.Forms con el tutorial de Visual Basic

Siga estos pasos para crear un proyecto de Xamarin.Forms simple que usa Visual Basic:

1. Crear un nuevo *Xamarin.Forms C#* solución que usa las bibliotecas de clases portables (PCL).
Vaya a **archivo > Nuevo proyecto** y en el **nuevo proyecto** ventana Navegar a **instalado > Plantillas > Visual C# > multiplataforma** , a continuación, elija  **Cross Platform App (Xamarin.Forms o nativo) > Xamarin.Forms**.

2. Haga doble clic en la solución y **Agregar > Nuevo proyecto**.

3. Elija la **Visual Basic > biblioteca de clases (Portable)** tipo de proyecto:

   [![](xamarin-forms-images/add-vb-2-sml.png "Agregar nuevo proyecto de biblioteca de clases portable")](xamarin-forms-images/add-vb-2.png#lightbox)

4. Seleccione las plataformas, como se muestra para configurar el perfil correcto de PCL (no olvide incluir Xamarin.iOS y Xamarin.Android):

   ![](xamarin-forms-images/add-vb-3-sml.png "Elija las plataformas para admitir")

5. Haga doble clic en el proyecto de Visual Basic y elija **propiedades**, a continuación, cambie la **espacio de nombres predeterminado** para que coincida con la existente de C# proyectos:

   ![](xamarin-forms-images/add-vb-4s-sml.png "Asegúrese de que el espacio de nombres de raíz de Visual Basic coincide con la aplicación de Xamarin.Forms")

6. Haga doble clic en el nuevo proyecto de Visual Basic y elija **administrar paquetes de Nuget**, a continuación, instale **Xamarin.Forms** y cierre la ventana del Administrador de paquetes.

   [![](xamarin-forms-images/add-vb-4-sml.png "Formularios y cierre la ventana del Administrador de paquetes")](xamarin-forms-images/add-vb-4.png#lightbox)

7. Cambiar el nombre de la opción predeterminada **Class1** archivo *y* clase a `App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "Cambiar el nombre el archivo predeterminado Class1 y la clase de aplicación")](xamarin-forms-images/add-vb-5.png#lightbox)

8. Pegue el código siguiente en el **App.vb** archivo, que se convertirá en el punto inicial de la aplicación de Xamarin.Forms. No olvide incluir `Imports Xamarin.Forms` y agregue `Inherits Application` a la clase:

    ```vb 
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

9. Ahora necesitamos punto iOS y Android proyectos en el nuevo proyecto de Visual Basic.
Haga doble clic en el **referencias** nodo en iOS y Android proyectos para abrir el **Administrador de referencias**. Acento anula la la biblioteca portable de C# y la biblioteca portable de VB graduación (no olvide, hágalo para iOS y Android proyectos).

   [![](xamarin-forms-images/add-vb-8-sml.png "Quitar referencia al proyecto anterior, agregue la referencia de Visual Basic")](xamarin-forms-images/add-vb-8.png#lightbox)

10. Eliminar el proyecto portable de C#. Agregar nuevas **.vb** fuera de archivos para compilar la aplicación de Xamarin.Forms. Una plantilla para el nuevo `ContentPage`s en Visual Basic se muestra a continuación:

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Limitaciones de Visual Basic en Xamarin.Forms

Como se mencionó en la [Portable Visual Basic.NET página](~/cross-platform/platform/visual-basic/index.md), Xamarin no admite el lenguaje Visual Basic. Esto significa que hay algunas limitaciones sobre dónde puede utilizar Visual Basic:

 - No se puede escribir representadores personalizados en Visual Basic, deben escribirse en C# en los proyectos de plataforma nativa.

 - Las implementaciones del servicio de dependencia no se puede escribir en Visual Basic, deben escribirse en C# en los proyectos de plataforma nativa.

 - No se puede incluir páginas XAML en el proyecto de Visual Basic: sólo puede crear el generador de código subyacente C#. Es posible que desee incluir XAML en una independiente, que se hace referencia, C# biblioteca de clases portable y usar el enlace de datos para rellenar los archivos XAML a través de modelos de Visual Basic (se incluye un ejemplo de esto en el [ejemplo](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

 - Xamarin no admite el lenguaje Visual Basic.NET.

## <a name="related-links"></a>Vínculos relacionados

- [XamarinFormsVB (ejemplo)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [El desarrollo multiplataforma con .NET Framework (Microsoft)](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
