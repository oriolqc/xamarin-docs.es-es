---
title: Introducción a iOS 7
description: Este artículo tratan las nuevas API principales introducidas en iOS 7, incluidas las transiciones de View Controller, mejoras a las animaciones UIView, UIKit Dynamics y Kit de texto. También tratan algunos de los cambios realizados en la interfaz de usuario y las nuevas capacidades de multitarea mejorada.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 9ae82eba78f099f675d21bf53a250923630a0ff6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-ios-7"></a>Introducción a iOS 7

_Este artículo tratan las nuevas API principales introducidas en iOS 7, incluidas las transiciones de View Controller, mejoras a las animaciones UIView, UIKit Dynamics y Kit de texto. También tratan algunos de los cambios realizados en la interfaz de usuario y las nuevas capacidades de multitarea mejorada._

iOS 7 es una actualización importante para iOS. Incluye un diseño de la interfaz de usuario completamente nueva que coloca el foco en chrome contenido en lugar de aplicación. Junto con los cambios visuales, iOS 7 agrega una gran cantidad de nuevas API para crear experiencias e interacciones más ricas. Este documento las nuevas tecnologías introducidos con iOS 7 y encuestas actúa como punto de partida para la exploración aún más.

## <a name="uiview-animation-enhancements"></a>Mejoras de animación UIView

iOS 7 aumenta la compatibilidad de animación en UIKit, permita que las aplicaciones realizar acciones que anteriormente requerían al colocar directamente en el marco de trabajo de animación de núcleo. Por ejemplo, `UIView` ahora puede realizar animaciones de tipo muelle, así como las animaciones de fotogramas clave, que anteriormente un `CAKeyframeAnimation` aplicado a un `CALayer`.

### <a name="spring-animations"></a>Animaciones de primavera

 `UIView` Ahora es compatible con los cambios de propiedades de animación con un efecto elástico. Para agregar este, llame a la `AnimateNotify` o `AnimateNotifyAsync` método, pasando los valores de la proporción de cambios de contraseñas de primavera y el progreso del muelle inicial, tal y como se describe a continuación:

-  `springWithDampingRatio` : Un valor entre 0 y 1, donde la oscilación aumenta para el valor más pequeño.
-  `initialSpringVelocity` : La velocidad de primavera inicial como un porcentaje de la distancia total para la animación por segundo.


El código siguiente produce un efecto de primavera cuando cambia el centro de la vista de la imagen:

```csharp
void AnimateWithSpring ()
{ 
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;
    
    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {
    
        imageView.Center = new CGPoint (imageView.Center.X, 400);   
            
    }, null);
}
```

Este efecto elástico hace que la vista de la imagen parezca que rebota cuando éste completa la animación a una nueva ubicación de centro, como se muestra a continuación:

 ![](images/spring-animation.png "Este efecto elástico hace que la vista de la imagen parezca que rebota cuando éste completa la animación a una nueva ubicación de centro")

### <a name="keyframe-animations"></a>Animaciones de fotogramas clave

El `UIView` clase ahora incluye la `AnimateWithKeyframes` método para crear animaciones de fotogramas clave en un `UIView`. Este método es similar a otro `UIView` métodos de animación, excepto en que más `NSAction` se pasa como un parámetro para incluir los fotogramas clave. En el `NSAction`, los fotogramas clave se agregan mediante una llamada a `UIView.AddKeyframeWithRelativeStartTime`.

