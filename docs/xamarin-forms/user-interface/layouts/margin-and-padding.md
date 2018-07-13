---
title: Márgenes y relleno
description: Los márgenes y relleno de las propiedades de controlan el comportamiento de diseño cuando se procesa un elemento en la interfaz de usuario. En este artículo se muestra la diferencia entre las dos propiedades y cómo establecerlas.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 595e673c59d23a45cbaf923a0d58faff2000c296
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996621"
---
# <a name="margin-and-padding"></a>Márgenes y relleno

_Los márgenes y relleno de las propiedades de controlan el comportamiento de diseño cuando se procesa un elemento en la interfaz de usuario. En este artículo se muestra la diferencia entre las dos propiedades y cómo establecerlas._

## <a name="overview"></a>Información general

Márgenes y relleno son conceptos de diseño relacionadas:

- El [ `Margin` ](xref:Xamarin.Forms.View.Margin) propiedad representa la distancia entre un elemento y sus elementos adyacentes y se usa para controlar la posición del elemento representación y la posición de representación de sus vecinos. `Margin` se pueden especificar valores en [diseño](~/xamarin-forms/user-interface/controls/layouts.md) y [vista](~/xamarin-forms/user-interface/controls/views.md) clases.
- El [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propiedad representa la distancia entre un elemento y sus elementos secundarios y se usa para separar el control de su propio contenido. `Padding` se pueden especificar valores en [diseño](~/xamarin-forms/user-interface/controls/layouts.md) clases.

El siguiente diagrama ilustra los dos conceptos:

[![](margin-and-padding-images/margins-and-padding-sml.png "Conceptos de relleno y márgenes")](margin-and-padding-images/margins-and-padding.png#lightbox "conceptos de relleno y márgenes")

Tenga en cuenta que [ `Margin` ](xref:Xamarin.Forms.View.Margin) los valores son aditivos. Por lo tanto, si dos elementos adyacentes especifican un margen de 20 píxeles, la distancia entre los elementos será 40 píxeles. Además, márgenes y relleno son aditivos cuando ambos se aplican, en que la distancia entre un elemento y cualquier contenido será el margen más relleno.

## <a name="specifying-a-thickness"></a>Especifica un grosor

El [ `Margin` ](xref:Xamarin.Forms.View.Margin) y [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propiedades son de tipo [ `Thickness` ](xref:Xamarin.Forms.Thickness). Existen tres posibilidades al crear un `Thickness` estructura:

- Crear un [ `Thickness` ](xref:Xamarin.Forms.Thickness) estructura definida por un único valor uniforme. El valor solo se aplica a la izquierda, superior, derecha y lados de la parte inferior del elemento.
- Crear un [ `Thickness` ](xref:Xamarin.Forms.Thickness) estructura definida por los valores horizontales y verticales. El valor horizontal simétricamente se aplica a los lados izquierdo y derecho del elemento, con el valor vertical simétricamente que se aplica a los lados superior e inferior del elemento.
- Crear un [ `Thickness` ](xref:Xamarin.Forms.Thickness) estructura definida por cuatro valores distintos que se aplican a la izquierda, superior, derecha y lados de la parte inferior del elemento.

El ejemplo de código XAML siguiente muestra las tres posibilidades:

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
> `Thickness` los valores pueden ser negativos, lo que normalmente recorta o el contenido de este.

## <a name="summary"></a>Resumen

En este artículo se muestra la diferencia entre el [ `Margin` ](xref:Xamarin.Forms.View.Margin) y [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propiedades y cómo establecerlas. Las propiedades controlan el comportamiento de diseño cuando se procesa un elemento en la interfaz de usuario.


## <a name="related-links"></a>Vínculos relacionados

- [Margen](xref:Xamarin.Forms.View.Margin)
- [Relleno](xref:Xamarin.Forms.Layout.Padding)
- [Grosor](xref:Xamarin.Forms.Thickness)
