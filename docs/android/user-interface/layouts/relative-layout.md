---
title: Uso de RelativeLayout en Xamarin.Android
description: Cómo usar RelativeLayout en una aplicación de Xamarin.Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af2972ecc92435836a75013e6203ba47c2c04627
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61303634"
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) es un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que muestra el elemento secundario [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
elementos en posiciones relativas. La posición de un [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) puede especificarse en relación con los elementos del mismo nivel (por ejemplo, con respecto a la izquierda de o por debajo de un elemento determinado) o en posiciones relativas a la [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
área (por ejemplo, alineado a la parte inferior, izquierda del centro).

Un [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) es una utilidad muy eficaz para diseñar una interfaz de usuario porque puede eliminar anidados [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s. Si se descubre mediante varios anidados [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
grupos, es posible que pueda reemplazarlos por una sola [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/).

Inicie un nuevo proyecto denominado **HelloRelativeLayout**.

Abra el **Resources/Layout/Main.axml** archivo e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

Tenga en cuenta cada uno de los `android:layout_*` atributos, como `layout_below`, `layout_alignParentRight`, y `layout_toLeftOf`.
Cuando se usa un [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), puede usar estos atributos para describir cómo desea colocar cada [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/). Cada uno de estos atributos definen un tipo diferente de la posición relativa. Algunos atributos utilizan el identificador de recurso de un elemento relacionado [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) para definir su posición relativa. Por ejemplo, la última [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) se define para que se encuentran a la izquierda de y alineado con-the-top-de la [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) identificado por el identificador `ok` (que es el anterior [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

Todos los atributos de diseño disponibles se definen en [ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/).

Asegúrese de cargar este diseño en el [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/) método carga el archivo de diseño para el [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado por el identificador de recurso &mdash; `Resource.Layout.Main` hace referencia a la **y diseño de recursos / Main.axml** archivo de diseño.

Ejecute la aplicación. Debería ver el siguiente diseño:

[![Captura de pantalla de un diseño con un objeto TextView EditText y dos botones relativo](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>Recursos

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*Las partes de esta página son modificaciones en función de trabajo creado y compartido por el Android Open Source Project y usarse de acuerdo con los términos descritos en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