Por ejemplo, el fragmento de código siguiente crea una animación de fotogramas clave para animar centro de la vista, así como para rotar la vista:

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => { 
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => { 
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

Los dos primeros parámetros para la `AddKeyframeWithRelativeStartTime` método especificar la hora de inicio y la duración del fotograma clave, respectivamente, como un porcentaje de la longitud total de la animación. En el ejemplo anterior, el resultado en la imagen vista si se anima a su centro nuevo con el primer, segundo, seguido de la rotación de 90 grados en el siguiente segundo. Puesto que la animación especifica `UIViewKeyframeAnimationOptions.Autoreverse` como una opción, ambos fotogramas clave animar en orden inverso también. Por último, se establecen los valores finales en el estado inicial en el controlador de finalización.

Las capturas de pantalla siguiente muestran la animación combinada a través de los fotogramas clave:

 ![](images/keyframes.png "Este capturas de pantalla muestran la animación combinada a través de los fotogramas clave")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics es un nuevo conjunto de API de UIKit que permiten a las aplicaciones crear interacciones animadas en función de física. UIKit Dynamics encapsula un motor 2D física para que esto sea posible.

La API es declarativa por naturaleza. Declare el comportan de las interacciones de física mediante la creación de objetos - denominados *comportamientos* : para conceptos de express física, como la gravedad, colisiones, springs, etcetera. A continuación, adjunte el behavior(s) a otro objeto, denominado un *animación dinámica*, que encapsula una vista. La animación dinámica toma se ocupa de aplicar los comportamientos de ciencias físicas declarado para *elementos dinámicos* -elementos que implementan `IUIDynamicItem`, como un `UIView`.

Hay varios comportamientos primitivos diferentes disponibles para desencadenar interacciones complejas, incluidas:

-  `UIAttachmentBehavior` – Asocia los dos elementos dinámicos, que se muevan juntos o asocia un elemento dinámico a un punto de conexión.
-  `UICollisionBehavior` : Permite elementos dinámicos para participar en las colisiones.
-  `UIDynamicItemBehavior` : Especifica un conjunto de propiedades que se aplican a los elementos dinámicos, como elasticidad, la densidad y la fricción general.
-  `UIGravityBehavior` -Gravedad se aplica a un elemento dinámico, haciendo que los elementos acelerar en la dirección gravitacional.
-  `UIPushBehavior` – Force se aplica a un elemento dinámico.
-  `UISnapBehavior` : Permite que se ajustan a una posición con un efecto de primavera un elemento dinámico.


Aunque hay muchos tipos primitivos, el proceso general para agregar las interacciones basadas en física a una vista con UIKit Dynamics sea coherente en comportamientos:

1.  Crear una animación dinámica.
1.  Crear behavior(s).
1.  Agregar comportamientos a la animación dinámica.


### <a name="dynamics-example"></a>Ejemplo de Dynamics

Echemos un vistazo a un ejemplo que agrega la gravedad y un límite de colisión para un `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

Agregando gravedad a una vista de la imagen sigue los 3 pasos descritos anteriormente.

Trabajaremos la `ViewDidLoad` método para este ejemplo. En primer lugar, agregue un `UIImageView` instancia como sigue:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Esto crea una vista de imagen centrada en el borde superior de la pantalla. Para hacer que la imagen "caída" con la gravedad, cree una instancia de un `UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

El `UIDynamicAnimator` toma una instancia de una referencia `UIView` o `UICollectionViewLayout`, que contiene los elementos que se animará por la behavior(s) adjunto.

A continuación, cree un `UIGravityBehavior` instancia. Puede pasar uno o más objetos que implementan la `IUIDynamicItem`, como un `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

El comportamiento se pasa una matriz de `IUIDynamicItem`, que en este caso contiene el único `UIImageView` instancia se está animando.

Finalmente, agregue el comportamiento a la animación dinámica:

```csharp
dynAnimator.AddBehavior (gravity);
```

Esto resulta en la imagen de animación hacia abajo con gravedad, como se muestra a continuación:

![](images/gravity2.png "La ubicación inicial de la imagen") 
![](images/gravity3.png "la ubicación de la imagen final")

Puesto que no hay nada al restringir los límites de la pantalla, la vista de la imagen se encuentra simplemente la parte inferior. Para limitar la vista para que la imagen está en conflicto con los bordes de la pantalla, podemos agregar una `UICollisionBehavior`. Hablaremos sobre esto en la sección siguiente.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Comenzaremos creando un `UICollisionBehavior` y éste se agrega a la animación dinámica, al igual que hicimos con la `UIGravityBehavior`.

Modifique el código para incluir el `UICollisionBehavior`:

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

El `UICollisionBehavior` tiene una propiedad denominada `TranslatesReferenceBoundsIntoBoundry`. Si se establece en `true` hace que la referencia de los límites de la vista que se usará como un límite de colisión.

Ahora, cuando la imagen se anima hacia abajo con gravedad, rebota ligeramente la parte inferior de la pantalla antes de establecerse para rest no existe.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Podemos controlar aún más el comportamiento de la vista de la imagen está reduciendo con comportamientos adicionales. Por ejemplo, podríamos agregar un `UIDynamicItemBehavior` para aumentar la elasticidad, haciendo que la vista de imagen salten más al que está en conflicto con la parte inferior de la pantalla.

Agregar un `UIDynamicItemBehavior` sigue los mismos pasos al igual que con los otros comportamientos. Primero, cree el comportamiento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

A continuación, agregue el comportamiento de la animación dinámica:

 `dynAnimator.AddBehavior (dynBehavior);`

Con este comportamiento en su lugar, la vista de la imagen más rebota cuando está en conflicto con el límite.

## <a name="general-user-interface-changes"></a>Cambios en la interfaz de usuario general

Además de las nuevas APIs UIKit como UIKit Dynamics y transiciones de controlador, animaciones de UIView mejoradas que se ha descrito anteriormente, iOS 7 presenta una variedad de cambios visuales en la interfaz de usuario y cambios en la API para varias vistas y los controles relacionados. Para obtener más información, consulte el [iOS 7 introducción de la interfaz de usuario](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit de texto

Kit de texto es una nueva API que ofrece características de diseño y la representación de texto eficaz. Se basa en el marco del texto principal de nivel inferior, pero es mucho más fácil de usar que texto principal.

Para obtener más información, vea nuestra [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitarea

iOS 7 cambia cuándo y cómo se realiza el trabajo en segundo plano. Finalización de la tarea de iOS 7 ya no mantiene activo aplicaciones cuando las tareas se ejecutan en segundo plano y las aplicaciones se hayan activado para el procesamiento de una manera no contiguas en segundo plano. iOS 7 también agrega tres nuevas API para actualizar las aplicaciones con el nuevo contenido en segundo plano:

-  Captura de fondo: permite que las aplicaciones para actualizar el contenido en segundo plano a intervalos regulares.
-  Notificaciones remotas - permite a las aplicaciones actualizar el contenido al recibir una notificación de inserción. Las notificaciones pueden ser silenciosa o puede mostrar un encabezado en la pantalla de bloqueo.
-  Servicio de transferencia: permite cargar y descargar de datos, como archivos de gran tamaño, sin un límite de tiempo fijo en segundo plano.


Para obtener más detalles sobre las nuevas capacidades de multitarea, consulte las secciones de iOS de la Xamarin [Backgrounding guía](~/ios/app-fundamentals/backgrounding/index.md).

## <a name="summary"></a>Resumen

Este artículo tratan varias nuevas adiciones principales para iOS. En primer lugar, muestra cómo agregar transiciones personalizadas a los controladores de la vista. A continuación, muestra cómo utilizar las transiciones en las vistas de colección, tanto desde dentro de un controlador de navegación, así como de forma interactiva entre las vistas de colección. A continuación, presenta varias mejoras realizadas a las animaciones UIView, que muestra cómo las aplicaciones usan UIKit cosas que anteriormente requerían programar directamente en la animación de núcleo. Por último, se introdujo nuevas API de Dynamics UIKit, que ofrece un motor de físico a UIKit, junto con la compatibilidad de texto enriquecido ahora disponible en el marco de trabajo de Kit de texto.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)
