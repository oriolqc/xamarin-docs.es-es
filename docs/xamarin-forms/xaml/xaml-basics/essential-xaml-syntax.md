---
title: Parte 2. Sintaxis XAML esenciales
description: XAML está diseñado principalmente para crear instancias e inicializar objetos. Pero a menudo, se deben establecer propiedades en objetos más complejos que no se puede representar fácilmente como cadenas XML y a veces se deben establecer propiedades definidas por una clase en una clase secundaria. Estas dos necesidades necesitan las características de sintaxis XAML esenciales de elementos de propiedad y propiedades adjuntas.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 776b0d07de947c01761f8e8d8809b24cf7f3c3cb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintaxis XAML esenciales

_XAML está diseñado principalmente para crear instancias e inicializar objetos. Pero a menudo, se deben establecer propiedades en objetos más complejos que no se puede representar fácilmente como cadenas XML y a veces se deben establecer propiedades definidas por una clase en una clase secundaria. Estas dos necesidades necesitan las características de sintaxis XAML esenciales de elementos de propiedad y propiedades adjuntas._

## <a name="property-elements"></a>Elementos de propiedad

En XAML, las propiedades de las clases normalmente se establecen como atributos XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Sin embargo, hay una manera alternativa para establecer una propiedad en XAML. Para probar esta alternativa con `TextColor`, primero debe eliminar las existentes `TextColor` configuración:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Abra el elemento vacío `Label` etiqueta al separar en etiquetas inicial y final:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

Dentro de estas etiquetas, agregar etiquetas de inicio y finalización que consisten en el nombre de clase y un nombre de propiedad separados por un punto:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Establezca el valor de propiedad como contenido de estas etiquetas nuevas, como el siguiente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Estas dos maneras de especificar el `TextColor` propiedad son funcionalmente equivalentes, pero no use las dos maneras para la misma propiedad ya que se pueden establecer de forma efectiva la propiedad dos veces y podrían ser ambiguas.

Con esta nueva sintaxis, se puede introducir terminología práctica:

-  `Label` es un *elemento object*. Es un objeto de Xamarin.Forms expresado como un elemento XML.
-  `Text`, `VerticalOptions`, `FontAttributes` y `FontSize` son *atributos de la propiedad*. Únicamente son propiedades de Xamarin.Forms expresadas como atributos XML.
-  En ese fragmento final, `TextColor` se ha convertido en un *elemento property*. Se trata de una propiedad Xamarin.Forms pero ahora es un elemento XML.


La definición de propiedad podrían elementos en primero parecen estar una infracción de la sintaxis XML, pero no es así. El período de no tiene ningún significado especial en XML. A un descodificador XML, `Label.TextColor` es simplemente un elemento secundario normal.

En XAML, sin embargo, esta sintaxis es muy especial. Una de las reglas para los elementos de propiedad es que no hay ningún otro puede aparecer en la `Label.TextColor` etiqueta. El valor de la propiedad siempre se define como contenido entre el elemento de la propiedad etiquetas inicial y final.

Puede usar la sintaxis de elemento de propiedad en más de una propiedad:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

O bien, puede usar sintaxis de elemento de propiedad para todas las propiedades:

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

En primer lugar, sintaxis de elemento de propiedad pueden parecer un reemplazo extiende innecesario para comparativamente bastante sencillo, y en estos ejemplos por supuesto, es el caso.

Sin embargo, la sintaxis de elemento de propiedad resulta fundamental cuando el valor de una propiedad es demasiado complejo para expresarse como una cadena simple. Dentro de las etiquetas de elemento de propiedad puede crear una instancia de otro objeto y establecer sus propiedades. Por ejemplo, se puede establecer explícitamente una propiedad como `VerticalOptions` a una `LayoutOptions` valor con valores de propiedad:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Otro ejemplo: el `Grid` tiene dos propiedades denominadas `RowDefinitions` y `ColumnDefinitions`. Estas dos propiedades son de tipo `RowDefinitionCollection` y `ColumnDefinitionCollection`, que son conjuntos de `RowDefinition` y `ColumnDefinition` objetos. Debe utilizar la sintaxis de elemento de propiedad para establecer estas colecciones.

