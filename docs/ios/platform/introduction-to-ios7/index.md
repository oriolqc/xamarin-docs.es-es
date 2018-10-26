---
title: Introducción a iOS 7
description: En este artículo se trata las principales nuevas API incluidas en iOS 7, incluidas las transiciones de controlador de vista, las mejoras a las animaciones UIView, UIKit Dynamics y el Kit de texto. También se tratan algunos de los cambios en la interfaz de usuario y las nuevas capacidades de multitarea mejorada.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: db2ce779962947e2121ff03280544a080e193e2e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118050"
---
# <a name="introduction-to-ios-7"></a>Introducción a iOS 7

_En este artículo se trata las principales nuevas API incluidas en iOS 7, incluidas las transiciones de controlador de vista, las mejoras a las animaciones UIView, UIKit Dynamics y el Kit de texto. También se tratan algunos de los cambios en la interfaz de usuario y las nuevas capacidades de multitarea mejorada._

iOS 7 es una actualización principal para iOS. Presenta un diseño de la interfaz de usuario completamente nueva que coloca el foco en el contenido en lugar de aplicación de chrome. Junto con el objeto visual cambia, iOS 7 agrega una gran cantidad de nuevas API para crear experiencias e interacciones más completas. Este encuestas documento las nuevas tecnologías incorporadas con iOS 7 y sirve como punto de partida para la exploración más a fondo.

## <a name="uiview-animation-enhancements"></a>Mejoras de animación UIView

iOS 7 amplía la compatibilidad de animación en UIKit, permitiendo a las aplicaciones hacer cosas que anteriormente era necesario colocar directamente en el marco de animación básica. Por ejemplo, `UIView` ahora puede realizar animaciones de muelle, así como animaciones de fotogramas clave, que anteriormente un `CAKeyframeAnimation` aplicado a un `CALayer`.

### <a name="spring-animations"></a>Animaciones de muelle

 `UIView` ahora admite cambios de propiedades de animación con un efecto de spring. Para agregar esto, llamar a la `AnimateNotify` o `AnimateNotifyAsync` método, pasando los valores de amortiguamiento de la primavera y la velocidad inicial de spring, tal como se describe a continuación:

-  `springWithDampingRatio` : Un valor entre 0 y 1, donde la oscilación aumenta para el valor más pequeño.
-  `initialSpringVelocity` : La velocidad inicial de spring como un porcentaje de la distancia total para la animación por segundo.


El código siguiente produce un efecto spring cuando cambia el centro de la vista de imagen:

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

Este efecto spring hace que la imagen parezca que rebota cuanto finaliza la animación a una nueva ubicación de centro, como se muestra a continuación:

 ![](images/spring-animation.png "Este efecto spring hace que la imagen parezca que rebota en cuanto finaliza la animación a una nueva ubicación de centro")

### <a name="keyframe-animations"></a>Animaciones de fotogramas clave

El `UIView` clase ahora incluye la `AnimateWithKeyframes` método para crear animaciones de fotogramas clave en un `UIView`. Este método es similar a otros `UIView` métodos de animación, excepto en que más `NSAction` se pasa como un parámetro para incluir los fotogramas clave. Dentro de la `NSAction`, se agregan los fotogramas clave mediante una llamada a `UIView.AddKeyframeWithRelativeStartTime`.

Por ejemplo, el fragmento de código siguiente crea una animación de fotograma clave para animar el centro de una vista, así como para girar la vista:

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

Los dos primeros parámetros para el `AddKeyframeWithRelativeStartTime` método especificar la hora de inicio y la duración del fotograma clave, respectivamente, como un porcentaje de la longitud total de la animación. El ejemplo anterior, el resultado en la imagen vista si se anima a su centro de nuevo con el primer, segundo, seguido de la rotación de 90 grados en el siguiente segundo. Puesto que la animación especifica `UIViewKeyframeAnimationOptions.Autoreverse` como opción, ambos fotogramas clave animar en orden inverso también. Por último, los valores finales se establecen en el estado inicial en el controlador de finalización.

Las capturas de pantalla siguiente muestran la animación combinada a través de los fotogramas clave:

 ![](images/keyframes.png "Este capturas de pantalla muestran la animación combinada a través de los fotogramas clave")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics es un nuevo conjunto de API en UIKit que permiten a las aplicaciones crear interacciones animadas en función de leyes físicas. UIKit Dynamics encapsula un motor de físicas 2D para que esto sea posible.

La API es declarativa por naturaleza. Declarar el comportamiento de las interacciones de leyes físicas mediante la creación de objetos - denominados *comportamientos* : conceptos de física rápida como la gravedad, colisiones, springs, etcetera. A continuación, adjunte el sus propios comportamientos a otro objeto, llama a un *animador dinámica*, que encapsula una vista. El animador dinámico toma se ocupa de aplicar los comportamientos de leyes físicas declarado a *elementos dinámicos* -elementos que implementan `IUIDynamicItem`, como un `UIView`.

Hay varios comportamientos primitivos diferentes disponibles para desencadenar interacciones complejas, incluidas:

-  `UIAttachmentBehavior` – Asocia los dos elementos dinámicos, que se muevan juntos o asocia un elemento dinámico a un punto de conexión.
-  `UICollisionBehavior` : Permite que los elementos dinámicos participar en colisiones.
-  `UIDynamicItemBehavior` : Especifica un conjunto general de propiedades que se aplicarán a los elementos dinámicos, como elasticidad, la densidad y fricción.
-  `UIGravityBehavior` -Gravedad se aplica a un elemento dinámico, causando elementos acelerar en la dirección gravitacional.
-  `UIPushBehavior` – Force se aplica a un elemento dinámico.
-  `UISnapBehavior` : Permite un elemento dinámico que se ajuste a una posición con un efecto de spring.


