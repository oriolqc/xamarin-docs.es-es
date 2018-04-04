---
title: Seguimiento de multitoque dedo
description: Este documento muestra cómo realizar el seguimiento de eventos touch desde varios dedos
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 178bd0a68166f033293b2d807fea449355c1c584
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="multi-touch-finger-tracking"></a>Seguimiento de multitoque dedo

_Este documento muestra cómo realizar el seguimiento de eventos touch desde varios dedos_

Hay ocasiones cuando una aplicación multitoque necesita realizar un seguimiento individuales dedos medida que se mueven al mismo tiempo en la pantalla. Una aplicación típica es un programa de finger-paint. Desea que el usuario que puedan para dibujar con un solo dedo, sino también para dibujar con los dedos varias al mismo tiempo. Como el programa procesa varios eventos de entrada táctil, debe distinguir entre estas dedos.

Cuando un dedo toca la pantalla por primera vez, iOS crea un [ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/) objeto para ese dedo. Este objeto sigue siendo el mismo que el dedo mueve en la pantalla y, a continuación, se elimina de la pantalla, momento en que se elimina el objeto. Para realizar un seguimiento de dedos, un programa debe evitar almacenar esto `UITouch` objeto directamente. En su lugar, puede usar el [ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propiedad de tipo `IntPtr` para identificar estos `UITouch` objetos.

Casi siempre, un programa que realiza un seguimiento de los dedos individuales mantiene un diccionario para entrada táctil de seguimiento. En un programa de iOS, es la clave del diccionario el `Handle` valor que identifica un dedo determinado. El valor del diccionario depende de la aplicación. En el [pintura con los dedos](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programa, cada trazo dedo (de entrada táctil en la versión) está asociado con un objeto que contiene toda la información necesaria representar la línea dibujada con ese dedo. El programa define un pequeño `FingerPaintPolyline` clase para este propósito:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

Cada polilínea tiene un color, el ancho del trazo y un gráfico de iOS [ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/) objeto para acumular y representar varios puntos de la línea que se va a dibujar.


El resto del código se muestra a continuación se encuentra en un `UIView` derivado denominado `FingerPaintCanvasView`. Que la clase mantiene un diccionario de objetos de tipo `FingerPaintPolyline` durante el tiempo que activamente se va a dibujar por uno o más dedos:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Este diccionario permite que la vista obtener rápidamente la `FingerPaintPolyline` información asociada con cada dedo en función de la `Handle` propiedad de la `UITouch` objeto.

El `FingerPaintCanvasView` clase también mantiene un `List` objeto para las polilíneas que se han completado:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Los objetos en este `List` están en el mismo orden en que se dibujaron.

`FingerPaintCanvasView` invalida los cinco métodos definidos por `View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

Los distintos `Touches` invalidaciones acumularán los puntos que forman las polilíneas.

El [`Draw`] invalidación dibuja las polilíneas completadas y, a continuación, las polilíneas en curso:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Cada uno de los `Touches` invalidaciones potencialmente informa de las acciones de varios dedos, indicados por uno o varios `UITouch` objetos almacenados en el `touches` argumento al método. El `TouchesBegan` invalidaciones bucle a través de estos objetos. Para cada `UITouch` de objeto, el método crea e inicializa un nuevo `FingerPaintPolyline` objeto, incluido el almacenamiento de la ubicación inicial del dedo obtenido de la `LocationInView` método. Esto `FingerPaintPolyline` objeto se agrega a la `InProgressPolylines` diccionario utilizando la `Handle` propiedad de la `UITouch` objeto como una clave de diccionario:

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

El método concluye mediante una llamada a `SetNeedsDisplay` para generar una llamada a la `Draw` invalidar y para actualizar la pantalla.

Como al mover el dedo o los dedos en la pantalla, la `View` obtiene varias llamadas a su `TouchesMoved` invalidar. Esta invalidación del mismo modo recorre el `UITouch` objetos almacenados en el `touches` argumento y agrega la ubicación actual del dedo a la ruta de acceso de gráficos:

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

El `touches` colección contiene solo los `UITouch` objetos para los dedos que se han movido desde la última llamada a `TouchesBegan` o `TouchesMoved`. Si alguna vez necesitas `UITouch` objetos correspondientes a *todos los* los dedos actualmente están en contacto con la pantalla, esa información está disponible a través de la `AllTouches` propiedad de la `UIEvent` argumento al método.

El `TouchesEnded` reemplazo tiene dos trabajos. Debe agregar el último punto en la ruta de acceso de gráficos y la transferencia la `FingerPaintPolyline` objeto desde el `inProgressPolylines` diccionario para el `completedPolylines` lista:

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

El `TouchesCancelled` invalidación controla simplemente abandonar la `FingerPaintPolyline` objeto en el diccionario:

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

En conjunto, este procesamiento permite la [pintura con los dedos](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programa para realizar un seguimiento de los dedos individuales y dibujar los resultados en la pantalla:

[![](touch-tracking-images/image01.png "Seguimiento dedos individuales y el dibujo de los resultados en la pantalla")](touch-tracking-images/image01.png#lightbox)

Ahora ha visto cómo puede realizar un seguimiento de los dedos individuales en la pantalla y distinguir entre ellos.



## <a name="related-links"></a>Vínculos relacionados

- [Guía de Xamarin para Android equivalente](~/android/app-fundamentals/touch/touch-tracking.md)
- [Pintura con los dedos (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
