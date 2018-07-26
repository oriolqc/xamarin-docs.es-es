---
title: Animación básica de Xamarin.iOS
description: En este artículo se examina el marco de animación básica, que muestra cómo permiten animaciones fluidas en UIKit, así como la de alto rendimiento se usa directamente para el control de animación de nivel inferior.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3d26e58822385c20f3c08d0b75ba468467c2c9b1
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242137"
---
# <a name="core-animation-in-xamarinios"></a>Animación básica de Xamarin.iOS

_En este artículo se examina el marco de animación básica, que muestra cómo permiten animaciones fluidas en UIKit, así como la de alto rendimiento se usa directamente para el control de animación de nivel inferior._

incluye iOS [ *animación básica* ](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) para proporcionar compatibilidad con animación vistas en la aplicación.
Todas las animaciones y fluidas en iOS, como el desplazamiento de las tablas y Deslizar rápidamente entre vistas diferentes de realizan, así como lo hacen porque se basan en Core Animation internamente.

Los marcos de trabajo de animación básica y Core Graphics pueden trabajar juntos para crear atractivas, gráficos 2D animados. De hecho animación básica puede incluso transformar el gráficos 2D en el espacio 3D, crear increíbles experiencias de cinematográficas. Sin embargo, para crear gráficos en 3D es true, se deberá usar algo como OpenGL ES, o para juegos a su vez a una API como MonoGame, aunque 3D está fuera del ámbito de este artículo.

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Animación básica

iOS usa el marco de animación básica para crear efectos de animación, como realizar la transición entre las vistas, deslizamiento de menús y desplazamiento efectos para nombrar algunas. Hay dos formas de trabajar con animación:

