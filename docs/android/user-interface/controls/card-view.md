---
title: CardView
description: El widget Cardview es un componente de interfaz de usuario que presenta el contenido de texto e imagen en vistas que son similares a las tarjetas. Esta guía explica cómo usar y personalizar CardView en Xamarin.Android aplicaciones mientras se mantiene la compatibilidad con versiones anteriores de Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 21e2a2e8ef04936664344cb4fb758bc2af3b4d05
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="cardview"></a>CardView

_El widget Cardview es un componente de interfaz de usuario que presenta el contenido de texto e imagen en vistas que son similares a las tarjetas. Esta guía explica cómo usar y personalizar CardView en Xamarin.Android aplicaciones mientras se mantiene la compatibilidad con versiones anteriores de Android._


## <a name="overview"></a>Información general

El `Cardview` widget, introducido en Android 5.0 (círculo), es un componente de interfaz de usuario que presenta el contenido de texto e imagen en vistas que son similares a las tarjetas. `CardView` se implementa como un `FrameLayout` widget con esquinas redondeadas y una sombra. Normalmente, un `CardView` se utiliza para presentar un elemento de fila única en una `ListView` o `GridView` grupo de vista. Por ejemplo, la captura de pantalla siguiente es un ejemplo de una aplicación de reservas de viajes que implementa `CardView`-tarjetas de destino de viaje en un desplazamiento basadas en `ListView`:

![Aplicación de ejemplo con un objeto CardView para cada destino de viaje](card-view-images/01-cardview-example.png)

Esta guía explica cómo agregar la `CardView` proyecto de paquete para su Xamarin.Android, cómo agregar `CardView` para el diseño y cómo personalizar la apariencia de `CardView` en la aplicación. Además, esta guía proporciona una lista detallada de `CardView` atributos que se pueden cambiar, incluidos los atributos para ayudarle a usar `CardView` en versiones de Android anteriores a Android 5.0 el círculo.

<a name="requirements" />

## <a name="requirements"></a>Requisitos

Se requiere lo siguiente para usar nueva Android 5.0 y versiones posteriores características (incluidos los `CardView`) en aplicaciones basadas en Xamarin:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 o posterior debe instalarse y configurarse con Visual Studio o Visual Studio para Mac.

-  **SDK de Android** &ndash; Android 5.0 (API de 21) o posterior debe estar instalado mediante el Administrador de SDK de Android.

