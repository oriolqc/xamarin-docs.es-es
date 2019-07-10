---
title: Editar texto
description: Cómo usar el widget de EditText para aceptar la entrada del usuario.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: 518c13aea431a8e973579768cc70b8281a31acac
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674730"
---
# <a name="edit-text"></a>Editar texto

En esta sección, utilizará el [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) widget para crear un campo de texto de entrada del usuario. Una vez que se ha escrito texto en el campo, el **ENTRAR** clave mostrará el texto en un mensaje de notificación del sistema.

Abra **Resources/layout/activity_main.axml** y agregue el [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento a un contenedor de diseño. En el siguiente ejemplo **activity_main.axml** tiene un `EditText` que se ha agregado a un `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

En este ejemplo de código, el `EditText` atributo `android:imeOptions` está establecido en `actionGo`. Esta configuración cambia el valor predeterminado [realiza](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) acción a la [vaya](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) acción para que al puntear en el **ENTRAR** clave desencadenadores el `KeyPress` controlador de entrada.
(Normalmente, `actionGo` se usa para que el **ENTRAR** clave lleva al usuario en el destino de una dirección URL que se escribe en.)

Para controlar la entrada de texto de usuario, agregue el código siguiente al final de la [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) método **MainActivity.cs**:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) 
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

Además, agregue el siguiente `using` instrucción al principio del **MainActivity.cs** si aún no está presente:

```csharp
using Android.Views;
```

Este ejemplo de código aumenta el [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento desde el diseño y agrega un [KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) controlador que define la acción que se realizará cuando se presiona una tecla mientras el widget tiene el foco. En este caso, el método se define para escuchar el **ENTRAR** clave (cuando se pulsa) y, a continuación, se abrirá un [Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensaje con el texto que se ha especificado. Tenga en cuenta que el [Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propiedad siempre debe ser `true` si se ha controlado el evento. Esto es necesario para evitar que el evento de propagación arriba (lo que daría lugar a un retorno de carro en el campo de texto).

Ejecute la aplicación y escriba algún texto en el campo de texto. Cuando presiona el **ENTRAR** clave, la notificación se mostrará como se muestra en la parte derecha:

[![Ejemplos de escritura de texto en EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Las partes de esta página son modificaciones en función de trabajo creado y* [ *comparten el Android Open Source Project* ](http://code.google.com/policies.html) *y usarse de acuerdo con los términos descritos en el* [ *Licencia de atribución de creative Commons 2.5* ](http://creativecommons.org/licenses/by/2.5/) *. En este tutorial se basa en el* [ *tutorial Android cosas formulario* ](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*


## <a name="related-links"></a>Vínculos relacionados

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