Este es el principio del archivo XAML para una `GridDemoPage` (clase), que muestra las etiquetas de elemento de propiedad para el `RowDefinitions` y `ColumnDefinitions` colecciones:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

Observe la sintaxis abreviada para definir las celdas ajuste automático de tamaño, las celdas de la configuración de estrella y el alto y el ancho de píxel.

## <a name="attached-properties"></a>Propiedades asociadas

Ya ha visto que la `Grid` requiere que los elementos de propiedad para el `RowDefinitions` y `ColumnDefinitions` recopilaciones para definir las filas y columnas. Sin embargo, también debe haber alguna manera para el programador indicar la fila y columna donde cada miembro secundario de la `Grid` reside.

Dentro de la etiqueta para cada miembro secundario de la `Grid` especificar la fila y columna de ese elemento secundario mediante los atributos siguientes:

-  `Grid.Row`
-  `Grid.Column`

Los valores predeterminados de estos atributos son 0. También puede indicar si un elemento secundario ocupa más de una fila o columna con estos atributos:

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

Estos dos atributos tienen valores predeterminados de 1.

Este es el archivo GridDemoPage.xaml completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

El `Grid.Row` y `Grid.Column` configuración de 0 no son necesarias pero están generalmente incluidos para una mayor claridad.

Este es su aspecto en las tres plataformas:

