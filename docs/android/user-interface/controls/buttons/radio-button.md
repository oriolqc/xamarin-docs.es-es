---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 1267491f2d9b7519f76651df059722420fa8e1eb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763119"
---
# <a name="radiobutton"></a>RadioButton

En esta sección, creará dos botones de radio se excluyen mutuamente (lo que permite una deshabilita el otro), con el [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) y [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) widgets. Cuando se presiona cualquier botón de radio, se mostrará un mensaje de notificación del sistema.


Abra la **Resources/layout/Main.axml** de archivos y agregar dos [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s, anidados en un [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (dentro de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

Es importante que la [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s se agrupan por la [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) elemento para que no haya más de uno se puede seleccionar a la vez. Esta lógica se administra automáticamente el sistema Android. Cuando una [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) dentro de un grupo seleccionado, todos los otros son automáticamente no están seleccionados.

Para realizar alguna acción cuando cada [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) está seleccionado, es necesario escribir un controlador de eventos:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

En primer lugar, el remitente que se pasa se convierte en un botón de opción.
Un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensaje muestra el texto del botón de radio seleccionado.

Ahora, en la parte inferior de la [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) método, agregue lo siguiente:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Captura cada uno de los [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s del diseño y agrega la handlerto de evento recién creado.

Ejecute la aplicación.

**Sugerencia:** si tiene que cambiar manualmente el estado (como cuando carga guardado [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), use la [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) establecedor de propiedades o [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/) (método).

*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/). 
