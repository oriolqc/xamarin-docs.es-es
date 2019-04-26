---
title: TextKit en Xamarin.iOS
description: Este documento describe cómo usar TextKit en Xamarin.iOS. TextKit proporciona texto eficaces características de representación y diseño.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f08e37d17cc32e45232d54cc4a51bb48d7ec94b1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61184527"
---
# <a name="textkit-in-xamarinios"></a>TextKit en Xamarin.iOS

TextKit es una nueva API que ofrece características de diseño y la representación de texto eficaz. Se basa en el marco de texto principal de bajo nivel, pero es mucho más fácil de usar que el texto principal.

Para que las características de TextKit disponible para los controles estándar, varios controles de texto de iOS han vuelto a implementados para usar TextKit, incluyendo:

-  UITextView
-  UITextField
-  UILabel

## <a name="architecture"></a>Arquitectura

TextKit proporciona una arquitectura por capas que separa el almacenamiento de texto desde el diseño y la presentación, incluidas las siguientes clases:

-  `NSTextContainer` : Proporciona el sistema de coordenadas y la geometría que se usa para el texto del diseño.
-  `NSLayoutManager` : Coloca texto girando texto en glifos. 
-  `NSTextStorage` : Contiene los datos de texto, así como controla las actualizaciones de propiedad de texto de lote. Las actualizaciones por lotes se entregan al administrador de diseño para el procesamiento real de los cambios, como volver a calcular el diseño y volver a dibujar el texto.


Estas clases se aplican a una vista que representa el texto. Controlar vistas, como el texto integrado `UITextView`, `UITextField`, y `UILabel` aún tengan establecido, pero puede crear y aplicar a cualquier `UIView` también la instancia.

En la siguiente ilustración se muestra esta arquitectura:

 ![](textkit-images/textkitarch.png "Esta ilustración muestra la arquitectura TextKit")

## <a name="text-storage-and-attributes"></a>Los atributos y el almacenamiento de texto

La `NSTextStorage` clase contiene el texto que se muestra una vista. También se comunica los cambios en el texto, como los cambios realizados en caracteres o sus atributos: para el Administrador de diseño para su presentación. `NSTextStorage` hereda de `MSMutableAttributed` cadena, lo que permite cambios en los atributos de texto que se especifique en lotes entre `BeginEditing` y `EndEditing` llamadas.

Por ejemplo, el fragmento de código siguiente especifica un cambio en el primer plano y fondo colores, respectivamente y tiene como destino de rangos determinados:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Después de `EndEditing` es llamado, los cambios se envían al administrador de diseño, que a su vez lleva a cabo cualquier diseño es necesario y los cálculos de representación para el texto que se mostrará en la vista.

## <a name="layout-with-exclusion-path"></a>Diseño con la ruta de exclusión

TextKit también es compatible con diseño y permite escenarios complejos como texto de varias columnas y mover el texto en torno a las rutas de acceso especificadas denominado *las rutas de exclusión*. Las rutas de exclusión se aplican en el contenedor de texto, lo que modifica la geometría del diseño del texto, haciendo que el texto fluya alrededor de las rutas de acceso especificadas.

Agregar una ruta de acceso de exclusión requiere establecer el `ExclusionPaths` propiedad en el Administrador de diseño. Al establecer esta propiedad hace que el Administrador de diseño invalidar el diseño del texto y ajustar el texto en torno a la ruta de acceso de exclusión.

### <a name="exclusion-based-on-a-cgpath"></a>Exclusión según un CGPath

Tenga en cuenta la siguiente `UITextView` implementación subclase:

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

Este código agrega compatibilidad para dibujar en la vista de texto con gráficos esenciales. Puesto que la `UITextView` clase ahora se ha creado para usar TextKit para su representación de texto y el diseño, puede utilizar todas las características de TextKit, como el establecimiento de las rutas de exclusión.

> [!IMPORTANT]
> Subclases de este ejemplo `UITextView` para agregar un toque soporte técnico de dibujo. Creación de subclases de `UITextView` no es necesario para obtener las características de TextKit.



Después de que el usuario se dibuja en la vista de texto, el dibujado `CGPath` se aplica a un `UIBezierPath` instancia estableciendo el `UIBezierPath.CGPath` propiedad:

```csharp
bezierPath.CGPath = exclusionPath;
```

Actualización de la línea de código siguiente hace que el diseño del texto actualizar en torno a la ruta de acceso:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

Captura de pantalla siguiente muestra cómo se cambia el diseño del texto para que fluyan en torno a la guía dibujada:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "Esta captura de pantalla muestra cómo se cambia el diseño del texto para que fluyan en torno a la guía dibujada")

Tenga en cuenta que el Administrador de diseño `AllowsNonContiguousLayout` propiedad se establece en false en este caso. Esto hace que el diseño se vuelvan a calcular para todos los casos donde el texto cambia. Si se establece en true puede beneficiar el rendimiento evitando una actualización de diseño completo, especialmente en el caso de documentos de gran tamaño. Sin embargo, establecer `AllowsNonContiguousLayout` a true impiden que la ruta de exclusión de actualización del diseño en algunas circunstancias - por ejemplo, si se escribe texto en tiempo de ejecución sin devolver un carro final antes de la ruta de acceso que se va a establecer.


## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)