Aunque hay muchas primitivas, el proceso general para agregar las interacciones basadas en física a una vista con UIKit Dynamics es coherente entre los comportamientos:

1.  Crear una animación dinámica.
1.  Cree sus propios comportamientos.
1.  Agregar comportamientos a la animación dinámica.


### <a name="dynamics-example"></a>Ejemplo de Dynamics

Veamos un ejemplo que agrega la gravedad y un límite de colisión para un `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

Adición de gravedad a una vista de imagen sigue los 3 pasos descritos anteriormente.

Vamos a trabajar el `ViewDidLoad` método para este ejemplo. En primer lugar, agregue un `UIImageView` instancia como sigue:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Esto crea una vista de imagen centrada en el borde superior de la pantalla. Para que la imagen "caída" con la gravedad, cree una instancia de un `UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

El `UIDynamicAnimator` toma una instancia de una referencia `UIView` o `UICollectionViewLayout`, que contiene los elementos que se animará por la sus propios comportamientos adjunto.

A continuación, cree un `UIGravityBehavior` instancia. Puede pasar uno o más objetos que implementan la `IUIDynamicItem`, como un `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

El comportamiento se pasa una matriz de `IUIDynamicItem`, en este caso, que contiene el tipo single `UIImageView` nos estamos animar de instancia.

Finalmente, agregue el comportamiento a la animación dinámica:

```csharp
dynAnimator.AddBehavior (gravity);
```

Esto da como resultado la imagen hacia abajo animar con gravedad, como se muestra a continuación:

![](images/gravity2.png "La ubicación de la imagen inicial") 
![](images/gravity3.png "la ubicación de la imagen final")

Dado que no hay nada que restringe los límites de la pantalla, la vista de imagen decaiga simplemente la parte inferior. Para restringir la vista para que la imagen está en conflicto con los bordes de la pantalla, podemos agregar un `UICollisionBehavior`. Hablaremos sobre esto en la sección siguiente.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Empezaremos creando un `UICollisionBehavior` y agregarlo a la animación dinámica, tal como se hizo el `UIGravityBehavior`.

Modificar el código para incluir el `UICollisionBehavior`:

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

Ahora, cuando la imagen se anima hacia abajo con gravedad, rebota ligeramente fuera de la parte inferior de la pantalla antes de fijar para rest no existe.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Podemos controlar aún más el comportamiento de la vista de imagen bajan con comportamientos adicionales. Por ejemplo, podríamos agregar un `UIDynamicItemBehavior` para aumentar la elasticidad, provocando la vista de imagen más rebote cuando choca con la parte inferior de la pantalla.

Agregar un `UIDynamicItemBehavior` sigue los mismos pasos al igual que con los otros comportamientos. Primero debe crear el comportamiento:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

A continuación, agregue el comportamiento de la animación dinámica:

 `dynAnimator.AddBehavior (dynBehavior);`

Con este comportamiento en su lugar, la vista de imagen más rebota cuando choca con el límite.

## <a name="general-user-interface-changes"></a>Cambios en la interfaz de usuario general

Además de las nuevas APIs UIKit como UIKit Dynamics, controlador transiciones y animaciones de UIView mejoradas que se ha descrito anteriormente, iOS 7 presenta una variedad de cambios visuales en la interfaz de usuario y cambios relacionados en la API para varias vistas y los controles. Para obtener más información, consulte el [iOS 7 información general de la interfaz de usuario](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit de texto

Kit de texto es una nueva API que ofrece características de diseño y la representación de texto eficaz. Se basa en el marco de texto de núcleo de bajo nivel, pero es mucho más fácil de usar que el texto principal.

Para obtener más información, consulte nuestra [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitarea

iOS 7 cambia cuándo y cómo se realiza el trabajo en segundo plano. Finalización de la tarea en iOS 7 ya no mantiene las aplicaciones activo cuando se ejecutan las tareas en segundo plano y las aplicaciones se hayan activado para el procesamiento de manera que no son contiguas en segundo plano. iOS 7 también agrega tres nuevas API para actualizar las aplicaciones con nuevo contenido en segundo plano:

-  Captura de fondo: permite que las aplicaciones para actualizar el contenido en segundo plano a intervalos regulares.
-  Notificaciones remotas: permite que las aplicaciones actualizar el contenido cuando se recibe una notificación de inserción. Las notificaciones pueden ser silenciosa o puede mostrar un banner en la pantalla de bloqueo.
-  Servicio de transferencia en segundo plano: permite que carga y descarga de datos, como archivos de gran tamaño, sin un límite de tiempo fijo.


Para obtener más detalles sobre las nuevas capacidades de multitarea, consulte las secciones de iOS de Xamarin [Guía del procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md).

## <a name="summary"></a>Resumen

En este artículo se trata varias adiciones nuevas principales para iOS. En primer lugar, muestra cómo agregar transiciones personalizadas a los controladores de vista. A continuación, muestra cómo usar las transiciones en las vistas de colección, tanto desde dentro de un controlador de navegación, así como de forma interactiva entre las vistas de colección. A continuación, presenta varias mejoras realizadas a las animaciones UIView, que muestra cómo las aplicaciones usan UIKit para los elementos que anteriormente requerían programar directamente con animación básica. Por último, se introduce la nueva API de Dynamics UIKit, que ofrece un motor de leyes físicas en UIKit, junto con la compatibilidad de texto enriquecido ahora disponible en el marco del Kit de texto.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)
