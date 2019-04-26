---
title: TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 25b2393d2133c0a1f3f8354584c276fcd7ddaa4b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61305168"
---
# <a name="tablelayout"></a>TableLayout

[`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) es un [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
que muestra el elemento secundario [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
elementos de filas y columnas.

Inicie un nuevo proyecto denominado **HelloTableLayout**.

Abra el **Resources/Layout/Main.axml** archivo e inserte lo siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

Tenga en cuenta cómo se parece a la estructura de una tabla HTML. El [`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/)
elemento es como el código HTML `<table>` elemento; [`TableRow`](https://developer.xamarin.com/api/type/Android.Widget.TableRow/)
es similar a un `<tr>` elemento; pero para las celdas, puede usar cualquier tipo de [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elemento. En este ejemplo, un [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
se utiliza para cada celda. Entre algunas de las filas, también hay un basic [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/), que se usa para dibujar una línea horizontal.

Asegúrese de que su **HelloTableLayout** actividad carga este diseño en el [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) método carga el archivo de diseño para el [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado por el identificador de recurso &mdash; `Resource.Layout.Main` hace referencia a la **y diseño de recursos / Main.axml** archivo de diseño.

Ejecute la aplicación. Debería ver lo siguiente:

[![Captura de pantalla de ejemplo de aplicación TableLayout mostrar varias filas de tabla](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)



## <a name="references"></a>Referencias

-   [`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) 

-   [`TableRow`](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) 

-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Las partes de esta página son modificaciones en función de trabajo creado y compartido por el Android Open Source Project y usarse de acuerdo con los términos descritos en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