- [A través de UIKit](#Using_UIKit_Animation), que incluye animaciones basadas en vistas, así como las transiciones animadas entre los controladores.
- [A través de la animación principal](#Using_Core_Animation), qué capas directamente, lo que permite un control específico.

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>Uso de animación de UIKit

UIKit proporciona varias características que facilitan la tarea de agregar animación a una aplicación. Si bien utiliza internamente Core Animation, abstrae, para que trabajar sólo con controladores y vistas.

Esta sección describen las características de animación de UIKit incluidos:

-  Transiciones entre los controladores
-  Transiciones entre las vistas
-  Animación de propiedades de vista


### <a name="view-controller-transitions"></a>Transiciones de controladores de vista

 `UIViewController` proporciona compatibilidad integrada para realizar la transición entre los controladores de vista a través de la `PresentViewController` método. Cuando se usa `PresentViewController`, opcionalmente se puede animar la transición al segundo controlador.

Por ejemplo, considere la posibilidad de una aplicación con dos controladores, donde se llama a tocar un botón en el primer controlador `PresentViewController` para mostrar un segundo controlador. Para controlar qué animación de transición se usa para mostrar el segundo controlador, basta con establecer su [ `ModalTransitionStyle` ](https://developer.xamarin.com/api/type/UIKit.UIModalTransitionStyle/) propiedad tal como se muestra a continuación:

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

En este caso un `PartialCurl` se usa la animación, aunque otras están disponibles, incluyendo:

-  `CoverVertical` : Diapositivas seguridad de la parte inferior de la pantalla
-  `CrossDissolve` : La vista anterior se atenúa & desaparece la nueva vista
-  `FlipHorizontal` -Voltear horizontal derecha a izquierda. En el despido la transición voltea izquierda a derecha.


Para animar la transición, pasar `true` como el segundo argumento `PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

Captura de pantalla siguiente muestra el aspecto de la transición para el `PartialCurl` caso:

 ![](core-animation-images/06-view-transitions.png "Esta captura de pantalla muestra la transición PartialCurl")

### <a name="view-transitions"></a>Transiciones de vista

Además de las transiciones entre los controladores, UIKit también admite animación transiciones entre las vistas para intercambiar una vista para otro.

Por ejemplo, digamos tenía un controlador con `UIImageView`, donde pulsar en la imagen debe mostrar un segundo `UIImageView`. Para animar la imagen es tan sencillo como llamar a supervista de la vista para realizar la transición a la segunda vista de imagen `UIView.Transition`, pasándole el `toView` y `fromView` tal como se muestra a continuación:

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` También toma un `duration` parámetro que controla cuánto tiempo se ejecuta la animación, así como [ `options` ](https://developer.xamarin.com/api/type/UIKit.UIViewAnimationOptions/) para especificar aspectos como la animación que se usará y la función de aceleración. Además, puede especificar un controlador de finalización que se llamará cuando se completa la animación.

La captura de pantalla siguientes se muestra la transición entre la imagen animada vistas si `TransitionFlipFromTop` se usa:

 ![](core-animation-images/07-animated-transition.png "Esta captura de pantalla muestra la transición entre las vistas de la imagen cuando se usa TransitionFlipFromTop animada")

### <a name="view-property-animations"></a>Animaciones de propiedad de vista

UIKit admite una serie de propiedades de la animación en la `UIView` clase gratuitamente, incluidos:

-  Fotograma
-  Límites
-  Centrar
-  Alpha
-  Transformación
-  Color


Estas animaciones hacer de forma implícita mediante la especificación de los cambios de propiedad en un `NSAction` delegado pasado a estático `UIView.Animate` método. Por ejemplo, el código siguiente anima el punto central de un `UIImageView`:

```csharp
pt = imgView.Center;

UIView.Animate (
    duration: 2, 
    delay: 0, 
    options: UIViewAnimationOptions.CurveEaseInOut | 
        UIViewAnimationOptions.Autoreverse,
    animation: () => {
        imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
            - imgView.Frame.Width / 2, pt.Y);},
    completion: () => {
        imgView.Center = pt; }
);
```

Esto da como resultado una imagen animar y hacia atrás en la parte superior de la pantalla, tal como se muestra a continuación:

 ![](core-animation-images/08-animate-center.png "Una imagen animar y hacia atrás en la parte superior de la pantalla como salida")

Igual que con el `Transition` método `Animate` permite que la duración debe establecerse, junto con la función de aceleración. En este ejemplo también usa el `UIViewAnimationOptions.Autoreverse` opción, lo que hace que la animación que se va a animar el valor a la inicial. Sin embargo, el código también establece el `Center` a su valor inicial en un controlador de finalización. Mientras que una animación interpolan los valores de propiedad con el tiempo, el valor real del modelo de la propiedad es siempre el valor final que se ha establecido. En este ejemplo, el valor es un punto cercano a la derecha de la supervista. Sin establecer el `Center` hasta el punto inicial, que es donde se completa la animación debido a la `Autoreverse` que se va a establecer, la imagen podría ciñen a la parte derecha una vez completada la animación, tal como se muestra a continuación:

 ![](core-animation-images/09-animation-complete.png "Sin establecer el centro en el punto inicial, la imagen podría ciñen a la derecha una vez completada la animación")

## <a name="using-core-animation"></a>Uso de la animación principal

 `UIView` las animaciones permiten una gran capacidad y deben usarse si es posible debido a la facilidad de implementación. Como se mencionó anteriormente, las animaciones UIView usan el marco de animación básica. Sin embargo, no se puede realizar algunas de las cosas con `UIView` animaciones, como animar propiedades adicionales que no se pueden animar con una vista o interpolar a lo largo de una ruta de acceso no lineal. En tales casos donde se necesita un control más preciso, puede utilizarse también directamente animación básica.

### <a name="layers"></a>Capas

Al trabajar con animación básica, ocurre animación mediante *capas*, que son de tipo `CALayer`. Una capa es conceptualmente similar a una vista en que hay una jerarquía de la capa, mucho que hay una jerarquía de vistas. En realidad, las capas de realizar una copia de las vistas, con la vista agregando compatibilidad para la interacción del usuario. Puede tener acceso a la capa de cualquier vista a través de la vista `Layer` propiedad. De hecho, se utiliza el contexto en `Draw` método `UIView` realmente se crea desde la capa. Internamente, la capa de seguridad de un `UIView` tiene establecido en la propia vista, que es lo que llama a su delegado `Draw`. Por lo que al dibujar en un `UIView`, realmente se dibuja en su capa.

Las animaciones de la capa pueden ser implícita o explícita. Las animaciones implícitas son declarativas. Declarar simplemente lo que deben cambiar las propiedades de capa y funciona la animación. Las animaciones explícitas por otro lado se crean a través de una clase de animación que se agrega a una capa. Animaciones explícitas permiten un control de suma sobre cómo se crea una animación. Las secciones siguientes profundizan en las animaciones implícitas y explícitas en mayor profundidad.

### <a name="implicit-animations"></a>Animaciones implícitas

Una forma de animar las propiedades de una capa es a través de una animación implícita. `UIView` las animaciones creación animaciones implícitas. Sin embargo, puede crear animaciones implícitas directamente en una capa también.

Por ejemplo, el código siguiente establece una capa `Contents` desde una imagen, establece un ancho de borde y el color, y se agrega la capa como una subcapa de capa de la vista:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    layer = new CALayer ();
    layer.Bounds = new CGRect (0, 0, 50, 50);
    layer.Position = new CGPoint (50, 50);
    layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
    layer.ContentsGravity = CALayer.GravityResize;
    layer.BorderWidth = 1.5f;
    layer.BorderColor = UIColor.Green.CGColor;

    View.Layer.AddSublayer (layer);
}
```

Para agregar una animación implícita para la capa, simplemente se ajustan los cambios de propiedad en un `CATransaction`. Esto permite animar las propiedades que no se pueden animar con una animación de la vista, como el `BorderWidth` y `BorderColor` tal como se muestra a continuación:

```csharp
public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    CATransaction.Begin ();
    CATransaction.AnimationDuration = 10;
    layer.Position = new CGPoint (50, 400);
    layer.BorderWidth = 5.0f;
    layer.BorderColor = UIColor.Red.CGColor;
    CATransaction.Commit ();
}
```

Este código también anima la capa `Position`, que es la ubicación del punto de anclaje de la capa medido desde la parte superior izquierda de coordenadas del superlayer. El punto de anclaje de una capa es un punto normalizado dentro del sistema de coordenadas de la capa.

La siguiente ilustración muestra la posición y el delimitador punto:

 ![](core-animation-images/10-postion-anchorpt.png "Esta ilustración muestra el punto de posición y el delimitador")

Cuando se ejecuta el ejemplo, el `Position`, `BorderWidth` y `BorderColor` animar como se muestra en las capturas de pantalla siguiente:

 ![](core-animation-images/11-implicit-animation.png "Cuando se ejecuta el ejemplo, la posición, BorderWidth y BorderColor animación tal como se muestra")

### <a name="explicit-animations"></a>Animaciones explícitas

Además de animaciones implícitas, Core Animation incluye una serie de clases que heredan de `CAAnimation` que permiten encapsular animaciones que, a continuación, se agregan explícitamente a una capa. Estos permiten un control preciso sobre animaciones, como modificar el valor inicial de una animación, las animaciones de agrupación y especificar los fotogramas clave para permitir que las rutas de acceso no lineal.

El código siguiente muestra un ejemplo de una animación explícita mediante una `CAKeyframeAnimation` para el nivel mostrado anteriormente (en la sección de animación implícito):

```csharp
public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);
    
    // get the initial value to start the animation from
    CGPoint fromPt = layer.Position;
    
    /* set the position to coincide with the final animation value
    to prevent it from snapping back to the starting position
    after the animation completes*/
    layer.Position = new CGPoint (200, 300);
    
    // create a path for the animation to follow
    CGPath path = new CGPath ();
    path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
    
    // create a keyframe animation for the position using the path
    CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
    animPosition.Path = path;
    animPosition.Duration = 2;
    
    // add the animation to the layer.
    /* the "position" key is used to overwrite the implicit animation created
    when the layer positino is set above*/
    layer.AddAnimation (animPosition, "position");
}
```

Este código cambia el `Position` de la capa mediante la creación de una ruta de acceso que, a continuación, se utiliza para definir una animación de fotograma clave. Tenga en cuenta que la capa `Position` se establece en el valor final de la `Position` de la animación. Sin esto, la capa de forma brusca volvería a su `Position` antes de la animación porque la animación sólo cambia el valor de presentación y no el valor real del modelo. Estableciendo el valor de modelo para el valor final de la animación, la capa permanece al final de la animación.

Las capturas de pantalla siguientes muestran la capa que contenga la animación de imagen a través de la ruta de acceso especificada:

 ![](core-animation-images/12-explicit-animation.png "Esta captura de pantalla muestra la capa que contenga la animación de imagen a través de la ruta de acceso especificada")
 
## <a name="summary"></a>Resumen

En este artículo explicamos las capacidades de animación que se proporciona a través de la *animación básica* marcos de trabajo. Hemos visto la animación básica, que muestra cómo trasmisión con animaciones en UIKit tanto cómo se puede utilizar directamente para el control de animación de nivel inferior.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de animación de Core](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Gráficos básicos](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Gráficos y animación tutorial](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animación básica](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
