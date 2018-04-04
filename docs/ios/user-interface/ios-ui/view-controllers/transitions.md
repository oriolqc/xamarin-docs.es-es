---
title: Transiciones de controlador de vista
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 253a2556872ea9186eec832a84f4cfff9cb1c209
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="view-controller-transitions"></a>Transiciones de controlador de vista

UIKit agrega compatibilidad con la personalización de la transición animada que se produce cuando se presentan los controladores de la vista. Esta compatibilidad se incluye con controladores integrados, así como todos los controladores personalizados que heredan directamente de `UIViewController`. Además, `UICollectionViewController` aprovecha las ventajas de la personalización de la transición de controlador para aprovechar las transiciones animadas en diseños de la vista de colección.

## <a name="custom-transitions"></a>Transiciones personalizadas

La transición animada entre los controladores de la vista de iOS 7 es totalmente personalizable. `UIViewController` ahora incluye un `TransitioningDelegate` propiedad que proporciona una clase de animación personalizada en el sistema cuando se produce una transición.

Para usar una transición personalizada con `PresentViewController`:

1.  Establecer el `ModalPresentationStyle` a `UIModalPresentationStyle.Custom` en el controlador que se van a presentar.
2.  Implemente `UIViewControllerTransitioningDelegate` para crear una clase de animación, que es una instancia de `UIViewControllerAnimatedTransitioning` .
3.  Establecer el `TransitioningDelegate` propiedad a una instancia de `UIViewControllerTransitioningDelegate` , también en el controlador que se van a presentar.
4.  Presentar el controlador de vista.


Por ejemplo, el código siguiente muestra un controlador de vista de tipo `ControllerTwo` - a `UIViewController` subclase:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

Ejecutar la aplicación y pulsar el botón hace que la animación predeterminada de la vista del segundo controlador para la animación en la parte inferior, tal y como se muestra a continuación:

 ![](transitions-images/no-custom-transition.png "Ejecutar la aplicación y pulsar el botón hace que la animación de valor predeterminado de la segunda vista de controladores a la animación en la parte inferior")

Sin embargo, establecer el `ModalPresentationStyle` y `TransitioningDelegate` da como resultado una animación personalizada para la transición:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom;
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

El `TransitioningDelegate` es responsable de crear una instancia de la `UIViewControllerAnimatedTransitioning` subclase - denominado `CustomAnimator` en el ejemplo siguiente:

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

Cuando realiza la transición, el sistema crea una instancia de `IUIViewControllerContextTransitioning`, que pasa a los métodos de la animación. `IUIViewControllerContextTransitioning` contiene el `ContainerView` donde se produce la animación, así como el controlador de vista iniciar la transición y el controlador de vista que se ha pasado a.

La `UIViewControllerAnimatedTransitioning` clase controla la animación real. Se deben implementar dos métodos:

1.  `TransitionDuration` : devuelve la duración de la animación en segundos.
1.  `AnimateTransition` : realiza la animación real.


Por ejemplo, la siguiente clase implementa `UIViewControllerAnimatedTransitioning` para animar el marco de la vista del controlador:

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
        {
            var inView = transitionContext.ContainerView;
            var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
            var toView = toVC.View;

            inView.AddSubview (toView);

            var frame = toView.Frame;
            toView.Frame = CGRect.Empty;

            UIView.Animate (TransitionDuration (transitionContext), () => {
                toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
            }, () => {
                transitionContext.CompleteTransition (true);
            });
        }
}
```

Ahora, cuando el botón que se derivan, la animación se implementa en el `UIViewControllerAnimatedTransitioning` se utiliza la clase:

 ![](transitions-images/custom-transition.png "Un ejemplo del zoom en vigor ejecutando")

## <a name="collection-view-transitions"></a>Transiciones de vista de colección

Vistas de colección tienen compatibilidad integrada para la creación de transiciones animadas:

-  **Controladores de navegación** : el animar la transición entre dos `UICollectionViewController` instancias opcionalmente se pueden controlar automáticamente cuando un `UINavigationController` administra.
-  **Diseño de la transición** : un nuevo `UICollectionViewTransitionLayout` permite interactiva de realizar la transición entre los diseños de clase.


### <a name="navigation-controller-transitions"></a>Transiciones de controlador de navegación

Cuando se utiliza dentro de un controlador de navegación, un `UICollectionViewController` incluye compatibilidad con transiciones animadas entre los controladores. Esta compatibilidad está integrada y requiere solo unos pocos pasos sencillos para implementar:

1.  Establecer `UseLayoutToLayoutNavigationTransitions` a `false` en un `UICollectionViewController` .
1.  Debe agregar una instancia de la `UICollectionViewController` a la raíz de la pila del controlador de navegación.
1.  Cree un segundo `UICollectionViewController` y establecer su `UseLayoutToLayoutNavigtionTransitions` propiedad `true` .
1.  Insertar el segundo `UICollectionViewController` en pila del controlador de navegación.


El código siguiente agrega un `UICollectionViewController` subclase denominada `ImagesCollectionViewController` a la raíz de la pila del controlador de navegación, con el `UseLayoutToLayoutNavigationTransitions` propiedad establecida en `false`:

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false;
        }

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();
    
    return true;
}
```

