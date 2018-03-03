---
title: "Márgenes y relleno"
description: "Propiedades de relleno y el margen de controlan el comportamiento de diseño cuando se representa un elemento en la interfaz de usuario. En este artículo se muestra la diferencia entre las dos propiedades y cómo establecerlas."
ms.topic: article
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 7bab512ef11f8e0f553a00f0240d82f860fe2676
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="margin-and-padding"></a>Márgenes y relleno

_Propiedades de relleno y el margen de controlan el comportamiento de diseño cuando se representa un elemento en la interfaz de usuario. En este artículo se muestra la diferencia entre las dos propiedades y cómo establecerlas._

## <a name="overview"></a>Información general

Márgenes y relleno son conceptos de diseño relacionadas:

- El [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propiedad representa la distancia entre un elemento y sus elementos adyacentes y se utiliza para controlar la posición de representación del elemento y la posición de representación de sus vecinos. `Margin` se pueden especificar valores en [diseño](~/xamarin-forms/user-interface/controls/layouts.md) y [vista](~/xamarin-forms/user-interface/controls/views.md) clases.
- El [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propiedad representa la distancia entre un elemento y sus elementos secundarios y se utiliza para separar el control de su propio contenido. `Padding` se pueden especificar valores en [diseño](~/xamarin-forms/user-interface/controls/layouts.md) clases.

El siguiente diagrama muestra los dos conceptos:

[![](margin-and-padding-images/margins-and-padding-sml.png "Márgenes y relleno conceptos")](margin-and-padding-images/margins-and-padding.png "conceptos de relleno y márgenes")

Tenga en cuenta que [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) valores son aditivos. Por lo tanto, si dos elementos adyacentes especifican un margen de 20 píxeles, la distancia entre los elementos serán 40 píxeles. Además, márgenes y relleno son aditivos cuando ambos se aplican, en que la distancia entre un elemento y cualquier tipo de contenido será el margen más el relleno.

## <a name="specifying-a-thickness"></a>Especifica un grosor

El [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) y [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propiedades son de tipo [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/). Existen tres posibilidades al crear un `Thickness` estructura:

- Crear un [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) estructura definida por un único valor uniforme. El valor solo se aplica a la izquierda, superior, derecha y lados de la parte inferior del elemento.
- Crear un [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) estructura definida por los valores horizontales y verticales. El valor horizontal simétricamente se aplica a los lados izquierdo y derecho del elemento, con el valor vertical simétricamente que se aplica a los lados superior e inferior del elemento.
- Crear un [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) estructura definida por cuatro valores distintos que se aplican a la izquierda, superior, derecha y lados de la parte inferior del elemento.

El ejemplo de código XAML siguiente muestra todas las tres posibilidades:

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> **Tenga en cuenta**: `Thickness` valores pueden ser negativos, lo que normalmente recorta o el contenido de este.

## <a name="summary"></a>Resumen

Este artículo muestra la diferencia entre el [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) y [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propiedades y cómo establecerlas. Las propiedades controlan el comportamiento de diseño cuando se representa un elemento en la interfaz de usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Margin](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)
- [Padding](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)
- [Grosor](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)
