---
title: Seguimiento de multitoque dedo
description: Este tema muestra cómo realizar el seguimiento de eventos touch desde varios dedos
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 9c0206de17e0c60803252328ff0398cee0997dbb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="multi-touch-finger-tracking"></a>Seguimiento de multitoque dedo

_Este tema muestra cómo realizar el seguimiento de eventos touch desde varios dedos_

Hay ocasiones cuando una aplicación multitoque necesita realizar un seguimiento individuales dedos medida que se mueven al mismo tiempo en la pantalla. Una aplicación típica es un programa de finger-paint. Desea que el usuario que puedan para dibujar con un solo dedo, sino también para dibujar con los dedos varias al mismo tiempo. A medida que el programa debe procesar varios eventos de entrada táctil, necesita distinguir qué eventos se corresponden con cada dedo. Android proporciona un código de identificador para este propósito, pero obtener y ese código de control pueden ser un poco complicadas.

Para todos los eventos asociados con un dedo determinado, el código de identificador sigue siendo el mismo. El código de identificador se asigna cuando un dedo toca la pantalla en primer lugar y deja de ser válido después de que levante el dedo desde la pantalla.
Estos códigos de identificación son enteros suelen ser muy pequeños y Android vuelve a usar para los eventos de toque más adelante.

Casi siempre, un programa que realiza un seguimiento de los dedos individuales mantiene un diccionario para entrada táctil de seguimiento. La clave del diccionario es el código de identificador que identifica un dedo determinado. El valor del diccionario depende de la aplicación. En el [pintura con los dedos](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programa, cada trazo dedo (de entrada táctil en la versión) está asociado con un objeto que contiene toda la información necesaria representar la línea dibujada con ese dedo. El programa define un pequeño `FingerPaintPolyline` clase para este propósito:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

Cada polilínea tiene un color, el ancho del trazo y un gráfico Android [ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/) objeto para acumular y representar varios puntos de la línea que se va a dibujar.

El resto del código se muestra a continuación se encuentra en un `View` derivado denominado `FingerPaintCanvasView`. Que la clase mantiene un diccionario de objetos de tipo `FingerPaintPolyline` durante el tiempo que activamente se va a dibujar por uno o más dedos:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Este diccionario permite que la vista obtener rápidamente la `FingerPaintPolyline` información asociada con un dedo determinado.

El `FingerPaintCanvasView` clase también mantiene un `List` objeto para las polilíneas que se han completado:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Los objetos en este `List` están en el mismo orden en que se dibujaron.

`FingerPaintCanvasView` reemplaza los dos métodos definidos por `View`: [ `OnDraw` ](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/) y [ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/).
En su `OnDraw` override, la vista dibuja las polilíneas completadas y, a continuación, se dibuja las polilíneas en curso.

La invalidación de la `OnTouchEvent` método comienza obteniendo un `pointerIndex` valor desde el `ActionIndex` propiedad. Esto `ActionIndex` valor distingue entre varios dedos, pero no sea coherente en varios eventos. Por esta razón, utilizar el `pointerIndex` para obtener el puntero `id` valor desde el `GetPointerId` método. Este Id. de *es* coherente a través de varios eventos:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

Tenga en cuenta que utiliza la invalidación de la `ActionMasked` propiedad en el `switch` instrucción en lugar del `Action` propiedad. El motivo es el siguiente:

Cuando se trata con multitoque, el `Action` propiedad tiene un valor de `MotionEventsAction.Down` para el primer dedo en toque la pantalla y, a continuación, los valores de `Pointer2Down` y `Pointer3Down` como los dedos de segundo y terceros también toque la pantalla. Medida que hacen los dedos cuarto y quinto contacto, el `Action` propiedad tiene valores numéricos que incluso no corresponden a los miembros de la `MotionEventsAction` enumeración! Debe examinar los valores de marcas de bits en los valores que se va a interpretar su significado.

De forma similar, como los dedos deje la pantalla, póngase en contacto con el `Action` propiedad tiene valores de `Pointer2Up` y `Pointer3Up` para los dedos segundo y terceros, y `Up` para el primer dedo.

El `ActionMasked` propiedad toma en un menor número de valores, ya que está diseñada para utilizarse junto con el `ActionIndex` propiedad para diferenciar entre varios dedos. Cuando dedos toque la pantalla, la propiedad solo puede ser `MotionEventActions.Down` para el primer dedo y `PointerDown` para dedos posteriores. Como los dedos deja la pantalla, `ActionMasked` tiene valores de `Pointer1Up` para los dedos posteriores y `Up` para el primer dedo.

Cuando se usa `ActionMasked`, `ActionIndex` distingue entre los dedos posteriores para entrada táctil y dejar la pantalla, pero normalmente no es necesario utilizar ese valor excepto como un argumento a otros métodos en el `MotionEvent` objeto. Para la entrada táctil, uno de los más importantes de estos métodos es `GetPointerId` llama en el código anterior. Que método devuelve un valor que puede utilizar para una clave de diccionario para asociar eventos particulares que dedos.

El `OnTouchEvent` invalidar en la [pintura con los dedos](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) procesos de programa la `MotionEventActions.Down` y `PointerDown` eventos de forma idéntica al crear un nuevo `FingerPaintPolyline` objeto y lo agrega al diccionario:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

Tenga en cuenta que el `pointerIndex` también se utiliza para obtener la posición del dedo dentro de la vista. Toda la información de entrada táctil está asociada con el `pointerIndex` valor. El `id` identifica singularmente dedos entre varios mensajes, por lo que la utilizada para crear la entrada del diccionario.

De forma similar, el `OnTouchEvent` invalidar también controla la `MotionEventActions.Up` y `Pointer1Up` exactamente igual transfiriéndolo polilíneas completado para la `completedPolylines` recopilación de manera que se pueden dibujar durante el `OnDraw` invalidar. El código también se quita el `id` entrada del diccionario:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

Ahora para la parte complicada.

Entre la profundidad y eventos, por lo general hay muchos `MotionEventActions.Move` eventos. Estos se empaquetan en una sola llamada a `OnTouchEvent`, y debe administrarse de manera diferente de la `Down` y `Up` eventos. El `pointerIndex` valor obtenido anteriormente en el `ActionIndex` se debe omitir la propiedad. En su lugar, el método debe obtener varios `pointerIndex` valores mediante un bucle entre 0 y el `PointerCount` propiedad y, a continuación, obtener una `id` para cada uno de ellos `pointerIndex` valores:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

Este tipo de procesamiento permite la [pintura con los dedos](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programa para realizar un seguimiento de los dedos individuales y dibujar los resultados en la pantalla:

[![Captura de pantalla de ejemplo del ejemplo de pintura con los dedos](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Ahora ha visto cómo puede realizar un seguimiento de los dedos individuales en la pantalla y distinguir entre ellos.


## <a name="related-links"></a>Vínculos relacionados

- [Guía de iOS equivalente de Xamarin](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Pintura con los dedos (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
