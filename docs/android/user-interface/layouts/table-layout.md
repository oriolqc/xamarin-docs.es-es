---
title: TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9a77655091e4b552bd4a9d440f50b6a3cbeabcc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="tablelayout"></a>TableLayout

[`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) es un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que muestra secundarios [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementos en filas y columnas.

Inicie un nuevo proyecto denominado **HelloTableLayout**.

Abra la **Resources/Layout/Main.axml** archivo e inserte lo siguiente:

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

Tenga en cuenta cómo se parece a la estructura de una tabla HTML. El [ `TableLayout` ](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) elemento es como el código HTML `<table>` elemento; [ `TableRow` ](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) es similar a una `<tr>` elemento; pero para las celdas, puede usar cualquier tipo de [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elemento. En este ejemplo, un [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) se utiliza para cada celda. Entre algunas de las filas, también hay un basic [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/), que se utiliza para dibujar una línea horizontal.

Asegúrese de que su **HelloTableLayout** actividad carga este diseño en el [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

El [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) método carga el archivo de diseño para la [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado por el identificador de recurso &mdash; `Resource.Layout.Main` hace referencia a la **recursos/diseño / Main.axml** archivo de diseño.

Ejecute la aplicación. Debería ver lo siguiente:

[![Captura de pantalla de ejemplo de aplicación de TableLayout mostrar varias filas de tabla](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)



## <a name="references"></a>Referencias

-   [`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) 

-   [`TableRow`](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) 

-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Algunas partes de esta página son las modificaciones que se basa en el trabajo creado y comparten la Android Open Source Project y usarse de acuerdo con los términos que se describe en el*
[*licencia de atribución 2.5 de Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
