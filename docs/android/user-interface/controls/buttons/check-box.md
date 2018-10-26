---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: e03595e8d88a2f12341b9e339d0581c631224848
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120806"
---
# <a name="checkbox"></a>CheckBox

En esta sección, creará una casilla de verificación para seleccionar los elementos, con el [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox)
widget. Cuando se presiona la casilla de verificación, un mensaje de notificación indicará el estado actual de la casilla de verificación.

Abra el **Resources/layout/Main.axml** y agréguele el [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento (dentro de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para hacer algo cuando se cambia el estado, agregue el código siguiente al final de la [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
método:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Esta forma se capturan el [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
elemento del diseño, a continuación, controla el evento Click, que define la acción que se realizará cuando se hace clic en la casilla de verificación. Al hacer clic, el [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
propiedad se llama para comprobar el estado nueva de la casilla de verificación. Si se ha seleccionado un [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
Muestra el mensaje "Selected", en caso contrario, que muestra "Desactivada". El [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
controla los cambios en su propio estado, por lo que solo deberá consultar el estado actual.

Ejecútelo.

**Sugerencia:** si tiene que cambiar manualmente el estado (por ejemplo, cuando carga guardado [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference), utilice el [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked)
establecedor de propiedades o [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle)
.

*Las partes de esta página son modificaciones en función de trabajo creado y compartido por el Android Open Source Project y usarse de acuerdo con los términos descritos en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
