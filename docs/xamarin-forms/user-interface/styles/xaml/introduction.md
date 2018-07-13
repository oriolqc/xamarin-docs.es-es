---
title: Introducción a los estilos de Xamarin.Forms
description: Los estilos permiten la apariencia de los elementos visuales para personalizarse. Los estilos se definen para un tipo específico y contienen valores para las propiedades disponibles en ese tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 8f84c960f17f56fce2a1bba143a215ce930f6f4e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996115"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introducción a los estilos de Xamarin.Forms

_Los estilos permiten la apariencia de los elementos visuales para personalizarse. Los estilos se definen para un tipo específico y contienen valores para las propiedades disponibles en ese tipo._

A menudo, las aplicaciones de Xamarin.Forms contienen varios controles que tienen una apariencia idéntica. Por ejemplo, una aplicación puede tener varios [ `Label` ](xref:Xamarin.Forms.Label) instancias que tienen las mismas opciones de fuente y las opciones de diseño, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El siguiente ejemplo de código muestra la página equivalente creada en C#:

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

Cada [ `Label` ](xref:Xamarin.Forms.Label) instancia tiene valores idénticos de propiedad para controlar la apariencia del texto mostrado por el `Label`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](introduction-images/no-styles.png "Apariencia sin estilos de etiqueta")](introduction-images/no-styles-large.png#lightbox "apariencia sin estilos de etiqueta")

Establecer la apariencia de cada control individual puede ser repetitiva y propensas a errores. En su lugar, puede crearse un estilo que define la apariencia y, a continuación, se aplica a los controles necesarios.

## <a name="creating-a-style"></a>Creación de un estilo

El [ `Style` ](xref:Xamarin.Forms.Style) clase agrupa una colección de valores de propiedad en un objeto que, a continuación, se puede aplicar a varias instancias del elemento visual. Esto ayuda a reducir el marcado repetitivo y permite una apariencia de las aplicaciones a cambiarse más fácilmente.

Aunque los estilos se han diseñado principalmente para aplicaciones basadas en XAML, también se pueden crear en C#:

- [`Style`](xref:Xamarin.Forms.Style) las instancias creadas en XAML se definen normalmente en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) que se asigna a la [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) colección de un control, página, o a la [ `Resources` ](xref:Xamarin.Forms.Application.Resources) recolección de la aplicación.
- [`Style`](xref:Xamarin.Forms.Style) las instancias creadas en C# normalmente se definen en la clase de página o en una clase que se puede acceder de forma global.

Elegir dónde se puede definir un [ `Style` ](xref:Xamarin.Forms.Style) impactos en el que se puede usar:

- [`Style`](xref:Xamarin.Forms.Style) las instancias definidas en el nivel de control solo pueden aplicarse al control y a sus elementos secundarios.
- [`Style`](xref:Xamarin.Forms.Style) las instancias definidas en el nivel de página sólo pueden aplicarse a la página y a sus elementos secundarios.
- [`Style`](xref:Xamarin.Forms.Style) las instancias definidas en el nivel de aplicación se pueden aplicar a lo largo de la aplicación.

Cada [ `Style` ](xref:Xamarin.Forms.Style) instancia contiene una colección de uno o varios [ `Setter` ](xref:Xamarin.Forms.Setter) objetos, con cada `Setter` tener un [ `Property` ](xref:Xamarin.Forms.Setter.Property) y un [`Value`](xref:Xamarin.Forms.Setter.Value). El `Property` es el nombre de la propiedad enlazable del elemento que se aplica el estilo, y el `Value` es el valor que se aplica a la propiedad.

Cada [ `Style` ](xref:Xamarin.Forms.Style) instancia puede ser *explícita*, o *implícita*:

- Un *explícita* [ `Style` ](xref:Xamarin.Forms.Style) instancia se define mediante la especificación de un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) y un `x:Key` valor y establezca el elemento de destino [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propiedad a la `x:Key` referencia. Para obtener más información acerca de *explícita* estilos, consulte [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md).
- Un *implícita* [ `Style` ](xref:Xamarin.Forms.Style) instancia se define especificando solo un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType). El `Style` instancia, a continuación, automáticamente se aplicará a todos los elementos de ese tipo. Tenga en cuenta que las subclases de la `TargetType` no tienen automáticamente la `Style` aplicado. Para obtener más información acerca de *implícita* estilos, consulte [los estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

Al crear un [ `Style` ](xref:Xamarin.Forms.Style), [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propiedad siempre es necesaria. El siguiente ejemplo de código muestra un *explícita* estilo (tenga en cuenta la `x:Key`) creado en XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Para aplicar un `Style`, el objeto de destino debe ser un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) que coincide con el [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) valor de propiedad de la `Style`, tal y como se muestra en el ejemplo de código XAML siguiente:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Estilos de más abajo en la jerarquía de vistas tienen prioridad sobre las define mayor seguridad. Por ejemplo, si se establece un [ `Style` ](xref:Xamarin.Forms.Style) que establece [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) a `Red` en la aplicación de nivel serán reemplazado por un estilo de nivel de página establece `Label.TextColor` a `Green`. De forma similar, un estilo de nivel de página se reemplazará por un estilo de nivel de control. Además, si `Label.TextColor` se establece directamente en una propiedad de control, esto tiene prioridad sobre los estilos.

Los artículos de esta sección se muestran y se explica cómo crear y aplicar *explícita* y *implícita* estilos, cómo crear estilos globales, aplicar el estilo de herencia, cómo responder a los cambios de estilo en tiempo de ejecución y cómo usar los estilos integrados incluidos en Xamarin.Forms.

> [!NOTE]
> **¿Qué es StyleId?**
>
> Anteriores a Xamarin.Forms 2.2, el [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propiedad se utiliza para identificar los elementos individuales de una aplicación para la identificación en las pruebas de interfaz de usuario y en los motores de temas como Pixate. Sin embargo, se ha introducido Xamarin.Forms 2.2 la [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId) propiedad, que reemplazó el [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propiedad. Para obtener más información, consulte [pruebas de automatización de Xamarin.Forms con Xamarin.UITest y Test Cloud](~/xamarin-forms/deploy-test/uitest-and-test-cloud.md).

## <a name="summary"></a>Resumen

A menudo, las aplicaciones de Xamarin.Forms contienen varios controles que tienen una apariencia idéntica. Establecer la apariencia de cada control individual puede ser repetitiva y propensas a errores. En su lugar, los estilos pueden crearse que personalizar la apariencia del control mediante la agrupación y propiedades de configuración disponibles en el tipo de control.


## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilo](xref:Xamarin.Forms.Style)
- [Establecedor](xref:Xamarin.Forms.Setter)
