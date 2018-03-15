---
title: "Resumen de capítulo 14. Diseño absoluto"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a3980c63c31f4fdf0297fdc9b05da3590f0cac54
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Resumen de capítulo 14. Diseño absoluto

Al igual que `StackLayout`, [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) deriva `Layout<View>` y hereda un `Children` propiedad. `AbsoluteLayout` implementa un sistema de diseño que exige que el programador especificar las posiciones de sus elementos secundarios y, opcionalmente, su tamaño. La posición se especifica mediante la esquina superior izquierda del elemento secundario con respecto a la esquina superior izquierda de la `AbsoluteLayout` en unidades independientes del dispositivo. `AbsoluteLayout` También implementa una característica de ajuste de tamaño y posición proporcionales.

`AbsoluteLayout` se deben considerar como un sistema de diseño especial para usarse solo cuando el programador puede imponer un tamaño de los elementos secundarios (por ejemplo, `BoxView` elementos) o cuando el tamaño del elemento no afecta a la posición de otros elementos secundarios. El `HorizontalOptions` y `VerticalOptions` propiedades no tienen ningún efecto en los elementos secundarios de un `AbsoluteLayout`.

En este capítulo también presenta la característica importante de *enlazables propiedades adjuntas* que permiten propiedades definidas en una clase (en este caso `AbsoluteLayout`) para adjuntarlo a otra clase (un elemento secundario de la `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout en el código

Puede agregar un elemento secundario a la `Children` colección de un `AbsoluteLayout` utilizando el estándar [ `Add` ](https://developer.xamarin.com/api/member/System.Collections.Generic.ICollection%3CT%3E.Add/p/T/) método, pero `AbsoluteLayout` también proporciona un extendido [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Rectangle/Xamarin.Forms.AbsoluteLayoutFlags/) método que le permite especificar un [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/). Otro [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Point/) método sólo requiere un [ `Point` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Point/), en cuyo caso el elemento secundario no está restringido y ajusta su tamaño.

Puede crear un `Rectangle` valor con un [constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/System.Double/System.Double/System.Double/System.Double/) que requiere cuatro valores &mdash; los dos primeros, que indica la posición de la esquina superior izquierda del elemento secundario con respecto a su elemento primario y el segundo (dos) que indica el tamaño del elemento secundario. O bien puede usar un [constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/Xamarin.Forms.Point/Xamarin.Forms.Size/) que requiere un `Point` y un [ `Size` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/) valor.

Estos `Add` métodos se muestran en [ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), las posiciones `BoxView` elementos mediante `Rectangle` valores y un `Label` elemento utilizando solo un `Point` valor.

El [ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) ejemplo utiliza 32 `BoxView` elementos que se va a crear el patrón de tablero de ajedrez. El programa le ofrece la `BoxView` tamaño de elementos un codificado de forma rígida del cuadrado de 35 unidades. El `AbsoluteLayout` tiene su `HorizontalOptions` y `VerticalOptions` establecido en `LayoutOptions.Center`, lo que hace que la `AbsoluteLayout` tenga un tamaño total del cuadrado 280 unidades.

## <a name="attached-bindable-properties"></a>Puede enlazables propiedades adjuntas

También es posible establecer la posición y, opcionalmente, el tamaño de un elemento secundario de un `AbsoluteLayout` después de que se ha agregado a la `Children` colección utilizando el método estático [ `AbsoluteLayout.SetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutBounds/p/Xamarin.Forms.BindableObject/Xamarin.Forms.Rectangle/). El primer argumento es el elemento secundario; el segundo es un `Rectangle` objeto. Puede especificar que el elemento secundario ajusta su tamaño horizontalmente o verticalmente estableciendo valores de ancho y alto la [ `AbsoluteLayout.AutoSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/) constante.

El [ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) ejemplo coloca la `AbsoluteLayout` en un `ContentView` con un `SizeChanged` controlador para llamar a `AbsoluteLayout.SetLayoutBounds` en todos los elementos secundarios para que sean tan grande como sea posible.  

La propiedad adjunta enlazable que `AbsoluteLayout` define es el campo estático de solo lectura de tipo `BindableProperty` denominado [ `AbsoluteLayout.LayoutBoundsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/). El método estático `AbsoluteLayout.SetLayoutBounds` método se implementa mediante una llamada a `SetValue` en el elemento secundario con el `AbsoluteLayout.LayoutBoundsProperty`. El elemento secundario contiene un diccionario en el que se almacenan la propiedad adjunta enlazable y su valor. Durante el diseño, la `AbsoluteLayout` puede obtener ese valor mediante una llamada a [ `AbsoluteLayout.GetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutBounds/p/Xamarin.Forms.BindableObject/), que se implementa con un `GetValue` llamar.

## <a name="proportional-sizing-and-positioning"></a>Tamaño proporcional y la posición

`AbsoluteLayout` implementa un cambio de tamaño proporcional y la posición de la característica. La clase define una segunda propiedad enlazable adjuntada, [ `LayoutFlagsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/), con los métodos estáticos relacionados [ `AbsoluteLayout.SetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutFlags/p/Xamarin.Forms.BindableObject/Xamarin.Forms.AbsoluteLayoutFlags/) y [ `AbsoluteLayout.GetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutFlags/p/Xamarin.Forms.BindableObject/).

