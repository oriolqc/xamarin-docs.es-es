---
title: Ruta de acceso de enlace de Xamarin.Forms
description: En este artículo se explica cómo usar enlaces de datos de Xamarin.Forms para tener acceso a las subpropiedades y miembros de la colección con la propiedad de ruta de acceso de la clase de enlace.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 5ffc167b1e5695663dff6005f3d7e0ba0ea958db
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172111"
---
# <a name="xamarinforms-binding-path"></a>Ruta de acceso de enlace de Xamarin.Forms

En todos los ejemplos de enlace de datos anteriores, el [ `Path` ](xref:Xamarin.Forms.Binding.Path) propiedad de la `Binding` clase (o la [ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path) propiedad de la `Binding` extensión de marcado) se ha establecido para una sola propiedad. En realidad es posible establecer `Path` a un *subpropiedad* (propiedad de una propiedad), o a un miembro de una colección.

Por ejemplo, supongamos que la página contiene un `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

El `Time` propiedad de `TimePicker` es de tipo `TimeSpan`, pero es posible que desee crear un enlace de datos que hace referencia a la `TotalSeconds` propiedad eso `TimeSpan` valor. Este es el enlace de datos:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

El `Time` propiedad es de tipo `TimeSpan`, que tiene un `TotalSeconds` propiedad. El `Time` y `TotalSeconds` propiedades son simplemente conectadas con un punto. Los elementos de la `Path` cadena siempre hacen referencia a propiedades y no a los tipos de estas propiedades.

Que el ejemplo y muchas otras personas se muestran en el **variaciones de la ruta de acceso** página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=mscorlib"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />

        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The second Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

En el segundo `Label`, el origen de enlace es la propia página. El `Content` propiedad es de tipo `StackLayout`, que tiene un `Children` propiedad de tipo `IList<View>`, que tiene un `Count` propiedad que indica el número de elementos secundarios.

## <a name="paths-with-indexers"></a>Rutas de acceso con los indizadores

El enlace en la tercera `Label` en el **variaciones de la ruta de acceso** referencias de páginas la [ `CultureInfo` ](xref:System.Globalization.CultureInfo) clase en el `System.Globalization` espacio de nombres:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

El origen está establecido en estático `CultureInfo.CurrentCulture` propiedad, que es un objeto de tipo `CultureInfo`. Que la clase define una propiedad denominada `DateTimeFormat` typu [ `DateTimeFormatInfo` ](xref:System.Globalization.DateTimeFormatInfo) que contiene un `DayNames` colección. El índice selecciona el cuarto elemento.

El cuarto `Label` hace algo similar pero en caso de la referencia cultural asociada con Francia. El `Source` propiedad del enlace está establecida en `CultureInfo` objeto con un constructor:

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

Consulte [pasar argumentos de Constructor](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para obtener más información sobre cómo especificar argumentos de constructor en XAML.

Por último, el último ejemplo es similar a la segunda, excepto en que hace referencia a uno de los elementos secundarios de la `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Ese elemento secundario es un `Label`, que tiene un `Text` propiedad de tipo `String`, que tiene un `Length` propiedad. La primera `Label` informes el `TimeSpan` establecido el `TimePicker`, de modo que cuando se cambia el texto, el último `Label` también cambia.

Este es el programa que se ejecuta:

[![Las variaciones de la ruta de acceso](binding-path-images/pathvariations-small.png "variaciones de la ruta de acceso")](binding-path-images/pathvariations-large.png#lightbox "variaciones de la ruta de acceso")

## <a name="debugging-complex-paths"></a>Depuración de trazados complejos

Las definiciones de ruta complejo pueden ser difíciles de construir: que necesita saber el tipo de cada propiedad subdirectorio o el tipo de elementos de la colección para agregar correctamente la subpropiedad siguiente, pero los tipos en sí no aparecen en la ruta de acceso. Es una buena técnica compilar la ruta de acceso de forma incremental y observar los resultados intermedios. Ese último ejemplo, puede empezar con no `Path` definición en absoluto:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Que muestra el tipo de origen de enlace, o `DataBindingDemos.PathVariationsPage`. Sabrá `PathVariationsPage` deriva `ContentPage`, por lo que tiene un `Content` propiedad:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

El tipo de la `Content` propiedad ahora se revela que `Xamarin.Forms.StackLayout`. Agregar el `Children` propiedad a la `Path` y el tipo es `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, que es una clase interna para Xamarin.Forms, pero obviamente un tipo de colección. Agregar un índice a la que y el tipo es `Xamarin.Forms.Label`. Continuar de esta manera.

A medida que Xamarin.Forms procesa la ruta de acceso de enlace, instala un `PropertyChanged` controlador en cualquier objeto de la ruta de acceso que implementa el `INotifyPropertyChanged` interfaz. Por ejemplo, el enlace final reacciona ante un cambio en la primera `Label` porque el `Text` los cambios de propiedad.

Si una propiedad en la ruta de acceso de enlace no implementa `INotifyPropertyChanged`, se omitirán todos los cambios a esa propiedad. Algunos cambios podrían invalidar completamente la ruta de acceso de enlace, por lo que debe usar esta técnica solo cuando la cadena de propiedades y subpropiedades nunca se convierten en no válido.



## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos del libro de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
