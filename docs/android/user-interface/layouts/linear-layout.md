---
title: LinearLayout
ms.topic: article
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: fc6bc9e1d4625f8f45887b0a144a31383046b296
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) es un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que muestra secundarios [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementos en una dirección lineal, ya sea vertical u horizontalmente.

Debe tener cuidado al usar exceso el [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).
Si empieza a varios anidamiento [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s, es podrán que deba plantearse usar un [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) en su lugar.

Inicie un nuevo proyecto denominado **HelloLinearLayout**.

Abra **Resources/Layout/Main.axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "fill_parent"
    android:layout_height=    "fill_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "fill_parent"
      android:layout_height=    "fill_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "fill_parent"
    android:layout_height=    "fill_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

Compruebe cuidadosamente este XML. Hay una raíz [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) que define su orientación a vertical &ndash; todos los secundarios [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s (de donde tiene dos) se van a apilar verticalmente. El primer elemento secundario es otra [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) que utiliza una orientación horizontal y el segundo nodo secundario es un [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) que utiliza una orientación vertical. Cada uno de ellos anidado [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s contiene varias [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) elementos, que se orientan entre sí en la forma definida por su elemento primario [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

Ahora, abra **HelloLinearLayout.cs** y asegúrese de que se carga el **Resources/Layout/Main.axml** diseño en el [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) método carga el archivo de diseño para la [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado por el identificador de recurso &ndash; `Resources.Layout.Main` hace referencia a la **recursos/diseño / Main.axml** archivo de diseño.

Ejecute la aplicación. Debería ver lo siguiente:

[![Captura de pantalla de aplicación LinearLayout primera se organizan horizontalmente, verticalmente segundo](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Observe cómo los atributos XML definen el comportamiento de la vista. Intente experimentar con valores diferentes para `android:layout_weight` ver cómo se distribuye el espacio real en pantalla en función del peso de cada elemento. Consulte la [objetos comunes de diseño](http://developer.android.com/guide/topics/ui/declaring-layout.html) documento para obtener más información acerca de cómo [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) controla la `android:layout_weight` atributo.


## <a name="references"></a>Referencias

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).

