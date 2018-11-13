---
title: Entrada táctil de Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a25a1c3be8c952536c0ef40b7f7c4a64f5748516
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527240"
---
# <a name="touch-in-android"></a>Entrada táctil de Android

Mucho, como iOS, Android crea un objeto que contiene datos sobre la interacción del usuario físico con la pantalla &ndash; un `Android.View.MotionEvent` objeto. Este objeto contiene los datos, como qué acción se realiza, coloque donde tuvo la entrada táctil, ¿cuánto presión se aplicó, etcetera. Un `MotionEvent` objeto desglosa el movimiento en los siguientes valores:

-  Código de acción que describe el tipo de movimiento, por ejemplo, la interacción inicial, el toque mover a través de la pantalla o el final de interacción.

-  Un conjunto de valores del eje que describen la posición de la `MotionEvent` y otras propiedades de movimiento como donde la entrada táctil está teniendo lugar, cuando la interacción tuvo lugar y se usó la presión.
   El eje de valores pueden variar en función del dispositivo, por lo que la lista anterior no describe todos los valores del eje.


La `MotionEvent` objeto pasarán a un método adecuado en una aplicación. Hay tres maneras para que una aplicación de Xamarin.Android responder a un evento de toque:

-  *Asignar un controlador de eventos `View.Touch`*  : la `Android.Views.View` clase tiene un `EventHandler<View.TouchEventArgs>` las aplicaciones que pueden asignar un controlador. Este comportamiento es normal. NET.

-  *Implementar `View.IOnTouchListener`*  -instancias de esta interfaz se pueden asignar a un objeto de vista mediante la vista. `SetOnListener` método. Esto es funcionalmente equivalente a asignar un controlador de eventos para el `View.Touch` eventos. Si hay alguna lógica común o compartido que puedan necesitar muchas vistas diferentes cuando se han tocado, será más eficaz para crear una clase e implementar este método más to asignar a su propio controlador de eventos de cada vista.

-  *Invalidar `View.OnTouchEvent`*  -todas las vistas de subclase Android `Android.Views.View`. Cuando se toca una vista, llamará a Android el `OnTouchEvent` y pasarle un `MotionEvent` objeto como parámetro.


> [!NOTE]
> No todos los dispositivos Android admiten pantallas táctiles. 

Al agregar la etiqueta a la siguiente al archivo de manifiesto, Google Play para mostrar solo la aplicación en los dispositivos de pantalla táctil:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Gestos

Un gesto es una forma de mano dibujado en la pantalla táctil. Un gesto puede tener uno o varios trazos, cada trazo que consta de una secuencia de puntos creado por otro punto de contacto con la pantalla. Android puede admitir muchos tipos diferentes de los gestos, desde un romance simple en la pantalla en los gestos complejos que implican la tecnología multitoque.

Android proporciona el `Android.Gestures` espacio de nombres específicamente para administrar y responder a los gestos. En el corazón de todos los gestos es una clase especial denominada `Android.Gestures.GestureDetector`. Como el nombre implica, esta clase realizará escuchas para los gestos y eventos en función de `MotionEvents` proporcionada por el sistema operativo.

Para implementar un detector de movimiento, debe crear una instancia de una actividad una `GestureDetector` clase y proporcionar una instancia de `IOnGestureListener`, tal y como se muestra en el siguiente fragmento de código:

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

Cuando una instancia de `GestureDetector` identifica un gesto de interés, lo notificará a la actividad o la aplicación generando un evento o a través de una devolución de llamada proporcionada por `GestureDetector.IOnGestureListener`.
Esta interfaz proporciona seis métodos para los gestos distintos:

-  *OnDown* -se le llama cuando se produce una derivación, pero no se libera.

-  *OnFling* -se invoca cuando un romance se produce y proporciona datos sobre la interacción de inicio y finalización que desencadenó el evento.

-  *OnLongPress* -se le llama cuando se produce una presión prolongada.

-  *OnScroll* -se le llama cuando se produce un evento de desplazamiento.

