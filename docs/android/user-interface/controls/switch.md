---
title: Modificador
description: Cómo usar el widget de conmutador en una aplicación de Xamarin.Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: ef400aaa31992b577762ad695418b865882e2e2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075784"
---
# <a name="switch"></a>Modificador

El `Switch` widget (se muestra a continuación) permite a los usuarios alternar entre dos Estados, como en o desactivado. El `Switch` valor predeterminado es OFF. A continuación se muestra el widget en sus estados activado y desactivado:

[![Capturas de pantalla de un widget de conmutador en Estados y activar](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>Creación de un conmutador

Para crear un conmutador, simplemente declare un `Switch` elemento XML como sigue:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Esto crea un conmutador básico, como se muestra a continuación:

[![Captura de pantalla de la aplicación de demostración muestra un conmutador en el estado desactivado](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>Cambiar los valores predeterminados

El texto que muestra el control para el ON y OFF Estados y el valor predeterminado son configurables. Por ejemplo, para realizar el cambio de valor predeterminado es ON y leer NO o Sí en lugar de encendido y apagado, podemos establecer el `checked`, `textOn`, y `textOff` atributos en el siguiente código XML.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Proporcionar un título

El `Switch` widget también admite como una etiqueta de texto estableciendo el `text` atributo como sigue:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Este marcado crea la siguiente captura de pantalla en tiempo de ejecución:

[![Captura de pantalla de la aplicación de demostración con texto horizontalmente anterior el widget de conmutador](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Cuando un `Switch`de cambios de valor, genera un `CheckedChange` eventos.
Por ejemplo, en el código siguiente se captura este evento y presentar un `Toast` widget con un mensaje basado en el `isChecked` valor `Switch`, que se pasa al controlador de eventos como parte de la `CompoundButton.CheckedChangeEventArg` argumento.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>Vínculos relacionados

- [SwitchDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/SwitchDemo/)
- [Pestaña diseño Tutorial](~/android/user-interface/layouts/tab-layout/index.md)
