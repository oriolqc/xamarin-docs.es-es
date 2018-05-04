---
title: Usar el RelativeLayout en Xamarin.Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: cd2d7537036978e30c97b5776155e429178b6dac
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) es un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que muestra secundarios [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementos en posiciones relativas. La posición de un [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) puede especificarse en relación con elementos del mismo nivel (por ejemplo, en cuanto a la izquierda del o por debajo de un elemento determinado) o en posiciones relativas a la [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) área (como Alinea a la parte inferior, izquierda del centro).

A [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) es una herramienta muy eficaz para diseñar una interfaz de usuario porque puede eliminar anidados [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s. Si descubre que tiene que usar varias anidada [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) grupos, es posible que pueda reemplazarlos por una sola [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/).

Inicie un nuevo proyecto denominado **HelloRelativeLayout**.

Abra la **Resources/Layout/Main.axml** archivo e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="fill_parent"
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
Cuando se usa un [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), puede usar estos atributos para describir cómo desea colocar cada [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/). Cada uno de estos atributos definen un tipo diferente de la posición relativa. Algunos atributos de usan el identificador de recurso de un elemento relacionado [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) para definir su propia posición relativa. Por ejemplo, la última [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) se define para permanecer a la izquierda de y alineado-con-the-top-de la [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) identificado por el identificador `ok` (que es la anterior [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

Todos los atributos de diseño disponibles se definen en [ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/).

Asegúrese de cargar este diseño en el [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/) método carga el archivo de diseño para la [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado por el identificador de recurso &mdash; `Resource.Layout.Main` hace referencia a la **recursos/diseño / Main.axml** archivo de diseño.

Ejecute la aplicación. Debería ver el siguiente diseño:

[![Captura de pantalla de un diseño relativo con un control TextView, EditText y dos botones](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>Recursos

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