-  *OnShowPress* : llamado después de que se ha producido un OnDown y un movimiento o evento no se ha realizado.

-  *OnSingleTapUp* -se le llama cuando se produce un único punteo.


En muchos casos las aplicaciones solo pueden estar interesadas en un subconjunto de los gestos. En este caso, las aplicaciones deben ampliar la clase GestureDetector.SimpleOnGestureListener y reemplazar los métodos que corresponden a los eventos que están interesados en.

## <a name="custom-gestures"></a>Gestos personalizados

Los gestos son una excelente manera para que los usuarios interactúan con una aplicación. Las API que hemos visto hasta ahora sería suficiente para movimientos simples, pero pueden resultar un poco más onerosas para los gestos más complicados. Para ayudar con los gestos más complicados, Android proporciona otro conjunto de API en el espacio de nombres Android.Gestures que facilitará la parte de la carga asociada con gestos personalizados.

### <a name="creating-custom-gestures"></a>Creación de gestos personalizados

Desde Android 1.6, el SDK de Android incluye una aplicación previamente instalado en el emulador de generador de gestos. Esta aplicación permite a los desarrolladores crear predefinidos los gestos que se pueden incrustar en una aplicación. Captura de pantalla siguiente muestra un ejemplo del generador de gestos:

[![Captura de pantalla del generador de gestos con gestos de ejemplo](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Encontrará una versión mejorada de esta aplicación llama a la herramienta de gesto de Google Play. Herramienta de movimiento es muy similar a gestos generador excepto en que permite probar los gestos después de haber creado. Esta captura de pantalla siguiente muestra el generador de gestos:

[![Herramienta de captura de pantalla de gestos con gestos de ejemplo](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

Herramienta de movimiento es un poco más útil para crear los gestos personalizados ya que permite a los gestos que se va a probar como se crean y está fácilmente disponible a través de Google Play.

Herramienta gesto permite que crear un gesto de dibujo en la pantalla y asignando un nombre. Una vez creados los gestos se guardan en un archivo binario en la tarjeta SD del dispositivo. Este archivo debe ser recuperado desde el dispositivo y, a continuación, se empaquetan con una aplicación en la carpeta de /Resources/raw. Este archivo se puede recuperar desde el emulador con Android Debug Bridge. El ejemplo siguiente muestra copiando el archivo en el directorio de recursos de una aplicación un Galaxy Nexus:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Después de recuperar el archivo debe estar empaquetado con su aplicación dentro del directorio de Resources/sin formato. La manera más fácil de usar este archivo gesto es cargar el archivo en un GestureLibrary, como se muestra en el siguiente fragmento de código:

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Uso de gestos personalizados

Para reconocer gestos personalizados en una actividad, debe tener un objeto de Android.Gesture.GestureOverlay agregado a su diseño. El fragmento de código siguiente muestra cómo agregar mediante programación un GestureOverlayView a una actividad:

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

El `GestureOverlayView` tiene varios eventos que se generará durante el proceso de dibujo de un gesto. El evento más interesante es `GesturePerformed`. Este evento se desencadena cuando el usuario ha completado su gesto de dibujo.

Cuando se genera este evento, la actividad pide un `GestureLibrary` para intentar coincidir con el gesto creados por el usuario con uno de los movimientos de la herramienta gesto. `GestureLibrary` Devuelve una lista de objetos de predicción.

Cada objeto de predicción contiene una puntuación y el nombre de uno de los gestos en el `GestureLibrary`. Cuanto mayor sea la puntuación, más probable que el gesto mencionado en la predicción coincide con el gesto dibujado por el usuario.
Por lo general, las puntuaciones de menores que 1.0 se consideran a coincidencias deficientes.

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

De este modo, debe tener una comprensión de cómo usar el tacto y gestos en una aplicación de Xamarin.Android. Ahora nos gustaría pasar a un tutorial y vea todos los conceptos en una aplicación de ejemplo funcional.



## <a name="related-links"></a>Vínculos relacionados

- [Android tocar inicio (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (ejemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
