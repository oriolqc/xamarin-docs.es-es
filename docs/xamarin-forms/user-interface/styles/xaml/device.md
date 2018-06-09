---
title: Estilos de dispositivo de Xamarin.Forms
description: Xamarin.Forms incluye seis estilos dinámicos, conocidos como estilos de dispositivo, en la clase Device.Styles. Este artículo explica cómo utilizar los estilos de dispositivo en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 6b5d4623bb331f4bf52faa096afeacb21d6d7489
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245600"
---
# <a name="device-styles-in-xamarinforms"></a>Estilos de dispositivo de Xamarin.Forms

_Xamarin.Forms incluye seis estilos dinámicos, conocidos como estilos de dispositivo, en la clase Device.Styles._

El *dispositivo* estilos son:

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)

Solo pueden aplicarse a todos los seis estilos [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias. Por ejemplo, un `Label` que están mostrando el cuerpo de un párrafo puede establecer su [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedad [ `BodyStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/).

En el ejemplo de código siguiente se muestra cómo utilizar el *dispositivo* estilos en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Los estilos de dispositivo se enlazan al uso de la `DynamicResource` extensión de marcado. La naturaleza dinámica de los estilos puede verse en iOS cambiando el **accesibilidad** configuración de tamaño del texto. La apariencia de la *dispositivo* estilos es diferente en cada plataforma, como se muestra en las capturas de pantalla siguiente:

![](device-images/device-styles.png "Estilos de dispositivo en cada plataforma")

*Dispositivo* estilos también se pueden derivar de estableciendo la [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propiedad y el nombre de clave para el estilo de dispositivo. En el ejemplo de código anterior, `myBodyStyle` hereda de [ `BodyStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/) y establece un color de texto letras acentuadas. Para obtener más información acerca de la herencia de estilo dinámico, vea [herencia de estilo dinámico](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

El [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedad de cada [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancia se establece en la propiedad adecuada de la [ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) clase.

## <a name="accessibility"></a>Accesibilidad

El *dispositivo* estilos respetan las preferencias de accesibilidad, por lo que cambiarán los tamaños de fuente que se modifiquen las preferencias de accesibilidad en cada plataforma. Por lo tanto, para admitir texto accesible, asegúrese de que el *dispositivo* estilos se usan como base para los estilos de texto dentro de la aplicación.

Las capturas de pantalla siguientes muestran los estilos de dispositivo en cada plataforma, con el menor tamaño de fuente accesible:

[![](device-images/minimum-size.png "Estilos de dispositivo pequeño accesible en cada plataforma")](device-images/minimum-size-large.png#lightbox "estilos de dispositivo pequeño accesible en cada plataforma")

Las capturas de pantalla siguientes muestran los estilos de dispositivo en cada plataforma, con el mayor tamaño de fuente accesible:

![](device-images/maximum-size.png "Estilos de dispositivo grandes accesible en cada plataforma")

## <a name="summary"></a>Resumen

Xamarin.Forms incluye seis *dinámica* estilos, conocidos como *dispositivo* estilos, en la [ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) clase. Solo pueden aplicarse a todos los seis estilos [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias.


## <a name="related-links"></a>Vínculos relacionados

- [Estilos de texto](~/xamarin-forms/user-interface/text/styles.md)
- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinámicos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Trabajar con estilos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Establecedor](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
