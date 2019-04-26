---
title: Eventos de toque y gestos en Xamarin.iOS
description: Este documento describe cómo trabajar con eventos de toque, multitoque, gestos, varios movimientos y gestos personalizados en aplicaciones de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f7160c48e1b1ac85f4aa0173c0eb9f42b8fefca2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218635"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Eventos de toque y gestos en Xamarin.iOS

Es importante comprender los eventos de toque y las API de contacto en una aplicación de iOS, ya que son fundamentales para todas las interacciones con el dispositivo físicas. Todas las interacciones táctiles implican un `UITouch` objeto. En este artículo, obtendrá información sobre cómo usar el `UITouch` clase y sus API para admitir la interacción. Más adelante, se expandirá en nuestro conocimiento para obtener información sobre cómo admitir gestos.

## <a name="enabling-touch"></a>Habilitación de Touch

Los controles en `UIKit` son: las subclases de control: por lo que depende de la interacción del usuario que tienen los gestos integrados en UIKit y, por tanto, no es necesario habilitar la interacción táctil. Ya está habilitado.

Sin embargo, muchas de las vistas de `UIKit` no tiene interacción habilitada de forma predeterminada. Hay dos maneras de habilitar la interacción táctil en un control. La primera manera es activar la casilla habilitado de interacción de usuario en el panel de propiedades de iOS Designer, tal como se muestra en la siguiente captura de pantalla:

 [![](touch-in-ios-images/image1.png "Active la casilla habilitado de interacción de usuario en el panel de propiedades del Diseñador de iOS")](touch-in-ios-images/image1.png#lightbox)

También podemos usar un controlador para establecer el `UserInteractionEnabled` propiedad en true en un `UIView` clase. Esto es necesario si se crea la interfaz de usuario en el código.

La siguiente línea de código es un ejemplo:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventos de funciones táctiles

Hay tres fases de la función táctil que se producen cuando el usuario toca la pantalla, su dedo mueve o elimina su dedo. Estos métodos se definen en `UIResponder`, que es la clase base para UIView. iOS invalidará los métodos asociados en el `UIView` y `UIViewController` para controlar la interacción:

-  `TouchesBegan` : Esto se denomina cuando primero se toca la pantalla.
-  `TouchesMoved` : Esto se llama cuando la ubicación de los cambios táctil como el usuario esté desplazando los dedos alrededor de la pantalla.
-  `TouchesEnded` o `TouchesCancelled` – `TouchesEnded` se llama cuando los dedos del usuario se levantan fuera de la pantalla.  `TouchesCancelled` se llama si iOS cancela el touch, por ejemplo, si un usuario desliza sonará su dedo fuera de un botón para cancelar la acción de presionar una.


Toque los eventos de forma recursiva de viaje hacia abajo a través de la pila de UIViews, para comprobar si el evento de toque está dentro de los límites de un objeto de vista. Esto se suele denominar _la prueba de posicionamiento_. En primer lugar se llamará en el primer `UIView` o `UIViewController` y, a continuación, se llamará el `UIView` y `UIViewControllers` por debajo de ellas en la jerarquía de vistas.

Un `UITouch` se creará objeto cada vez que el usuario toca la pantalla. La `UITouch` objeto incluye datos sobre la entrada táctil, como cuando se produjo la entrada táctil, donde se produjo, si la entrada táctil era un dedo, etcetera. Los eventos de toque se pasan a una propiedad de un toque: un `NSSet` que contiene uno o varios toques. Podemos usar esta propiedad para obtener una referencia a una entrada táctil y determinar la respuesta de la aplicación.

Las clases que reemplazar uno de los eventos de toque, deben llamar primero a la implementación base y, a continuación, obtenga el `UITouch` objeto asociado al evento. Para obtener una referencia al primer toque, llame a la `AnyObject` propiedad y convertirla a una `UITouch` como se muestra en el ejemplo siguiente:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        //code here to handle touch
    }
}
```

iOS reconoce automáticamente las sucesivas rápido toca en la pantalla y recopilará todos como un toque en un único `UITouch` objeto. Esto hace que la comprobación para un doble punteo tan fácil como comprobando el `TapCount` propiedad, como se muestra en el código siguiente:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        if (touch.TapCount == 2)
        {
            // do something with the double touch.
        }
    }
}
```

