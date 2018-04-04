---
title: Formato de cadena
description: Usar enlaces de datos para dar formato y mostrar objetos como cadenas
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 4e143f650c3cde7577def1a95e53b207608a088a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="string-formatting"></a>Formato de cadena

A veces es conveniente usar enlaces de datos para mostrar la representación de cadena de un objeto o valor. Por ejemplo, puede usar un `Label` para mostrar el valor actual de un `Slider`. En este enlace de datos, el `Slider` es el origen y el destino es el `Text` propiedad de la `Label`.

Para mostrar las cadenas en el código, la herramienta más eficaz es el método estático [ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/) método. La cadena de formato incluye códigos específicos para distintos tipos de objetos de formato, y puede incluir otro texto junto con los valores que se está dando formato. Consulte la [aplicar formato a tipos en .NET](/dotnet/standard/base-types/formatting-types/) artículo para obtener más información sobre cómo dar formato de cadena.

## <a name="the-stringformat-property"></a>La propiedad StringFormat

Esta función se traslada en enlaces de datos: establece el [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/) propiedad de `Binding` (o [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.StringFormat/) propiedad de la `Binding` extensión de marcado) a un cadena con un marcador de posición de formato de .NET estándar:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Tenga en cuenta que la cadena de formato está delimitada por caracteres de comillas simples (apóstrofo) para evitar el analizador de XAML de tratar las llaves como otra extensión de marcado XAML. En caso contrario, que cadena sin el carácter de comillas simples es la misma cadena que se utilizaría para mostrar un valor de punto flotante en una llamada a `String.Format`. Una especificación de formato de `F2` hace que el valor que se mostrará con dos posiciones decimales.

El `StringFormat` propiedad sólo tiene sentido cuando la propiedad de destino es de tipo `string`, y el modo de enlace es `OneWay` o `TwoWay`. Para enlaces bidireccionales, el `StringFormat` solo es aplicable para los valores que se pasan desde el origen al destino.

Como verá en el siguiente artículo en el [Binding Path](binding-path.md), enlaces de datos pueden llegar a ser bastante compleja y complicadas. Al depurar estos enlaces de datos, puede agregar un `Label` en el archivo XAML con un `StringFormat` para mostrar algunos de los resultados intermedios. Incluso si se usa solo para mostrar un tipo de objeto, que puede resultar útil.

El **formato de cadena** página muestra varios usos de la `StringFormat` propiedad:

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

Los enlaces en el `Slider` y `TimePicker` se muestra el uso de las especificaciones de formato específicos de `double` y `TimeSpan` tipos de datos. El `StringFormat` que muestra el texto de la `Entry` vista muestra cómo especificar las comillas dobles en la cadena de formato con el uso de la `&quot;` entidad HTML.

La siguiente sección en el archivo XAML es un `StackLayout` con un `BindingContext` establecido en un `x:Static` extensión de marcado que hace referencia el método estático `DateTime.Now` propiedad. El primer enlace no tiene propiedades:

```xaml
<Label Text="{Binding}" />
```

Esto muestra simplemente la `DateTime` valor de la `BindingContext` con formato predeterminado. Muestra el enlace la `Ticks` propiedad de `DateTime`, mientras que los otros dos enlaces mostrar el `DateTime` propio con un formato específico. Tenga en cuenta esto `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Si tiene que mostrar hacia la izquierda o derecha las llaves en la cadena de formato, use simplemente un par de ellos.

Los últimos conjuntos de sección la `BindingContext` en el valor de `Math.PI` y se muestra con formato de forma predeterminada y dos tipos diferentes de formato numérico.

Este es el programa que se ejecuta en las tres plataformas:

[![Formato de cadena](string-formatting-images/stringformatting-small.png "formato de cadena")](string-formatting-images/stringformatting-large.png#lightbox "formato de cadena")

## <a name="viewmodels-and-string-formatting"></a>Cadena de formato y ViewModels

Cuando usas `Label` y `StringFormat` para mostrar el valor de una vista que también es el destino de un modelo de vista, se puede definir el enlace de la vista para la `Label` o desde el modelo de vista para el `Label`. En general, el segundo enfoque es mejor porque comprueba que los enlaces entre la vista y el modelo de vista se están trabajando.

Este enfoque se muestra en el **mejor Selector de colores** ejemplo, que usa el mismo modelo de vista como la **Selector de Color sencillo** programa se muestra en el [ **Binding Mode** ](binding-mode.md) artículo:

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

Ahora hay tres pares de `Slider` y `Label` elementos que están enlazados al mismo origen de propiedad en el `HslColorViewModel` objeto. La única diferencia es que `Label` tiene un `StringFormat` propiedad para mostrar cada uno de ellos `Slider` valor.

[![Selector de Color mejor](string-formatting-images/bettercolorselector-small.png "mejor el Selector de Color")](string-formatting-images/bettercolorselector-large.png#lightbox "mejor el Selector de Color")

Tal vez se pregunte cómo puede mostrar los valores RGB (rojos, verdes, azul) en formato hexadecimal de dos dígitos tradicionales. Los valores enteros no están directamente disponibles desde el `Color` estructura. Una solución sería calcular valores enteros de los componentes de color en el modelo de vista y exponerlos como propiedades. A continuación, podría formatearlos mediante el `X2` especificación de formato.

Otro enfoque es más general: puede escribir un *convertidor de valores de enlace* tal como se describe en el artículo de una versión posterior, [ **convertidores de valores de enlace**](converters.md).

El siguiente artículo, sin embargo, se explora la [ **Binding Path** ](binding-path.md) con más detalle y muestran cómo se puede utilizar para hacer referencia a elementos de colecciones y subpropiedades.


## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos de la libreta de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