[![](essential-xaml-syntax-images/griddemo.png "Diseño de cuadrícula")](essential-xaml-syntax-images/griddemo-large.png#lightbox "diseño de cuadrícula")

Juzgar únicamente a partir de la sintaxis, estos `Grid.Row`, `Grid.Column`, `Grid.RowSpan`, y `Grid.ColumnSpan` atributos aparecen como campos estáticos ni propiedades de `Grid`, pero Curiosamente, `Grid` no definir cualquier elemento con nombre `Row`, `Column`, `RowSpan`, o `ColumnSpan`.

En su lugar, `Grid` define cuatro propiedades enlazables denominadas `RowProperty`, `ColumnProperty`, `RowSpanProperty`, y `ColumnSpanProperty`. Estos son tipos especiales de propiedades enlazables conocidas como *propiedades adjuntas*. Están definidos por el `Grid` clase pero se establece en elementos secundarios de la `Grid`.

Cuando desee utilizar estas propiedades en el código, adjuntas el `Grid` clase proporciona métodos estáticos denominados `SetRow`, `GetColumn`, y así sucesivamente. Pero en XAML, se establecen estas propiedades adjuntas como atributos en los elementos secundarios de la `Grid` con nombres de propiedades simples.

Propiedades adjuntas siempre son reconocibles en archivos XAML como atributos que contiene una clase y un nombre de propiedad separados por puntos. Se les llama *propiedades adjuntas* porque están definidos por una clase (en este caso, `Grid`) pero adjunta a otros objetos (en este caso, los elementos secundarios de la `Grid`). Durante el diseño, el `Grid` puede consultar los valores de estas propiedades adjuntas para saber dónde se debe colocar cada elemento secundario.

El `AbsoluteLayout` clase define dos propiedades asociadas con el nombre `LayoutBounds` y `LayoutFlags`. Este es un patrón de cuadros bicolores realizadas mediante la posición proporcional y características de ajuste de tamaño de `AbsoluteLayout`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

Y aquí es:

[![](essential-xaml-syntax-images/absolutedemo-large.png "Diseño absoluto")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "diseño absoluto")

Algo parecido a esto, podría preguntarse los conocimientos del uso de XAML. De hecho, la repetición y la regularidad de la `LayoutBounds` rectángulo sugiere que se podría generarse mejor en código.

Sin duda es una preocupación legítima y no hay ningún problema con el uso de código y marcado de equilibrio al definir las interfaces de usuario. Es fácil de definir algunos de los objetos visuales en XAML y, a continuación, utilice el constructor del archivo de código subyacente para agregar algunos elementos visuales más que podrían generarse mejor en bucles.

## <a name="content-properties"></a>Propiedades de contenido

En los ejemplos anteriores, la `StackLayout`, `Grid`, y `AbsoluteLayout` objetos se establecen en los `Content` propiedad de la `ContentPage`, y los elementos secundarios de estos diseños son realmente los elementos de la `Children` colección. Aunque estos `Content` y `Children` propiedades son ningún destino en el archivo XAML.

Por supuesto, puede incluir la `Content` y `Children` propiedades como elementos de propiedad, como en el **XamlPlusCode** ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

La verdadera pregunta es: ¿por qué estos elementos de propiedad *no* necesarios en el archivo XAML?

Los elementos definidos en Xamarin.Forms para su uso en XAML pueden tener una propiedad marcada en el `ContentProperty` atributo en la clase. Si busca la `ContentPage` clase en la documentación en línea de Xamarin.Forms, verá este atributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Esto significa que el `Content` etiquetas de elemento de propiedad no son necesarias. Cualquier contenido XML que aparece entre el inicio y fin `ContentPage` etiquetas se supone que se asignará a la `Content` propiedad.

 `StackLayout`, `Grid`, `AbsoluteLayout`, y `RelativeLayout` todos se derivan `Layout<View>`, y si buscará `Layout<T>` en la documentación de Xamarin.Forms, verá otra `ContentProperty` atributo:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Que permite que el contenido del diseño que se agregan automáticamente a la `Children` recopilación sin explícita `Children` etiquetas de elemento de propiedad.

También tienen otras clases `ContentProperty` definiciones de atributo. Por ejemplo, la propiedad de contenido de `Label` es `Text`. Consulte la documentación de API para que otros usuarios.

## <a name="platform-differences-with-onplatform"></a>Diferencias de plataforma con OnPlatform

En aplicaciones de una página, es común para establecer el `Padding` propiedad en la página para evitar sobrescribir la barra de estado de iOS. En el código, puede usar el `Device.RuntimePlatform` propiedad para este propósito:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

También puede hacer algo muy parecido en XAML con el `OnPlatform` y `On` clases. Incluir primero los elementos de propiedad para el `Padding` propiedad cerca de la parte superior de la página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Dentro de estas etiquetas incluyen un `OnPlatform` etiqueta. `OnPlatform` es una clase genérica. Debe especificar el argumento de tipo genérico, en este caso, `Thickness`, que es el tipo de `Padding` propiedad. Afortunadamente, hay un atributo XAML específicamente para definir argumentos genéricos llama `x:TypeArguments`. Esto debe coincidir con el tipo de la propiedad que está configurando:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` tiene una propiedad denominada `Platforms` que es un `IList` de `On` objetos. Usar etiquetas de elemento de propiedad para esa propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Ahora, agregue `On` elementos. Para cada onem establecer el `Platform` propiedad y el `Value` propiedad en el marcado para la `Thickness` propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Se puede simplificar este marcado. La propiedad de contenido de `OnPlatform` es `Platforms`, por lo que se pueden quitar estas etiquetas de elemento de propiedad:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

El `Platform` propiedad de `On` es de tipo `IList<string>`, por lo que puede incluir varias plataformas si los valores son los mismos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Dado que Windows y Android se establecen en el valor predeterminado de `Padding`, que se puede quitar la etiqueta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Esta es la manera estándar para establecer una depende de la plataforma `Padding` propiedad en XAML. Si el `Value` configuración no se puede representar mediante una cadena única, puede definir elementos de propiedad para él:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

## <a name="summary"></a>Resumen

Con los elementos de propiedad y propiedades adjuntas, gran parte de la sintaxis básica de XAML se ha establecido. Sin embargo, en ciertas ocasiones necesitará establecer propiedades a los objetos de una manera indirecta, por ejemplo, en un diccionario de recursos. Este enfoque se trata en la siguiente parte, parte [3. Las extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).



## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introducción a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
