---
title: Uso de Core gráficos y animación básica de Xamarin.iOS
description: En este artículo paso a paso muestra cómo crear una aplicación que utiliza los principales gráficos y animación básica. Muestra cómo se dibujan en la pantalla en respuesta a la interacción del usuario, así como cómo animar una imagen para recorrer a lo largo de una ruta de acceso.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 2690b60abe963cf7b02ca282b32091098a224ccf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61091375"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Uso de Core gráficos y animación básica de Xamarin.iOS

En este tutorial vamos a dibujar un trazado con gráficos esenciales en respuesta a entrada táctil. A continuación, agregaremos un `CALayer` que contiene una imagen que se va a animar a lo largo de la ruta de acceso.

Captura de pantalla siguiente muestra la aplicación completada:

![](graphics-animation-walkthrough-images/00-final-app.png "La aplicación completada")

Antes de comenzar descarga el *GraphicsDemo* ejemplo que acompaña a esta guía. Se puede descargar [aquí](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/) y se encuentra dentro de la **GraphicsWalkthrough** directory inicie el proyecto denominado **GraphicsDemo_starter** haciendo doble clic en él, y Abra el `DemoView` clase.

## <a name="drawing-a-path"></a>Dibujar un trazado


1. En `DemoView` agregar un `CGPath` variable a la clase y crear instancias de ella en el constructor. También se declare dos `CGPoint` variables, `initialPoint` y `latestPoint`, que se usará para capturar el punto de toque desde el que construimos la ruta de acceso:
    
    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;
    
        public DemoView ()
        {
            BackgroundColor = UIColor.White;
    
            path = new CGPath ();
        }
    }
    ```

2. Agregue las siguientes directivas using:

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. A continuación, invalide `TouchesBegan` y `TouchesMoved,` y agregue las siguientes implementaciones para capturar el punto de toque inicial y cada punto de toque posteriores respectivamente:

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){
    
        base.TouchesBegan (touches, evt);
    
        UITouch touch = touches.AnyObject as UITouch;
        
        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }
    
    public override void TouchesMoved (NSSet touches, UIEvent evt){
    
        base.TouchesMoved (touches, evt);
    
        UITouch touch = touches.AnyObject as UITouch;
        
        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay` se llamará cada vez que mover un toque en orden para `Draw` que se llamará en el siguiente paso de bucle de ejecución.

4. Vamos a agregar líneas a la ruta de acceso en el `Draw` método y el uso para dibujar con una línea roja discontinua. [Implemente `Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md) con el código se muestra a continuación:

    ```csharp
    public override void Draw (CGRect rect){
    
        base.Draw (rect);
    
        if (!initialPoint.IsEmpty) {
    
            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){
                    
                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();
    
                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }
            
                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });
                                
                //add geometry to graphics context and draw it
                g.AddPath (path);       
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

Si se ejecuta la aplicación ahora, nos podemos táctil para dibujar en la pantalla, como se muestra en la captura de pantalla siguiente:

![](graphics-animation-walkthrough-images/01-path.png "En la pantalla de dibujo")

## <a name="animating-along-a-path"></a>Animar a lo largo de una ruta de acceso

Ahora que hemos implementado el código para permitir que los usuarios que se va a dibujar la ruta de acceso, vamos a agregar el código para animar una capa a lo largo de la guía dibujada.

1. En primer lugar, agregue un [ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md) variable a la clase y crearla en el constructor:

    ```csharp
    public class DemoView : UIView
        {
            …
    
            CALayer layer;
    
            public DemoView (){
                …
    
                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. A continuación, vamos a agregar la capa como una subcapa de capa de la vista cuando el usuario levanta el dedo desde la pantalla. A continuación, vamos a crear una animación de fotograma clave mediante la ruta de acceso, si anima la capa `Position`.

    Para lograr esto es necesario para invalidar el `TouchesEnded` y agregue el código siguiente:

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. Ejecute la aplicación ahora y después de dibujo, una capa con una imagen se agrega y viaja a lo largo del trazado dibujado:

![](graphics-animation-walkthrough-images/00-final-app.png "Una capa con una imagen se agrega y viaja a lo largo del trazado dibujado")

## <a name="summary"></a>Resumen

En este artículo, hemos avanzado a través de un ejemplo que vinculadas entre sí los conceptos de gráficos y animación. En primer lugar, hemos mostrado cómo usar Core Graphics para dibujar un trazado un `UIView` en respuesta a la interacción del usuario. A continuación, mostramos cómo usar animación básica para crear una imagen recorrer a lo largo de esa ruta de acceso.


## <a name="related-links"></a>Vínculos relacionados

- [Animación básica](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Gráficos básicos](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Recetas de animación de Core](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
