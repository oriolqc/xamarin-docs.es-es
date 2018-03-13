---
title: "Entrada táctil de iOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 838a11f078d735759eda1d45a082ccbad51e2779
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="touch-in-ios"></a>Entrada táctil de iOS

Es importante comprender los eventos de toque y se describe la API en una aplicación de iOS, ya que son esenciales para todas las interacciones con el dispositivo físicas. Todas las interacciones táctiles implican una `UITouch` objeto. En este artículo se obtendrá información sobre cómo usar la `UITouch` clase y sus API para admitir la entrada táctil. Más adelante, se expandirá en nuestro conocimiento para obtener información sobre cómo admitir movimientos.

## <a name="enabling-touch"></a>Habilitar la entrada táctil

Controles de `UIKit` son: las subclases de control: por lo que depende de interacción del usuario que tienen movimientos integradas en UIKit y, por tanto, no es necesario habilitar la interacción táctil. Ya está habilitado.

Sin embargo, muchas de las vistas en `UIKit` no tiene pantalla táctil de forma predeterminada. Hay dos maneras de habilitar la interacción táctil de un control. La primera manera consiste en comprobar la casilla habilitado de interacción de usuario en el panel de propiedades del diseñador, iOS como se muestra en la siguiente captura de pantalla:

 [![](touch-in-ios-images/image1.png "Active la casilla habilitado de interacción de usuario en el panel de propiedades del Diseñador de iOS")](touch-in-ios-images/image1.png#lightbox)

También se puede usar un controlador para establecer el `UserInteractionEnabled` propiedad en true en un `UIView` clase. Esto es necesario si la interfaz de usuario se crea en el código.

La siguiente línea de código es un ejemplo:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventos de funciones táctiles

Hay tres fases de táctiles que se producen cuando el usuario toca la pantalla, mueve el dedo o quita su dedo. Estos métodos se definen en `UIResponder`, que es la clase base para UIView. iOS invalidará los métodos asociados en el `UIView` y `UIViewController` para controlar la entrada táctil:

-  `TouchesBegan` : Esto se denomina cuando primero se toca la pantalla.
-  `TouchesMoved` : Esto se llama cuando la ubicación de los cambios de táctil que el usuario esté desplazando los dedos alrededor de la pantalla.
-  `TouchesEnded` o `TouchesCancelled` : `TouchesEnded` se llama cuando se elevación los dedos del usuario fuera de la pantalla.  `TouchesCancelled` se invoca si iOS cancela la entrada táctil: por ejemplo, si un usuario diapositivas su mano fuera de un botón para cancelar la acción de presionar una.


Toque eventos de forma recursiva de desplazamiento hacia abajo por la pila de UIViews, para comprobar si el evento de entrada táctil está dentro de los límites de un objeto de vista. Esto se suele denominar _la prueba de posicionamiento_. En primer lugar se llamará en el nivel superior `UIView` o `UIViewController` y, a continuación, se llamará en el `UIView` y `UIViewControllers` por debajo de ellos en la jerarquía de vista.

Un `UITouch` se creará objeto cada vez que el usuario toca la pantalla. La `UITouch` objeto incluye datos sobre la entrada táctil, como cuando se produjo la entrada táctil, que se produjo, si la entrada táctil es una deslice el dedo, etcetera. Los eventos de toque se pasan a una propiedad de los últimos retoques – un `NSSet` que contiene uno o más dispositivos táctiles. Podemos usar esta propiedad para obtener una referencia a una entrada táctil y determinar la respuesta de la aplicación.

Las clases que reemplazar uno de los eventos de toque deben llamar primero a la implementación base y, a continuación, obtener la `UITouch` objeto asociado al evento. Para obtener una referencia al primer toque, llame a la `AnyObject` propiedad y convertirla a una `UITouch` como presentación en el ejemplo siguiente:

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

iOS reconoce automáticamente rápida sucesivas afecta a la pantalla y recopilará todos como en un único equipo con un solo toque `UITouch` objeto. Esto hace que la comprobación para un doble punteo tan sencillo como comprobar el `TapCount` propiedad, como se muestra en el código siguiente:

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

## <a name="multi-touch"></a>Multi-Touch

Multitoque no está habilitado de forma predeterminada en los controles. Multitoque puede habilitarse en el diseñador, iOS como se muestra en la captura de pantalla siguiente:

 [![](touch-in-ios-images/image2.png "Multitoque habilitado en el Diseñador de iOS")](touch-in-ios-images/image2.png#lightbox)

También es posible establecer multitoque mediante programación estableciendo la `MultipleTouchEnabled` propiedad tal y como se muestra en la siguiente línea de código:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Para determinar cuántos dedos toca la pantalla, use la `Count` propiedad en el `UITouch` propiedad:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinar la ubicación de entrada táctil

El método `UITouch.LocationInView` devuelve un objeto CGPoint que contiene las coordenadas de la entrada táctil dentro de una vista determinada. Además, podemos probar para ver si esa ubicación dentro de un control llamando al método `Frame.Contains`. El fragmento de código siguiente muestra un ejemplo de esto:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Ahora que tenemos una descripción de los eventos touch en iOS, vamos a obtener información sobre identificadores de gestos.

## <a name="gesture-recognizers"></a>Identificadores de gestos

Identificadores de gestos en gran medida pueden simplificar y reducir el esfuerzo de programación para admitir la entrada táctil en una aplicación. identificadores de gesto de iOS agregan una serie de eventos touch en un evento solo toque.

Xamarin.iOS proporciona la clase `UIGestureRecognizer` como una clase base para los identificadores de gestos integrados siguientes:

-  *UITapGesturesRecognizer* : se trata de derivaciones de uno o más.
-  *UIPinchGestureRecognizer* – Pinching y propagación dedos separadas.
-  *UIPanGestureRecognizer* : panorámica o arrastrar.
-  *UISwipeGestureRecognizer* – Deslizar rápidamente en cualquier dirección.
-  *UIRotationGestureRecognizer* : rotación de dos dedos en un movimiento hacia la derecha o hacia la izquierda.
-  *UILongPressGestureRecognizer* – presionar y mantener presionado, que se denomina a veces un presionándolos una larga o larga y haga clic en.


El patrón básico para utilizar un reconocedor de movimiento es como sigue:

1.  **Crear una instancia del reconocedor de movimientos** : en primer lugar, crear una instancia de un `UIGestureRecognizer` subclase. El objeto que se crea una instancia se asociará con una vista y elementos no utilizados recopilarán cuando se elimina la vista de. No es necesario crear esta vista como una variable de nivel de clase.
1.  **Configure las opciones de movimiento** : el paso siguiente consiste en configurar el reconocedor de movimientos. Consulte la documentación de Xamarin en `UIGestureRecognizer` y sus subclases para obtener una lista de propiedades que se pueden establecer para controlar el comportamiento de un `UIGestureRecognizer` instancia.
1.  **Configurar el destino** – debido a su herencia Objective-C, Xamarin.iOS no se genera eventos cuando un reconocedor de movimiento coincide con un gesto.  `UIGestureRecognizer` tiene un método – `AddTarget` – que puede aceptar un delegado anónimo o un selector de Objective-C con el código que se ejecutará cuando el reconocedor de movimientos realiza una coincidencia.
1.  **Habilitar el reconocedor de movimientos** : al igual que con los eventos de entrada táctil, los movimientos se reconocen solo si están habilitadas las interacciones táctiles.
1.  **Agregue el reconocedor de movimiento a la vista** : el último paso consiste en agregar el movimiento a una vista mediante una llamada a `View.AddGestureRecognizer` y se le pasa un objeto de reconocimiento de gestos.

Hacer referencia a la [ejemplos reconocedor de gestos](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) para obtener más información sobre cómo implementarlas en el código.

Cuando se llama el destino del movimiento, se pasarán como una referencia a los gestos que se ha producido. Esto permite que el destino de movimiento obtener información acerca del movimiento que se ha producido. La extensión de la información disponible depende del tipo de reconocedor de movimiento que se utilizó. Consulte la documentación de Xamarin para obtener información acerca de los datos disponibles para cada `UIGestureRecognizer` subclase.

Es importante recordar que cuando se haya agregado un reconocedor de movimiento a una vista, la vista (y todas las vistas por debajo de él) no recibirá los eventos de toque. Para permitir los eventos de toque simultáneamente con gestos, los `CancelsTouchesInView` propiedad debe establecerse en false, como se muestra en el código siguiente:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Cada `UIGestureRecognizer` tiene una propiedad de estado que proporciona información importante sobre el estado del reconocedor de movimientos. Cada vez que cambia el valor de esta propiedad, iOS llamará al método de suscripción que proporciona una actualización. Si un reconocedor de movimiento personalizada nunca actualiza la propiedad State, nunca se llama el suscriptor, inútil el reconocedor de movimientos.

Movimientos se pueden resumir como uno de dos tipos:

1.  *Discretos* : estos movimientos única activan la primera vez que se reconocen.
1.  *Continuo* : estos movimientos continuarán activan mientras se reconocen.


Identificadores de gestos se incluye en uno de los siguientes estados:

-  *Posibles* : este es el estado inicial de todos los identificadores de gestos. Este es el valor predeterminado la propiedad State.
-  *Began* : cuando primero se reconoce un movimiento continuo, el estado se establece en Began. Esto permite que se suscribe para diferenciar entre cuando se inicia el reconocimiento de gestos y cuando se cambia.
-  *Changed* – después de un movimiento continuo ha iniciado, pero no ha finalizado, el estado se establecerá en Changed cada vez que una entrada táctil se mueve o cambia, siempre y cuando sea aún dentro de los parámetros previstos de los gestos.
-  *Cancelar* : este estado se establecerá si el reconocedor fue de Began a Changed y, a continuación, los retoques cambiados de tal manera que ya no ajustan al patrón de los gestos.
-  *Reconoce* : el estado se establecerá cuando el reconocedor de movimientos coincide con un conjunto de los últimos retoques y le informará de que el movimiento ha finalizado el suscriptor.
-  *Finalizó* : se trata de un alias para el estado de reconoce.
-  *No se pudo* : cuando el reconocedor de movimientos ya no puede coincidir con los retoques está realizando escuchas para, el estado cambiado a Failed.


Xamarin.iOS representa estos valores en el `UIGestureRecognizerState` enumeración.

## <a name="working-with-multiple-gestures"></a>Trabajar con varios movimientos

De forma predeterminada, iOS no permite gestos de forma predeterminada ejecutar al mismo tiempo. En su lugar, cada reconocedor de movimientos recibirá eventos touch en un orden no determinista. El fragmento de código siguiente muestra cómo hacer que un reconocedor de movimiento se ejecuten al mismo tiempo:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

También es posible deshabilitar un gesto en iOS. Hay dos propiedades de delegado que permiten un reconocedor de movimiento examinar el estado de la aplicación y los eventos de toque actual, a tomar decisiones sobre cómo y si debe reconocer un gesto. Los dos eventos son:

1.  *ShouldReceiveTouch* : este delegado se llama justo antes de que el reconocedor de gestos se pasa un evento de entrada táctil y proporciona una oportunidad para examinar los retoques y decidir qué los últimos retoques será procesados por el reconocedor de movimientos.
1.  *ShouldBegin* : Esto se llama cuando un reconocedor intenta cambiar el estado de los posibles a algún otro estado. Devuelve false, se forzará el estado del reconocedor de movimientos cambiarse a Failed.


Puede invalidar estos métodos con un fuertemente tipado `UIGestureRecognizerDelegate`, un delegado débil o enlace a través de la sintaxis de controlador de eventos, como se muestra en el siguiente fragmento de código:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Por último, es posible poner en cola un reconocedor de movimiento para que solo se realizará correctamente si se produce un error en otro reconocedor de movimientos. Por ejemplo, un reconocedor de movimiento único punteo solo debe ejecutarse correctamente cuando se produce un error en un reconocedor de movimiento de doble punteo. El fragmento de código siguiente muestra un ejemplo de esto:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Crear un gesto personalizado

Aunque iOS proporciona algunos predeterminado identificadores de gestos, puede ser necesario crear identificadores de gesto personalizado en ciertos casos. Crear un reconocedor de movimiento personalizada implica los pasos siguientes:

1.  Subclase `UIGestureRecognizer` .
1.  Reemplace los métodos de evento táctil adecuado.
1.  Propagarse de estado de reconocimiento a través de la propiedad de estado de la clase base.


Un ejemplo práctico de esto se explicará en el [usar Touch en iOS](ios-touch-walkthrough.md) tutorial.
