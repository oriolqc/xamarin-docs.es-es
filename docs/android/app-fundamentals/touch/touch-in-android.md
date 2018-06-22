---
title: Entrada táctil de Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: f1ccc86a20cb441bfdda864b8c7e263a691f5ab7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767497"
---
# <a name="touch-in-android"></a>Entrada táctil de Android

Mucho, como iOS, Android crea un objeto que contiene datos sobre la interacción del usuario físico con la pantalla &ndash; un `Android.View.MotionEvent` objeto. Este objeto contiene los datos como qué acción se realiza, donde la entrada táctil tardó coloque, cuánto presión se aplicó, etcetera. Un `MotionEvent` objeto desglosa el movimiento en a los valores siguientes:

-  Código de acción que describe el tipo de movimiento, como toque inicial, toque mover a través de la pantalla o el final de la entrada táctil.

-  Un conjunto de valores del eje que describen la posición de la `MotionEvent` y otras propiedades de movimiento como donde la entrada táctil está teniendo lugar, cuando la entrada táctil tuvo lugar y se utilizó la cantidad presión.
   Los valores del eje pueden ser diferentes en función del dispositivo, por lo que la lista anterior describen todos los valores de eje.


La `MotionEvent` objeto se pasará a un método adecuado en una aplicación. Hay tres maneras para que una aplicación Xamarin.Android para responder a un evento de entrada táctil:

-  *Asignar un controlador de eventos para `View.Touch`*  - la `Android.Views.View` clase tiene un `EventHandler<View.TouchEventArgs>` qué aplicaciones pueden asignar un controlador. Este comportamiento es normal. NET.

-  *Implementar `View.IOnTouchListener`*  -instancias de esta interfaz pueden asignarse a un objeto de vista mediante la vista. `SetOnListener` método. Esto es funcionalmente equivalente a asignar un controlador de eventos para el `View.Touch` eventos. Si hay alguna lógica comunes o compartido que muchas vistas diferentes que necesite cuando están modificadas, será más eficaz para crear una clase e implementar este método más to asignar a su propio controlador de eventos de cada vista.

-  *Invalidar `View.OnTouchEvent`*  -todas las vistas de la subclase Android `Android.Views.View`. Cuando se toca una vista, llamará a Android el `OnTouchEvent` y pasarle una `MotionEvent` objeto como parámetro.


> [!NOTE]
> No todos los dispositivos Android admiten pantallas táctiles. 

Al agregar la etiqueta siguiente al archivo de manifiesto, Google Play mostrar solo la aplicación en los dispositivos de pantalla táctil:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Gestos

Un movimiento es una forma dibujada a mano en la pantalla táctil. Un gesto puede tener uno o más trazos, cada trazo que consta de una secuencia de puntos creado por otro punto de contacto con la pantalla. Android puede admitir muchos tipos distintos de movimientos, desde un simple fling a través de la pantalla en los gestos complejos que implican multitoque.

Android proporciona el `Android.Gestures` espacio de nombres específicamente para administrar y responder a los gestos. En el núcleo de todos los gestos es una clase especial denominada `Android.Gestures.GestureDetector`. Como su nombre indica, esta clase realizará escuchas para los gestos y eventos basándose en `MotionEvents` proporcionado por el sistema operativo.

Para implementar un detector de gestos, debe crear una instancia de una actividad una `GestureDetector` clase y proporcionar una instancia de `IOnGestureListener`, tal y como se muestra en el siguiente fragmento de código:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Una actividad también debe implementar la OnTouchEvent y pasar la MotionEvent para el detector de movimiento. El fragmento de código siguiente muestra un ejemplo de esto:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

Cuando una instancia de `GestureDetector` identifica un gesto de interés, le notificará la actividad o la aplicación generando un evento o a través de una devolución de llamada proporcionada por `GestureDetector.IOnGestureListener`.
Esta interfaz proporciona seis métodos para los movimientos de distintos:

-  *OnDown* -se le llama cuando se produce una derivación, pero no se libera.

-  *OnFling* -se le llama cuando un fling se produce y proporciona datos acerca de la entrada táctil inicial y final que desencadenó el evento.

-  *OnLongPress* -se le llama cuando se produce la acción de presionar una larga.

