---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 45e625b28bbdf0009b5cfcf661b00ce17638771d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667235"
---
# <a name="gridlayout"></a>GridLayout

El `GridLayout` es una nueva `ViewGroup` subclase que admite el diseño de vistas en una cuadrícula 2D, similar a una tabla HTML, como se muestra a continuación:

 [![Recorta GridLayout muestra cuatro celdas](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` funciona con una jerarquía de vista sin formato, donde vistas secundarias configure sus ubicaciones en la cuadrícula mediante la especificación de las filas y columnas deben tener. De este modo, el *GridLayout* es capaz de colocar las vistas en la cuadrícula sin necesidad de que todas las vistas intermedias proporcionan una estructura de tabla, tal como se muestra en las filas de tabla utilizadas en el TableLayout. Mediante el mantenimiento de una jerarquía plana, *GridLayout* es capaz de diseño con más rapidez sus vistas secundarias. Echemos un vistazo a un ejemplo para ilustrar este concepto realmente significado en el código.


## <a name="creating-a-grid-layout"></a>Crear un diseño de cuadrícula

El siguiente código XML agrega varias `TextView` controles a un *GridLayout*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

El diseño ajustará los tamaños de fila y columna para que las celdas pueden ajustar su contenido, como se muestra en el diagrama siguiente:

 [![Diagrama de diseño que muestra dos celdas de la izquierda inferior de la derecha](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Esto da como resultado la siguiente interfaz de usuario cuando se ejecuta en una aplicación:

 [![Aplicación de captura de pantalla de GridLayoutDemo muestra cuatro celdas](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>Especifica la orientación

Tenga en cuenta en el XML anterior, cada uno de ellos `TextView` no especifica una fila o columna. Cuando no se especifican, el `GridLayout` asigna cada vista secundaria en orden, según la orientación. Por ejemplo, vamos a cambiar orientación del GridLayout del valor predeterminado, que es la posición horizontal, vertical a similar al siguiente:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

Ahora, el `GridLayout` colocará las celdas de arriba a abajo, en cada columna, en lugar de izquierda a derecha, como se muestra a continuación:

 [![Diagrama que ilustra cómo se colocan las celdas en orientación vertical](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Esto da como resultado la siguiente interfaz de usuario en tiempo de ejecución:

 [![Captura de pantalla de GridLayoutDemo con celdas que se coloca en orientación vertical](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>Especifica la posición explícitos

Si desea controlar de manera explícita las posiciones de las vistas secundarias en el `GridLayout`, podemos establecer sus `layout_row` y `layout_column` atributos. Por ejemplo, se producirá el siguiente código XML en el que se muestra en la primera captura de pantalla (mostrado arriba), independientemente de la orientación de diseño.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```



### <a name="specifying-spacing"></a>Especifica el espaciado

Tenemos un par de opciones que proporcionará el espaciado entre el elemento secundario vistas de la `GridLayout`. Podemos usar el `layout_margin` atributo para establecer el margen en cada vista secundaria directamente, como se muestra a continuación

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Además, en Android 4, llama una nueva vista de uso general espaciado `Space` ahora está disponible. Para ello, basta con agregarlo como una vista secundaria.
Por ejemplo, el XML siguiente agrega una fila adicional a la `GridLayout` estableciendo su `rowcount` a 3 y agrega un `Space` vista que proporciona el espaciado entre el `TextViews`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

Este XML crea espacios en el `GridLayout` tal como se muestra a continuación:

 [![Captura de pantalla de GridLayoutDemo que ilustra las celdas más grandes con espaciado](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

La ventaja de usar el nuevo `Space` vista es que permite para el espaciado y no requieren el establecimiento de atributos en cada vista secundaria.



### <a name="spanning-columns-and-rows"></a>Expansión de las columnas y filas

El `GridLayout` también es compatible con las celdas que abarcan varias columnas y filas. Por ejemplo, supongamos que agregamos otra fila que contiene un botón a la `GridLayout` tal como se muestra a continuación:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

Esto dará como resultado de la primera columna de la `GridLayout` que se ajusta para adaptarse al tamaño del botón, como vemos aquí:

[![Captura de pantalla de GridLayoutDemo con el botón de expansión solo la primera columna](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Para evitar la primera columna del ajuste, podemos establecer el botón para abarcar dos columnas estableciendo su columnspan similar al siguiente:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Esto da como resultado un diseño para el `TextViews` que es similar al diseño teníamos anteriormente, con el botón Agregar a la parte inferior de la `GridLayout` tal como se muestra a continuación:

 [![Captura de pantalla de GridLayoutDemo con el botón que abarca ambas columnas](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>Vínculos relacionados

- [GridLayoutDemo (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Introducción a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android de 4.0](https://developer.android.com/sdk/android-4.0.html)
