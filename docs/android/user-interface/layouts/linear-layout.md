---
title: LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: f3d0394f6b2388918f728bd5a25e9e809a832ca6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187101"
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) es un [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
que muestra el elemento secundario [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
los elementos en una dirección lineal, ya sea vertical u horizontalmente.

Debe tener cuidado sobre el uso excesivo del [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).
Si inicia múltiples anidamiento [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s, que es posible que desee considerar el uso un [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
en su lugar.

Inicie un nuevo proyecto denominado **HelloLinearLayout**.

Abra **Resources/Layout/Main.axml** e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

Inspeccione detenidamente este XML. Hay una raíz [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
que define su orientación a vertical &ndash; todos los secundarios [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s (de qué TI tiene dos) se van a apilar verticalmente. El primer elemento secundario es otra [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
que utiliza una orientación horizontal y el segundo elemento secundario es un [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
que utiliza una orientación vertical. Cada una de ellas anidados [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s contiene varios [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
los elementos, que se orientan entre sí en la forma definida por su elemento primario [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

Ahora abra **HelloLinearLayout.cs** y asegúrese de se carga el **Resources/Layout/Main.axml** diseño en el [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) método carga el archivo de diseño para el [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado por el identificador de recurso &ndash; `Resources.Layout.Main` hace referencia a la **y diseño de recursos / Main.axml** archivo de diseño.

Ejecute la aplicación. Debería ver lo siguiente:

[![Captura de pantalla de aplicación se organizan horizontalmente LinearLayout primer, segundo verticalmente](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Observe cómo los atributos XML definen el comportamiento de la vista. Intente experimentar con valores diferentes para `android:layout_weight` ver cómo se distribuye el espacio en pantalla en función del peso de cada elemento. Consulte la [comunes de diseño de objetos](https://developer.android.com/guide/topics/ui/declaring-layout.html) documento para obtener más información acerca de cómo [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
Controla el `android:layout_weight` atributo.


## <a name="references"></a>Referencias

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Las partes de esta página son modificaciones en función de trabajo creado y compartido por el Android Open Source Project y usarse de acuerdo con los términos descritos en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).

