---
title: ToggleButton
ms.topic: article
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 9e1e9711d218f4f4be825ff223b650ae932ad041
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="togglebutton"></a>ToggleButton

En esta sección, creará un botón que se utiliza específicamente para alternar entre los dos Estados, debe usar el [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) widget. Este widget es una excelente alternativa a botones de opción si tiene dos estados simples que se excluyen mutuamente ("on" y "off", por ejemplo). Android 4.0 (API nivel 14) introdujo una alternativa para el botón de alternancia que se conoce como un [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Un ejemplo de un **ToggleButton** puede verse en el par de la parte izquierda de imágenes, mientras que el par de derecho de imágenes presenta un ejemplo de un **conmutador**:

![Ejemplos de conmutadores y ToggleButtons en ambas activar y desactivar Estados](toggle-button-images/togglebutton-switch.png)  

Control que usa una aplicación es una cuestión de estilo. Los widgets son funcionalmente equivalentes.

Abra la **Resources/layout/Main.axml** de archivos y agregar el [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento (dentro de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

Para hacer algo cuando se cambia el estado, agregue el código siguiente al final de la [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) método:

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

Captura el [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento del diseño y controla el evento Click, que define la acción que se va a realizar cuando se hace clic en el botón. En este ejemplo, el método comprueba el estado del botón nuevo, a continuación, se muestra un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensaje que indica el estado actual.

Tenga en cuenta que la [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) controla su propio estado cambie entre checked y unchecked, por lo que simplemente pida que lo es.

Ejecute la aplicación.


**Sugerencia:** si tiene que cambiar manualmente el estado (como cuando carga guardado [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), use la [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) establecedor de propiedades o [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/) (método).


## <a name="related-links"></a>Vínculos relacionados

- [ToggleButton](http://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](http://developer.android.com/reference/android/widget/Switch.html)
