---
title: "Animación de núcleo"
description: "Este artículo examina el marco de trabajo de animación de núcleo, que muestra cómo habilita fluidos animaciones en UIKit, así como el modo de alto rendimiento para usarlo directamente para el control de animación de nivel inferior."
ms.topic: article
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: f0cb4e00abffead854c2590bde6df45c200ff0bb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="core-animation"></a>Animación de núcleo

_Este artículo examina el marco de trabajo de animación de núcleo, que muestra cómo habilita fluidos animaciones en UIKit, así como el modo de alto rendimiento para usarlo directamente para el control de animación de nivel inferior._

iOS incluye [ *Core animación* ](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) para proporcionar compatibilidad de animación de vistas en la aplicación.
Todas las animaciones y fluidas en iOS, como el desplazamiento de tablas y Deslizar rápidamente entre diferentes vistas de realizan, así como lo hacen porque se basan en Core animación internamente.

Los marcos de animación de núcleo y los gráficos esenciales pueden trabajar juntos para crear atractivas, animar gráficos 2D. De hecho Core animación puede incluso transformar los gráficos 2D en un espacio 3D, crear experiencias increíbles cinematográficas. Sin embargo, para crear gráficos 3D es true, se debe usar algo como OpenGL ES, o para activar de juegos a una API como MonoGame, aunque 3D queda fuera del ámbito de este artículo.

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Animación de núcleo

iOS utiliza el marco de trabajo de animación de núcleo para crear efectos de animación, como la transición entre las vistas de deslizamiento de menús y desplazamiento efectos para nombrar algunos. Hay dos maneras de trabajar con animación:

