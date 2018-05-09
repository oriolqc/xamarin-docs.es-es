---
title: Introducción a los estilos
description: Los estilos permiten la apariencia de los elementos visuales se personalice. Los estilos se definen para un tipo específico y contienen valores para las propiedades disponibles en ese tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 453c4d6edafd6493272f8ca0435fcc86e2f3b2f7
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="introduction-to-styles"></a>Introducción a los estilos

_Los estilos permiten la apariencia de los elementos visuales se personalice. Los estilos se definen para un tipo específico y contienen valores para las propiedades disponibles en ese tipo._

Aplicaciones de Xamarin.Forms suelen contengan varios controles que tienen un aspecto idéntico. Por ejemplo, una aplicación puede tener varios [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias que tienen las mismas opciones de fuente y las opciones de diseño, tal como se muestra en el siguiente ejemplo de código XAML:

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

En el ejemplo de código siguiente se muestra la página equivalente creada en C#:

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

Cada [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancia tiene valores de propiedad idénticos para controlar la apariencia del texto mostrado por el `Label`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](introduction-images/no-styles.png "Etiquetar apariencia sin estilos")](introduction-images/no-styles-large.png#lightbox "apariencia sin estilos de columna de etiqueta")

Configuración de la apariencia de cada control individual puede ser repetitiva y es proclive a errores. En su lugar, puede crearse un estilo que define la apariencia y, a continuación, se aplica a los controles necesarios.

## <a name="creating-a-style"></a>Creación de un estilo

El [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) clase agrupa una colección de valores de propiedad en un objeto que, a continuación, se pueden aplicar a varias instancias de elementos visuales. Esto ayuda a reducir el marcado repetitivo y permite un aspecto de las aplicaciones más fácilmente cambiarse.

A pesar de estilos se han diseñado principalmente para aplicaciones basadas en XAML, también pueden crearse en C#:

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias creadas en XAML se definen normalmente en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) que se asigna a la [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) colección de un control, página, o a la [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) recolección de la aplicación.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias creadas en C# normalmente se definen en la clase de la página, o en una clase que se puede acceder de forma global.

Elegir dónde se defina un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) impactos dónde pueden utilizarse:

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) solo pueden aplicarse instancias definidas en el nivel de control para el control y sus elementos secundarios.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias definidas en el nivel de página solo pueden aplicarse a la página y a sus elementos secundarios.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias definidas en el nivel de aplicación pueden aplicarse a lo largo de la aplicación.

Cada [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancia contiene una colección de uno o varios [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) objetos, con cada `Setter` tener un [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) y un [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/). El `Property` es el nombre de la propiedad enlazable del elemento se aplica el estilo, y `Value` es el valor que se aplica a la propiedad.

Cada [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancia puede ser *explícita*, o *implícita*:

- Un *explícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancia se define mediante la especificación de un [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) y un `x:Key` valor y establezca el elemento de destino [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedad a la `x:Key` referencia. Para obtener más información acerca de *explícita* estilos, consulte [estilos explícita](~/xamarin-forms/user-interface/styles/explicit.md).
- Un *implícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancia se define especificando solo un [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/). El `Style` instancia, a continuación, se aplicarán automáticamente a todos los elementos de ese tipo. Tenga en cuenta que las subclases de la `TargetType` automáticamente no tienen la `Style` aplicado. Para obtener más información acerca de *implícita* estilos, consulte [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

Al crear un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/), [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propiedad siempre es necesaria. El siguiente ejemplo de código muestra un *explícita* estilo (tenga en cuenta el `x:Key`) creadas en XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Para aplicar un `Style`, el objeto de destino debe ser un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) que coincida con el [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) valor de propiedad de la `Style`, tal y como se muestra en el siguiente ejemplo de código XAML:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Estilos inferiores en la jerarquía de vista tienen prioridad sobre los definidos mayor seguridad. Por ejemplo, si se establece un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) que establece [ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) a `Red` en la aplicación de nivel serán reemplazado por un estilo de nivel de página que establece `Label.TextColor` a `Green`. De forma similar, un estilo de nivel de página se reemplazará por un estilo de nivel de control. Además, si `Label.TextColor` se establece directamente en una propiedad de control, ésta tiene prioridad sobre los estilos.

Los artículos de esta sección se muestran y se explica cómo crear y aplicar *explícita* y *implícita* estilos, cómo crear estilos globales, aplicar el estilo de herencia, cómo responder a los cambios de estilo en tiempo de ejecución y cómo usar los estilos incorporados incluidos en Xamarin.Forms.

> [!NOTE]
> **¿Qué es StyleId?**
>
> Anteriores a 2.2 de Xamarin.Forms, la [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) propiedad se utiliza para identificar los elementos individuales de una aplicación para la identificación en las pruebas de interfaz de usuario y en los motores de tema como Pixate. Sin embargo, se ha introducido 2.2 de Xamarin.Forms el [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/) propiedad, que ha reemplazado el [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) propiedad. Para obtener más información, consulte [Xamarin.Forms automatizar pruebas con Xamarin.UITest y Test Cloud](~/xamarin-forms/deploy-test/uitest-and-test-cloud.md).

## <a name="summary"></a>Resumen

Aplicaciones de Xamarin.Forms suelen contengan varios controles que tienen un aspecto idéntico. Configuración de la apariencia de cada control individual puede ser repetitiva y es proclive a errores. En su lugar, estilos pueden crearse que personalizan la apariencia de los controles por agrupación y propiedades de configuración disponibles en el tipo de control.


## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Establecedor](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
