---
title: Dedo multitoque de seguimiento en Xamarin.Android
description: Este tema muestra cómo realizar el seguimiento de eventos de toque desde varios dedos
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 34a9d2d9b8acb05a1b978a70e85038507032faaa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104692"
---
# <a name="multi-touch-finger-tracking"></a>Seguimiento de la tecnología multitoque dedo

_Este tema muestra cómo realizar el seguimiento de eventos de toque desde varios dedos_

Hay veces cuando una aplicación de la tecnología multitoque debe realizar un seguimiento de los dedos individuales cuando se mueven simultáneamente en la pantalla. Una aplicación típica es un programa finger-paint. Desea que el usuario para poder dibujar con un solo dedo, sino también para dibujar con varios dedos a la vez. A medida que su programa procesa varios eventos de toque, necesita distinguir qué eventos se corresponden con cada dedo. Android proporciona un código de identificador para este propósito, pero la obtención y ese código de control pueden ser un poco complicados.

Para todos los eventos asociados con un dedo, el código de identificador sigue siendo el mismo. El código de identificador se asigna cuando un dedo toca la pantalla en primer lugar y deja de ser válido después de que levante el dedo desde la pantalla.
Estos códigos de identificación son enteros suelen ser muy pequeños y Android reutiliza para eventos de toque posteriores.

Casi siempre, un programa que realiza el seguimiento de los dedos individuales mantiene un diccionario para toque de seguimiento. La clave del diccionario es el código de Id. que identifica un dedo. El valor del diccionario depende de la aplicación. En el [pintura con los dedos](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programa, cada trazo del dedo (desde la funcionalidad táctil para liberar) está asociado a un objeto que contiene toda la información necesaria para representar la línea dibujada con ese dedo. El programa define un pequeño `FingerPaintPolyline` clase para este propósito:

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

Cada polilínea tiene un color, ancho del trazo y un Android gráficos [ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/) objeto acumular y represente varios puntos de la línea que se va a dibujar.

El resto del código se muestra a continuación se encuentra en un `View` derivado denominado `FingerPaintCanvasView`. Que la clase mantiene un diccionario de objetos de tipo `FingerPaintPolyline` durante el tiempo que activamente se va a dibujar por uno o más dedos:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Este diccionario permite que la vista obtener rápidamente la `FingerPaintPolyline` información asociada con un dedo.

El `FingerPaintCanvasView` clase también mantiene un `List` objeto para las polilíneas que se han completado:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Los objetos de este `List` están en el mismo orden que se dibujaron.

`FingerPaintCanvasView` reemplaza dos métodos definidos por `View`: [`OnDraw`](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/)
y [ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/).
En su `OnDraw` override, la vista dibuja las polilíneas completadas y, a continuación, dibuja las polilíneas en curso.

La invalidación de la `OnTouchEvent` método comienza mediante la obtención de un `pointerIndex` valor desde el `ActionIndex` propiedad. Esto `ActionIndex` valor distingue entre varios dedos, pero no sea coherente en varios eventos. Por ese motivo, usa el `pointerIndex` para obtener el puntero `id` valor desde el `GetPointerId` método. Este identificador *es* coherente en varios eventos:

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

Tenga en cuenta que usa la invalidación del `ActionMasked` propiedad en el `switch` instrucción en lugar de `Action` propiedad. El motivo es el siguiente:

Cuando está trabajando con la tecnología multitoque, la `Action` propiedad tiene un valor de `MotionEventsAction.Down` para el primer dedo que tocar la pantalla y, a continuación, los valores de `Pointer2Down` y `Pointer3Down` como la segundo y terceros dedos también toque la pantalla. Como los dedos cuarto y quinto hacer contacto, el `Action` propiedad tiene valores numéricos que incluso no corresponden a los miembros de la `MotionEventsAction` enumeración! Debe examinar los valores de marcas de bits en los valores que se va a interpretar su significado.

De forma similar, como los dedos dejan de contacto con la pantalla, el `Action` tiene los valores de propiedad `Pointer2Up` y `Pointer3Up` para los dedos de segundo y terceros, y `Up` para el primer dedo.

El `ActionMasked` propiedad toma en un menor número de valores, ya que está pensado para usarse junto con el `ActionIndex` propiedad para diferenciar entre varios dedos. Cuando los dedos toque la pantalla, la propiedad sólo puede ser igual `MotionEventActions.Down` para el primer dedo y `PointerDown` para los dedos posteriores. Como los dedos dejan la pantalla, `ActionMasked` tiene valores de `Pointer1Up` para los dedos posteriores y `Up` para el primer dedo.

Cuando se usa `ActionMasked`, `ActionIndex` distingue entre los dedos posteriores y táctiles y deja la pantalla, pero normalmente no es necesario usar ese valor, excepto como argumento a otros métodos en el `MotionEvent` objeto. Para la tecnología multitoque, uno de los más importantes de estos métodos es `GetPointerId` llamado en el código anterior. Que método devuelve un valor que puede utilizar para una clave de diccionario para asociar eventos concretos para los dedos.

El `OnTouchEvent` invalidar en el [pintura con los dedos](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) procesos de programa la `MotionEventActions.Down` y `PointerDown` eventos de forma idéntica al crear un nuevo `FingerPaintPolyline` objeto y éste se agrega al diccionario:

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

Tenga en cuenta que el `pointerIndex` también se usa para obtener la posición del dedo dentro de la vista. Toda la información de contacto está asociada con el `pointerIndex` valor. El `id` identifica los dedos en varios mensajes, para que la utilizada para crear la entrada del diccionario.

De forma similar, el `OnTouchEvent` invalidar también controla la `MotionEventActions.Up` y `Pointer1Up` exactamente igual mediante la transferencia de la polilínea completada para la `completedPolylines` recopilación de manera que se pueden dibujar durante el `OnDraw` invalidar. El código también se quita el `id` entrada del diccionario:

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

Entre la profundidad y los eventos, por lo general hay muchos `MotionEventActions.Move` eventos. Estos se agrupan en una sola llamada a `OnTouchEvent`, y debe administrarse de manera diferente de la `Down` y `Up` eventos. El `pointerIndex` valor obtenido anteriormente en el `ActionIndex` se debe omitir la propiedad. En su lugar, el método debe obtener varios `pointerIndex` valores mediante un bucle entre 0 y el `PointerCount` propiedad y, a continuación, obtenga una `id` para cada uno de esos `pointerIndex` valores:

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

- [Guía de iOS de Xamarin equivalente](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Pintura con los dedos (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
