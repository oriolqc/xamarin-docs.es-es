---
title: Herencia de estilos
description: Estilos pueden heredar de otros estilos para reducir la duplicación y volver a usar.
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 7b489e90daec2659a6d11b2776731582bdf368ff
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848426"
---
# <a name="style-inheritance"></a>Herencia de estilos

_Estilos pueden heredar de otros estilos para reducir la duplicación y volver a usar._

## <a name="style-inheritance-in-xaml"></a>Herencia de estilos en XAML

Herencia de estilos se realiza estableciendo la [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) propiedad a un archivo [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/). En XAML, esto se logra estableciendo la `BasedOn` propiedad a una `StaticResource` extensión de marcado que se hace referencia a una que se han creado previamente `Style`. En C#, esto se logra estableciendo la `BasedOn` propiedad a un `Style` instancia.

Pueden incluir los estilos que se heredan de un estilo base [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) instancias nuevas propiedades o usarlos para reemplazar estilos al estilo de base. Además, los estilos que se heredan de un estilo de base deben tener como destino el mismo tipo o un tipo que deriva del tipo de destino mediante el estilo de base. Por ejemplo, si se dirige a un estilo base [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) instancias, los estilos que se basan en el estilo base pueden tener como destino `View` instancias o tipos que derivan de la `View` de la clase, como [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) y [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancias.

El código siguiente muestra *explícita* herencia de estilos en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El `baseStyle` destinos [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) instancias y establece la [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) y [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades. El `baseStyle` no se establece directamente en todos los controles. En su lugar, `labelStyle` y `buttonStyle` heredan de él, establecer valores de propiedad enlazable adicionales. El `labelStyle` y `buttonStyle` , a continuación, se aplican a la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias y [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancia estableciendo sus [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Un estilo implícito se puede derivar de un estilo explícito, pero no puede derivar un estilo explícito de un estilo implícito.

### <a name="respecting-the-inheritance-chain"></a>Respeta la cadena de herencia

Sólo puede heredar un estilo de estilos en el mismo nivel, o superior, en la jerarquía de vista. Esto significa que:

- Un recurso de nivel de aplicación sólo puede heredar de otros recursos de nivel de aplicación.
- Un recurso de nivel de página puede heredar de recursos de nivel de aplicación y otros recursos de nivel de página.
- Puede heredar un recurso de nivel de control de recursos de nivel de aplicación, los recursos de nivel de página y otros recursos de nivel de control.

Esta cadena de herencia se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En este ejemplo, `labelStyle` y `buttonStyle` es recursos de nivel de control mientras `baseStyle` es un recurso de nivel de página. Sin embargo, mientras `labelStyle` y `buttonStyle` heredarlo `baseStyle`, no es posible `baseStyle` heredar de `labelStyle` o `buttonStyle`, debido a sus respectivas ubicaciones en la jerarquía de vista.

## <a name="style-inheritance-in-c35"></a>Herencia de estilo de C&#35;

La página C# equivalente, donde [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias se asignan directamente a la [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades de los controles necesarios, se muestra en el ejemplo de código siguiente:

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

El `baseStyle` destinos [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) instancias y establece la [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) y [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades. El `baseStyle` no se establece directamente en todos los controles. En su lugar, `labelStyle` y `buttonStyle` heredan de él, establecer valores de propiedad enlazable adicionales. El `labelStyle` y `buttonStyle` , a continuación, se aplican a la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias y [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancia estableciendo sus [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades.

## <a name="summary"></a>Resumen

Estilos pueden heredar de otros estilos para reducir la duplicación y volver a usar. Herencia de estilos se realiza estableciendo la [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) propiedad a un archivo [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/).


## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabajar con estilos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Establecedor](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
