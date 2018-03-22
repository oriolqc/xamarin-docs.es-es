---
title: TextKit
description: "La API de Kit de texto ofrece texto eficaces características de diseño y la representación en Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: d3a370c3a8d930c817d38422b249b496532fa33f
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="text-kit"></a>Kit de texto

Kit de texto es una nueva API que ofrece características de diseño y la representación de texto eficaz. Se basa en el marco del texto principal de nivel inferior, pero es mucho más fácil de usar que texto principal.

Para disponer de las características del Kit de texto a los controles estándar, se han implementado volver a usar el Kit de texto, incluidos varios controles de texto de iOS:

-  UITextView
-  UITextField
-  UILabel


## <a name="architecture"></a>Arquitectura

Kit de texto proporciona una arquitectura en capas que separa el almacenamiento de texto desde el diseño y la presentación, incluidas las siguientes clases:

-  `NSTextContainer` : Proporciona la geometría que se usa para el texto del diseño y el sistema de coordenadas.
-  `NSLayoutManager` – Distribuye texto activando el texto en glifos. 
-  `NSTextStorage` : Contiene los datos de texto, así como controla las actualizaciones de propiedad de texto de lote. Las actualizaciones por lotes se va a pasar al administrador de diseño para el procesamiento real de los cambios, como volver a calcular el diseño y volver a dibujar el texto.


Estas tres clases se aplican a una vista que representa el texto. Controlar vistas, como el texto integrado `UITextView`, `UITextField`, y `UILabel` aún tengan establecido, pero puede crear y aplicar a cualquier `UIView` también la instancia.

En la siguiente ilustración se muestra esta arquitectura:

 ![](textkit-images/textkitarch.png "Esta ilustración muestra la arquitectura del Kit de texto")

## <a name="text-storage-and-attributes"></a>Almacenamiento de texto y atributos

La `NSTextStorage` clase contiene el texto que se muestra una vista. También se comunica los cambios en el texto, como cambios de caracteres o de sus atributos: para el Administrador de diseño para su presentación. `NSTextStorage` hereda de `MSMutableAttributed` cadena, lo que permite cambios en los atributos de texto que se especifique en lotes entre `BeginEditing` y `EndEditing` llamadas.

Por ejemplo, el fragmento de código siguiente especifica un cambio en primer plano y fondo colores, respectivamente y tiene como destino intervalos determinados:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Después de `EndEditing` es llama, los cambios se envían al administrador de diseño, que a su vez lleva a cabo cualquier diseño es necesario y los cálculos de representación para el texto que se mostrará en la vista.

## <a name="layout-with-exclusion-path"></a>Diseño con la ruta de acceso de exclusión

Kit de texto también admite el diseño y permite escenarios complejos como texto de varias columna y el texto del flujo alrededor de rutas de acceso especificadas llama *las rutas de acceso de exclusión*. Las rutas de acceso de exclusión se aplican al contenedor de texto, lo que modifica la geometría del diseño del texto, haciendo que el texto fluya alrededor de las rutas de acceso especificadas.

Agregar una ruta de acceso de exclusión requiere la configuración del `ExclusionPaths` propiedad en el Administrador de diseño. Al establecer esta propiedad hace que el Administrador de diseño invalidar el diseño del texto y transmitir el texto alrededor de la ruta de acceso de exclusión.

### <a name="exclusion-based-on-a-cgpath"></a>Tomando como base un CGPath de exclusión

Tenga en cuenta la siguiente `UITextView` implementación de subclase:

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

Este código agrega compatibilidad para dibujar en la vista de texto con gráficos de núcleo. Puesto que la `UITextView` clase ahora está integrada para usar el Kit de texto para su representación de texto y el diseño, puede utilizar todas las características del Kit de texto, por ejemplo, configurar las rutas de acceso de exclusión.

> [!IMPORTANT]
> Este subclases de ejemplo `UITextView` para agregar un toque dibujo soporte técnico. Creación de subclases `UITextView` no es necesario para obtener las características del Kit de texto.



Después de que el usuario se dibuja en la vista de texto, el dibujado `CGPath` se aplica a un `UIBezierPath` instancia estableciendo la `UIBezierPath.CGPath` propiedad:

```csharp
bezierPath.CGPath = exclusionPath;
```

Actualización de la línea de código siguiente hace que el diseño del texto actualizar alrededor de la ruta de acceso:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

Captura de pantalla siguiente muestra cómo se cambia el diseño del texto para que fluyan alrededor de la ruta de acceso dibujado:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "Esta captura de pantalla muestra cómo se cambia el diseño del texto para que fluyan alrededor de la ruta de acceso dibujado")

Tenga en cuenta que el Administrador de diseño `AllowsNonContiguousLayout` propiedad se establece en false en este caso. Esto hace que el diseño se vuelva a calcular para todos los casos donde el texto cambia. Si se establece en true puede mejorar el rendimiento al evitar que una actualización de diseño completo, especialmente en el caso de documentos de gran tamaño. Sin embargo, establecer `AllowsNonContiguousLayout` a true podría impedir que la ruta de acceso de exclusión actualizara el diseño en algunas circunstancias - por ejemplo, si se escribe texto en tiempo de ejecución sin un final retorno de carro antes de la ruta de acceso que se va a establecer.


## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)
