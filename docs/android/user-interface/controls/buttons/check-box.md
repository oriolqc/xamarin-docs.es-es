---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f6f594d86cab8b1173ee9f67402862e1ec2890b2
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510372"
---
# <a name="checkbox"></a>CheckBox

En esta sección, creará una casilla para seleccionar elementos, mediante el[`CheckBox`](xref:Android.Widget.CheckBox)
widget. Cuando se presiona la casilla, un mensaje del sistema indicará el estado actual de la casilla.

Abra el archivo Resources **/layout/main. axml** y [`CheckBox`](xref:Android.Widget.CheckBox) agregue el elemento ( [`LinearLayout`](xref:Android.Widget.LinearLayout)dentro del):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Para hacer algo cuando se cambia el estado, agregue el código siguiente al final de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Esto captura el[`CheckBox`](xref:Android.Widget.CheckBox)
del diseño y, a continuación, controla el evento click, que define la acción que se realizará cuando se haga clic en la casilla. Al hacer clic en él, el[`Checked`](xref:Android.Widget.CompoundButton.Checked)
se llama a la propiedad para comprobar el nuevo estado de la casilla. Si se ha activado,[`Toast`](xref:Android.Widget.Toast)
muestra el mensaje "seleccionado"; en caso contrario, muestra "no seleccionado". El[`CheckBox`](xref:Android.Widget.CheckBox)
controla sus propios cambios de estado, por lo que solo necesita consultar el estado actual.

Ejecútelo.

> [!TIP]
> Si necesita cambiar el estado por su cuenta (por ejemplo, al cargar un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)guardado, use el[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> establecedor de propiedad o[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> .

*Algunas partes de esta página son modificaciones basadas en el trabajo creado y compartido por el proyecto de código abierto de Android y que se usan según los términos descritos en la*
licencia de atribución de[*Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/).
