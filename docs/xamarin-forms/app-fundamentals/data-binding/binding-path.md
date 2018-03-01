---
title: Ruta de acceso de enlace
description: "Utilizar los enlaces de datos a las subpropiedades de acceso y miembros de colección"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 39d326714a6fee1abe242a7256888647784cdec3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="binding-path"></a>Ruta de acceso de enlace

En todos los ejemplos de enlace de datos anteriores, el [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) propiedad de la `Binding` clase (o la [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/) propiedad de la `Binding` extensión de marcado) se ha establecido para una sola propiedad. En realidad es posible establecer `Path` a una *subpropiedad* (una propiedad de una propiedad), o a un miembro de una colección.

Por ejemplo, supongamos que la página contiene un `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

El `Time` propiedad de `TimePicker` es de tipo `TimeSpan`, pero es posible que desee crear un enlace de datos que hace referencia a la `TotalSeconds` propiedad de ese `TimeSpan` valor. Este es el enlace de datos:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```
         
El `Time` propiedad es de tipo `TimeSpan`, que tiene un `TotalSeconds` propiedad. El `Time` y `TotalSeconds` propiedades son simply conectados con un punto. Los elementos de la `Path` cadena siempre hacen referencia a propiedades y no a los tipos de estas propiedades.

Que se muestran en el ejemplo se y varios otros en el **variaciones de la ruta de acceso** página:

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
                              StringFormat='The first Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

En el segundo `Label`, el origen de enlace es la propia página. El `Content` propiedad es de tipo `StackLayout`, que tiene un `Children` propiedad de tipo `IList<View>`, que tiene un `Count` propiedad que indica el número de elementos secundarios.

## <a name="paths-with-indexers"></a>Rutas de acceso con los indizadores

El enlace en la tercera `Label` en el **variaciones de la ruta de acceso** páginas referencias la [ `CultureInfo` ](https://developer.xamarin.com/api/type/System.Globalization.CultureInfo/) clase en el `System.Globalization` espacio de nombres:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

El origen se establece en el método estático `CultureInfo.CurrentCulture` propiedad, que es un objeto de tipo `CultureInfo`. Que la clase define una propiedad denominada `DateTimeFormat` de tipo [ `DateTimeFormatInfo` ](https://developer.xamarin.com/api/type/System.Globalization.DateTimeFormatInfo/) que contiene un `DayNames` colección. El índice selecciona el cuarto elemento.

El cuarto `Label` hace algo similar pero en caso de la referencia cultural asociados a Francia. El `Source` propiedad del enlace está establecida en `CultureInfo` objeto con un constructor:

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

Vea [pasar argumentos de Constructor](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para obtener más información sobre cómo especificar argumentos de constructor en XAML.

Por último, el último ejemplo es similar a la segunda, salvo que hace referencia a uno de los elementos secundarios de la `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Ese elemento secundario es un `Label`, que tiene un `Text` propiedad de tipo `String`, que tiene un `Length` propiedad. La primera `Label` informes la `TimeSpan` establecido el `TimePicker`, de modo que cuando se cambia el texto, la última `Label` también cambia.

Este es el programa que se ejecuta en las tres plataformas:

[![Las variaciones de la ruta de acceso](binding-path-images/pathvariations-small.png "variaciones de la ruta de acceso")](binding-path-images/pathvariations-large.png "variaciones de la ruta de acceso")

## <a name="debugging-complex-paths"></a>Depuración de las rutas de acceso complejas

Las definiciones de ruta de acceso compleja pueden ser difíciles de construir: debe conocer el tipo de cada propiedad secundaria o el tipo de elementos de la colección a correctamente, agregue la siguiente propiedad secundaria, pero los tipos en sí no aparecen en la ruta de acceso. Es una buena técnica compilar la ruta de acceso de forma incremental y examine los resultados intermedios. En ese último ejemplo, puede empezar con un no `Path` definición en absoluto:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Que muestra el tipo del origen de enlace, o `DataBindingDemos.PathVariationsPage`. Sabrá `PathVariationsPage` deriva `ContentPage`, por lo que tiene un `Content` propiedad:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

El tipo de la `Content` propiedad ahora se revele a ser `Xamarin.Forms.StackLayout`. Agregar el `Children` propiedad a la `Path` y el tipo es `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, que es una clase interna de Xamarin.Forms, pero obviamente un tipo de colección. Agregar un índice a la que y el tipo es `Xamarin.Forms.Label`. Continuar de esta manera.

Como Xamarin.Forms procesa la ruta de acceso de enlace, instala un `PropertyChanged` controlador sobre cualquier objeto en la ruta de acceso que implementa el `INotifyPropertyChanged` interfaz. Por ejemplo, el enlace final reacciona a un cambio en la primera `Label` porque el `Text` cambios de propiedad. 

Si una propiedad en la ruta de acceso de enlace no implementa `INotifyPropertyChanged`, se pasará por alto los cambios en esa propiedad. Algunos cambios podrían invalidar completamente la ruta de acceso de enlace, por lo que debe usar esta técnica solo cuando la cadena de propiedades y subpropiedades nunca se convierten en no válido.



## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de enlace de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de enlace de datos de la libreta de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
