---
title: Uso de controles personalizados con iOS Designer
description: Este documento describe cómo crear un control personalizado y su uso con el Diseñador de Xamarin para iOS. Muestra cómo hacer que el control disponible en el cuadro de herramientas del Diseñador de iOS, implementar el control, por lo que se represente correctamente y diseñar tiempo y mucho más.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: d24d62bf190a2cb078bf82d7a8676f302e740762
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115593"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>Uso de controles personalizados con iOS Designer

## <a name="requirements"></a>Requisitos

El Diseñador de Xamarin para iOS está disponible en Visual Studio para Mac y Visual Studio 2015 y 2017 en Windows.

Esta guía asume un conocimiento con el contenido que se tratan en el [guías de introducción a](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Tutorial

> [!IMPORTANT]
> A partir de Xamarin.Studio 5.5, la manera en que se crean controles personalizados es ligeramente diferente a las versiones anteriores. Para crear un control personalizado, ya sea el `IComponent` interfaz es necesaria (con los métodos de implementación asociados) o la clase pueden anotarse con `[DesignTimeVisible(true)]`. El último método que se sirve en el ejemplo siguiente del tutorial.


1. Crear una nueva solución desde la **iOS > aplicación > aplicación de vista única > C#** plantilla, asígnele el nombre `ScratchTicket`y continúe con el Asistente para nuevo proyecto:

    [![](ios-designable-controls-walkthrough-images/01new.png "Crear una nueva solución")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Cree un nuevo archivo de clase vacía denominado `ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "Cree una nueva clase ScratchTicketView")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Agregue el código siguiente para `ScratchTicketView` clase:

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;
    
            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }
    
            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }
    
            public ScratchTicketView()
            {
                Initialize();
            }
    
            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }
    
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }
    
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }
    
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }
    
            public override void Draw(CGRect rect)
            {
                base.Draw(rect);
    
                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);
    
                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();
    
                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }
    
                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);        
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```


1. Agregar el `FillTexture.png`, `FillTexture2.png` y `Monkey.png` archivos (disponible [desde GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) a la **recursos** carpeta.
    
1. Haga doble clic en el `Main.storyboard` archivo para abrirlo en el diseñador:

    [![](ios-designable-controls-walkthrough-images/03new.png "El Diseñador de iOS")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. Arrastrar y colocar un **vista de imagen** desde el **cuadro de herramientas** en la vista en el guión gráfico.

    [![](ios-designable-controls-walkthrough-images/04new.png "Vista de la imagen que se agregan al diseño del")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. Seleccione el **vista de imagen** y cambie su **imagen** propiedad `Monkey.png`.

    [![](ios-designable-controls-walkthrough-images/05new.png "Establecer propiedad de imagen de vista en Monkey.png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. Como estamos usando las clases de tamaño necesitamos restringir esta vista de imagen. Haga clic en la imagen dos veces para ponerlo en modo de restricción. Vamos a restringir al centro, haga clic en el identificador de anclaje de centro y alinéelo tanto vertical como horizontalmente:

    [![](ios-designable-controls-walkthrough-images/06new.png "Centrar la imagen")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Para restringir el alto y ancho, haga clic en los controladores de tamaño de los bordes (los identificadores en forma de ' huesos') y seleccione el ancho y alto, respectivamente:

    [![](ios-designable-controls-walkthrough-images/07new.png "Agregar restricciones")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. Actualizar el marco basado en las restricciones, haga clic en el botón de actualización de la barra de herramientas:

    [![](ios-designable-controls-walkthrough-images/08new.png "La barra de herramientas de restricciones")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. A continuación, compile el proyecto para que el **Scratch vale vista** aparecen bajo **componentes personalizados** en el cuadro de herramientas:

    [![](ios-designable-controls-walkthrough-images/09new.png "El cuadro de herramientas de componentes personalizados")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. Arrastre y coloque un **Scratch vale vista** para que aparezca a través de la imagen de monkey. Ajuste los controladores de arrastre para que la vista de vale temporal cubre el objeto monkey completamente, tal como se muestra a continuación:

    [![](ios-designable-controls-walkthrough-images/10new.png "Una vista de vale temporal a través de la vista de imagen")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Restringir la vista de vale temporal a la vista de imagen dibujando un rectángulo delimitador para seleccionar ambas vistas. Seleccione las opciones para restringirlo a los marcos de ancho, alto, centro e intermedio y update en función de las restricciones, como se muestra a continuación:

    [![](ios-designable-controls-walkthrough-images/11new.png "Centrar y agregar restricciones")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. Ejecute la aplicación y deben "Rascarse" la imagen para mostrar el objeto monkey.

    [![](ios-designable-controls-walkthrough-images/10-app.png "Ejecución de una aplicación de ejemplo")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Agregar propiedades de tiempo de diseño

El diseñador incluye también compatibilidad en tiempo de diseño para controles personalizados de numérica del tipo de propiedad, enumeración, string, bool, CGSize, UIColor y UIImage. Para demostrar, vamos a agregar una propiedad a la `ScratchTicketView` para establecer la imagen que está "arañada desactivado."

Agregue el código siguiente a la `ScratchTicketView` clase para la propiedad:

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image 
{
    get { return image; }
    set { 
            image = value;
            SetNeedsDisplay ();
        }
}
```

También nos conviene agregar una comprobación de null para el `Draw` método, de este modo:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);
            
        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);       
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Incluyendo un `ExportAttribute` y un `BrowsableAttribute` con el argumento establecido en `true` da como resultado la propiedad que se muestra en el diseñador **propiedad** panel. Si cambia la propiedad a otra imagen incluido con el proyecto, como `FillTexture2.png`, da como resultado la actualización de control en tiempo de diseño, como se muestra a continuación:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Editar propiedades de tiempo de diseño")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo hemos visto cómo crear un control personalizado, así como para usarla en una aplicación iOS mediante el Diseñador de iOS. Hemos visto cómo crear y compilar el control para que esté disponible para una aplicación en el diseñador **cuadro de herramientas**. Además, hemos visto cómo implementar el control que se representa correctamente en tiempo de diseño y tiempo de ejecución, así como cómo exponer propiedades de control personalizado en el diseñador.



## <a name="related-links"></a>Vínculos relacionados

- [ScratchTicket (ejemplo)](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [imágenes necesarias (ejemplo)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
