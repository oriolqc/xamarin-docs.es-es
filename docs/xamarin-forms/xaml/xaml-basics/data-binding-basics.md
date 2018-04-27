---
title: Parte 4. Conceptos básicos del enlace de datos
description: Enlaces de datos permiten propiedades de los dos objetos para vincularse para que un cambio en uno hace un cambio en la otra. Se trata de una herramienta muy valiosa y mientras completamente en código, se pueden definir los enlaces de datos, XAML proporciona accesos directos y la comodidad. Por lo tanto, una de las extensiones de marcado más importantes de Xamarin.Forms enlaza.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 4e3e090d826aa46d503f8c612250fd5122bc703e
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="part-4-data-binding-basics"></a>Parte 4. Conceptos básicos del enlace de datos

_Enlaces de datos permiten propiedades de los dos objetos para vincularse para que un cambio en uno hace un cambio en la otra. Se trata de una herramienta muy valiosa y mientras completamente en código, se pueden definir los enlaces de datos, XAML proporciona accesos directos y la comodidad. Por lo tanto, una de las extensiones de marcado más importantes de Xamarin.Forms enlaza._

## <a name="data-bindings"></a>Enlaces de datos

Enlaces de datos conectan las propiedades de dos objetos, denominado el *origen* y *destino*. En el código, se requieren dos pasos: el `BindingContext` propiedad del objeto de destino debe establecerse en el objeto de origen y el `SetBinding` (método) (a menudo se utiliza junto con la `Binding` clase) se debe llamar en el objeto de destino para enlazar una propiedad de ese objeto a una propiedad del objeto de origen.

La propiedad de destino debe ser una propiedad enlazable, lo que significa que el objeto de destino debe derivar de `BindableObject`. La documentación en línea de Xamarin.Forms indica qué propiedades son propiedades enlazables. Una propiedad de `Label` como `Text` está asociado a la propiedad enlazable `TextProperty`.

En el marcado, también debe realizar los mismos dos pasos que son necesarios en el código, salvo que la `Binding` extensión de marcado ocupa el lugar de la `SetBinding` llamar a y el `Binding` clase.

Sin embargo, al definir los enlaces de datos en XAML, hay varias maneras de establecer el `BindingContext` del objeto de destino. A veces se establece en el archivo de código subyacente, a veces usando una `StaticResource` o `x:Static` extensión de marcado y a veces como el contenido de `BindingContext` etiquetas de elemento de propiedad.

Enlaces se usan con más frecuencia para conectar los objetos visuales de un programa con un modelo de datos subyacente, por lo general en un cumplimiento de la arquitectura de aplicación MVVM (Model-View-ViewModel), como se describe en [parte 5. Desde los enlaces de datos para MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), pero también son posibles otros escenarios.

## <a name="view-to-view-bindings"></a>Para ver enlaces

Puede definir los enlaces de datos para vincular las propiedades de las dos vistas en la misma página. En este caso, establezca el `BindingContext` del objeto de destino con el `x:Reference` extensión de marcado.

Este es un archivo XAML que contiene un `Slider` y dos `Label` vistas, uno de los cuales se gira la `Slider` valor y otra que muestra la `Slider` valor:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

El `Slider` contiene un `x:Name` atributo que hace referencia a los dos `Label` vistas mediante el `x:Reference` extensión de marcado.

El `x:Reference` extensión de enlace define una propiedad denominada `Name` para establecer en el nombre del elemento que se hace referencia, en este caso `slider`. Sin embargo, el `ReferenceExtension` clase que define la `x:Reference` extensión de marcado también define un `ContentProperty` atributo `Name`, lo que significa que no requiere de forma explícita. Solo para diversos, el primer `x:Reference` incluye "nombre =" pero no así la segunda:

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

El `Binding` propia extensión de marcado puede tener varias propiedades, igual que el `BindingBase` y `Binding` clase. El `ContentProperty` para `Binding` es `Path`, pero la "ruta de acceso =" parte de la extensión de marcado puede omitirse si la ruta de acceso es el primer elemento en el `Binding` extensión de marcado. El primer ejemplo tiene "ruta de acceso =", pero omite en el segundo ejemplo:

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Las propiedades pueden estar en una línea o separan en varias líneas:

```csharp
Text="{Binding Value, 
               StringFormat='The angle is {0:F0} degrees'}"
```

Hacer lo que sea conveniente.

Observe el `StringFormat` propiedad en el segundo `Binding` extensión de marcado. De Xamarin.Forms, enlaces no realizar ninguna conversión de tipos implícita, y si se necesitan para mostrar un objeto no es una cadena como una cadena debe proporcionar un convertidor de tipos o utilizar `StringFormat`. En segundo plano, el método estático `String.Format` método se utiliza para implementar `StringFormat`. Que es potencialmente un problema, dado que las especificaciones de formato de .NET implican entre llaves, que también se utiliza para delimitar las extensiones de marcado. Esto crea un riesgo de confundir el analizador de XAML. Para evitar esto, coloque toda la cadena de formato en comillas simples:

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Este es el programa en ejecución:

[![](data-binding-basics-images/sliderbinding.png "Para ver enlaces")](data-binding-basics-images/sliderbinding-large.png#lightbox "enlaces para ver ")

## <a name="the-binding-mode"></a>El modo de enlace 

Una vista única puede tener enlaces de datos en varias de sus propiedades. Sin embargo, cada vista solo puede tener un `BindingContext`, por lo que varios enlaces de datos en esa vista, deben hacer referencia a propiedades del mismo objeto.

La solución a este y otros problemas implica la `Mode` propiedad, que se establece en un miembro de la `BindingMode` enumeración:

- `Default` 
- `OneWay` : los valores se transfieren desde el origen al destino
- `OneWayToSource` : los valores se transfieren desde el destino al origen
- `TwoWay` : los valores se transfieren ambos sentidos entre el origen y de destino

El programa siguiente muestra un uso común de la `OneWayToSource` y `TwoWay` modos de enlace. Cuatro `Slider` vistas están pensadas para controlar la `Scale`, `Rotate`, `RotateX`, y `RotateY` propiedades de un `Label`. En primer lugar, parece como si estas cuatro propiedades de la `Label` debería ser destinos de enlace de datos porque cada una se establece un `Slider`. Sin embargo, el `BindingContext` de `Label` puede ser un único objeto, y hay cuatro controles deslizantes diferentes.

Por esta razón, todos los enlaces se establecen en aparentemente hacia atrás maneras: el `BindingContext` de cada uno de los controles cuatro deslizantes se establece en el `Label`, y los enlaces están definidos en la `Value` propiedades de los controles deslizantes. Mediante el uso de la `OneWayToSource` y `TwoWay` modos, estos `Value` propiedades pueden establecer las propiedades de origen, que son el `Scale`, `Rotate`, `RotateX`, y `RotateY` propiedades de la `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

Los enlaces en tres de los `Slider` vistas son `OneWayToSource`, lo que significa que la `Slider` valor hace que un cambio en la propiedad de su `BindingContext`, que es el `Label` denominado `label`. Estos tres `Slider` vistas provocan cambios en el `Rotate`, `RotateX`, y `RotateY` propiedades de la `Label`.

Sin embargo, el enlace para el `Scale` propiedad es `TwoWay`. Esto es porque el `Scale` propiedad tiene un valor predeterminado de 1 y que estás usando un `TwoWay` enlace causas el `Slider` inicial del valor que se establecerá en 1 en lugar de 0. Si fuera de ese enlace `OneWayToSource`, `Scale` inicialmente se establecería la propiedad en 0 de la `Slider` valor predeterminado. El `Label` no estará visible y que pueden confundir al usuario.

 [![](data-binding-basics-images/slidertransforms.png "Con las versiones anteriores enlaces")](data-binding-basics-images/slidertransforms-large.png#lightbox "hacia atrás enlaces")

## <a name="bindings-and-collections"></a>Enlaces y las colecciones

Nada muestra la eficacia de XAML y enlaces de datos mejores que una plantilla `ListView`.

`ListView` define un `ItemsSource` propiedad de tipo `IEnumerable`, y muestra los elementos de la colección. Estos elementos pueden ser objetos de cualquier tipo. De forma predeterminada, `ListView` usa el `ToString` método de cada elemento para mostrar ese elemento. A veces, esto es lo que desea, pero en muchos casos, `ToString` devuelve solo el nombre de clase completo del objeto.

Sin embargo, los elementos de la `ListView` colección se puede mostrar como desee mediante el uso de un *plantilla*, lo que implica una clase que deriva de `Cell`. La plantilla se clona para todos los elementos de la `ListView`, y los enlaces de datos que se han establecido en la plantilla se transfieren a los clones individuales.

Con frecuencia, deseará crear una celda personalizada para estos elementos mediante el `ViewCell` clase. Este proceso es un poco complicado en el código, pero en XAML es muy sencillo.

Incluye en el XamlSamples proyecto es una clase denominada `NamedColor`. Cada `NamedColor` objeto tiene `Name` y `FriendlyName` propiedades de tipo `string`y un `Color` propiedad de tipo `Color`. Además, `NamedColor` tiene 141 campos estáticos de solo lectura de tipo `Color` correspondientes a los colores definidos en el Xamarin.Forms `Color` clase. Un constructor estático crea una `IEnumerable<NamedColor>` colección que contiene `NamedColor` objetos correspondientes a estos campos estáticos y se asigna a su public static `All` propiedad.

Establecer el método estático `NamedColor.All` propiedad a la `ItemsSource` de un `ListView` es fácil con el `x:Static` extensión de marcado:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

La presentación resultante establece que los elementos son realmente del tipo `XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "Enlazar a una colección")](data-binding-basics-images/listview1-large.png#lightbox "enlace a una colección")

No es toda la información, pero la `ListView` es desplazable y seleccionable.

Para definir una plantilla para los elementos, desea dividir el `ItemTemplate` propiedad como un elemento de propiedad y establézcalo en un `DataTemplate`, que hace referencia a continuación, un `ViewCell`. Para el `View` propiedad de la `ViewCell` puede definir un diseño de una o varias vistas para mostrar cada elemento. Este es un ejemplo simple:

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

El `Label` elemento está establecido en el `View` propiedad de la `ViewCell`. (El `ViewCell.View` etiquetas no son necesarios porque el `View` propiedad es la propiedad de contenido de `ViewCell`.) Este marcado muestra el `FriendlyName` propiedad de cada `NamedColor` objeto:

[![](data-binding-basics-images/listview2.png "Enlazar a una colección con un objeto DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "enlace a una colección con una plantilla de datos")

Mucho mejor. Ahora todo lo que se necesita es refinar la plantilla de elemento con el color real y obtener más información. Para admitir esta plantilla, algunos valores y los objetos se han definido en el diccionario de recursos de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Observe el uso de `OnPlatform` para definir el tamaño de un `BoxView` y el alto de la `ListView` filas. Aunque los valores de las tres plataformas son los mismos, el marcado fácilmente pudo adaptado para otros valores ajustar la presentación. 

## <a name="binding-value-converters"></a>Convertidores de valores de enlace

Anterior **ListView demostración** archivo XAML muestra el individuo `R`, `G`, y `B` propiedades de la Xamarin.Forms `Color` estructura. Estas propiedades son de tipo `double` y oscilan entre 0 y 1. Si desea mostrar los valores hexadecimales, no se puede usar simplemente `StringFormat` con una especificación de formato "X2". Que solo funciona para números enteros y además, la `double` los valores tienen que ser multiplicado por 255.

Este pequeño problema se resolvió con una *convertidor de valores*, también denominado una *convertidor enlace*. Se trata de una clase que implementa el `IValueConverter` interfaz, lo que significa tiene dos métodos denominados `Convert` y `ConvertBack`. El `Convert` método se llama cuando se transfiere un valor de origen al destino; el `ConvertBack` método se llama para las transferencias de destino a origen de `OneWayToSource` o `TwoWay` enlaces:

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

El `ConvertBack` método desempeñan un papel de este programa porque los enlaces son solo una manera de origen a destino. 

Un enlace hace referencia a un convertidor de enlace con el `Converter` propiedad. Un convertidor de enlace también puede aceptar un parámetro especificado con el `ConverterParameter` propiedad. Para algunos versatilidad, es cómo se especifica el multiplicador. El convertidor de enlace comprueba el parámetro de convertidor para válido `double` valor.

El convertidor se crea una instancia del diccionario de recursos para que se pueda compartir entre varios enlaces:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Enlaces de datos de tres hacen referencia a esta instancia única. Tenga en cuenta que la `Binding` extensión de marcado contiene incrustada `StaticResource` extensión de marcado:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Este es el resultado:

[![](data-binding-basics-images/listview3.png "Enlazar a una colección con una plantilla de datos y los convertidores")](data-binding-basics-images/listview3-large.png#lightbox "enlace a una colección con una plantilla de datos y los convertidores")

El `ListView` es bastante sofisticada para controlar los cambios que pueden producirse dinámicamente en la base datos, pero solo si seguir unos pasos determinados. Si la colección de elementos que se asigna a la `ItemsSource` propiedad de la `ListView` cambios en tiempo de ejecución, es decir, si los elementos se pueden agregar a o quitar de la colección: usar un `ObservableCollection` clase para estos elementos. `ObservableCollection` implementa el `INotifyCollectionChanged` interfaz, y `ListView` instalará un controlador para el `CollectionChanged` eventos.

Si las propiedades de los mismos elementos cambian en tiempo de ejecución, los elementos de la colección deben implementar la `INotifyPropertyChanged` interfaz y señal los cambios a los valores de propiedad mediante el `PropertyChanged` eventos. Esto se muestra en la siguiente parte de esta serie, [parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumen

Enlaces de datos proporcionan un mecanismo eficaz para vincular propiedades entre dos objetos dentro de una página, o entre objetos visuales y los datos subyacentes. Pero cuando la aplicación comienza a trabajar con orígenes de datos, un modelo de arquitectura de aplicaciones populares comienza a aparecer como un paradigma útil. Este tema se trata en [parte 5. Desde los enlaces de datos para MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introducción a XAML (ejemplo)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxis de XAML esenciales (ejemplo)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensiones de marcado de XAML (ejemplo)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. De enlaces de datos a MVVM (ejemplo)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
