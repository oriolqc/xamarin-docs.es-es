---
title: Editar texto
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d6be8ae1587742a8c2a37b22b2da3701187dde2a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774608"
---
# <a name="edit-text"></a>Editar texto

En esta sección, creará un campo de texto de entrada del usuario, mediante la [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) widget. Una vez que se ha especificado el texto en el campo, la tecla "Entrar" mostrará el texto en un mensaje de notificación del sistema.

Abra la <code>Resources\layout\main.xml</code> de archivos y agregar el [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento (dentro de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<pre><code class=" syntax brush-C#">&lt;EditText
    android:id="@+id/edittext"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"/&gt;</code></pre>
```

Para hacer algo con el texto que escribe el usuario, agregue el código siguiente al final de la [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) método:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
 e.Handled = false;
 if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) {
  Toast.MakeText (this, edittext.Text, ToastLength.Short).Show ();
  e.Handled = true;
         }
};
```

Captura el [ `EditText` ](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento desde el diseño y establece un [ `KeyPress` ](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) controlador, que define la acción que se realizará cuando se presiona una tecla mientras el widget tiene el foco. En este caso, se define el método para realizar escuchas de la tecla ENTRAR (cuando está presionado), a continuación, abrirá un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensaje con el texto que se ha especificado. El [ `Handled` ](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propiedad siempre debe ser `true` si se controló el evento, por lo que el evento no burbujas vertical (lo que daría como resultado un retorno de carro en el campo de texto).

Ejecute la aplicación.

*Algunas partes de esta página son las modificaciones basadas en el trabajo creado y* [ *comparten el Android Open Source Project* ](http://code.google.com/policies.html) *y usarse de acuerdo con los términos que se describe en el* [ *Licencia de creative Commons 2.5 atribución* ](http://creativecommons.org/licenses/by/2.5/) *. Este tutorial se basa en el* [ *tutorial Android material de formulario* ](http://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*



## <a name="related-links"></a>Vínculos relacionados

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
