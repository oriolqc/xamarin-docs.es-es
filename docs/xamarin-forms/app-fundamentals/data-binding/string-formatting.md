---
title: Formato de cadena de Xamarin.Forms
description: En este artículo se explica cómo usar enlaces de datos de Xamarin.FOrms para dar formato y mostrar los objetos como cadenas. Esto se logra estableciendo el StringFormat del enlace en una cadena de formato estándar de .NET con un marcador de posición.
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 8efd93204b848113e0ed95c8066a5506eb517ac6
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52170954"
---
# <a name="xamarinforms-string-formatting"></a>Formato de cadena de Xamarin.Forms

A veces es conveniente usar enlaces de datos para mostrar la representación de cadena de un objeto o valor. Por ejemplo, desea utilizar un `Label` para mostrar el valor actual de un `Slider`. En este enlace de datos, el `Slider` es el origen y el destino es el `Text` propiedad de la `Label`.

Cuando se muestran las cadenas en el código, la herramienta más eficaz es estático [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) método. La cadena de formato incluye códigos específicos para distintos tipos de objetos de formato, y puede incluir otros textos junto con los valores que se va a aplicar formato. Consulte la [aplicar formato a tipos en .NET](/dotnet/standard/base-types/formatting-types/) artículo para obtener más información sobre el formato de cadena.

## <a name="the-stringformat-property"></a>La propiedad StringFormat

Esta función se traslada en enlaces de datos: establece el [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) propiedad de `Binding` (o la [ `StringFormat` ](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) propiedad de la `Binding` extensión de marcado) a un cadena con un marcador de posición de formato estándar. NET:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Tenga en cuenta que la cadena de formato está delimitada por caracteres de comillas simples (apóstrofo) para evitar el analizador XAML de tratamiento de las llaves como otra extensión de marcado XAML. En caso contrario, esa cadena sin el carácter de comillas simples es la misma cadena que utilizaría para mostrar un valor de punto flotante en una llamada a `String.Format`. Una especificación de formato de `F2` hace que el valor que se mostrará con dos posiciones decimales.

El `StringFormat` propiedad sólo tiene sentido cuando la propiedad de destino es de tipo `string`, y el modo de enlace es `OneWay` o `TwoWay`. Para los enlaces bidireccionales, el `StringFormat` solo es aplicable para los valores que se pasa desde el origen al destino.

Como verá en el siguiente artículo en el [Binding Path](binding-path.md), enlaces de datos pueden llegar a ser bastante complejo y compleja. Al depurar estos enlaces de datos, puede agregar un `Label` en el archivo XAML con un `StringFormat` mostrar algunos resultados intermedios. Incluso si se usa solo para mostrar un tipo de objeto, que puede resultar útil.

El **aplicarles un formato** página muestra varios usos de la `StringFormat` propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Los enlaces en el `Slider` y `TimePicker` muestra el uso de las especificaciones de formato específica de `double` y `TimeSpan` tipos de datos. El `StringFormat` que muestra el texto de la `Entry` vista muestra cómo especificar las comillas dobles en la cadena de formato con el uso de la `&quot;` entidad HTML.

La siguiente sección en el archivo XAML es un `StackLayout` con un `BindingContext` establecido en un `x:Static` extensión de marcado que se hace referencia a estático `DateTime.Now` propiedad. El primer enlace no tiene propiedades:

```xaml
<Label Text="{Binding}" />
```

Esto muestra simplemente el `DateTime` valor de la `BindingContext` con el formato predeterminado. Muestra el segundo enlace el `Ticks` propiedad de `DateTime`, mientras que los otros dos enlaces mostrar el `DateTime` propio con un formato específico. Tenga en cuenta esto `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Si necesita mostrar hacia la izquierda o derecha entre llaves en la cadena de formato, simplemente use un par de ellos.

Los conjuntos de la última sección del `BindingContext` al valor de `Math.PI` y se muestra con formato de forma predeterminada y dos tipos diferentes de formato numérico.

Este es el programa que se ejecuta:

[![Formato de cadena](string-formatting-images/stringformatting-small.png "formato de cadena")](string-formatting-images/stringformatting-large.png#lightbox "formato de cadena")

## <a name="viewmodels-and-string-formatting"></a>ViewModels y formato de cadena

Cuando se usa `Label` y `StringFormat` para mostrar el valor de una vista que también es el destino de una clase ViewModel, puede definir el enlace de la vista para la `Label` o desde el ViewModel a la `Label`. En general, el segundo enfoque es mejor porque comprueba que funcionan los enlaces entre View y ViewModel.

Este método se muestra en el **mejor Selector de colores** ejemplo, que usa el mismo modelo de vista como el **Selector de colores Simple** programa que aparece en el [ **Binding Mode** ](binding-mode.md) artículo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Ahora hay tres pares de `Slider` y `Label` elementos que están enlazados al mismo origen de propiedad en el `HslColorViewModel` objeto. La única diferencia es que `Label` tiene un `StringFormat` propiedad para mostrar cada `Slider` valor.

[![Selector de Color mejor](string-formatting-images/bettercolorselector-small.png "Selector de Color mejor")](string-formatting-images/bettercolorselector-large.png#lightbox "mejor el Selector de Color")

Tal vez se pregunte cómo podría mostrar sus valores RGB (rojos, verdes y azules) en formato hexadecimal de dos dígitos tradicionales. Los valores enteros no están directamente disponibles desde el `Color` estructura. Una solución sería calcular valores enteros de los componentes de color dentro de ViewModel y exponerlos como propiedades. A continuación, puede aplicarles utilizando el `X2` especificación de formato.

Otro enfoque es más general: puede escribir un *convertidor de valores de enlace* como se describe en el artículo posterior, [ **convertidores de valores de enlace**](converters.md).

El siguiente artículo, sin embargo, explora la [ **Binding Path** ](binding-path.md) con más detalle y muestran cómo se puede utilizar para hacer referencia a elementos de las colecciones y subpropiedades.


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
