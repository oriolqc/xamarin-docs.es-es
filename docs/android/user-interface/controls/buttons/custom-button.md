---
title: "Botón personalizado"
ms.topic: article
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f77a9b8d3bb69bb47d973a56aed5ad1d49f9a02d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="custom-button"></a>Botón personalizado

En esta sección, creará un botón con una imagen personalizada en lugar de texto, con el [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) widget y un archivo XML que define tres imágenes distintas que se usará para los Estados del botón diferente. Cuando se presiona el botón, se mostrará un mensaje breve.

Haga clic en y descargar las imágenes de tres siguientes y, a continuación, cópielos en el **/puede dibujar recursos** directorio del proyecto. Se usará para los Estados del botón diferente.

 [![Icono verde Android para el estado normal](custom-button-images/android-normal.png)](custom-button-images/android-normal.png) [ ![naranja Android icono de estado tiene el foco](custom-button-images/android-focused.png)](custom-button-images/android-focused.png) [ ![icono amarillo Android para estado presionado](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png)

Crear un nuevo archivo en el **/puede dibujar recursos** directorio denominado **android_button.xml**. Inserte el siguiente código XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

Esto define un único recurso con estas características, lo que cambiará su imagen basada en el estado actual del botón. La primera `<item>` define **android_pressed.png** como la imagen cuando se presiona el botón (se ha activado); el segundo `<item>` define **android_focused.png** como la imagen cuando el botón se centra (cuando se resalta el botón con la bola de seguimiento o el control de dirección); y el tercero `<item>` define **android_normal.png** como la imagen para el estado normal (cuando se presiona ni centrado). Este archivo XML ahora representa un único recurso puede dibujar y cuando hace referencia a un [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) para el fondo, la imagen se muestra cambiará en función de estos tres estados.


> [!NOTE]
> **Nota:** el orden de los `<item>` elementos es importante. Cuando se hace referencia este con estas características, los `<item>`son recorren en orden para determinar cuál es adecuado para el estado actual del botón.
> Dado que la imagen "normal" es el última, es solo se aplica cuando las condiciones `android:state_pressed` y `android:state_focused` evalúen ambas false.

Abra la **Resources/layout/Main.axml** de archivos y agregar el [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) elemento:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

El `android:background` atributo especifica el recurso que se utilizará para el fondo del botón puede dibujar (que, cuando se guarda en **Resources/drawable/android.xml**, se hace referencia como `@drawable/android`). Esto reemplaza la imagen de fondo normal utilizada para los botones en todo el sistema. En orden para cambiar su imagen basada en el estado del botón puede dibujar, la imagen se debe aplicar al fondo.

Para crear el botón de realizar alguna acción cuando presiona, agregue el código siguiente al final de la [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/) método:

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Captura el [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) del diseño, a continuación, agrega un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) mensaje que se mostrará cuando el [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) se hace clic en.

Ahora ejecute la aplicación.


*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