El argumento `AbsoluteLayout.SetLayoutFlags` y el valor devuelto de `AbsoluteLayout.GetLayoutFlags` es un valor de tipo [ `AbsoluteLayoutFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/), una enumeración con los miembros siguientes:

- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.None/) (igual a 0)
- [`XProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.XProportional/) (1)
- [`YProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.YProportional/) (2)
- [`PositionProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional/) (3)
- [`WidthProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional/) (4)
- [`HeightProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional/) (8)
- [`SizeProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional/) (12)
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.All/) (\xFFFFFFFF)

Puede combinar con el C# operador bit a bit OR.

Con este conjunto de marcas, algunas propiedades de la `Rectangle` estructura de límites de diseño utilizada para la posición y el tamaño del elemento secundario se interpretan proporcionalmente.

Cuando el `WidthProportional` marca está establecida, un `Width` valor 1 significa que el elemento secundario es el mismo ancho que la `AbsoluteLayout`. Se usa un enfoque similar para el alto.

La posición proporcional, el tamaño tiene en cuenta. Cuando el `XProportional` marca está establecida, el `X` propiedad de la `Rectangle` límites de diseño es proporcional. Un valor de 0 significa que el elemento secundario del borde izquierdo se coloca en el borde izquierdo de la `AbsoluteLayout`, sino una posición de 1 significa que el borde derecho del elemento secundario se coloca en el borde derecho de la `AbsoluteLayout`, no más allá del borde derecho de la `AbsoluteLayout` como podría expec t. Un `X` propiedad de 0,5 centra el elemento secundario horizontalmente en la `AbsoluteLayout`.

El [ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) ejemplo muestra el uso de tamaño proporcional y la posición.

## <a name="working-with-proportional-coordinates"></a>Trabajar con coordenadas proporcionales

A veces, resulta más fácil pensar en posición proporcional de forma diferente a cómo se implementa en el `AbsoluteLayout`. Es preferible trabajar con coordenadas proporcionales donde un `X` propiedad 1 coloca el borde izquierdo del elemento secundario (en lugar de la parte derecha) en el borde derecho de la `AbsoluteLayout`.

Este esquema de colocación alternativo puede llamarse "coordinates secundarios fracciones." Puede convertir de coordenadas secundarias fracciones en los límites de diseño necesarios para `AbsoluteLayout` con las fórmulas siguientes:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

El [ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) ejemplo se muestra cómo hacerlo.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout y XAML

Puede usar un `AbsoluteLayout` en XAML y establecer las propiedades de enlazables conectadas en los elementos secundarios de un `AbsoluteLayout` con valores de atributo de `AbsoluteLayout.LayoutBounds` y `AbsoluteLayout.LayoutFlags`. Esto se muestra en el [ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) y [ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) ejemplos. El último programa contiene 32 `BoxView` elementos pero usa un modo implícito `Style` que incluye el `AbsoluteLayout.LayoutFlags` propiedad para mantener el marcado reduce al mínimo.

Un atributo en XAML que consta de un nombre de clase, un punto y un nombre de propiedad no es *siempre* una propiedad enlazable adjunta.

## <a name="overlays"></a>Superposiciones

Puede usar `AbsoluteLayout` para construir un *superposición*, que cubre la página con otros controles, quizás para impedir que el usuario interactúa con los controles normales en la página. 

El [ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) ejemplo muestra esta técnica y también muestra el [ `ProgressBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/), que muestra la extensión a la que se ha completado un programa un tarea.

## <a name="some-fun"></a>Un buen rato

El [ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) ejemplo muestra la hora actual con una pantalla simulados matricial de 5 x 7. Cada punto es una `BoxView` (no hay 228 de ellos) tamaño y se colocan en la `AbsoluteLayout`.

[![Captura de pantalla triple de reloj de la matriz de puntos](images/ch14fg08-small.png "matricial reloj")](images/ch14fg08-large.png#lightbox "reloj matricial")

El [ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) programa anima dos `Label` objetos rebote horizontalmente y verticalmente en la pantalla.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 14 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Ejemplos de capítulo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
