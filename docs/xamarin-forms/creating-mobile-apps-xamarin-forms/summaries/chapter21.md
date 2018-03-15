---
title: "Resumen de capítulo 21. Transformaciones"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a40b4f00fd2a0dd3f61c4882a2ef25b8eb68a3a5
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-21-transforms"></a>Resumen de capítulo 21. Transformaciones

Aparece una vista de Xamarin.Forms en la pantalla en una ubicación y tamaño determinado por su elemento primario, que suele ser un `Layout` o `Layout<View>` derivado. El *transformar* es una característica de Xamarin.Forms que puede modificar esa ubicación, tamaño u orientación incluso.

Xamarin.Forms admite tres tipos básicos de transformaciones:

- *Traducción* &mdash; un elemento de desplazamiento horizontal o verticalmente
- *Escala* &mdash; cambiar el tamaño de un elemento
- *Rotación* &mdash; activar un elemento alrededor de un punto o un eje

De Xamarin.Forms, la escala es isótropo; afecta a la anchura y altura uniformemente. Se admite el giro tanto en la superficie bidimensional de la pantalla y en el espacio 3D. No hay ninguna transformación sesga (o total) y ninguna transformación de matriz generalizado.

Transformaciones son compatibles con ocho propiedades de tipo `double` definido por el `VisualElement` clase:

