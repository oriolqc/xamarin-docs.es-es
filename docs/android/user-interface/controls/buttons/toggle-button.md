---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 22eb8f999450ed8fb46b1f7809c92540be13aa65
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105927"
---
# <a name="togglebutton"></a>ToggleButton

En esta sección, creará un botón que se utiliza específicamente para alternar entre dos Estados, con el [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) widget. Este widget es una alternativa excelente a botones de opción si tiene dos estados simple que son mutuamente excluyentes ("on" y "off", por ejemplo). Android 4.0 (API nivel 14) introdujo una alternativa para el botón de alternancia que se conoce como un [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Un ejemplo de un **ToggleButton** pueden verse en el par de la parte izquierda de imágenes, mientras que el par de mano derecha de las imágenes presenta un ejemplo de un **conmutador**:

![Ejemplos de conmutadores y ToggleButtons en ambos y desactivar los Estados](toggle-button-images/togglebutton-switch.png)  

Control que usa una aplicación es una cuestión de estilo. Los widgets son funcionalmente equivalentes.

Abra el **Resources/layout/Main.axml** y agréguele el [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento (dentro de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

Para hacer algo cuando se cambia el estado, agregue el código siguiente al final de la [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
método:

```csharp
ToggleButton togglebutton = FindViewById<ToggleButton>(Resource.Id.togglebutton);

togglebutton.Click += (o, e) => {
    // Perform action on clicks
    if (togglebutton.Checked)
        Toast.MakeText(this, "Checked", ToastLength.Short).Show ();
    else
        Toast.MakeText(this, "Not checked", ToastLength.Short).Show ();
};
```

Esta forma se capturan el [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento desde el diseño y controla el evento Click, que define la acción que se realizará cuando se hace clic en el botón. En este ejemplo, el método comprueba el estado del botón nuevo, a continuación, se muestra un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensaje que indica el estado actual.

Tenga en cuenta que el [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) identificadores cambiar su propio estado entre activado y desactivado, por lo que basta con preguntar a cuál es.

Ejecute la aplicación.


**Sugerencia:** si tiene que cambiar manualmente el estado (por ejemplo, cuando carga guardado [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), use el [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
establecedor de propiedades o [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
.


## <a name="related-links"></a>Vínculos relacionados

- [ToggleButton](http://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](http://developer.android.com/reference/android/widget/Switch.html)
