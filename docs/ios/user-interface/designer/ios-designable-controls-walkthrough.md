---
title: "Tutorial: usar controles personalizados con el Diseñador de Xamarin para iOS"
description: "Este artículo proporciona un tutorial paso a paso que muestra cómo crear un control personalizado y usarlo en el Diseñador de Xamarin para iOS. Muestra cómo hacer que un control esté disponible en el cuadro de herramientas del diseñador para que se puede arrastrar y colocar en una vista. Además, se muestra cómo implementar un control, por lo que se representa correctamente en tiempo de diseño y en tiempo de ejecución, así como cómo crear propiedades que se pueden establecer en tiempo de diseño."
ms.topic: article
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 3e12e33b254e58759043ed838d5c34dd7bd024fd
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="walkthrough---using-custom-controls-with-the-xamarin-designer-for-ios"></a>Tutorial: usar controles personalizados con el Diseñador de Xamarin para iOS

_Este artículo proporciona un tutorial paso a paso que muestra cómo crear un control personalizado y usarlo en el Diseñador de Xamarin para iOS. Muestra cómo hacer que un control esté disponible en el cuadro de herramientas del diseñador para que se puede arrastrar y colocar en una vista. Además, se muestra cómo implementar un control, por lo que se representa correctamente en tiempo de diseño y en tiempo de ejecución, así como cómo crear propiedades que se pueden establecer en tiempo de diseño._

## <a name="requirements"></a>Requisitos

El Diseñador de Xamarin para iOS está disponible en Visual Studio para Mac y Visual Studio 2015 y 2017 en Windows.

Esta guía se da por supuesto una familiaridad con el contenido que se tratan en la [Introducción le guía](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Tutorial

> [!IMPORTANT]
> A partir de Xamarin.Studio 5.5, la manera en que se crean controles personalizados es ligeramente diferente a las versiones anteriores. Para crear un control personalizado, ya sea el `IComponent` interfaz es necesaria (con los métodos de implementación asociados) o puede ser la clase se pueden anotar con `[DesignTimeVisible(true)]`. El último método es que se utiliza en el siguiente ejemplo de tutorial.


1. Cree una nueva solución de la **iOS > aplicaciones > única aplicación de vista > C#** plantilla, asígnele el nombre `ScratchTicket`y continúe con el Asistente para nuevo proyecto:


    [![](ios-designable-controls-walkthrough-images/01new.png "Cree una nueva solución")](ios-designable-controls-walkthrough-images/01new.png#lightbox)


1. Crear un nuevo archivo de clase vacía denominado `ScratchTicketView`:


    [![](ios-designable-controls-walkthrough-images/02new.png "Cree una nueva clase de ScratchTicketView")](ios-designable-controls-walkthrough-images/02new.png#lightbox)


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


1. Arrastrar y colocar un **imagen vista** desde el **cuadro de herramientas** en la vista en el guión gráfico.

    [![](ios-designable-controls-walkthrough-images/04new.png "Vista de la imagen se agregan al diseño del")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. Seleccione el **imagen vista** y cambiar su **imagen** propiedad `Monkey.png`.

    [! [] (ios-pueden diseñar-controles-walkthrough-imágenes/05new.png "propiedad de imagen de vista de imagen de configuración para Monkey.png)](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. Como estamos usando las clases de tamaño necesitamos restringir esta vista de la imagen. Haga clic en la imagen dos veces para ponerla en modo de restricción. Vamos a restringir al centro, haga clic en el identificador del Anclando center le ayude y alinéelo tanto vertical como horizontalmente:

    [![](ios-designable-controls-walkthrough-images/06new.png "Centrar la imagen")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Para restringir el alto y ancho, haga clic en los identificadores de anclaje de tamaño (los identificadores 'hueso' en forma de) y seleccione el ancho y alto respectivamente:

    [![](ios-designable-controls-walkthrough-images/07new.png "Agregar restricciones")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. Actualice el marco basado en las restricciones, haga clic en el botón de actualización en la barra de herramientas:

    [![](ios-designable-controls-walkthrough-images/08new.png "La barra de herramientas de restricciones")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. A continuación, compile el proyecto para que la **Scratch vale vista** aparecerá en **componentes personalizados** en el cuadro de herramientas:

    [![](ios-designable-controls-walkthrough-images/09new.png "El cuadro de herramientas de componentes personalizados")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. Arrastre y coloque una **Scratch vale vista** para que aparezca en la imagen de mono. Ajustar los manipuladores de arrastre para que la vista de vale Scratch cubre el mono completamente, tal y como se muestra a continuación:

    [![](ios-designable-controls-walkthrough-images/10new.png "Una vista de vale de desecho en la vista de imagen")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Restringir la vista de vale Scratch a la vista de la imagen dibujando un rectángulo delimitador para seleccionar ambas vistas. Seleccione las opciones para restringir a los marcos de ancho, alto, centrar e intermedio y actualización en función de las restricciones, como se muestra a continuación:

    [![](ios-designable-controls-walkthrough-images/11new.png "Centrar y agregar restricciones")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. Ejecute la aplicación y "scratch desactivar" la imagen para que muestre el mono.

    [![](ios-designable-controls-walkthrough-images/10-app.png "Ejecutar una aplicación de ejemplo")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Agregar propiedades en tiempo de diseño

El diseñador también incluye compatibilidad en tiempo de diseño para los controles personalizados de numérico del tipo de propiedad, enumeración, string, bool, CGSize, UIColor y UIImage. Para mostrar, vamos a agregar una propiedad a la `ScratchTicketView` para establecer la imagen que está "arañada desactivado."

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

Podemos también queremos agregar una comprobación de valores null para la `Draw` método, de este modo:

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

Incluido un `ExportAttribute` y un `BrowsableAttribute` con el argumento establecido en `true` da como resultado la propiedad que se va a mostrar en el diseñador **propiedad** panel. Cambiar la propiedad en otra imagen incluida con el proyecto, como `FillTexture2.png`, da como resultado la actualización de control en tiempo de diseño, tal y como se muestra a continuación:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Editar propiedades de tiempo de diseño")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo hemos visto cómo crear un control personalizado, así como para usarlo en una aplicación de iOS mediante el Diseñador de iOS. Hemos visto cómo crear y compilar el control para que esté disponible para una aplicación en el diseñador **cuadro de herramientas**. Además, hemos observado cómo implementar el control de forma que se representa correctamente en tiempo de diseño y en tiempo de ejecución, así como cómo exponer propiedades de control personalizado en el diseñador.



## <a name="related-links"></a>Vínculos relacionados

- [ScratchTicket (ejemplo)](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [imágenes necesarias (ejemplo)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
