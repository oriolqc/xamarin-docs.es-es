---
title: LayoutOptions
description: "Cada vista Xamarin.Forms tiene propiedades HorizontalOptions y VerticalOptions, del tipo LayoutOptions. Este artículo explica el efecto que tiene cada valor LayoutOptions en la alineación y la expansión de una vista."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: 978985c4e9803fad33760e4b40ab73d57f3ec420
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="layoutoptions"></a>LayoutOptions

_Cada vista Xamarin.Forms tiene propiedades HorizontalOptions y VerticalOptions, del tipo LayoutOptions. Este artículo explica el efecto que tiene cada valor LayoutOptions en la alineación y la expansión de una vista._

## <a name="overview"></a>Información general

El [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estructura encapsula dos de las preferencias de diseño:

- **Alineación** : la vista preferida de alineación, que determina su posición y el tamaño del diseño de su primario.
- **Expansión** : se usa únicamente por un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)e indica si la vista debe utilizar espacio adicional, si está disponible.

Estas preferencias de diseño pueden aplicarse a un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/), relativa a su elemento primario, estableciendo el [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) o [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedad de la `View` a uno de los campos públicos de la [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estructura. Los campos públicos son los siguientes:

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

El `Start`, `Center`, `End`, y `Fill` campos se utilizan para definir la alineación de la vista del diseño del elemento primario:

- Para la alineación horizontal, [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/) posiciones el [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) en el lado izquierdo del diseño del elemento primario y para la alineación vertical, coloca el `View` en la parte superior de la diseño de elemento primario.
- Para la alineación horizontal y vertical, [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/) se centra horizontalmente o verticalmente la [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/).
- Para la alineación horizontal, [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/) posiciones el [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) en el lado derecho del diseño del elemento primario y para la alineación vertical, coloca el `View` en la parte inferior del diseño del elemento primario.
- Para la alineación horizontal, [ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) garantiza que la [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) rellena el ancho del diseño del elemento primario y para la alineación vertical, se garantiza que la `View` rellena el alto del diseño del elemento primario.

El `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, y `FillAndExpand` valores se utilizan para definir la preferencia de alineación, y si la vista ocupan más espacio si está disponible dentro del elemento primario [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).

> [!NOTE]
> El valor predeterminado de una vista [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) y [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades es [ `LayoutOptions.Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/).

<a name="alignment" />

## <a name="alignment"></a>Alineación

Alineación controla cómo se sitúa una vista en su diseño de elemento primario cuando el diseño del elemento primario contiene espacio no utilizado (es decir, el diseño del elemento primario es mayor que el tamaño combinado de todos sus nodos secundarios).

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) solo respeta el `Start`, `Center`, `End`, y `Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) campos en las vistas secundarias que se encuentran en la dirección opuesta para el `StackLayout` orientación. Por lo tanto, se ve secundarios dentro de una orientación vertical `StackLayout` puede establecer sus [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propiedades a uno de los `Start`, `Center`, `End`, o `Fill` campos. De forma similar, vistas secundarias dentro de una orientación horizontal `StackLayout` puede establecer sus [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades a uno de los `Start`, `Center`, `End`, o `Fill` campos.

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) no respeta la `Start`, `Center`, `End`, y `Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) campos en las vistas secundarias que se encuentran en la misma dirección que el `StackLayout` orientación. Por lo tanto, una orientación vertical `StackLayout` pasa por alto el `Start`, `Center`, `End`, o `Fill` campos si están establecidos en el [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades de vistas secundarias. De forma similar, una orientación horizontal `StackLayout` pasa por alto el `Start`, `Center`, `End`, o `Fill` campos si están establecidos en el [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propiedades de vistas secundarias.

> [!NOTE]
> [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) Generalmente invalidaciones cambiar el tamaño de las solicitudes que se especifica utilizando el [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) y [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) propiedades.

El ejemplo de código XAML siguiente muestra una orientación vertical [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) donde cada elemento secundario [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) establece su [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propiedad en uno de los campos de cuatro alineación de la [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estructura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

El código equivalente en C# se muestra a continuación:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

El código produce el diseño que se muestra en las capturas de pantalla siguiente:

[![](layout-options-images/alignment.png "Opciones de diseño de alineación")](layout-options-images/alignment-large.png "opciones de diseño de alineación")

<a name="expansion" />

## <a name="expansion"></a>Expansión

Expansión controla si una vista ocupan más espacio, si está disponible, dentro de un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Si el `StackLayout` contiene espacio no utilizado (es decir, el `StackLayout` es mayor que el tamaño combinado de todos sus elementos secundarios), el espacio no utilizado se comparte de forma equitativa todas las vistas secundarias que solicitan expansión estableciendo sus [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)o [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades para un [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) campo que utiliza el `AndExpand` sufijo. Tenga en cuenta que, cuando todo el espacio en el `StackLayout` es utilizado, las opciones de expansión no tienen ningún efecto.

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) sólo se puede expandir vistas secundarias en la dirección de su orientación. Por lo tanto, una orientación vertical `StackLayout` puede expandir vistas secundarias que establecen sus [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades a uno de los `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, o `FillAndExpand` campos, si la `StackLayout` contiene espacio no utilizado. De forma similar, una orientación horizontal `StackLayout` puede expandir vistas secundarias que establecen sus [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propiedades a uno de los `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, o `FillAndExpand` campos, si la `StackLayout` contiene espacio no utilizado.

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) no se puede expandir vistas secundarias en la dirección opuesta a su orientación. Por lo tanto, en orientación vertical `StackLayout`, y establece la [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propiedad en una vista secundaria a [ `StartAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/) tiene el mismo efecto que establecer la propiedad en [ `Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/).

> [!NOTE]
> Tenga en cuenta que habilitar la expansión no cambia el tamaño de una vista a menos que use [ `LayoutOptions.FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/).

El ejemplo de código XAML siguiente muestra una orientación vertical [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) donde cada elemento secundario [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) establece su [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedad en uno de los campos de cuatro expansión de la [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estructura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

El código equivalente en C# se muestra a continuación:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

El código produce el diseño que se muestra en las capturas de pantalla siguiente:

[![](layout-options-images/expansion.png "Opciones de diseño de expansión")](layout-options-images/expansion-large.png "opciones de diseño de expansión")

Cada [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) ocupe la misma cantidad de espacio en el [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Sin embargo, solo la última `Label`, que establece su [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedad [ `FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/) con un tamaño diferente. Además, cada `Label` separados por una pequeña de color rojo [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/), lo que permite el espacio en el `Label` ocupa para verse fácilmente.

## <a name="summary"></a>Resumen

Este artículo explica el efecto que cada [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) tiene el valor de la estructura en la alineación y la expansión de una vista, en relación con su elemento primario. El `Start`, `Center`, `End`, y `Fill` campos se utilizan para definir la alineación de la vista del diseño del elemento primario y el `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, y `FillAndExpand` campos se utilizan para definir la preferencia de alineación y para determinar si la vista ocupan más espacio, si está disponible, dentro de un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).



## <a name="related-links"></a>Vínculos relacionados

- [LayoutOptions (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)
- [LayoutOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)
