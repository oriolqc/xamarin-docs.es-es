---
title: Estilos explícitos
description: Un estilo explícito es aquella que se aplica de manera selectiva a los controles estableciendo sus propiedades de estilo.
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 9f9e87ae0fd9d609cef56123e9052d85941bda51
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848192"
---
# <a name="explicit-styles"></a>Estilos explícitos

_Un estilo explícito es aquella que se aplica de manera selectiva a los controles estableciendo sus propiedades de estilo._

## <a name="creating-an-explicit-style-in-xaml"></a>Crear un estilo explícito en XAML

Para declarar un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) en el nivel de página, un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) debe agregarse a la página y, a continuación, uno o varios `Style` declaraciones pueden incluirse en la `ResourceDictionary`. A `Style` estará *explícita* dando a su declaración un `x:Key` atributo, que proporciona una clave descriptiva en el `ResourceDictionary`. *Explícita* estilos deben aplicarse a elementos específicos de visuales estableciendo sus [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades.

El siguiente ejemplo de código muestra *explícita* estilos declaran en XAML en una página `ResourceDictionary` y aplicada a la página [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define tres *explícita* estilos que se aplican a la página [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias. Cada `Style` se utiliza para mostrar texto en un color diferente, y también establece la fuente opciones de diseño de tamaño y horizontal y vertical. Cada `Style` se aplica a otro `Label` estableciendo su [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades mediante el `StaticResource` extensión de marcado. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](explicit-images/explicit-styles.png "Ejemplo de estilos explícita")](explicit-images/explicit-styles-large.png#lightbox "ejemplo estilos explícita")

Además, la última [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) tiene un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) aplicado, pero también invalida la [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) propiedad a un diferentes`Color`valor.

### <a name="creating-an-explicit-style-at-the-control-level"></a>Crear un estilo explícito en el Control de nivel

Además de crear *explícita* estilos en el nivel de página, también se pueden crear en el nivel de control, tal como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En este ejemplo, el *explícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias se asignan a la [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) colección de la [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) control. Los estilos, a continuación, se aplican al control y sus elementos secundarios.

Para obtener información sobre cómo crear estilos en una aplicación [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), consulte [estilos globales](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-explicit-style-in-c35"></a>Crear un estilo explícito en C&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias pueden agregarse a una página [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) colección en C# mediante la creación de un nuevo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)y, a continuación, agregando la `Style` instancias para el `ResourceDictionary`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

El constructor define tres *explícita* estilos que se aplican a la página [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias. Cada *explícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) se agrega a la [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) mediante la [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/) método, especifica un `key` cadena para hacer referencia a la `Style` instancia. Cada `Style` se aplica a otro `Label` estableciendo sus [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades.

Sin embargo, no hay ninguna ventaja en utilizar un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) aquí. En su lugar, [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias pueden asignarse directamente a la [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades de los elementos visuales necesarios y el `ResourceDictionary` puede quitarse, tal y como se muestra en el siguiente ejemplo de código:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

El constructor define tres *explícita* estilos que se aplican a la página [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias. Cada `Style` se utiliza para mostrar texto en un color diferente, y también establece la fuente opciones de diseño de tamaño y horizontal y vertical. Cada `Style` se aplica a otro `Label` estableciendo su [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades. Además, la última `Label` tiene un `Style` aplicado, pero también invalida la `TextColor` propiedad a otra `Color` valor.

## <a name="summary"></a>Resumen

A [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) estará *explícita* dando a su declaración un `x:Key` de atributo y, a continuación, selectivamente aplicárselo a controles estableciendo sus [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades.



## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabajar con estilos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Establecedor](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
