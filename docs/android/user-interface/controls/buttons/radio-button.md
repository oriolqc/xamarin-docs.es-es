---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ea8af3c84bb53434489d230d159a27b6b9d500d8
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830256"
---
# <a name="radiobutton"></a>RadioButton

En esta sección, creará dos botones de radio se excluyen mutuamente (habilitar uno deshabilita la otra), con el [`RadioGroup`](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)
Y [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
widgets. Cuando se presiona cualquier botón de radio, se mostrará un mensaje de notificación del sistema.


Abra el **Resources/layout/Main.axml** archivo y agregue dos [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s, anidado en un [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (dentro de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

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

Es importante que la [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s se agrupan por la [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) elemento para que se puede seleccionar no más de uno a la vez. Esta lógica se controla automáticamente el sistema Android. Cuando uno [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
dentro de un grupo seleccionado, todos los demás son automáticamente y no está seleccionados.

Para hacer algo cuando cada [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) está seleccionada, debemos escribir un controlador de eventos:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

En primer lugar, el remitente que se pasa se convierte en un botón de opción.
A continuación, un [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
mensaje muestra el texto del botón de radio seleccionado.

Ahora, en la parte inferior de la [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
método, agregue lo siguiente:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Esta forma capturan cada uno de los [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s desde el diseño y agrega el handlerto eventos recién creado.

Ejecute la aplicación.

> [!TIP]
> Si tiene que cambiar manualmente el estado (por ejemplo, cuando carga guardado [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), use el [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
> establecedor de propiedades o [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
> .

*Las partes de esta página son modificaciones en función de trabajo creado y compartido por el Android Open Source Project y usarse de acuerdo con los términos descritos en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/). 