- [`TranslationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)
- [`TranslationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)
- [`Scale`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)
- [`Rotation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)
- [`RotationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)
- [`RotationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)
- [`AnchorX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)
- [`AnchorY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)

Todas estas propiedades están respaldadas por propiedades enlazables. Se pueden ser destino de enlace de datos y un estilo. [**Capítulo 22. Animación** ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) muestra cómo se pueden animar estas propiedades, pero algunos ejemplos de este capítulo muestran cómo puede animar ellos usando el Xamarin.Forms [temporizador](~/xamarin-forms/platform/device.md#Device_StartTimer).

Transformar propiedades afectan a cómo sólo se representa el elemento y realice *no* afectan a cómo se percibe el elemento de diseño.

## <a name="the-translation-transform"></a>La transformación de traducción

Los valores distintos de cero de la [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) y [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propiedades de un elemento de desplazamiento horizontal o verticalmente.

El [ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) programa le permite experimentar con estas propiedades con dos `Slider` elementos que controlan la `TranslationX` y `TranslationY` propiedades de un `Frame`. La transformación también afecta a todos los elementos secundarios de ese `Frame`.

### <a name="text-effects"></a>Efectos de texto

Un uso habitual de las propiedades de la traducción es ligeramente desplazado la representación de texto. Esto se muestra en el [ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) ejemplo:

[![Captura de pantalla triple de desplazamientos de texto](images/ch21fg03-small.png "texto desplazamientos")](images/ch21fg03-large.png#lightbox "desplazamientos de texto")

Otro efecto es representar varias copias de una `Label` similares a un bloque 3D, tal como se muestra en el [ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) ejemplo.

### <a name="jumps-and-animations"></a>Saltos y las animaciones

El [ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) ejemplo usa traducción para mover un `Button` cada vez que se derivan, pero el propósito principal es demostrar que el `Button` recibe la entrada de usuario en la ubicación donde se representa el botón.

El [ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) ejemplo es similar pero utiliza un temporizador para animar la `Button` desde un punto a otro.

## <a name="the-scale-transform"></a>La transformación de escala

El [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) transformación puede aumentar o disminuir el tamaño del elemento presentado. El valor predeterminado es 1. Un valor de 0 hace que el elemento sea invisible. Los valores negativos hacen que el elemento que se va a aparecer como giradas 180 grados. El `Scale` no afecta la propiedad el `Width` o `Height` propiedades del elemento. Estos valores son iguales.

Puede experimentar con la `Scale` propiedad mediante la [ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) ejemplo.

El [ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) ejemplo demuestra la diferencia entre la animación de la `Scale` propiedad de un `Button` y animar la `FontSize` propiedad. El `FontSize` afecta a la propiedad cómo `Button` se percibe en diseño; el `Scale` propiedad no es así.

El [ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) ejemplo calcula un `Scale` propiedad que se aplica a un `Label` elemento para que sea tan grande como sea posible al tiempo que adaptar aún dentro de la página.

### <a name="anchoring-the-scale"></a>Delimitación de la escala

Los elementos de escala en los tres ejemplos anteriores han todos aumenta o disminuye de tamaño en relación al centro del elemento. En otras palabras, el elemento aumenta o disminuye de tamaño de la misma en todas las direcciones. Solo el punto en el centro del elemento permanece en la misma ubicación durante el ajuste de escala.

Puede cambiar el centro de la escala estableciendo la [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) y [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propiedades. Estas propiedades son relativas al propio elemento. Para `AnchorX`, un valor de 0 hace referencia a la izquierda del elemento y 1 hace referencia a la derecha. De forma similar para `AnchorY`, 0 es la parte superior y 1 es la parte inferior. Ambas propiedades tienen valores predeterminados de 0,5, que es el centro.

El [ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) ejemplo le permite experimentar con la `AnchorX` y `AnchorY` propiedades, así como la `Scale` propiedad.

En iOS, utilizando los valores no predeterminados de `AnchorX` y `AnchorY` propiedades es generalmente incompatible con los cambios de orientación de teléfono.

## <a name="the-rotation-transform"></a>La transformación de giro

El [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad se especifica en grados e indica un giro hacia la derecha alrededor de un punto del elemento definido por `AnchorX` y `AnchorY`. El [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) le permite experimentar con estas tres propiedades.

### <a name="rotated-text-effects"></a>Efectos de texto girado

El [ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) muestra las matemáticas necesarias para dibujar un círculo con 64 minúsculo girada `BoxView` elementos.

El [ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) ejemplo muestra varias `Label` girarán elementos con la misma cadena de texto para que aparezca como radios.

El [ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) ejemplo muestra una cadena de texto que aparece para incluir en un círculo.

### <a name="an-analog-clock"></a>Un reloj analógico

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene un [ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) clase que calcula los ángulos de las manecillas de un reloj. Para evitar las dependencias de plataforma en el modelo de vista, la clase utiliza `Task.Delay` en lugar de un temporizador para buscar un nuevo `DateTime` valor.

También se incluyen en **Xamarin.FormsBook.Toolkit** es un [ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) clase que implementa `IValueConverter` y sirve para redondear un ángulo de segundo al segundo más cercano.

El [ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) utiliza tres rotación `BoxView` elementos que se va a dibujar un reloj analógico.

El [ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) utiliza `BoxView` para gráficos más amplias, incluidas graduación marca alrededor de la cara del reloj y entrega decir girar una distancia poco desde sus extremos:

[![Captura de pantalla triple de reloj BoxView](images/ch21fg17-small.png "reloj analógico")](images/ch21fg17-large.png#lightbox "reloj analógico")

Además un [ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) clase **Xamarin.FormsBook.Toolkit** hace que la manecilla de los segundos en aparecer extraer un poco antes de Saltar adelante y, a continuación, vuelva a mover a su posición correcta.

### <a name="vertical-sliders"></a>¿Controles deslizantes verticales?

El [ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) ejemplo muestra que `Slider` elementos se pueden girar 90 grados y siguen funcionando. Sin embargo, resulta difícil colocar estos girar `Slider` elementos porque en el diseño todavía aparecen como horizontal.

## <a name="3d-ish-rotations"></a>Rotaciones de ish 3D

El [ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/) propiedad parece girar un elemento alrededor de un eje x 3D para que la parte superior e inferior del elemento parecen mover hacia o que están lejos del Visor. De forma similar, el [ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/) parece girar un elemento alrededor del eje y a los lados izquierdo y derecho del elemento que parezca que mover hacia o que están lejos del Visor.

El `AnchorX` afecta a la propiedad `RotationY` pero no `RotationX`. El `AnchorY` afecta a la propiedad `RotationX` pero no `RotationY`. Puede experimentar con la [ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) ejemplo para explorar las interacciones de estas propiedades.

El sistema de coordenadas 3D implicado en Xamarin.Forms es zurdo. Si eliges el índice de la mano izquierda en la dirección de X creciente coordina (a la derecha) y coordina el dedo intermedio en la dirección de Y creciente (abajo), a continuación, los puntos de control en la dirección de aumentar las coordenadas Z (fuera de la pantalla).

Además, para cualquiera de los tres ejes, si eliges el pulgar a izquierda en la dirección de aumentar los valores, a continuación, la curva de los dedos indica la dirección de rotación de los ángulos de rotación positivos.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 21 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Ejemplos de capítulo 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
