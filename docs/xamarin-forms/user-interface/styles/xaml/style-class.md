---
title: Clases de estilo de Xamarin.Forms
description: Las clases de estilo de Xamarin.Forms permiten varios estilos para aplicarse a un control, sin tener que recurrir a la herencia de estilo.
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: dd749a4a78adbab5317f1ae5ca6334caa009b9b3
ms.sourcegitcommit: 9dcb7377dc92ad921285fbb857b0be13030bbea3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668555"
---
# <a name="xamarinforms-style-classes"></a>Clases de estilo de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Las clases de estilo de Xamarin.Forms permiten varios estilos para aplicarse a un control, sin tener que recurrir a la herencia de estilo._

## <a name="create-style-classes"></a>Crear clases de estilo

Se puede crear una clase de estilo estableciendo el [ `Class` ](xref:Xamarin.Forms.Style.Class) propiedad en un [ `Style` ](xref:Xamarin.Forms.Style) a un `string` que representa el nombre de clase. La ventaja de esto ofrece, sobre la definición de un estilo explícito utilizando la `x:Key` atributo, es que se pueden aplicar varias clases de estilo para un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).

> [!IMPORTANT]
> Varios estilos pueden compartir el mismo nombre de clase, siempre se dirigen a distintos tipos. Esto permite que varias clases de estilo, que son con el mismo nombre, a distintos tipos de destino.

El ejemplo siguiente muestra tres [ `BoxView` ](xref:Xamarin.Forms.BoxView) de estilo de las clases y un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) clase de estilo:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="BoxView"
               Class="Separator">
            <Setter Property="BackgroundColor"
                    Value="#CCCCCC" />
            <Setter Property="HeightRequest"
                    Value="1" />
        </Style>

        <Style TargetType="BoxView"
               Class="Rounded">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="10" />
        </Style>    

        <Style TargetType="BoxView"
               Class="Circle">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="WidthRequest"
                    Value="100" />
            <Setter Property="HeightRequest"
                    Value="100" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="50" />
        </Style>

        <Style TargetType="VisualElement"
               Class="Rotated"
               ApplyToDerivedTypes="true">
            <Setter Property="Rotation"
                    Value="45" />
        </Style>        
    </ContentPage.Resources>
</ContentPage>
```

El `Separator`, `Rounded`, y `Circle` cada conjunto de clases de estilo [ `BoxView` ](xref:Xamarin.Forms.BoxView) propiedades con valores específicos.

El `Rotated` estilo clase tiene un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) de [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), lo que significa que sólo puede aplicarse a `VisualElement` instancias. Sin embargo, su [ `ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propiedad está establecida en `true`, lo que garantiza que se puede aplicar a todos los controles que derivan de `VisualElement`, tales como [ `BoxView` ](xref:Xamarin.Forms.BoxView). Para obtener más información sobre cómo aplicar un estilo a un tipo derivado, consulte [aplicar un estilo a los tipos derivados](implicit.md#apply-a-style-to-derived-types).

El código de C# equivalente es:

```csharp
var separatorBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Separator",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#CCCCCC")
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 1
        }
    }
};

var roundedBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Rounded",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 10
        }
    }
};

var circleBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Circle",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = VisualElement.WidthRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 50
        }
    }
};

var rotatedVisualElementStyle = new Style(typeof(VisualElement))
{
    Class = "Rotated",
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.RotationProperty,
            Value = 45
        }
    }
};

Resources = new ResourceDictionary
{
    separatorBoxViewStyle,
    roundedBoxViewStyle,
    circleBoxViewStyle,
    rotatedVisualElementStyle
};
```

## <a name="consume-style-classes"></a>Utilizar las clases de estilo

Las clases de estilo que pueden utilizarse al establecer el [ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass) propiedad del control, que es de tipo `IList<string>`, a una lista de nombres de clase de estilo. Las clases de estilo se aplicará siempre que el tipo del control coincide con el [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) de las clases de estilo.

El ejemplo siguiente muestra tres [ `BoxView` ](xref:Xamarin.Forms.BoxView) instancias, cada conjunto a las clases de estilo diferente:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <BoxView StyleClass="Separator" />       
        <BoxView WidthRequest="100"
                 HeightRequest="100"
                 HorizontalOptions="Center"
                 StyleClass="Rounded, Rotated" />
        <BoxView HorizontalOptions="Center"
                 StyleClass="Circle" />
    </StackLayout>
</ContentPage>    
```

En este ejemplo, la primera [ `BoxView` ](xref:Xamarin.Forms.BoxView) se ha adaptado para que sea un separador de línea, mientras que el tercer `BoxView` es circular. El segundo `BoxView` dos clases de estilo aplicado a él, que asigne a TI redondeada esquinas y giremoslo en 45 grados:

![](style-class-images/boxviews.png "Estilo BoxViews con las clases de estilo")

> [!IMPORTANT]
> Varias clases de estilo se pueden aplicar a un control porque el [ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass) propiedad es de tipo `IList<string>`. Cuando esto ocurre, las clases de estilo se aplican de forma ascendente de la lista. Por lo tanto, cuando varias clases de estilo establecen unas propiedades idénticas, la propiedad en la clase de estilo que se encuentra en la posición de la lista más alta tendrá prioridad.

El código de C# equivalente es:

```csharp
...
Content = new StackLayout
{
    Children =
    {
        new BoxView { StyleClass = new [] { "Separator" } },
        new BoxView { WidthRequest = 100, HeightRequest = 100, HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Rounded", "Rotated" } },
        new BoxView { HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Circle" } }
    }
};
```

## <a name="related-links"></a>Vínculos relacionados

- [Estilos básicos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