## <a name="multi-touch"></a>La tecnología multitoque

La tecnología multitoque no está habilitada de forma predeterminada en los controles. La tecnología multitoque se puede habilitar en iOS Designer, como se muestra en la captura de pantalla siguiente:

 [![](touch-in-ios-images/image2.png "La tecnología multitoque habilitada en el Diseñador de iOS")](touch-in-ios-images/image2.png#lightbox)

También es posible establecer multitoque mediante programación estableciendo la `MultipleTouchEnabled` propiedad tal como se muestra en la siguiente línea de código:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Para determinar cuántos dedos toca la pantalla, use el `Count` propiedad en el `UITouch` propiedad:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinar la ubicación de toque

El método `UITouch.LocationInView` devuelve un objeto CGPoint que contiene las coordenadas de la entrada táctil dentro de una vista determinada. Además, podemos probar para ver si esa ubicación está dentro de un control llamando al método `Frame.Contains`. El fragmento de código siguiente muestra un ejemplo de esto:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Ahora que tenemos una comprensión de los eventos touch en iOS, vamos a aprender acerca de los reconocedores de gestos.

## <a name="gesture-recognizers"></a>Reconocedores de gestos

Los reconocedores de gestos en gran medida pueden simplificar y reducir el esfuerzo de programación que admiten la función táctil en una aplicación. los reconocedores de gestos de iOS de agregado una serie de eventos de toque en un evento de toque único.

Xamarin.iOS proporciona la clase `UIGestureRecognizer` como una clase base para los reconocedores de gestos integrados siguientes:

-  *UITapGestureRecognizer* : se trata de uno o más derivaciones.
-  *UIPinchGestureRecognizer* – Pinching y esparce dedos separados.
-  *UIPanGestureRecognizer* : panorámica o arrastrar.
-  *UISwipeGestureRecognizer* – desplazándose en cualquier dirección.
-  *UIRotationGestureRecognizer* : rotación de dos dedos en un movimiento hacia la derecha o hacia la izquierda.
-  *UILongPressGestureRecognizer* : mantenga presionada, a veces se denomina una larga presione o larga y haga clic en.


El patrón básico para utilizar un reconocedor de movimiento es como sigue:

1.  **Crear una instancia el reconocedor de gestos** : en primer lugar, crear una instancia de un `UIGestureRecognizer` subclase. Se asociará el objeto que se crea una instancia por una vista y se elementos no utilizados recopilarán cuando se elimina la vista. No es necesario crear esta vista como una variable de nivel de clase.
1.  **Configure las opciones de gesto** : el siguiente paso es configurar el reconocedor de gestos. Consulte la documentación de Xamarin en `UIGestureRecognizer` y sus subclases para obtener una lista de propiedades que se pueden establecer para controlar el comportamiento de un `UIGestureRecognizer` instancia.
1.  **Configurar el destino** – debido a su patrimonio Objective-C, Xamarin.iOS no genera eventos cuando un reconocedor de movimiento coincide con un gesto.  `UIGestureRecognizer` tiene un método – `AddTarget` – que puede aceptar un delegado anónimo o un selector de Objective-C con el código para ejecutar cuando el reconocedor de gestos realiza una coincidencia.
1.  **Habilitar el reconocedor de gestos** : al igual que con los eventos de toque, gestos se reconocen solo si están habilitadas las interacciones táctiles.
1.  **Agregue el reconocedor de movimiento a la vista** : el último paso es agregar el gesto a una vista mediante una llamada a `View.AddGestureRecognizer` y se le pasa un objeto reconocedor de gestos.

Hacer referencia a la [ejemplos reconocedor de gestos](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) para obtener más información acerca de cómo se implementan en el código.

Cuando se llama el destino del movimiento, se pasará una referencia a los gestos que se ha producido. Esto permite que el destino de gesto obtener información sobre el gesto que se ha producido. La extensión de la información disponible depende del tipo de reconocedor de gestos que se usó. Consulte la documentación de Xamarin para obtener información acerca de los datos disponibles para cada `UIGestureRecognizer` subclase.

Es importante recordar que una vez que se ha agregado un reconocedor de movimiento para una vista, la vista (y todas las vistas debajo de él) no recibirá los eventos de toque. Para permitir los eventos de toque simultáneamente con los gestos, los `CancelsTouchesInView` propiedad debe establecerse en false, como se muestra en el código siguiente:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Cada `UIGestureRecognizer` tiene una propiedad de estado que proporciona información importante sobre el estado del reconocedor de gestos. Cada vez que cambia el valor de esta propiedad, iOS llamará al método suscripción darle una actualización. Si un reconocedor de movimiento personalizada nunca actualiza la propiedad State, nunca se llama el suscriptor, inútil el reconocedor de gestos.

Los gestos se pueden resumir como uno de los dos tipos:

1.  *Discretos* : estos gestos única fire la primera vez que se reconocen.
1.  *Continua* – continuarán estos gestos que se active, siempre se reconocen.


Los reconocedores de gestos existe en uno de los siguientes estados:

-  *Posible* : este es el estado inicial de todos los reconocedores de gestos. Este es el valor predeterminado la propiedad State.
-  *Began* : cuando se reconoció un gesto continua por primera vez, el estado se establece en Began. Esto permite que se suscribe para diferenciar entre cuando se inicia el reconocimiento de gestos y cuándo se puede cambiar.
-  *Changed* – después de un movimiento continuo se ha iniciado, pero no ha finalizado, el estado se establecerá en Changed cada vez que una entrada táctil se mueve o cambia, siempre y cuando se encuentre todavía dentro de los parámetros esperados del gesto.
-  *Cancelar* : este estado se establecerá si el reconocedor pasó de Began a ha cambiado y, a continuación, los retoques cambiados de tal forma que ya no ajustan al patrón del gesto.
-  *Reconoce* : el estado se establecerá cuando el reconocedor de gestos coincide con un conjunto de dispositivos táctiles y le informará de que el gesto ha terminado el suscriptor.
-  *Finalizó* : se trata de un alias para el estado reconoce.
-  *No se pudo* : cuando el reconocedor de gestos ya no puede coincidir con los retoques está escuchando para, el estado cambiado a Failed.


Xamarin.iOS representa estos valores en el `UIGestureRecognizerState` enumeración.

## <a name="working-with-multiple-gestures"></a>Trabajar con varios movimientos

De forma predeterminada, iOS no admite gestos predeterminados de ejecutar al mismo tiempo. En su lugar, cada reconocedor de gestos recibirá eventos de toque en un orden no determinista. El fragmento de código siguiente muestra cómo hacer que un reconocedor de movimiento se ejecuten simultáneamente:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

También es posible deshabilitar un gesto en iOS. Hay dos propiedades de delegado que permiten un reconocedor de gestos examinar el estado de la aplicación y los eventos de toque actual, a tomar decisiones acerca de cómo y si se debería reconocer un gesto. Son los dos eventos:

1.  *ShouldReceiveTouch* : este delegado se llama justo antes de que el reconocedor de gestos se pasa a un evento de toque y proporciona una oportunidad para examinar los retoques y decidir qué toques controlará el reconocedor de gestos.
1.  *ShouldBegin* : se llama cuando un reconocedor intenta cambiar el estado de posibles a algún otro estado. Devuelve el valor false forzará el estado del reconocedor de gestos cambiarse a error.


Puede invalidar estos métodos con fuertemente tipado `UIGestureRecognizerDelegate`, un delegado débil o enlace a través de la sintaxis de controlador de eventos, como se muestra en el siguiente fragmento de código:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Por último, es posible poner en cola un reconocedor de movimiento para que solo se realizará correctamente si se produce un error en otro reconocedor de gestos. Por ejemplo, un reconocedor de movimiento solo toque solo debería realizarse correctamente cuando se produce un error en un reconocedor de movimiento de un doble punteo. El siguiente fragmento de código proporciona un ejemplo de esto:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Creación de un gesto personalizado

Aunque iOS proporciona algunas predeterminados los reconocedores de gestos, puede ser necesario crear los reconocedores de gestos personalizados en ciertos casos. Creación de un reconocedor de movimiento personalizada implica los pasos siguientes:

1.  Subclase `UIGestureRecognizer` .
1.  Invalide los métodos de evento de toque adecuado.
1.  Ascienden en estado de reconocimiento a través de la propiedad de estado de la clase base.


Un ejemplo práctico de esto se explicará en el [usando entrada táctil en iOS](ios-touch-walkthrough.md) tutorial.