Cuando se selecciona un elemento, una segunda instancia de la `ImagesController` se crea, pero esta vez utilizando una clase de un diseño diferente. Para este controlador, `UseLayoutToLayoutNavigtionTransitions` se establece en `true`, tal y como se muestra a continuación:

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
        circleLayout = new CircleLayout (Monkeys.Instance.Count){
                ItemSize = new CGSize (100, 100)
            };
            
    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true;
        }

    NavigationController.PushViewController (controller2, true);
}
```

El `UseLayoutToLayoutNavigationTransitions` propiedad debe establecerse antes de agregar el controlador a la pila de navegación. Con este conjunto de propiedades, la transición deslizante horizontal normal se reemplaza con una transición animada entre los diseños de los dos controladores, como se muestra a continuación:

![](transitions-images/nav2.png "Una transición animada entre los diseños de los dos controladores")

### <a name="transition-layout"></a>Diseño de transición

Además de admitir la transición diseño dentro de los controladores de navegación, llama a un nuevo diseño `UICollectionViewTransitionLayout` ahora está disponible. Esta clase de diseño permite el control interactivo durante el proceso de transición de diseño, por lo que permite el `TransitionProgress` establecer desde el código. `UICollectionViewTransitionLayout` es distinto de - y no un reemplazo para - la `SetCollectionViewLayout` método de iOS 6 que desencadenó una transición de diseño animados. Ese método no proporcionó compatibilidad integrada para controlar el progreso de la transición animada.

 `UICollectionViewTransitionLayout` Por ejemplo, permite un reconocedor de movimiento a configurarse para controlar la transición entre los diseños en respuesta a la interacción del usuario, debido a que administra el diseño original, así como el diseño previsto pase al.

Los pasos para implementar una transición interactiva dentro de un reconocedor de movimiento mediante `UICollectionViewTransitionLayout` son los siguientes:

1.  Crear un reconocedor de movimiento.
1.  Llame a la `StartInteractiveTransition` método de la `UICollectionView` , pasándole el diseño de destino y un controlador de finalización.
1.  Establecer el `TransitionProgress` propiedad de la `UICollectionViewTransitionLayout` instancia devuelta desde el `StartInteractiveTransition` método.
1.  Invalidar el diseño.
1.  Llame a la `FinishInteractiveTransition` método de la `UICollectionView` para completar la transición o `CancelInteractiveTransition` método para cancelarla.  `FinishInteractiveTransition` hace que la animación completar su transición a la distribución de destino, mientras que `CancelInteractiveTransition` da como resultado la animación devolver al diseño original.
1.  Administrar la realización de transición en el controlador de finalización de la `StartInteractiveTransition` método.
1.  Agregue el reconocedor de movimiento a la vista de colección.


El código siguiente implementa una transición de diseño interactivo dentro de un reconocedor de gesto de alejar:

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {   
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

Como el usuario pinches la vista de colección, el `TransitionProgress` se establece en relación con la escala de los gestos. En esta implementación, si el usuario finaliza la acercar los dedos antes de que la transición es 50% completado, se cancela la transición. En caso contrario, finaliza la transición.




## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)
