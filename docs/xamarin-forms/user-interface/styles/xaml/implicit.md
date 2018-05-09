---
title: Estilos implícitos
description: Un estilo implícito es aquel que se usa por todos los controles de la mismo TargetType, sin necesidad de cada control para hacer referencia al estilo.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: d9c9f8816ea45ac122829739ad5134cc740263df
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="implicit-styles"></a>Estilos implícitos

_Un estilo implícito es aquel que se usa por todos los controles de la mismo TargetType, sin necesidad de cada control para hacer referencia al estilo._

## <a name="creating-an-implicit-style-in-xaml"></a>Crear un estilo implícito en XAML

Para declarar un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) en el nivel de página, un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) debe agregarse a la página y, a continuación, uno o varios `Style` declaraciones pueden incluirse en la `ResourceDictionary`. A `Style` estará *implícita* , no especifica un `x:Key` atributo. A continuación, se aplicará el estilo a elementos visuales que coinciden con la `TargetType` exactamente, pero no a los elementos que se derivan de la `TargetType` valor.

El siguiente ejemplo de código muestra un *implícita* estilo declarado en XAML en una página `ResourceDictionary`y se aplica a la página [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) instancias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define una sola *implícita* estilo que se aplica a la página [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) instancias. El `Style` se usa para mostrar el texto azul en un fondo amarillo, al establecer también otras opciones de apariencia. El `Style` se agrega a la página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) sin especificar un `x:Key` atributo. Por lo tanto, la `Style` se aplica a todos el `Entry` implícitamente instancias que coinciden con la [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propiedad de la `Style` exactamente. Sin embargo, el `Style` no se aplica a la `CustomEntry` instancia, que es una subclase `Entry`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](implicit-images/implicit-styles.png "Ejemplo de estilos implícitos")](implicit-images/implicit-styles-large.png#lightbox "ejemplo estilos implícitos")

Además, la cuarta [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) invalida la [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) y [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) propiedades del estilo implícita a diferentes `Color`valores.

### <a name="creating-an-implicit-style-at-the-control-level"></a>Crear un estilo implícito en el Control de nivel

Además de crear *implícita* estilos en el nivel de página, también se pueden crear en el nivel de control, tal como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En este ejemplo, el *implícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) se asigna a la [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) colección de la [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)control. El *implícita* estilo, a continuación, puede aplicarse al control y sus elementos secundarios.

Para obtener información sobre cómo crear estilos en una aplicación [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), consulte [estilos globales](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-implicit-style-in-c35"></a>Crear un estilo implícito en C&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias pueden agregarse a una página [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) colección en C# mediante la creación de un nuevo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)y, a continuación, agregando la `Style` instancias para el `ResourceDictionary`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

El constructor define una sola *implícita* estilo que se aplica a la página [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) instancias. El `Style` se usa para mostrar el texto azul en un fondo amarillo, al establecer también otras opciones de apariencia. El `Style` se agrega a la página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) sin especificar un `key` cadena. Por lo tanto, la `Style` se aplica a todos el `Entry` implícitamente instancias que coinciden con la [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propiedad de la `Style` exactamente. Sin embargo, el `Style` no se aplica a la `CustomEntry` instancia, que es una subclase `Entry`.

## <a name="summary"></a>Resumen

Un *implícita* estilo es aquel que se usa por todos los elementos visuales de la misma [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/), sin necesidad de cada control para hacer referencia al estilo. A `Style` estará *implícita* , no especifica un `x:Key` atributo. En su lugar, el `x:Key` atributo se convertirá automáticamente en el valor de la [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propiedad.



## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabajar con estilos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Establecedor](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
