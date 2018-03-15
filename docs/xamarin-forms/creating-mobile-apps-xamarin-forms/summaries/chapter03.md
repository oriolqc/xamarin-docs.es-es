---
title: "Resumen del capítulo 3. Profundiza en texto"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a3ef515feabfc142f30e7e00a8fed710e733f4dc
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Resumen del capítulo 3. Profundiza en texto

En este capítulo se explora la [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) ver de forma más minuciosa, incluidos los colores, fuentes y el formato.

## <a name="wrapping-paragraphs"></a>Puntos de ajuste

Cuando el [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedad de `Label` contiene texto largo, `Label` automáticamente ajusta en varias líneas como se muestra en el [ **Baskervilles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) ejemplo. Puede incrustar códigos Unicode, como '\u2014' para el guión, o los caracteres de C# como '\r' para interrumpir a una nueva línea.

Cuando el [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) y [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propiedades de un `Label` se establecen en `LayoutOptions.Fill`, el tamaño total de la `Label` se rige por el espacio que su contenedor hace que estén disponibles. El `Label` se dice *limitada*. El tamaño de la `Label` es el tamaño de su contenedor.

Cuando el `HorizontalOptions` y `VerticalOptions` propiedades se establecen en valores distintos de `LayoutOptions.Fill`, el tamaño de la `Label` se rige por el espacio necesario para representar el texto, hasta alcanzar el tamaño que su contenedor pone a disposición de los `Label`. El `Label` se dice *sin restricciones* y determina su propio tamaño.

(Nota: los términos *limitada* y *sin restricciones* puede ser contraproducente, dado que una vista sin restricciones es generalmente más pequeña que una vista restringida. Además, estos términos no se utilice de forma consistente en los primeros capítulos del libro.)

Una vista como una `Label` puede restringida a una dimensión y sin restricciones en la otra. Un `Label` sólo ajustar texto en varias líneas si se restringe horizontalmente.

Si un `Label` está restringido, puede ocupar mucho más espacio que el requerido para el texto. El texto se puede colocar dentro del área total de la `Label`. Establecer el [ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/) propiedad a un miembro de la [ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/) enumeración ([`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/), [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/), o [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)) para controlar la alineación de todas las líneas del párrafo. El valor predeterminado es `Start` y el texto alinea a la izquierda.

Establecer el [ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/) propiedad a un miembro de la `TextAlignment` enumeración para colocar el texto en la parte superior, el centro o la parte inferior del área ocupado por el `Label`.

Establecer el [ `LineBreakMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.LineBreakMode/) propiedad a un miembro de la [ `LineBreakMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LineBreakMode/) enumeración ([`WordWrap`](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.WordWrap/), [ `CharacterWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.CharacterWrap/), [ `NoWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.NoWrap/), [ `HeadTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.HeadTruncation/), [ `MiddleTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.MiddleTruncation/), o [ `TailTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.TailTruncation/)) a control de cómo el varias líneas en un salto de párrafo o se truncan.

## <a name="text-and-background-colors"></a>Colores de fondo y texto

Establecer el [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) y [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) propiedades de `Label` a [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) valores para controlar el color del texto y el fondo.

El `BackgroundColor` se aplica al fondo de todo el área ocupado por el `Label`. En función de la `HorizontalOptions` y `VerticalOptions` propiedades, que tamaño puede ser mucho mayor que el área requerida para mostrar el texto. Puede usar colores para experimentar con distintos valores de `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`, y `VerticalTextAlignment` para ver cómo afectan al tamaño y la posición de la `Label`y el tamaño y la posición del texto dentro de la `Label`.

## <a name="the-color-structure"></a>La estructura de Color

El [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) estructura le permite especificar colores como valores de rojo, verde y azul (RGB) o valores de matiz-saturación-luminosidad (HSL) o con un nombre de color. Un canal alfa también está disponible para indicar la transparencia.

Use un `Color` constructor para especificar:

- un [tonalidad de color gris](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/)
- un [valor RGB](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/)
- un [valor RGB con transparencia](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/)

Los argumentos son `double` valores comprendidos entre 0 y 1.

También puede utilizar varios métodos estáticos para crear `Color` valores:

- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/) para `double` valores RGB de 0 a 1
- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Int32/System.Int32/System.Int32/) para los valores RGB de enteros entre 0 y 255.
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Double/System.Double/System.Double/System.Double/) para `double` los valores RGB con transparencia
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) para los valores RGB de enteros con transparencia
- [`Color.FromHsla`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) para `double` HSL (valores) con transparencia
- [`Color.FromUint`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromUint/p/System.UInt32/) para una `uint` valor se calcula como (B + 256 * (G + 256 * (R + 256 * A)))
- [`Color.FromHex`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) para un `string` formato de dígitos hexadecimales en el formato "#AARRGGBB" o "#RRGGBB" o "#ARGB" o "#RGB", donde cada letra corresponde a un dígito hexadecimal para el canal alfa, rojo, verdes y azules canales. Este método es el principal utiliza para las conversiones de color XAML, como se describe en [capítulo 7, XAML frente a código](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Una vez creado, un `Color` valor es inmutable. Las características del color pueden obtenerse de las siguientes propiedades:

- [`R`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/)
- [`G`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/)
- [`B`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/)
- [`A`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/)
- [`Hue`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Hue/)
- [`Saturation`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Saturation/)
- [`Luminosity`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Luminosity/)

Todas son `double` valores comprendidos entre 0 y 1.

`Color` También define 240 campos estáticos públicos de sólo lectura para los colores comunes. En el momento en que el libro se ha escrito, sólo 17 colores comunes no estaban disponibles.

Otro campo de solo lectura por estático público define un color con todos los canales de color que se establecen en cero:

- [`Color.Transparent`](https://developer.xamarin.com/api/field/Xamarin.Forms.Color.Transparent/)

Varios métodos de instancia permiten modificar un color existente para crear un nuevo color:

- [`AddLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.AddLuminosity/p/System.Double/)
- [`MultiplyAlpha`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.MultiplyAlpha/p/System.Double/)
- [`WithHue`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithHue/p/System.Double/)
- [`WithLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithLuminosity/p/System.Double/)
- [`WithSaturation`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithSaturation/p/System.Double/)

Por último, dos propiedades de solo lectura estáticos definen valor de color especial:

- [`Color.Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/), todos los canales se establece en &ndash;1
- [`Color.Accent`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Accent/)

`Color.Default` está diseñado para aplicar la combinación de colores de la plataforma y, por consiguiente, tiene un significado diferente en contextos diferentes en distintas plataformas. De forma predeterminada son las combinaciones de colores de la plataforma:

- iOS: oscuro texto sobre un fondo claro
- Android: Claro a oscuro texto sobre un fondo claro o texto sobre un fondo oscuro (en el libro) (para Material de diseño a través de AppCompat en el **maestro** rama del repositorio de código de ejemplo)
- UWP: Oscuro texto sobre un fondo claro
- Windows 8.1: Texto claro sobre un fondo oscuro
- Windows Phone 8.1: Texto claro sobre un fondo oscuro

El `Color.Accent` valor da como resultado un color específico de la plataforma (y a veces seleccionable por el usuario) que está visible en un fondo oscuro o claro.

## <a name="changing-the-application-color-scheme"></a>Cambiar la combinación de colores de la aplicación

Las diversas plataformas de tengan una combinación de colores predeterminada tal como se muestra en la lista anterior.

Cuando el destino es Android, es posible cambiar a un esquema de luz de oscuro especificando un tema claro en el archivo Android.Manifest.xml, o [agregar AppCompat y Material de diseño](~/xamarin-forms/platform/android/appcompat.md).

Para las plataformas de Windows, el tema de color suele estar seleccionado por el usuario, pero puede agregar un `RequestedTheme` atributo establecido en `Light` o `Dark` en el archivo App.xaml de la plataforma. De forma predeterminada, contiene el archivo App.xaml en el proyecto de UWP un `RequestedTheme` atributo establecido en `Light`.

## <a name="font-sizes-and-attributes"></a>Atributos y tamaños de fuente

Establecer el [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontFamily/) propiedad de `Label` en una cadena como "Times Roman" para seleccionar una familia de fuentes. Sin embargo, debe especificar una familia de fuentes que se admite en la plataforma concreta, y las plataformas no son coherentes en este sentido.

Establecer el [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontSize/) propiedad de `Label` a un `double` para especificar el alto aproximado de la fuente. Vea [capítulo 5, tratar con tamaños](chapter05.md), para obtener más información sobre la elección de forma inteligente los tamaños de fuente.

Como alternativa, puede obtener uno de varios tamaños de fuente de valores preestablecidos depende de la plataforma. El método estático [ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/) método y [sobrecarga](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/Xamarin.Forms.Element/) ambos devuelven un `double` según el valor de tamaño de fuente adecuado para la plataforma de los miembros de la [ `NamedSize` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NamedSize/)enumeración ([`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Default/), [ `Micro` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Micro/), [ `Small` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Small/), [ `Medium` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Medium/),  y [ `Large` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Large/)). El valor devuelto de la `Medium` miembro no es necesariamente el mismo que `Default`. El [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) ejemplo muestra el texto con estos denominado sizes.

Establecer el [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontAttributes/) propiedad de `Label` a un miembro de estos [ `FontAttributes` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FontAttributes/) enumeración, [ `Bold` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Bold/), [ `Italic` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Italic/), o [ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.None/). Puede combinar la `Bold` y `Italic` miembros con el C# operador bit a bit OR.

## <a name="formatted-text"></a>Texto con formato

En todos los ejemplos hasta ahora, todo el texto se muestra por el `Label` se ha formateado de manera uniforme. Para cambiar el formato en una cadena de texto, no establece la `Text` propiedad de `Label`. En su lugar, establezca el [ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/) propiedad a un objeto de tipo [ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/).

`FormattedString` tiene una [ `Spans` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FormattedString.Spans/) propiedad que es una colección de [ `Span` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Span/) objetos. Cada `Span` objeto tiene su propio [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.Text/), [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontFamily/), [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontSize/), [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontAttributes/), [ `ForegroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.ForegroundColor/), y [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.BackgroundColor/) propiedades.

El [ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) ejemplo muestra cómo utilizar el `FormattedText` propiedad para una sola línea de texto, y [ **VariableFormattedParagraph** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) muestra la técnica para un párrafo completo, como se muestra aquí:

[![Captura de pantalla triple de variable con el formato párrafo](images/ch03fg06-small.png "texto de la etiqueta con el formato de Variable")](images/ch03fg06-large.png#lightbox "texto de la etiqueta con el formato de Variable")

El [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) programa utiliza una sola `Label` y un `FormattedString` objeto para mostrar todos los tamaños de fuente con nombre para cada plataforma.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 3 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Ejemplos de capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Ejemplos de capítulo 3 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Label](~/xamarin-forms/user-interface/text/label.md)
- [Trabajar con colores](~/xamarin-forms/user-interface/colors.md)
