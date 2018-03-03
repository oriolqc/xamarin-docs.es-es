---
title: Estilos globales
description: "Estilos pueden ponerse a disposición globalmente agregándolas al diccionario de recursos de la aplicación. Esto ayuda a evitar la duplicación de estilos a través de páginas o controles."
ms.topic: article
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: d258d993d8452c0c93c53d875d81683a294f80d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="global-styles"></a>Estilos globales

_Estilos pueden ponerse a disposición globalmente agregándolas al diccionario de recursos de la aplicación. Esto ayuda a evitar la duplicación de estilos a través de páginas o controles._

## <a name="creating-a-global-style-in-xaml"></a>Crear un estilo Global en XAML

De forma predeterminada, todas las aplicaciones de Xamarin.Forms creadas a partir de una plantilla utilizan el **aplicación** clase para implementar el [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) subclase. Para declarar un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) en el nivel de aplicación, en la aplicación [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) mediante XAML, el valor predeterminado **aplicación** clase debe sustituirse por un XAML **Aplicación** clase y código subyacente asociado. Para obtener más información, consulte [trabajar con la clase App](~/xamarin-forms/app-fundamentals/application-class.md).

El siguiente ejemplo de código muestra un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) declarados en el nivel de aplicación:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Esto [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define una sola *explícita* estilo, `buttonStyle`, que se usará para establecer el aspecto de [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancias. Sin embargo, pueden ser globales estilos *explícita* o *implícita*.

En el ejemplo de código siguiente se muestra una aplicación de página XAML el `buttonStyle` a la página [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](application-images/application-styles-1.png "Ejemplo de estilos globales")](application-images/application-styles-1-large.png "ejemplo estilos globales")

Para obtener información sobre cómo crear estilos en una página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), consulte [estilos explícita](~/xamarin-forms/user-interface/styles/explicit.md) y [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="overriding-styles"></a>Reemplazar estilos

Estilos inferiores en la jerarquía de vista tienen prioridad sobre los definidos mayor seguridad. Por ejemplo, si se establece un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) que establece [ `Button.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/) a `Red` en la aplicación de nivel serán reemplazado por un estilo de nivel de página que establece `Button.TextColor` a `Green`. De forma similar, un estilo de nivel de página se reemplazará por un estilo de nivel de control. Además, si `Button.TextColor` se establece directamente en una propiedad de control, esto tendrá prioridad sobre los estilos. Esta prioridad se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

La versión original `buttonStyle`, definido en el nivel de aplicación, se reemplaza por la `buttonStyle` instancia definida en el nivel de página. Además, el estilo de nivel de página se reemplaza por el nivel de control `buttonStyle`. Por lo tanto, la [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancias se muestran con texto azul, como se muestra en las capturas de pantalla siguiente:

[![](application-images/application-styles-2.png "Ejemplo de estilos de reemplazar")](application-images/application-styles-2-large.png "reemplazar el ejemplo de estilos")

## <a name="creating-a-global-style-in-c35"></a>Crear un estilo Global en C &#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias pueden agregarse a la aplicación [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) colección en C# mediante la creación de un nuevo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)y, a continuación, agregando la `Style` instancias para el `ResourceDictionary`, como se muestra en el ejemplo de código siguiente:

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,   Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

El constructor define una sola *explícita* estilo para aplicar a [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancias a lo largo de la aplicación. *Explícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instancias se agregan a la [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) mediante la [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/) método, especificando un `key`cadena para hacer referencia a la `Style` instancia. El `Style` instancia, a continuación, puede aplicarse a todos los controles del tipo correcto en la aplicación. Sin embargo, pueden ser globales estilos *explícita* o *implícita*.

En el ejemplo de código siguiente se muestra un C# página aplicar el `buttonStyle` a la página [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancias:

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

El `buttonStyle` se aplica a la [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instancias estableciendo sus [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades y controla el aspecto de la `Button` instancias.

## <a name="summary"></a>Resumen

Estilos pueden ponerse a disposición globalmente agregándolos a la aplicación [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Esto ayuda a evitar la duplicación de estilos a través de páginas o controles.



## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabajar con estilos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Style](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
