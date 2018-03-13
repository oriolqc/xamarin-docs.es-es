---
title: Animaciones simples
description: "La clase ViewExtensions proporciona métodos de extensión que pueden utilizarse para crear animaciones simples. Este artículo muestra cómo crear y cancelar las animaciones mediante la clase ViewExtensions."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: fb7ca216978e4c890349a44b07d5a383e9ca2384
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="simple-animations"></a>Animaciones simples

_La clase ViewExtensions proporciona métodos de extensión que pueden utilizarse para crear animaciones simples. Este artículo muestra cómo crear y cancelar las animaciones mediante la clase ViewExtensions._


El [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase proporciona los siguientes métodos de extensión que pueden utilizarse para crear animaciones simples:

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima el [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) y [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propiedades de un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`ScaleTo`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) anima el [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedad de un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) se aplica un aumento incremental animado o disminución en el [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedad de un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima el [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad de un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RelRotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) se aplica un aumento incremental animado o disminución en el [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad de un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateXTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima el [ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/) propiedad de un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateYTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima el [ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/) propiedad de un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`FadeTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima el [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) propiedad de un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).

De forma predeterminada, cada animación tardará 250 milisegundos. Sin embargo, puede especificarse una duración de cada animación al crear la animación.

El [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase también incluye una [ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) método que puede usarse para cancelar las animaciones.

> [!NOTE]
> El [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase proporciona un [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/) método de extensión. Sin embargo, este método está pensado para usarse con diseños para animar transiciones entre los Estados de diseño que contienen el tamaño y colocar los cambios. Por lo tanto, solo debe usarse por [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) subclases.

Los métodos de extensión de la animación en la [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase es asincrónica y devuelto un `Task<bool>` objeto. El valor devuelto es `false` si la animación se completa, y `true` si se cancela la animación. Por lo tanto, los métodos de animación normalmente deben utilizarse con el `await` operador, lo que permite determinar fácilmente cuándo se ha completado una animación. Además, a continuación, es posible crear animaciones secuenciales con métodos de animación posteriores que se ejecutan después de que ha completado el método anterior. Para obtener más información, consulte [compuesta animaciones](#compound).

Si hay un requisito para permitir que una animación completo en segundo plano, el `await` se puede omitir el operador. En este escenario, los métodos de extensión de la animación se devolverá rápidamente después de iniciar la animación, con la animación que se producen en segundo plano. Esta operación puede realizarse aprovechar al crear animaciones compuestas. Para obtener más información, consulte [animaciones compuesto](#composite).

Para obtener más información sobre la `await` (operador), consulte [Introducción al soporte técnico Async](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animaciones únicas

Cada método de extensión en la [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) implementa una operación única animación que progresivamente cambia una propiedad de un valor con otro valor durante un período de tiempo. Esta sección explora cada operación de animación.

### <a name="rotation"></a>Giro

En el ejemplo de código siguiente se muestra cómo utilizar el [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para animar la [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad de un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Este código anima la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia girando hasta 360 grados más de 2 segundos (2000 milisegundos). El [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método obtiene actual [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad valor para el inicio de la animación y, a continuación, se gira de ese valor en su primer argumento (360). Una vez finalizada la animación completa, la imagen [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad se restablece a 0. Esto garantiza que la `Rotation` propiedad no permanecen en 360 cuando termina la animación, que podrían impedir que rotaciones adicionales.

Las capturas de pantalla siguientes muestran la rotación en curso en cada plataforma:

![](simple-images/rotateto.png "Animación de giro")

### <a name="relative-rotation"></a>Rotación relativa

En el ejemplo de código siguiente se muestra cómo utilizar el [ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para aumentar o disminuir de forma incremental el [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad de un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelRotateTo (360, 2000);
```

Este código anima la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia girando 360 grados desde su posición inicial más de 2 segundos (2000 milisegundos). El [ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método obtiene actual [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad valor para el inicio de la animación y, a continuación, se gira de ese valor para el valor, además de su primer argumento (360). Esto garantiza que cada animación siempre será un giro de 360 grados desde la posición inicial. Por lo tanto, si se invoca una nueva animación mientras una animación ya está en curso, se iniciará desde la posición actual y puede acabar en una posición que no es un incremento de 360 grados.

Las capturas de pantalla siguientes muestran la rotación relativa en curso en cada plataforma:

![](simple-images/relrotateto.png "Animación de giro relativa")

### <a name="scaling"></a>Cambiar escala

En el ejemplo de código siguiente se muestra cómo utilizar el [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) método para animar la [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedad de un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.ScaleTo (2, 2000);
```

Este código anima la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia mediante el escalamiento vertical a dos veces su tamaño superior a 2 segundos (2000 milisegundos). El [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) método obtiene actual [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) el valor de propiedad (valor predeterminado de 1) para el inicio de la animación y, a continuación, escalas de ese valor en su primer argumento (2). Esto tiene el efecto de aumentar el tamaño de la imagen a dos veces su tamaño.

Las capturas de pantalla siguientes muestran el escalado en curso en cada plataforma:

![](simple-images/scaleto.png "Animación de ajuste de escala")

### <a name="relative-scaling"></a>Relativa al ajuste de escala

En el ejemplo de código siguiente se muestra cómo utilizar el [ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para animar la [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedad de un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelScaleTo (2, 2000);
```

Este código anima la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia mediante el escalamiento vertical a dos veces su tamaño superior a 2 segundos (2000 milisegundos). El [ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método obtiene actual [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) valor de propiedad para el inicio de la animación y, a continuación, escalas de ese valor para el valor, además de su primer argumento (2). Esto garantiza que cada animación siempre será una escala de 2 desde la posición inicial.

### <a name="scaling-and-rotation-with-anchors"></a>Ajuste de escala y la rotación con delimitadores

El [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) y [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propiedades establecen el centro de ajuste de escala o la rotación de la [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) y [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedades. Por lo tanto, sus valores también afectan a la [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) y [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) métodos.

Dado un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) que se ha colocado en el centro de un diseño, en el ejemplo de código siguiente se muestra la rotación de la imagen alrededor del centro del diseño estableciendo su [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propiedad:

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

Para girar el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia alrededor del centro del diseño, la [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) y [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propiedades deben establecerse en valores en relación con el ancho y alto de la `Image`. En este ejemplo, el centro de la `Image` se define como en el centro del diseño de modo que el valor predeterminado `AnchorX` valor de 0,5 no es necesario cambiar. Sin embargo, el `AnchorY` propiedad se vuelve a definir para que sea un valor de la parte superior de la `Image` al punto central del diseño. Esto garantiza que la `Image` hace un giro completo de 360 grados alrededor del punto central del diseño, como se muestra en las capturas de pantalla siguiente:

![](simple-images/rotate-anchors.png "Animación de giro con delimitadores")

### <a name="translation"></a>Conversión

En el ejemplo de código siguiente se muestra cómo utilizar el [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para animar la [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) y [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propiedades de un [ `Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Este código anima la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia traducirlo horizontalmente y verticalmente en 1 segundo (1000 milisegundos). El [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) método traduce al mismo tiempo la imagen 100 píxeles a la izquierda y 100 píxeles hacia arriba. Esto es porque los argumentos primero y segundo son ambos números negativos. Proporciona los números positivos se traduciría la imagen a la derecha y hacia abajo.

Las capturas de pantalla siguientes muestran la traducción en curso en cada plataforma:

![](simple-images/translateto.png "Animación de traducción")

> [!NOTE]
> Si el elemento se dispuestos inicialmente fuera de la pantalla y, a continuación, se traduce en la pantalla, después de la traducción de diseño de entrada del elemento permanece fuera de la pantalla y el usuario no puede interactuar con él. Por lo tanto, se recomienda que una vista debe disponerse en su posición final y, a continuación, las necesarias traducciones realizadas.

### <a name="fading"></a>Corrección selectiva

En el ejemplo de código siguiente se muestra cómo utilizar el [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para animar la [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) propiedad de un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Este código anima la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia haciendo que se desvanezcan en más de 4 segundos (4000 milisegundos). El [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método obtiene actual [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) valor de propiedad para el inicio de la animación y, a continuación, fusiones en de ese valor en su primer argumento (1).

Las capturas de pantalla siguientes muestran la atenuación en curso en cada plataforma:

![](simple-images/fadeto.png "Animación de atenuación")

<a name="compound" />

## <a name="compound-animations"></a>Animaciones compuestas

Una animación compuesta es una combinación secuencial de las animaciones y pueden crearse con el `await` operador, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

En este ejemplo, el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) se convierte en 6 segundos (6.000 milisegundos). La traducción de la `Image` usa animaciones de cinco, con el `await` operador que indica que cada animación se ejecuta de forma secuencial. Por lo tanto, los métodos posteriores animación ejecutan después de que ha completado el método anterior.

<a name="composite" />

## <a name="composite-animations"></a>Animaciones compuestas

Una animación compuesta es una combinación de animaciones donde se ejecutan simultáneamente dos o más de las animaciones. Pueden crearse animaciones compuestas mezclando animaciones esperadas y no esperan, como se muestra en el ejemplo de código siguiente:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

En este ejemplo, el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) es escalar y girar simultáneamente más de 4 segundos (4000 milisegundos). El escalado de la `Image` utiliza dos animaciones secuenciales que se producen al mismo tiempo como la rotación. El [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método se ejecuta sin un `await` operador y se devuelve inmediatamente, con la primera [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) , a continuación, a partir de animación. El `await` operador en la primera `ScaleTo` retrasa la llamada al método el segundo `ScaleTo` hasta la primera llamada a un método `ScaleTo` ha completado la llamada al método. En este momento la `RotateTo` animación es la mitad forma completado y el `Image` será giradas 180 grados. Durante los últimos 2 segundos (2000 milisegundos), el segundo `ScaleTo` animación y `RotateTo` animación las se completen.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Ejecución simultánea de varios métodos asincrónicos

El `static` `Task.WhenAny` y `Task.WhenAll` métodos se utilizan para ejecutar varios métodos asincrónicos simultáneamente y, por lo tanto, puede utilizarse para crear animaciones compuestas. Ambos métodos devuelven un `Task` objeto y Aceptar una colección de métodos que cada valor devuelto una `Task` objeto. El `Task.WhenAny` método finaliza después de cualquier método en su colección de ejecución, como se muestra en el ejemplo de código siguiente:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

En este ejemplo, el `Task.WhenAny` llamada de método contiene dos tareas. La primera tarea gira la imagen más de 4 segundos (4000 milisegundos), y la segunda tarea escala la imagen más de 2 segundos (2000 milisegundos). Cuando se complete la segunda tarea, la `Task.WhenAny` completa de la llamada al método. Sin embargo, aunque la [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método todavía se está ejecutando, el segundo [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) método puede comenzar.

El `Task.WhenAll` método se completa cuando se hayan completado todos los métodos en su colección, como se muestra en el ejemplo de código siguiente:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

En este ejemplo, el `Task.WhenAll` llamada de método contiene tres tareas, cada uno de los cuales se ejecuta en 10 minutos. Cada `Task` hace que un número diferente de 360 grados rotaciones – 307 rotaciones para [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), 251 rotaciones para [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)y 199 rotaciones para [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/). Estos valores son números primos, por lo tanto, garantizan que las rotaciones no están sincronizadas y, por tanto, no producir patrones repetitivos.

Las capturas de pantalla siguientes muestran las rotaciones varios en curso en cada plataforma:

![](simple-images/multiple-rotations.png "Animación compuesto")

## <a name="canceling-animations"></a>Cancelar las animaciones

Una aplicación puede cancelar uno o más animaciones con una llamada a la `static` [ `ViewExtensions.CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) método, como se muestra en el ejemplo de código siguiente:

```csharp
ViewExtensions.CancelAnimations (image);
```

Esta operación cancelará inmediatamente todas las animaciones que se están ejecutando en el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia.

## <a name="summary"></a>Resumen

Este artículo se han demostrado crear y cancelar las animaciones mediante el [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase. Esta clase proporciona métodos de extensión que pueden usarse para construir las animaciones simples que giran, escalan, traducen y atenuación [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) instancias.


## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [Animación básica (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
