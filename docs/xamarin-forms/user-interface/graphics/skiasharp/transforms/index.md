---
title: SkiaSharp transformaciones
description: Obtenga información acerca de las transformaciones para mostrar gráficos SkiaSharp
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 668488ab7efae66f1777e9ae6ded1f725833fe16
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="skiasharp-transforms"></a>SkiaSharp transformaciones

_Obtenga información acerca de las transformaciones para mostrar gráficos SkiaSharp_

SkiaSharp admite transformaciones tradicional de los gráficos que se implementan como métodos de la [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/) objeto. Matemáticamente, transformaciones alteran las coordenadas y los tamaños que se especifican en `SKCanvas` funciones de dibujo tal y como se representan los objetos gráficos. Transformaciones a menudo resultan prácticos para dibujar gráficos repetitivos o para ver la animación. Algunas técnicas &mdash; como la rotación de texto o mapas de bits &mdash; no son posibles sin el uso de transformaciones.

Transformaciones de SkiaSharp admiten las siguientes operaciones:

- *Traducir* a coordenadas de desplazamiento de una ubicación a otra
- *Escala* para aumentar o disminuir las coordenadas y los tamaños
- *Girar* para girar coordenadas alrededor de un punto
- *Sesgar* desplazar coordina horizontal o verticalmente para que un rectángulo se convierte en un paralelogramo

Se conocen como *afín* transforma. Transformaciones afines siempre mantiene líneas paralelas y nunca producen una coordenada o tamaño se convierta en infinito. Un cuadrado nunca se transforma en algo distinto de un paralelogramo y un círculo nunca se transforma en algo distinto de una elipse.

SkiaSharp también admite transformaciones no afines (también denominada *proyectiva* o *perspectiva* transforma) basado en una matriz de transformación de 3 por 3 estándar. Una transformación no afines permite un cuadrado se transforman en cualquier cuadrilátero convexo (una cuatro lados figura con todos los ángulos de interior menor que 180 grados). Transformaciones afines no pueden hacer coordenadas o tamaños que será infinita, pero son vitales para efectos 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Diferencias entre SkiaSharp y las transformaciones de Xamarin.Forms

Xamarin.Forms también admite transformaciones que son similares a las de SkiaSharp. El Xamarin.Forms [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) clase define las siguientes propiedades de transformación:

- `TranslationX` y `TranslationY`
- `Scale`
- `Rotation`, `RotationX` y `RotationY`

El `RotationX` y `RotationY` propiedades son las transformaciones de perspectiva que producen efectos 3D casi.

Hay varias diferencias fundamentales entre las transformaciones de Xamarin.Forms y SkiaSharp transformaciones:

La primera diferencia es que SkiaSharp transformaciones se aplican a toda la `SKCanvas` objeto mientras se aplican las transformaciones de Xamarin.Forms a persona `VisualElement` derivados. (Puede aplicar las transformaciones de Xamarin.Forms para el `SKCanvasView` objeto, porque `SKCanvasView` deriva `VisualElement`, pero dentro de ese `SKCanvasView`, aplican las transformaciones SkiaSkarp.)

Las transformaciones de SkiaSharp son relativas a la esquina superior izquierda de la `SKCanvas` mientras Xamarin.Forms transformaciones son relativas a la esquina superior izquierda de la `VisualElement` al que se aplican. Esta diferencia es importante cuando se aplica el ajuste de escala y giro transforma porque estas transformaciones son siempre con respecto a un punto determinado.

La realidad gran diferencia es que son transformaciones SKiaSharp *métodos* mientras son las transformaciones de Xamarin.Forms *propiedades*. Se trata de una diferencia semántica más allá de la diferencia sintáctica: SkiaSharp transformaciones realizan una operación mientras Xamarin.Forms transformaciones establecen un estado. SkiaSharp transformaciones se aplican a objetos gráficos dibujados a continuación, pero no a objetos gráficos que se dibujan antes de aplica la transformación. Por el contrario, una transformación de Xamarin.Forms se aplica a un elemento representado previamente en cuanto se establece la propiedad. Transformaciones de SkiaSharp son acumulativas, como los métodos se llaman; Transformaciones de Xamarin.Forms se sustituyen cuando se establece la propiedad con otro valor.

Todos los programas de ejemplo en esta sección aparecen bajo el encabezado **transforma** en la página principal de la [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programa y en el [ **Transforma** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms) carpeta de la solución.

## <a name="the-translate-transformtranslatemd"></a>[Transformación de traslación](translate.md)

Obtenga información acerca de cómo usar la transformación de traslación de desplazamiento SkiaSharp gráficos.

## <a name="the-scale-transformscalemd"></a>[Transformación de escala](scale.md)

Detectar la transformación de escala SkiaSharp para ajustar la escala para diversos tamaños de los objetos.

## <a name="the-rotate-transformrotatemd"></a>[Transformación de rotación](rotate.md)

Explorar los efectos y animaciones posibles con la transformación de giro SkiaSharp.

## <a name="the-skew-transformskewmd"></a>[Transformación de sesgo](skew.md)

Vea cómo la transformación sesga puede crear objetos gráficos superpuestos en SkiaSharp.

## <a name="matrix-transformsmatrixmd"></a>[Transformaciones de matriz](matrix.md)

Profundizar más en SkiaSharp transformaciones con la matriz de transformación versátil.

## <a name="touch-manipulationstouchmd"></a>[Manipulaciones táctiles](touch.md)

Matriz de uso se transforma para implementar las manipulaciones táctil para arrastrar, ajuste de escala y giro.

## <a name="non-affine-transformsnon-affinemd"></a>[Transformaciones no afines](non-affine.md)

Ir más allá de la oridinary con efectos de transformación no afines.

## <a name="3d-rotation3d-rotationmd"></a>[Giro 3D](3d-rotation.md)

Utilizar las transformaciones no afines para girar objetos 2D en un espacio 3D.


## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
