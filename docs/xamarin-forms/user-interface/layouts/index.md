---
title: Diseños de
description: Diseñar vistas en pantalla.
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 2f57ac5b5b54b2606618c5e59fb544cae7c77e88
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="layouts"></a>Diseños de

Xamarin.Forms tiene varios diseños y características para organizar el contenido en pantalla. 

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Diseños de Xamarin.Forms, por [Universidad de Xamarin](https://university.xamarin.com/)**

A continuación, se describe cada control de diseño, así como información detallada sobre cómo controlar los cambios de orientación de pantalla:

* **[StackLayout](stack-layout.md)**  &ndash; usada para organizar vistas linealmente, horizontal o verticalmente. Vistas en un StackLayout pueden ser alineadas en el centro, izquierdo o derecho del diseño.
* **[AbsoluteLayout](absolute-layout.md)**  &ndash; permiten organizar vistas mediante el establecimiento de coordenadas y cambio de tamaño en cuanto a los valores absolutos o proporciones. AbsoluteLayout puede utilizarse para vistas de las capas, así como fijarlos a la izquierda, derecha o centrado.
* **[RelativeLayout](relative-layout.md)**  &ndash; usada para organizar vistas mediante el establecimiento de restricciones con respecto a las dimensiones y la posición de sus elementos primarios.
* **[Cuadrícula](grid.md)**  &ndash; usada para organizar vistas en una cuadrícula. Filas y columnas se pueden especificar en cuanto a los valores absolutos o proporciones.
* **[FlexLayout](flex-layout.md)**  &ndash; usada para organizar vistas horizontal o verticalmente con ajuste.
* **[ScrollView](scroll-view.md)**  &ndash; utilizan para proporcionar desplazamiento cuando una vista no cabe por completo dentro de los límites de la pantalla.
* **[LayoutOptions](layout-options.md)**  &ndash; Definir alineación y expansión de una vista, en relación con su elemento primario.
* **[Entrada transparencia](#input_transparency)**  &ndash; especifica si un elemento recibe la entrada.
* **[Márgenes y relleno](margin-and-padding.md)**  &ndash; muestra cómo controlar el comportamiento de diseño cuando se representa un elemento en la interfaz de usuario.
* **[Orientación del dispositivo](device-orientation.md)**  &ndash; explica cómo controlar los cambios de orientación de dispositivo.
* **[Diseño en dispositivos de tableta y escritorio](tablet.md)**  &ndash; muestra cómo optimizar para pantallas más grandes en cada plataforma.
* **[Crear un diseño personalizado](custom.md)**  &ndash; explica cómo crear una clase de diseño personalizado.
* **[Compresión de diseño](layout-compression.md)**  &ndash; quita especificado diseño el árbol visual en un intento de mejorar el rendimiento de la presentación de página.

Controles de plataforma también pueden usarse directamente en los diseños de Xamarin.Forms con [ **incrustar nativo** ](~/xamarin-forms/platform/native-views/index.md) (nuevo en Xamarin.Forms 2.2), y puede [ **crear diseños personalizados** ](custom.md) para cumplir requisitos específicos.

El gráfico siguiente muestra los controles de diseño:

[![](images/layouts-sml.png "Diseños de Xamarin.Forms")](images/layouts.png#lightbox "diseños de Xamarin.Forms")

## <a name="choosing-the-right-layout"></a>Elegir el diseño de la derecha

Los diseños que elija en la aplicación pueden ayudar a o dañar tal y como se va a crear una aplicación de Xamarin.Forms atractiva y utilizable. Tarda algún tiempo para pensar en cómo cada funciona de diseño puede ayudarle a escribir código de interfaz de usuario más limpia y más escalable. Una pantalla puede tener una combinación de diseños diferentes para lograr un diseño específico.

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

El `StackLayout` se utiliza para mostrar vistas a lo largo de una línea horizontal o vertical. Posición y el tamaño en el diseño se determina basándose en una vista `HeightRequest`, `WidthRequest`, `HorizontalOptions` y `VerticalOptions`. `StackLayout` a menudo se usa como el diseño de base, organizar otros diseños en la pantalla.

Para obtener un ejemplo de cuándo `StackLayout` podría ser una buena elección, piense en una aplicación que necesita para mostrar un botón y una etiqueta, con la etiqueta que se alinea a la izquierda y el botón alineado a la derecha.

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

El `FlexLayout` es similar a `StackLayout` en que muestra vistas secundarias horizontal o verticalmente:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">
        
    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

Sin embargo, si hay demasiados elementos secundarios para caber en una sola fila o columna propio, `FlexLayout` también es capaz de esas vistas de ajuste. `FlexLayout` se basa en el módulo de diseño CSS cuadro Flexible y tiene muchas de las mismas opciones integradas para colocar y alinear a sus elementos secundarios.

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

El `AbsoluteLayout` se utiliza para mostrar vistas, con el tamaño y posición que se va a especificar ya sea como valores explícitos o en relación con el tamaño del diseño. A diferencia de `StackLayout` y `Grid`, `AbsoluteLayout` permite secundarios vistas se superponen. A diferencia de `RelativeLayout`, `AbsoluteLayout` no le permite colocar los elementos fuera de la pantalla.

Para obtener un ejemplo de cuándo `AbsoluteLayout` podría ser una buena elección, piense en una aplicación que necesita para presentar las colecciones de objetos como pilas. Esto a menudo se ve cuando se presentan los álbumes de fotos o canciones. El código siguiente proporciona la apariencia de una pila, con elementos girados a fin de sugerencia en el contenido de la pila:

En XAML:

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

Tenga en cuenta los siguientes aspectos del código anterior:

- Cada `Image` se muestra en la misma posición que él (en el centro del espacio horizontal)
- El `Padding` es considerado por `AbsoluteLayout`, al contrario que `RelativeLayout`, que se pasa por alto.
- `AbsoluteLayout.LayoutFlags` Especifica cómo se interpretarán los límites de diseño. En este caso `PositionProportional`, significa que las coordenadas será una relación entre el tamaño del diseño, mientras que el tamaño se interpretará como un tamaño específico.
- `AbsoluteLayout.Layoutbounds` Especifica la posición horizontal, posición vertical, ancho y alto en ese orden.

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

El `RelativeLayout` se utiliza para mostrar vistas, con el tamaño y posición especificada como valores con respecto a los valores de la distribución o en otra vista. Valores relativos no es necesario para que coincida con él correspondiente valor en la vista relacionada. Por ejemplo, es posible establecer una vista `Width` propiedad para que sea proporcional a otra vista `X` propiedad.

RelativeLayout puede utilizarse para crear interfaces de usuario que escalar proporcionalmente en tamaños de dispositivo. El código XAML siguiente implementa un diseño con cuadros en las esquinas superiores, con un asta de la bandera con la marca en el centro de:

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

Tenga en cuenta los siguientes aspectos del código anterior:

- Posiciones y tamaños se especifican como restricciones.
- Se denomina el asta de la bandera para que la marca (verde del cuadro) se puede establecer la posición en relación con el asta de la bandera.
- Las expresiones de restricción tienen `Factor` y `Constant` propiedades, que pueden utilizarse para definir las posiciones y tamaños como múltiplos (o fracciones de segundo) de las propiedades de otros objetos, además de una constante. Las constantes pueden ser negativas.

### <a name="gridgridmd"></a>[Grid](grid.md)

El `Grid` se utiliza para mostrar elementos en filas y columnas. Tenga en cuenta que la cuadrícula no es una tabla, por lo que no tiene el concepto de celdas, filas de encabezado y pie de página o los bordes entre las filas y columnas. En general, la cuadrícula no es adecuado para mostrar datos tabulares. Para que su uso, considere la posibilidad de un [ListView](~/xamarin-forms/user-interface/listview/index.md) o [TableView](~/xamarin-forms/user-interface/tableview.md).

Para obtener un ejemplo de cuándo una `Grid` es el diseño de la derecha para usar, considere la posibilidad de una entrada numérica para una calculadora. Una entrada numérica para una calculadora puede consistir en cuatro filas y tres columnas, cada una con un botón. El código siguiente implementa este diseño:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

Tenga en cuenta los siguientes aspectos del código anterior:

- Cuadrículas y las columnas se especifican explícitamente, no inferir a partir del contenido.
- `Height` y `Width` valores pueden establecerse en estrella, lo que significa que la cuadrícula establecerá los valores para rellenar el espacio disponible.
- Posición de cada botón se especifica mediante `Grid.Row`  &  `Grid.Column` propiedades.

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

El [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estructura puede utilizarse para definir la alineación y expansión de una vista, en relación con su elemento primario.

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[Margen y relleno](margin-and-padding.md)

El [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) y [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propiedades controlan el comportamiento de diseño cuando se representa un elemento en la interfaz de usuario.

<a name="input_transparency" />

### <a name="input-transparency"></a>Entrada de transparencia

Cada elemento tiene un [ `InputTransparent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.InputTransparent/) propiedad que se utiliza para definir si el elemento recibe la entrada. Su valor predeterminado es `false`, asegurándose de que el elemento recibe la entrada.

Cuando esta propiedad está establecida en una clase de contenedor, como una clase de diseño, sus transferencias de valor a los elementos secundarios. Por consiguiente, establecer el [ `InputTransparent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.InputTransparent/) propiedad `true` en un diseño de clase se producirá en los elementos en el diseño no recibe la entrada.

### <a name="device-orientationdevice-orientationmd"></a>[Orientación del dispositivo](device-orientation.md)

Xamarin.Forms y sus diseños integrados son capaces de administrar los cambios en la orientación del dispositivo. Tenga en cuenta las orientaciones de la aplicación va a admitir, así como cómo conseguirá que utilizar el espacio proporcionado en los modos horizontal y vertical.

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[Diseño de aplicaciones de escritorio y de Tablet PC](tablet.md)

iOS, Android y plataforma Universal de Windows todas ellas admiten tamaños de pantalla más grandes en los dispositivos de tableta (así como equipos portátiles y de escritorio de Windows). Xamarin.Forms le permite optimizar la aplicación para pantallas más grandes para detectar el tipo de dispositivo y ajusta el diseño de página o usar una página totalmente diferente por completo para pantallas más grandes.

### <a name="creating-a-custom-layoutcustommd"></a>[Creación de un diseño personalizado](custom.md)

Xamarin.Forms define cuatro clases de diseño - [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/), [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/), y [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/), y cada uno de ellos organiza sus elementos secundarios de forma diferente. Sin embargo, a veces su sea necesario para organizar el contenido de la página con un diseño no proporcionada por Xamarin.Forms. En este artículo se explica cómo escribir una clase de diseño personalizado y se muestra un dependientes de la orientación `WrapLayout` clase que sus elementos secundarios se organiza horizontalmente en la página y, a continuación, se ajusta la presentación de los elementos secundarios subsiguientes a filas adicionales.

### <a name="layout-compressionlayout-compressionmd"></a>[Compresión de diseño](layout-compression.md)

Compresión de diseño quita diseños especificados del árbol visual en un intento de mejorar el rendimiento de la presentación de página. La ventaja de rendimiento que esto ofrece varía según la complejidad de una página, la versión del sistema operativo que se va a usar y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos.

## <a name="making-your-choice"></a>Para facilitar su elección

Tenga en cuenta que en la mayoría de los casos, puede utilizar más de una opción de diseño para implementar el diseño deseado. Cuando hay varias opciones válidas, tenga en cuenta qué enfoque será más fácil para su situación.
No se puede realizar la mayoría de los diseños con un solo diseño, por lo que los diseños de anidamiento como sean necesarios para crear diseños más complejos.


## <a name="related-links"></a>Vínculos relacionados

- [Directrices de interfaz humana de Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Sitio Web de diseño de Android](https://developer.android.com/design/index.html)
- [Diseño (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Ejemplo de BusinessTumble (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
