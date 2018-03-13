---
title: "Resumen del capítulo 5. Tratar con tamaños"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 1df1751c55c6a031bf9f26d774b739f4ca83fa91
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Resumen del capítulo 5. Tratar con tamaños

Se detectaron varios tamaños de Xamarin.Forms hasta ahora:

- El alto de la barra de estado de iOS es 20
- El `BoxView` tiene un ancho y alto predeterminados de 40
- El valor predeterminado `Padding` en un `Frame` es 20
- El valor predeterminado `Spacing` en el `StackLayout` es 6
- El `Device.GetNamedSize` método devuelve un tamaño de fuente numérico

Estos tamaños no son píxeles. En su lugar, son unidades independientes del dispositivo reconocidos por separado por cada plataforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Píxeles, puntos, DP, DIP y DIUs

Al principio de los historiales de Apple Mac y Microsoft Windows, los programadores trabajaban en unidades de píxeles. Sin embargo, la llegada de las pantallas de mayor resolución requiere un enfoque más virtualizado y abstracto a coordenadas de pantalla. En el mundo de Mac, los programadores trabajaban en unidades de *puntos*, tradicionalmente 1/72 pulgadas, mientras los desarrolladores de Windows usa *unidades independientes del dispositivo* (DIUs) en función de 1/96 de pulgada.

Dispositivos móviles, sin embargo, por lo general se mantienen mucho más cercano de la cara y tiene una resolución mayor escritorio pantallas, lo que implica que puede tolerar una mayor densidad de píxeles.

Los programadores que usar como destino dispositivos iPhone y iPad de Apple continuarán funcionando en unidades de *puntos*, pero hay 160 de estos puntos por pulgada. En función del dispositivo, puede ser 1, 2 ó 3 píxeles para el punto.

Android es similar. Los programadores que trabajan con unidades de *independiente de la densidad de píxeles* (DP), y la relación entre DP y píxeles se basa en 160 DP por pulgada.

El tiempo de ejecución de Windows también ha establecido los factores de escala que implican algo similar a 160 unidades independientes del dispositivo para el valor de pulgada.

En resumen, un programador de Xamarin.Forms dirigida a los teléfonos y tabletas puede suponer que todas las unidades de medida que se basan en el siguiente criterio:

- 160 unidades por pulgada, equivalente a
- 64 unidades para el centímetros

Sólo lectura [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) y [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) propiedades definidas por `VisualElement` tienen el valor predeterminado "simular" valores de & #x 2013; 1. Solo cuando un elemento se ha de tamaño e incluir en el diseño de estas propiedades refleja el tamaño real del elemento en unidades independientes del dispositivo. Este tamaño incluye alguno `Padding` establecida en el elemento, pero no el `Margin`.

Un elemento visual se activa la [ `SizeChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/) eventos cuando su `Width` o `Height` ha cambiado. El [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) ejemplo utiliza este evento para mostrar el tamaño de la pantalla del programa.

## <a name="metrical-sizes"></a>Tamaños de métrica:

El [ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) utiliza [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) y [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) para mostrar una `BoxView` una pulgada de alto y uno centímetros amplia.

## <a name="estimated-font-sizes"></a>Tamaños de fuente estimado

El [ **FontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) ejemplo muestra cómo usar la regla de 160 unidades-a-the-pulgadas para especificar los tamaños de fuente en unidades de puntos. La coherencia visual entre las plataformas con esta técnica es mejor que `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Ajustar el texto al tamaño disponible

Es posible ajustar un bloque de texto dentro de un rectángulo determinado calculando una `FontSize` de la `Label` usando los siguientes criterios:

- Interlineado es 120% del tamaño de fuente (130% en las plataformas de Windows).
- Promedio de carácter ancho es un 50% del tamaño de fuente.

El [ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) ejemplo muestra esta técnica. Este programa se escribió antes de la [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propiedad estaba disponible, por lo que utiliza un [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) con un [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) configuración para simular un margen.

[![Captura de pantalla de triple del tamaño de fuente estimado](images/ch05fg07-small.png "texto se ajusta al tamaño disponible")](images/ch05fg07-large.png#lightbox "texto se ajusta al tamaño disponible")

## <a name="a-fit-to-size-clock"></a>Un reloj de ajustar al tamaño

El [ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) ejemplo muestra cómo utilizar [ `Device.StartTimer` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.StartTimer/p/System.TimeSpan/System.Func%7BSystem.Boolean%7D/) para iniciar un temporizador que notifica periódicamente a la aplicación cuando llega el momento para actualizar el reloj. El tamaño de fuente se establece en un sexto del ancho de página para realizar la presentación tan grande como sea posible.

## <a name="accessibility-issues"></a>Problemas de accesibilidad

El **EstimatedFontSize** programa y **FitToSizeClock** programa ambos contienen una ligera imperfección: si el usuario cambia la configuración de accesibilidad del teléfono Android o Windows 10 Mobile, el programa ya no puede calcular el tamaño que se representa el texto en función del tamaño de fuente. El [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) ejemplo muestra este problema.

## <a name="empirically-fitting-text"></a>Empíricamente ajustar texto

Otra forma de ajustar el texto a un rectángulo es empíricamente calcular el tamaño del texto representado y ajústelo hacia arriba o hacia abajo. El programa en las llamadas de libro [ `GetSizeRequest` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/) en un elemento visual para obtener el tamaño del elemento deseado. Que método está desusado y programas en su lugar, deben llamar a [`Measure`] (/ api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/).

Para una `Label`, el primer argumento debe ser el ancho del contenedor (para permitir el ajuste), mientras que el segundo argumento debe establecerse en `Double.PositiveInfinity` para hacer que el alto sin restricciones. El [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) ejemplo muestra esta técnica.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 5 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Ejemplos de capítulo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Ejemplos de capítulo 5 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
