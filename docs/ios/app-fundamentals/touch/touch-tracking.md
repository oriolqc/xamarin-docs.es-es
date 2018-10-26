---
title: Dedo multitoque de seguimiento en Xamarin.iOS
description: Este documento describe cómo realizar el seguimiento de los dedos individuales en los gestos multitoque en una aplicación de Xamarin.iOS. Centra en un ejemplo de aplicación para.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a1ddcda84d51b5a8a9220558ddaf9476a2321ee8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105056"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Dedo multitoque de seguimiento en Xamarin.iOS

_Este documento muestra cómo realizar el seguimiento de eventos de toque desde varios dedos_

Hay veces cuando una aplicación de la tecnología multitoque debe realizar un seguimiento de los dedos individuales cuando se mueven simultáneamente en la pantalla. Una aplicación típica es un programa finger-paint. Desea que el usuario para poder dibujar con un solo dedo, sino también para dibujar con varios dedos a la vez. A medida que el programa procesa varios eventos de toque, debe distinguir entre los dedos.

Cuando un dedo toca la pantalla por primera vez, crea iOS un [ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/) objeto de ese dedo. Este objeto es igual el dedo se mueve en la pantalla y, a continuación, se levanta en la pantalla, momento en que se elimina el objeto. Para realizar un seguimiento de los dedos, un programa debe evitar almacenar esto `UITouch` objeto directamente. En su lugar, puede usar el [ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propiedad de tipo `IntPtr` para identificar estos `UITouch` objetos.

Casi siempre, un programa que realiza el seguimiento de los dedos individuales mantiene un diccionario para toque de seguimiento. Un programa de iOS, la clave del diccionario es el `Handle` valor que identifica un dedo. El valor del diccionario depende de la aplicación. En el [pintura con los dedos](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programa, cada trazo del dedo (desde la funcionalidad táctil para liberar) está asociado a un objeto que contiene toda la información necesaria para representar la línea dibujada con ese dedo. El programa define un pequeño `FingerPaintPolyline` clase para este propósito:

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

Cada polilínea tiene un color, ancho del trazo y un gráfico de iOS [ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/) objeto acumular y represente varios puntos de la línea que se va a dibujar.


El resto del código se muestra a continuación se encuentra en un `UIView` derivado denominado `FingerPaintCanvasView`. Que la clase mantiene un diccionario de objetos de tipo `FingerPaintPolyline` durante el tiempo que activamente se va a dibujar por uno o más dedos:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Este diccionario permite que la vista obtener rápidamente la `FingerPaintPolyline` información asociada con cada dedo en función de la `Handle` propiedad de la `UITouch` objeto.

El `FingerPaintCanvasView` clase también mantiene un `List` objeto para las polilíneas que se han completado:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Los objetos de este `List` están en el mismo orden que se dibujaron.

`FingerPaintCanvasView` invalida los cinco métodos definidos por `View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

Los distintos `Touches` invalidaciones acumulan los puntos que componen las polilíneas.

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

Cada uno de los `Touches` invalidaciones potencialmente informa de las acciones de muchos dedos, indicados por uno o varios `UITouch` objetos almacenados en el `touches` argumento del método. El `TouchesBegan` invalidaciones bucle a través de estos objetos. Para cada `UITouch` de objeto, el método crea e inicializa un nuevo `FingerPaintPolyline` objeto, incluido el almacenamiento de la ubicación inicial del dedo obtenida la `LocationInView` método. Esto `FingerPaintPolyline` objeto se agrega a la `InProgressPolylines` diccionario utilizando la `Handle` propiedad de la `UITouch` objeto como una clave de diccionario:

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

El método termina llamando a `SetNeedsDisplay` para generar una llamada a la `Draw` invalidar y actualizar la pantalla.

Cuando muevan el dedo o los dedos en la pantalla, el `View` obtiene varias llamadas a su `TouchesMoved` invalidar. Esta invalidación del mismo modo recorre el `UITouch` objetos almacenados en el `touches` argumento y agrega la ubicación actual del dedo a la ruta de acceso de gráficos:

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

El `touches` colección contiene sólo aquellos `UITouch` objetos para los dedos que se han movido desde la última llamada a `TouchesBegan` o `TouchesMoved`. Si alguna vez necesita `UITouch` objetos correspondientes a *todos los* los dedos actualmente sin llegar a la pantalla, esa información se encuentra disponible a través de la `AllTouches` propiedad de la `UIEvent` argumento al método.

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

El `TouchesCancelled` controla override simplemente abandonar la `FingerPaintPolyline` objeto en el diccionario:

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

[![](touch-tracking-images/image01.png "Seguimiento de dedos individuales y los resultados de dibujo en la pantalla")](touch-tracking-images/image01.png#lightbox)

Ahora ha visto cómo puede realizar un seguimiento de los dedos individuales en la pantalla y distinguir entre ellos.



## <a name="related-links"></a>Vínculos relacionados

- [Guía de Xamarin Android equivalente](~/android/app-fundamentals/touch/touch-tracking.md)
- [Pintura con los dedos (ejemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
