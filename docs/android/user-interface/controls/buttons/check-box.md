---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 85c505d03e7a763b24fb176b6a94c0fe43009b79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765758"
---
# <a name="checkbox"></a>CheckBox

En esta sección, creará una casilla para seleccionar elementos, con el [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox) widget. Cuando se presiona la casilla de verificación, un mensaje de notificación del sistema indicará el estado actual de la casilla de verificación.

Abra la **Resources/layout/Main.axml** de archivos y agregar el [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento (dentro de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para hacer algo cuando se cambia el estado, agregue el código siguiente al final de la [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) método:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Captura el [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento del diseño, a continuación, controla el evento Click, que define la acción que se realizará cuando se hace clic en la casilla de verificación. Al hacer clic, el [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) propiedad se llama para comprobar el nuevo estado de la casilla de verificación. Si se ha comprobado, un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) muestra el mensaje "Selected", en caso contrario, muestra "Desactivada". El [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) controla los cambios en su propio estado, por lo que solo necesita consultar el estado actual.

Ejecútelo.

**Sugerencia:** si tiene que cambiar manualmente el estado (como cuando carga guardado [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference), use la [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked) establecedor de propiedades o [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle) (método).

*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