-  **Java JDK 1.8** &ndash; JDK 1.7 puede usarse si se está específicamente como destino la API de nivel 23 y versiones anterior. 1.8 de JDK está disponible en [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

La aplicación también debe incluir el `Xamarin.Android.Support.v7.CardView` paquete. Para agregar el `Xamarin.Android.Support.v7.CardView` paquetes en Visual Studio para Mac:

1. Abra el proyecto, haga clic en **paquetes** y seleccione **agregar paquetes de**.

2. En el **agregar paquetes de** cuadro de diálogo, busque **CardView**.

3. Seleccione **CardView v7 de biblioteca de compatibilidad de Xamarin**, a continuación, haga clic en **Agregar paquete**.

Para agregar el `Xamarin.Android.Support.v7.CardView` paquetes en Visual Studio:

1. Abra el proyecto, haga clic en el **referencias** nodo (en el **el Explorador de soluciones** panel) y seleccione **administrar paquetes de NuGet...** .

2. Cuando el **administrar paquetes de NuGet** se muestra el cuadro de diálogo, escriba **CardView** en el cuadro de búsqueda.

3. Cuando **CardView v7 de biblioteca de compatibilidad de Xamarin** aparece, haga clic en **instalar**.

Para obtener información sobre cómo configurar un proyecto de aplicación de Android 5.0, consulte [configuración de un proyecto de Android 5.0](~/android/platform/lollipop.md).
Para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: incluidos unos NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


## <a name="introducing-cardview"></a>Introducción a CardView

El valor predeterminado `CardView` es similar a una tarjeta blanca con esquinas redondeadas como mínimo y una sombra ligera. En el ejemplo siguiente se **Main.axml** diseño muestra una sola `CardView` widget que contiene un `TextView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Si usa este código XML para reemplazar el contenido existente de **Main.axml**, no olvide comentar cualquier código en **MainActivity.cs** que hace referencia a los recursos en el XML anterior.

En este ejemplo de diseño crea un valor predeterminado `CardView` con una sola línea de texto, tal como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de CardView con un fondo blanco y línea de texto](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

En este ejemplo, se establece el estilo de la aplicación a la luz Material tema (`Theme.Material.Light`) para que la `CardView` bordes y sombras son fáciles de ver. Para obtener más información acerca de las aplicaciones de creación de temas Android 5.0, consulte [Material tema](~/android/user-interface/material-theme.md). En la siguiente sección, se obtendrá información sobre cómo personalizar `CardView` para una aplicación.


## <a name="customizing-cardview"></a>Personalizar CardView

Puede modificar las opciones básicas `CardView` atributos para personalizar la apariencia de la `CardView` en la aplicación. Por ejemplo, la elevación de la `CardView` puede aumentarse para convertir una sombra mayor (lo que hace que la tarjeta parece float más alto por encima del fondo). Además, puede aumentarse el radio de redondeo para hacer las esquinas de la tarjeta más redondeadas.

En el siguiente ejemplo de diseño, una personalizada `CardView` se utiliza para crear una simulación de una fotografía de impresión (una "instantánea"). Un `ImageView` se agrega a la `CardView` para mostrar la imagen y un `TextView` se coloca debajo de la `ImageView` para mostrar el título de la imagen.
En este ejemplo de diseño, el `CardView` tiene las siguientes personalizaciones:

-  El `cardElevation` ha aumentado a 4dp para convertir una sombra mayor.
-  El `cardCornerRadius` ha aumentado a 5dp para que las esquinas aparezcan más redondeadas.

Dado que `CardView` es proporcionada por la biblioteca de compatibilidad de Android v7, sus atributos no están disponibles desde el `android:` espacio de nombres. Por lo tanto, debe definir su propio espacio de nombres XML y usar ese espacio de nombres como el `CardView` prefijo del atributo. En el siguiente ejemplo de diseño, usaremos esta línea para definir un espacio de nombres denominado `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Puede llamar a este espacio de nombres `card_view` o incluso `myapp` si elige (es accesible únicamente dentro del ámbito de este archivo). Todo lo que desee llamar a este espacio de nombres, debe utilizar como prefijo para el `CardView` atributo que desea modificar. En este ejemplo de diseño, el `cardview` espacio de nombres es el prefijo de `cardElevation` y `cardCornerRadius`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Cuando se usa en este ejemplo de diseño para mostrar una imagen en una aplicación de visualización de fotografías, la `CardView` tiene la apariencia de una instantánea de la foto, como se describe en la siguiente captura de pantalla:

[![CardView con una imagen y la leyenda debajo de la imagen](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Esta captura de pantalla se toma de la [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) aplicación de ejemplo que utiliza un `RecyclerView` widget para presentar una lista desplazable de `CardView` imágenes para ver fotografías. Para obtener más información acerca de `RecyclerView`, consulte el [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) guía.

Tenga en cuenta que un `CardView` puede mostrar más de una vista secundaria en su área de contenido. Por ejemplo, en el ejemplo de aplicación de visualización de fotografías de anterior, el área de contenido se compone de un `ListView` que contiene un `ImageView` y `TextView`. Aunque `CardView` instancias a menudo se organizan verticalmente, también se puede organizar en horizontal (vea [crear un estilo de vista personalizada](~/android/user-interface/material-theme.md#customview) para una captura de pantalla de ejemplo).


### <a name="cardview-layout-options"></a>Opciones de diseño de CardView

`CardView` se pueden personalizar diseños estableciendo uno o más atributos que afectan a su relleno, la elevación, el radio de redondeo y el color de fondo:

[![Diagrama de CardView atributos](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Cada atributo también se puede cambiar dinámicamente mediante una llamada a un equivalente `CardView` (método) (para obtener más información sobre `CardView` métodos, vea el [referencia de clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Tenga en cuenta que estos atributos (excepto para el color de fondo) aceptan un valor de la dimensión, que es un número decimal seguido de la unidad. Por ejemplo, `11.5dp` especifica 11,5 píxeles independientes de densidad.


#### <a name="padding"></a>Relleno
`
CardView` ofrece cinco atributos de relleno para colocar el contenido dentro de la tarjeta. Se puede establecer en el diseño de XML o puede llamar a métodos análogos en el código:

[![Diagrama de CardView atributos de relleno](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Los atributos de relleno se explican los siguientes:

-  `contentPadding` &ndash; Interna el relleno entre las vistas secundarias de la `CardView` y todos los bordes de la tarjeta.

-  `contentPaddingBottom` &ndash; Interna el relleno entre las vistas secundarias de la `CardView` y el borde inferior de la tarjeta.

-  `contentPaddingLeft` &ndash; Interna el relleno entre las vistas secundarias de la `CardView` y el borde izquierdo de la tarjeta.

-  `contentPaddingRight` &ndash; Interna el relleno entre las vistas secundarias de la `CardView` y el borde derecho de la tarjeta.

-  `contentPaddingTop` &ndash; Interna el relleno entre las vistas secundarias de la `CardView` y el borde superior de la tarjeta.

Los atributos de relleno de contenido son con respecto a los límites del área de contenido en lugar de a cualquier determinado widget ubicados en el área de contenido.
Por ejemplo, si `contentPadding` suficientemente aumentaran en la aplicación de visualización de fotografías, la `CardView` podría recortar la imagen y el texto que aparece en la tarjeta.



#### <a name="elevation"></a>Elevación

`CardView` ofrece dos atributos de elevación para controlar su elevación y, como resultado, el tamaño de la sombra:

[![Diagrama de atributos de elevación CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Los atributos de elevación se explican los siguientes:

-  `cardElevation` &ndash; La elevación de los `CardView` (representa su eje Z).

-  `cardMaxElevation` &ndash; El valor máximo de la `CardView`de elevación.

Los valores mayores de `cardElevation` aumentar el tamaño de la sombra para realizar `CardView` parece float más alto por encima del fondo. El `cardElevation` atributo también determina el orden de dibujo de superpuestas vistas; es decir, el `CardView` se dibujará en otra vista que se superponen con un valor de elevación superior y por encima de todas las vistas que se superponen con un valor inferior de elevación.
El `cardMaxElevation` opción es útil para cuando la aplicación cambia dinámicamente a elevación &ndash; se evita que la sombra de extender más allá del límite que se define con esta configuración.


#### <a name="corner-radius-and-background-color"></a>Radio de redondeo y el Color de fondo

`CardView` ofrece los atributos que se pueden usar para controlar el radio de redondeo y su color de fondo. Estas dos propiedades permiten cambiar el estilo general de la `CardView`:

[![Diagrama de esquina CardView radious y atributos de color de fondo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Estos atributos se explican los siguientes:

-  `cardCornerRadius` &ndash; El radio de redondeo de todas las esquinas de la `CardView`.

-  `cardBackgroundColor` &ndash; El color de fondo de la `CardView`.

En este diagrama, `cardCornerRadius` está establecido en un 10dp más redondeado y `cardBackgroundColor` se establece en `"#FFFFCC"` (amarillo claro).


## <a name="compatibility"></a>Compatibilidad

Puede usar `CardView` en versiones de Android anteriores a Android 5.0 el círculo. Dado que `CardView` forma parte de la biblioteca de compatibilidad de Android v7, que se puede usar `CardView` con Android 2.1 (API nivel 7) y versiones posteriores.
Sin embargo, debe instalar la `Xamarin.Android.Support.v7.CardView` paquete tal y como se describe en [requisitos](#requirements)anteriormente.

`CardView` exhibe un comportamiento ligeramente diferente en los dispositivos antes de círculo (API nivel 21):

-  `CardView` utiliza una implementación de instantáneas mediante programación que agrega relleno adicionales.

-  `CardView` no se recorta vistas secundarias que forman una intersección con el `CardView`de esquinas redondeadas.

Para ayudarle a administrar estas diferencias de compatibilidad, `CardView` proporciona varios atributos adicionales que puede configurar en el diseño:

-   `cardPreventCornerOverlap` &ndash; Establezca este atributo en `true` para agregar relleno cuando la aplicación se ejecuta en anteriores versiones de Android (nivel de API 20 y versiones anterior). Esta configuración impide que `CardView` contenido de intersección entre el `CardView`de esquinas redondeadas.

-   `cardUseCompatPadding` &ndash; Establezca este atributo en `true` para agregar relleno cuando la aplicación se ejecuta en las versiones de Android en o mayor que el nivel de API 21. Si desea usar `CardView` en dispositivos de círculo previo y para que busque el mismo en el círculo (o posterior), establezca este atributo en `true`. Cuando este atributo está habilitado, `CardView` agrega relleno adicionales para dibujar las sombras cuando se ejecuta en dispositivos de círculo previo. Esto ayuda a superar las diferencias de relleno que se producen cuando las implementaciones de instantáneas mediante programación previa círculo están en vigor.

Para obtener más información acerca de cómo mantener la compatibilidad con versiones anteriores de Android, consulte [mantener compatibilidad](https://developer.android.com/training/material/compatibility.html).


## <a name="summary"></a>Resumen

Esta guía introdujo el nuevo `CardView` widget incluido en Android 5.0 (círculo). Muestra el valor predeterminado `CardView` apariencia y la explicación de cómo personalizar `CardView` cambiando su elevación, redondeados de las esquinas, relleno de contenido y color de fondo. Aparecerán los `CardView` atributos de diseño (con los diagramas de referencia) y explica cómo usar `CardView` en dispositivos Android anteriores a Android 5.0 el círculo. Para obtener más información acerca de `CardView`, consulte el [referencia de clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Vínculos relacionados

- [RecyclerView (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introducción a círculo](~/android/platform/lollipop.md)
- [Referencia de clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
