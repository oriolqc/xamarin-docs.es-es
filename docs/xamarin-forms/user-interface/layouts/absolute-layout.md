---
title: AbsoluteLayout
description: Utilice AbsoluteLayout para crear interfaces de usuario perfecta de píxeles.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: d07026fbcc43a43a9f26d85ad15d5a4e3165e2ef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="absolutelayout"></a>AbsoluteLayout

[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) posiciones y tamaños de los elementos secundarios proporcionales a su propio tamaño y posición o por valores absolutos. Pueden ser vistas secundarias coloca y tamaño utilizando valores proporcionales o valores estáticos y proporcional y se pueden combinar valores estáticos.

[![](absolute-layout-images/layouts-sml.png "Diseños de Xamarin.Forms")](absolute-layout-images/layouts.png#lightbox "diseños de Xamarin.Forms")

En este artículo se tratará:

- **[Propósito](#Purpose)**  &ndash; usos comunes de `AbsoluteLayout`.
- **[Uso de](#Usage)**  &ndash; cómo usar `AbsoluteLayout` para lograr el diseño deseado.
  - **[Diseños proporcionales](#Proportional_Layouts)**  &ndash; comprender cómo proporcionales valores funcionan en un `AbsoluteLayout`.
  - **[Especificar valores](#Specifying_Values)**  &ndash; comprender cómo se especifican proporcional y valores absolutos.
  - **[Valores proporcionales](#Proportional_Values)**  &ndash; comprender cómo proporcionales valores de trabajo.
    - **[Valores absolutos](#Absolute_Values)**  &ndash; comprender cómo funcionan los valores absolutos.

<a name="Purpose" />

## <a name="purpose"></a>Propósito

Debido al modelo de posición de `AbsoluteLayout`, el diseño resulta bastante sencillo colocar los elementos para que resulten alineado con cualquiera de los lados del diseño o centrada. Con proporcionales tamaños y posiciones, los elementos en una `AbsoluteLayout` puede escalar automáticamente a cualquier tamaño de la vista. Para los elementos que se debe escalar solo la posición, pero no el tamaño, se pueden combinar valores absolutos y proporcionales.

`AbsoluteLayout` se puede usar desde cualquier lugar elementos se deben colocar en una vista y resulta especialmente útil para alinear los elementos con bordes.

<a name="Usage" />

## <a name="usage"></a>Uso

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>Diseños proporcionales

`AbsoluteLayout` tiene un modelo de delimitador único mediante el cual se coloca el delimitador del elemento respecto a su elemento como el elemento se coloca en relación con el diseño cuando se utiliza la posición proporcional. Cuando se utiliza la posición absoluta, el delimitador es (0,0) dentro de la vista. Esto tiene dos consecuencias importantes:

- No se puede colocar elementos fuera de la pantalla con los valores proporcionales.
- Los elementos se pueden colocar una forma confiable en cualquier lado del diseño o en el centro, independientemente del tamaño de la distribución o el dispositivo.

`AbsoluteLayout`, como `RelativeLayout`, puede colocar elementos de forma que se superpongan.

Observe que, en la siguiente captura de pantalla, el delimitador del cuadro es un punto blanco. Tenga en cuenta la relación entre el delimitador y el cuadro como si moviera por el diseño:

![](absolute-layout-images/anchor-start.png "Delimitador en el inicio de")
![](absolute-layout-images/anchor-center.png "delimitador en centro")
![](absolute-layout-images/anchor-end.png "delimitador final")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>Especificar valores

Vistas dentro de un `AbsoluteLayout` se colocan con cuatro valores:

- **X** &ndash; la posición x (horizontal) del delimitador de la vista
- **Y** &ndash; la posición y (vertical) del delimitador de la vista
- **Ancho** &ndash; el ancho de la vista
- **Alto** &ndash; el alto de la vista

Cada uno de estos valores puede establecerse como un [proporcional](#Proportional_Values) valor o una [absoluta](#Absolute_Values) valor.

Los valores se especifican como una combinación de límites y una marca. `LayoutBounds` es un [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/) que consta de cuatro valores: `x`, `y`, `width`, `height`.

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/) Especifica cómo se interpretarán los valores y tiene las siguientes opciones predeterminadas:

- **Ninguno** &ndash; interpreta todos los valores como absoluto. Este es el valor predeterminado si no se especifica ningún marcador de diseño.
- **Todos los** &ndash; interpreta todos los valores como proporcional.
- **WidthProportional** &ndash; interpreta la `Width` valor como proporcional y todos los demás valores como absoluto.
- **HeightProportional** &ndash; interpreta solo el valor del alto como proporcional con todos los demás valores absolutos.
- **XProportional** &ndash; interpreta la `X` valor como proporcional, mientras que todos los demás valores como absoluto.
- **YProportional** &ndash; interpreta la `Y` valor como proporcional, mientras que todos los demás valores como absoluto.
- **PositionProportional** &ndash; interpreta la `X` y `Y` valores como proporcional, mientras que los valores de tamaño se interpretan como absoluto.
- **SizeProportional** &ndash; interpreta la `Width` y `Height` valores como proporcional, mientras que los valores de posición son absolutos.

En XAML, los límites y los indicadores se establecen como parte de la definición de vistas en el diseño, usando la `AbsoluteLayout.LayoutBounds` propiedad. Límites se establecen como una lista separada por comas de valores, `X`, `Y`, `Width`, y `Height`, en ese orden. Marcas también se especifican en la declaración de vistas en el diseño mediante la `AbsoluteLayout.LayoutFlags` propiedad. Tenga en cuenta que las marcas se pueden combinar en XAML mediante una lista separada por comas. Considere el ejemplo siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![](absolute-layout-images/exploration.png "Ejemplos de AbsoluteLayout")

Tenga en cuenta lo siguiente:

- La etiqueta en el centro se coloca con los valores de tamaño y posición absolutos. Por este motivo, aparece centrado en iPhone 4S e inferior, pero no centrado en los dispositivos más grandes.
- Se coloca el texto en la parte inferior del diseño con los valores de tamaño y posición proporcionales. Siempre aparecerá en la parte inferior central del diseño, pero su tamaño crece junto con los tamaños más grandes de diseño.
- Tres colores `BoxView`s se colocan en los bordes superiores, izquierdos y derecho de la pantalla con posición proporcional y el tamaño absoluto.

A continuación, logra el mismo diseño de C#:

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```
<a name="Proportional_Values" />

### <a name="proportional-values"></a>Valores proporcionales

Valores proporcionales definen una relación entre un diseño y una vista. Esta relación define la posición o el valor de escala de una vista secundaria como una proporción del valor correspondiente del diseño del elemento primario. Estos valores se expresan como `double`s con valores comprendidos entre 0 y 1.

Proporcionales valores se utilizan para la posición y las vistas de tamaño en el diseño. Por lo tanto, cuando se establece el ancho de una vista como una proporción, el valor de ancho resultante es la proporción multiplicada por el `AbsoluteLayout`del ancho. Por ejemplo, con un `AbsoluteLayout` de ancho `500` y una vista que se define para tener una anchura proporcional de.5, el ancho de la vista representado será 250 (500 x.5.

Para usar valores proporcionales, establezca `LayoutBounds` usando (x, y) proporciones y tamaños proporcionales, a continuación, establezca `LayoutFlags` a `All`.

En XAML:

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

En C#:

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

<a name="Absolute_Values" />

### <a name="absolute-values"></a>Valores absolutos

Valores absolutos definir explícitamente dónde deben situarse vistas en el diseño. A diferencia de los valores proporcionales, valores absolutos son capaces de colocación y ajustar el tamaño de una vista que no se ajusta dentro de los límites del diseño.

Usar valores absolutos para determinar la posición puede ser peligroso cuando no se conoce el tamaño del diseño. Al utilizar posiciones absolutas, se puede desplazar un elemento en el centro de la pantalla en un tamaño en cualquier otro tamaño. Es importante probar la aplicación a través de los distintos tamaños de pantalla de los dispositivos compatibles.

Para usar valores de diseño absoluta, establezca `LayoutBounds` usando (x, y) coordenadas y los tamaños explícitos, a continuación, establezca `LayoutFlags` a `None`.

En XAML:

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

En C#:

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>Explorar un diseño complejo

Cada uno de los diseños tiene ventajas y desventajas de la creación de diseños determinados. A lo largo de esta serie de artículos de diseño, se ha creado una aplicación de ejemplo con el mismo diseño de página que se implementa mediante tres diseños diferentes.

Tenga en cuenta el siguiente código XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

El código anterior genera el siguiente diseño:

![](absolute-layout-images/abs.png "AbsoluteLayout compleja")

Tenga en cuenta que, debido a una diferencia en cómo se representan los botones por Windows Phone, algunos de los círculos se han reemplazado por boxviews en la captura de pantalla de Windows Phone.

Tenga en cuenta que `AbsoluteLayout`s están anidados, porque en algunos casos diseños de anidamiento puede ser más sencillo que presentar todos los elementos en el mismo diseño.



## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)
- [Diseño (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Ejemplo de BusinessTumble (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