- [A través de UIKit](#Using_UIKit_Animation), que incluye las animaciones basadas en vistas, así como transiciones animadas entre los controladores.
- [A través de la animación de núcleo](#Using_Core_Animation), las capas directamente, que permite un control más precisa.

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>Con UIKit animación

UIKit proporciona varias características que facilitan la agregar animación a una aplicación. Si bien utiliza animación Core internamente, abstrae, para que funcione sólo con controladores y vistas.

En esta sección se describe las características de animación de UIKit incluidos:

-  Transiciones entre los controladores
-  Transiciones entre las vistas
-  Animación de la propiedad de vista


### <a name="view-controller-transitions"></a>Transiciones de controlador de vista

 `UIViewController` proporciona compatibilidad integrada para la transición entre los controladores de vista a través de la `PresentViewController` método. Cuando se usa `PresentViewController`, opcionalmente se puede animar la transición al segundo controlador.

Por ejemplo, considere la posibilidad de una aplicación con dos controladores, donde se llame a tocar un botón en el primer controlador de `PresentViewController` para mostrar un segundo controlador. Para controlar la animación de transición se usa para mostrar el segundo controlador, basta con establecer su [ `ModalTransitionStyle` ](https://developer.xamarin.com/api/type/UIKit.UIModalTransitionStyle/) propiedad tal y como se muestra a continuación:

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

En este caso un `PartialCurl` se usa la animación, aunque algunas de las que están disponibles, incluido:

-  `CoverVertical` : Diapositivas seguridad de la parte inferior de la pantalla
-  `CrossDissolve` : La vista anterior se atenúa & intensifica la nueva vista
-  `FlipHorizontal` -Voltear a horizontal de derecha a izquierda. En despido la transición voltea izquierda a derecha.


Para animar la transición, pasar `true` como segundo argumento de `PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

Captura de pantalla siguiente muestra el aspecto de la transición el `PartialCurl` caso:

 ![](core-animation-images/06-view-transitions.png "Esta captura de pantalla muestra la transición de PartialCurl")

### <a name="view-transitions"></a>Transiciones de vista

Además de las transiciones entre los controladores, UIKit también admite la animación transiciones entre las vistas para intercambiar una vista por otra cosa.

Por ejemplo, supongamos tenía un controlador con `UIImageView`, donde puntee en la imagen debe mostrar un segundo `UIImageView`. Para animar la imagen es tan sencillo como llamar a supervista de la vista para realizar la transición a la segunda vista de imagen `UIView.Transition`, pasándole el `toView` y `fromView` tal y como se muestra a continuación:

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` También toma un `duration` parámetro que controla cuánto tiempo se ejecuta la animación, así como [ `options` ](https://developer.xamarin.com/api/type/UIKit.UIViewAnimationOptions/) para especificar elementos como la animación que se usará y la función de aceleración. Además, puede especificar un controlador de finalización que se llamará cuando se completa la animación.

La captura de pantalla siguientes se muestra la transición animada entre la imagen de vistas si `TransitionFlipFromTop` se utiliza:

 ![](core-animation-images/07-animated-transition.png "Esta captura de pantalla muestra la transición entre las vistas de la imagen cuando se utiliza TransitionFlipFromTop animada")

### <a name="view-property-animations"></a>Animaciones de propiedad de vista

UIKit admite una variedad de propiedades de la animación en la `UIView` clase de forma gratuita, incluidos:

-  Fotograma
-  Límites
-  Centrar
-  Alpha
-  Transformación
-  Color


Estas animaciones hacer de forma implícita mediante la especificación de cambios de propiedad en un `NSAction` delegado pasado a estático `UIView.Animate` método. Por ejemplo, el código siguiente anima el punto central de un `UIImageView`:

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

Esto resulta en una imagen animar y hacia atrás en la parte superior de la pantalla, tal y como se muestra a continuación:

 ![](core-animation-images/08-animate-center.png "Una imagen animar y hacia atrás en la parte superior de la pantalla como la salida")

Al igual que con la `Transition` método `Animate` permite la duración establecer, junto con la función de aceleración. En este ejemplo también utilizado el `UIViewAnimationOptions.Autoreverse` opción, que hace que la animación animar desde el valor nuevo a la inicial. Sin embargo, el código también establece el `Center` a su valor inicial en un controlador de finalización. Mientras que una animación es interpolar valores de propiedad con el tiempo, el valor real del modelo de la propiedad es siempre el valor final que se ha establecido. En este ejemplo, el valor es un punto cercano a la derecha de la supervista. Sin establecer el `Center` hasta el punto inicial, que es donde se completa la animación debido a la `Autoreverse` que se va a establecer, la imagen podría se ciñen a la derecha una vez completada la animación, tal y como se muestra a continuación:

 ![](core-animation-images/09-animation-complete.png "Sin establecer el centro hasta el punto inicial, la imagen se ciñen a la derecha una vez completada la animación")

## <a name="using-core-animation"></a>Uso de animación de núcleo

 `UIView` animaciones de permiten una gran cantidad de capacidad y deben usarse si es posible debido a la facilidad de implementación. Como se mencionó anteriormente, animaciones de UIView usar el marco de animación de núcleo. Sin embargo, algunas acciones no se puede realizar con `UIView` animaciones, como la animación de propiedades adicionales que no se pueden animar con una vista o interpolar a lo largo de una ruta de acceso no lineal. En tales casos en los que tenga un control más preciso, animación de Core se puede usar directamente así.

### <a name="layers"></a>Capas

Cuando se trabaja con Core animación, animación lleva a cabo mediante *capas*, que son de tipo `CALayer`. Una capa es conceptualmente similar a una vista en que hay una jerarquía de la capa, mucho que hay una jerarquía de vista. En realidad, capas hacer copia de vistas, con la vista agrega compatibilidad para la interacción del usuario. Puede tener acceso a la capa de cualquier vista a través de la vista `Layer` propiedad. De hecho, el contexto se utiliza en `Draw` método `UIView` se crea en realidad de la capa. Internamente, la capa de realizar una copia de un `UIView` tiene su delegado establecido en la propia vista, que se llama `Draw`. Así, al dibujo a un `UIView`, que se está dibujando realmente a su capa.

Las animaciones de capa pueden ser implícita o explícita. Las animaciones implícitas son declarativas. Se declara simplemente lo que deben cambiar propiedades de las capas y funciona la animación. Por otro lado, animaciones explícitas se crean a través de una clase de animación que se agrega a una capa. Animaciones explícitas permiten agregar un control sobre cómo se crea una animación. En las secciones siguientes profundizan en animaciones implícitas y explícitas en mayor profundidad.

### <a name="implicit-animations"></a>Animaciones implícita

Una manera de animar las propiedades de una capa es a través de una animación implícita. `UIView` las animaciones creación animaciones implícita. Sin embargo, también puede crear animaciones implícita directamente en una capa así.

Por ejemplo, el código siguiente establece una capa `Contents` desde una imagen, establece un ancho de borde y el color, y se agrega como una subcapa del nivel de la vista de la capa:

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

Para agregar una animación implícita de la capa, que simplemente encapsulan cambios de propiedad en un `CATransaction`. Esto permite animar propiedades que no se pueden animar con una animación de la vista, como el `BorderWidth` y `BorderColor` tal y como se muestra a continuación:

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

Este código también anima la capa `Position`, que es la ubicación del punto de anclaje de la capa medido desde la parte superior izquierda de coordenadas del superlayer. El punto de anclaje de una capa es un punto normalizado en el sistema de coordenadas de la capa.

La siguiente ilustración muestra el punto de posición y el delimitador:

 ![](core-animation-images/10-postion-anchorpt.png "Esta ilustración muestra el punto de posición y el delimitador")

Cuando se ejecuta el ejemplo, el `Position`, `BorderWidth` y `BorderColor` animar tal y como se muestra en las capturas de pantalla siguiente:

 ![](core-animation-images/11-implicit-animation.png "Cuando se ejecuta el ejemplo, la posición, BorderWidth y BorderColor animación tal como se muestra")

### <a name="explicit-animations"></a>Animaciones explícitas

Además de las animaciones implícita, animación de Core incluye una serie de clases que heredan de `CAAnimation` que permiten encapsular las animaciones que, a continuación, se agregan explícitamente a una capa. Estos permiten un control preciso sobre animaciones, como modificar el valor inicial de una animación, animaciones de agrupación y especificar los fotogramas clave para permitir que las rutas de acceso no lineal.

El código siguiente muestra un ejemplo de una animación explícita mediante un `CAKeyframeAnimation` para la capa que se muestra anteriormente (en la sección animación implícita):

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

Este código cambia el `Position` de la capa mediante la creación de una ruta de acceso que, a continuación, se utiliza para definir una animación de fotogramas clave. Tenga en cuenta que la capa `Position` se establece en el valor final de la `Position` de la animación. Sin esto, la capa repentinamente devolvería a su `Position` antes de la animación como la animación solo cambia el valor de presentación y no el valor real del modelo. Al establecer el valor de modelo con el valor final de la animación, la capa permanecen en su lugar al final de la animación.

Las capturas de pantalla siguientes muestran la capa que contenga la imagen que se anima a través de la ruta de acceso especificada:

 ![](core-animation-images/12-explicit-animation.png "Esta captura de pantalla muestra la capa que contenga la imagen que se anima a través de la ruta de acceso especificada")
 
## <a name="summary"></a>Resumen

En este artículo analizamos las capacidades de animación proporcionadas a través de la *Core animación* marcos de trabajo. Se examina Core animación, que muestra cómo trabaja con animaciones en UIKit tanto cómo se puede usar directamente para el control de animación de nivel inferior.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de animación de núcleo](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Gráficos básicos](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Gráficos y animación tutorial](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animación básica](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
