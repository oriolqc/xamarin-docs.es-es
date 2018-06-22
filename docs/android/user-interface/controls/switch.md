---
title: Modificador
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0f4bfc3646f1ccd956ee8151468b3de20f6e1e2b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762664"
---
# <a name="switch"></a>Modificador

El `Switch` widget (se muestra a continuación) permite al usuario alternar entre dos Estados, como en o desactivado. El `Switch` valor predeterminado es OFF. A continuación se muestra el widget en sus Estados ON y OFF:

[![Capturas de pantalla de un widget de conmutador en desactivar Estados](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>Crear un conmutador

Para crear un conmutador, simplemente declare un `Switch` elemento XML como se indica a continuación:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Esto crea un conmutador básico tal y como se muestra a continuación:

[![Captura de pantalla de aplicación de demostración mostrar un conmutador en el estado desactivado](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>Cambiar valores predeterminados

El texto que muestra el control para el ON y OFF Estados y el valor predeterminado son configurables. Por ejemplo, para realizar el cambio ON de forma predeterminada y leer NO/sí en lugar de encendido y apagado, podemos establecer la `checked`, `textOn`, y `textOff` atributos en el siguiente código XML.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Proporciona un título

El `Switch` widget también admite como una etiqueta de texto, establecer el `text` atributo como se indica a continuación:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Este marcado produce la siguiente captura de pantalla en tiempo de ejecución:

[![Captura de pantalla de aplicación de demostración con texto anterior horizontalmente el widget de conmutador](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Cuando un `Switch`de cambios de valor, genera un `CheckedChange` eventos.
Por ejemplo, en el código siguiente se capturar este evento y presentar una `Toast` widget con un mensaje en función de la `isChecked` valo `Switch`, que se pasa al controlador de eventos como parte de la `CompoundButton.CheckedChangeEventArg` argumento.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>Vínculos relacionados

- [SwitchDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/SwitchDemo/)
- [Pestaña diseño Tutorial](~/android/user-interface/layouts/tab-layout/index.md)
- [Introducción a helado Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