-  *OnScroll* -se le llama cuando se produce un evento de desplazamiento.

-  *OnShowPress* : llama después de que se ha producido un OnDown y un movimiento o evento no se ha ejecutado.

-  *OnSingleTapUp* -se le llama cuando se produce un único punteo.


En muchos casos las aplicaciones solo pueden estar interesadas en un subconjunto de movimientos. En este caso, deben extender la clase GestureDetector.SimpleOnGestureListener e invalide los métodos que corresponden a los eventos de aplicaciones que le interesan.

## <a name="custom-gestures"></a>Gestos personalizados

Movimientos son una excelente manera para que los usuarios interactúan con una aplicación. Las API que hemos visto hasta ahora sería suficiente para los gestos simples, pero pueden resultar un poco costoso para los gestos más complicados. Para ayudar con movimientos más complicados, Android proporciona otro conjunto de API en el espacio de nombres Android.Gestures que facilite la parte de la carga asociada gestos personalizados.

### <a name="creating-custom-gestures"></a>Creación de gestos personalizados

Desde Android 1.6, el SDK de Android incluye una aplicación instalada previamente en el emulador llamado el generador de gestos. Esta aplicación permite a un desarrollador crear movimientos predefinidos que se pueden incrustar en una aplicación. La captura de pantalla siguiente muestra un ejemplo del generador de gestos:

[![Captura de pantalla de movimientos generador con movimientos de ejemplo](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Se encontrará una versión mejorada de esta aplicación denominada herramienta gesto de Google Play. Herramienta de movimiento es muy similar a generador de movimientos excepto en que permite probar movimientos después de que se hayan creado. Esta captura de pantalla siguiente muestra el generador de gestos:

[![Herramienta de captura de pantallas de gestos con movimientos de ejemplo](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

Herramienta de movimiento es un poco más útil para crear gestos personalizados, ya que permite a los movimientos se va a probar como que se están creando y está fácilmente disponible a través de Google Play.

Herramienta de movimiento permite que crear un gesto dibujando en la pantalla y asignarle un nombre. Una vez creados los movimientos se guardan en un archivo binario en la tarjeta SD del dispositivo. Este archivo debe recuperar desde el dispositivo y después se empaquetan con una aplicación en la carpeta /Resources/raw. Este archivo se puede recuperar desde el emulador con Android Debug Bridge. En el ejemplo siguiente se muestra copiar el archivo desde un Nexus Galaxy en el directorio de recursos de una aplicación:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Una vez que ha recuperado el archivo debe ser empaquetado con la aplicación en el directorio Resources/sin formato. La manera más fácil de usar este archivo de gestos es cargar el archivo en un GestureLibrary, tal como se muestra en el siguiente fragmento:

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Usar gestos personalizados

Para que reconozca los gestos personalizados en una actividad, debe tener un objeto de Android.Gesture.GestureOverlay agregado a su diseño. El fragmento de código siguiente muestra cómo agregar mediante programación un GestureOverlayView a una actividad:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

El fragmento XML siguiente muestra cómo agregar un GestureOverlayView mediante declaración:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

El `GestureOverlayView` tiene varios eventos que se producirá durante el proceso de dibujar un movimiento. El evento más interesante es `GesturePeformed`. Este evento se desencadena cuando el usuario ha completado su gestos de dibujo.

Cuando se genera este evento, la actividad solicita un `GestureLibrary` para probar y coincide con el movimiento creados por el usuario con uno de los movimientos de herramienta de gestos. `GestureLibrary` Devuelve una lista de objetos de predicción.

Cada objeto de predicción contiene una puntuación y el nombre de uno de los movimientos en el `GestureLibrary`. Cuanto mayor sea la puntuación, más probable que el movimiento con el nombre de la predicción coincide con el movimiento dibujado por el usuario.
Por lo general, las puntuaciones inferiores 1.0 se consideran a coincidencias deficientes.

El código siguiente muestra un ejemplo de un gesto de coincidencia:

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

De este modo, debe tener una comprensión de cómo usar táctiles y los gestos en una aplicación Xamarin.Android. Permítanos ahora pasar a un tutorial y ver todos los conceptos en una aplicación de ejemplo de trabajo.



## <a name="related-links"></a>Vínculos relacionados

- [Android Touch iniciar (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
