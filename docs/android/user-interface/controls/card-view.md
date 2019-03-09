---
title: CardView
description: El widget Cardview es un componente de interfaz de usuario que presenta el contenido de texto e imagen en vistas que son similares a las tarjetas. Esta guía explica cómo usar y personalizar CardView en aplicaciones de Xamarin.Android y mantener la compatibilidad con versiones anteriores con versiones anteriores de Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: cdb75207bff3f15a54d0cdd90fa0833da9c145e6
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670654"
---
# <a name="cardview"></a>CardView

_El widget Cardview es un componente de interfaz de usuario que presenta el contenido de texto e imagen en vistas que son similares a las tarjetas. Esta guía explica cómo usar y personalizar CardView en aplicaciones de Xamarin.Android y mantener la compatibilidad con versiones anteriores con versiones anteriores de Android._


## <a name="overview"></a>Información general

El `Cardview` widget, introducido en Android 5.0 (Lollipop), es un componente de interfaz de usuario que presenta el contenido de texto e imagen en vistas que son similares a las tarjetas. `CardView` se implementa como un `FrameLayout` widget con esquinas redondeadas y una sombra. Normalmente, un `CardView` se utiliza para presentar un elemento de fila única en un `ListView` o `GridView` grupo ver. Por ejemplo, la captura de pantalla siguiente es un ejemplo de una aplicación de reserva de viajes que implementa `CardView`-tarjetas de destino de viaje en un desplazamiento basadas en `ListView`:

![Aplicación de ejemplo con un CardView para cada destino de viaje](card-view-images/01-cardview-example.png)

Esta guía explica cómo agregar la `CardView` proyecto de paquete para su Xamarin.Android, cómo agregar `CardView` para el diseño y cómo personalizar la apariencia de `CardView` en la aplicación. Además, esta guía proporciona una lista detallada de `CardView` atributos que se pueden cambiar, incluidos los atributos para ayudarle a usar `CardView` en versiones de Android anteriores a Android 5.0 Lollipop.

<a name="requirements" />

## <a name="requirements"></a>Requisitos

Se requiere lo siguiente para usar nuevo Android 5.0 y versiones posteriores características (incluido `CardView`) en aplicaciones de Xamarin:

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

-  **SDK de Android** &ndash; Android 5.0 (API 21) o posterior debe estar instalado a través de Android SDK Manager.

-  **Java JDK 1.8** &ndash; JDK 1.7 puede usarse si se está específicamente dirigidos a API nivel 23 y versiones anteriores. Está disponible en JDK 1.8 [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

La aplicación también debe incluir el `Xamarin.Android.Support.v7.CardView` paquete. Para agregar el `Xamarin.Android.Support.v7.CardView` paquetes en Visual Studio para Mac:

1. Abra el proyecto, haga clic en **paquetes** y seleccione **agregar paquetes**.

2. En el **agregar paquetes** cuadro de diálogo, busque **CardView**.

3. Seleccione **Xamarin Support Library v7 CardView**, a continuación, haga clic en **Agregar paquete**.

Para agregar el `Xamarin.Android.Support.v7.CardView` paquetes en Visual Studio:

1. Abra el proyecto, haga clic en el **referencias** nodo (en el **el Explorador de soluciones** panel) y seleccione **administrar paquetes NuGet...** .

2. Cuando el **administrar paquetes de NuGet** muestra el cuadro de diálogo, escriba **CardView** en el cuadro de búsqueda.

3. Cuando **Xamarin Support Library v7 CardView** aparece, haga clic en **instalar**.

Para obtener información sobre cómo configurar un proyecto de aplicación de Android 5.0, consulte [configuración de un proyecto de Android 5.0](~/android/platform/lollipop.md).
Para obtener más información acerca de cómo instalar paquetes de NuGet, consulte [Tutorial: Incluir NuGet en el proyecto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


## <a name="introducing-cardview"></a>Introducción a CardView

El valor predeterminado `CardView` es similar a una tarjeta con esquinas redondeadas como mínimo y una leve sombrea blanco. En el siguiente ejemplo **Main.axml** diseño muestra un único `CardView` widget que contiene un `TextView`:

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

Si usa este código XML para reemplazar el contenido existente de **Main.axml**, no olvide Comente cualquier código en **MainActivity.cs** que hace referencia a recursos en el XML anterior.

Este ejemplo de diseño crea un valor predeterminado `CardView` con una sola línea de texto, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de CardView con un fondo blanco y línea de texto](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

En este ejemplo, se establece el estilo de la aplicación a la luz de Material de tema (`Theme.Material.Light`) para que el `CardView` bordes y sombras son más fáciles de ver. Para obtener más información acerca de las aplicaciones de creación de temas Android 5.0, consulte [Material tema](~/android/user-interface/material-theme.md). En la siguiente sección, se obtendrá información sobre cómo personalizar `CardView` para una aplicación.


## <a name="customizing-cardview"></a>Personalizar CardView

Puede modificar las opciones básicas `CardView` atributos para personalizar la apariencia de la `CardView` en la aplicación. Por ejemplo, la elevación de la `CardView` puede aumentarse para convertir una sombra más grande (lo que hace que la tarjeta se parezca a float más alto por encima del fondo). Además, se puede aumentar el radio de redondeo para hacer las esquinas de la tarjeta más redondeadas.

En el siguiente ejemplo de diseño, una personalizada `CardView` se usa para crear una simulación de una fotografía de impresión (una "instantánea"). Un `ImageView` se agrega a la `CardView` para mostrar la imagen y un `TextView` se coloca bajo el `ImageView` para mostrar el título de la imagen.
En este ejemplo de diseño, el `CardView` tiene las siguientes personalizaciones:

-  El `cardElevation` ha aumentado a 4dp para convertir una sombra más grande.
-  El `cardCornerRadius` ha aumentado a 5dp para que las esquinas aparezcan más redondeado.

Dado que `CardView` es proporcionada por la biblioteca de compatibilidad de Android v7, sus atributos no están disponibles desde el `android:` espacio de nombres. Por lo tanto, debe definir su propio espacio de nombres XML y usar ese espacio de nombres como el `CardView` prefijo del atributo. En el siguiente ejemplo de diseño, usaremos esta línea para definir un espacio de nombres denominado `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Puede llamar a este espacio de nombres `card_view` o incluso `myapp` si elige (es accesible únicamente dentro del ámbito de este archivo). Todo lo que elegir llamar a este espacio de nombres, debe usar como prefijo la `CardView` atributo que desea modificar. En este ejemplo de diseño, el `cardview` espacio de nombres es el prefijo de `cardElevation` y `cardCornerRadius`:

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

Cuando se usa en este ejemplo de diseño para mostrar una imagen en una aplicación de visualización de fotografías, la `CardView` tiene la apariencia de una instantánea de la foto, como se muestra en la captura de pantalla siguiente:

[![CardView con una imagen y título debajo de la imagen](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Esta captura de pantalla se toma de la [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) aplicación de ejemplo que usa un `RecyclerView` widget para presentar una lista desplazable de `CardView` imágenes para visualizar fotos. Para obtener más información acerca de `RecyclerView`, consulte el [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) guía.

Tenga en cuenta que un `CardView` puede mostrar más de una vista secundaria en su área de contenido. Por ejemplo, en el ejemplo de aplicación de visualización de fotografías de anterior, el área de contenido está formado por un `ListView` que contiene un `ImageView` y un `TextView`. Aunque `CardView` instancias a menudo se organizan verticalmente, también puede organizarlos horizontalmente (consulte [crear un estilo de vista personalizado](~/android/user-interface/material-theme.md#customview) para una captura de pantalla de ejemplo).


### <a name="cardview-layout-options"></a>Opciones de diseño CardView

`CardView` pueden personalizar los diseños estableciendo uno o varios atributos que afectan a su relleno, elevación, radio de redondeo y color de fondo:

[![Diagrama de CardView atributos](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Cada atributo también se puede cambiar dinámicamente mediante una llamada a un homólogo `CardView` (método) (para obtener más información sobre `CardView` métodos, vea el [referencia de clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Tenga en cuenta que estos atributos (excepto para el color de fondo) aceptan un valor de dimensión, que es un número decimal seguido de la unidad. Por ejemplo, `11.5dp` especifica 11,5 píxeles independientes de la densidad.


#### <a name="padding"></a>Relleno
`
CardView` ofrece cinco atributos de relleno para colocar el contenido dentro de la tarjeta. Puede establecerlas en su diseño XML o puede llamar a métodos análogos en el código:

[![Diagrama de CardView atributos de relleno](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Los atributos de relleno son explicarse como sigue:

-  `contentPadding` &ndash; Espaciado entre las vistas secundarias de interno el `CardView` y todos los bordes de la tarjeta.

-  `contentPaddingBottom` &ndash; Espaciado entre las vistas secundarias de interno el `CardView` y el borde inferior de la tarjeta.

-  `contentPaddingLeft` &ndash; Espaciado entre las vistas secundarias de interno el `CardView` y el borde izquierdo de la tarjeta.

-  `contentPaddingRight` &ndash; Espaciado entre las vistas secundarias de interno el `CardView` y el borde derecho de la tarjeta.

-  `contentPaddingTop` &ndash; Espaciado entre las vistas secundarias de interno el `CardView` y el borde superior de la tarjeta.

Los atributos de contenido de relleno son con respecto a los límites del área de contenido en lugar de a cualquier widget determinado que se encuentra dentro del área de contenido.
Por ejemplo, si `contentPadding` suficientemente se han aumentado en la aplicación de visualización de fotografías, la `CardView` desea recortar la imagen y el texto que aparece en la tarjeta.



#### <a name="elevation"></a>Elevación

`CardView` ofrece dos atributos de elevación para controlar su elevación y, como resultado, el tamaño de su sombra:

[![Diagrama de atributos de elevación CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Los atributos de elevación se explican los siguientes:

-  `cardElevation` &ndash; La elevación de la `CardView` (representa su eje Z).

-  `cardMaxElevation` &ndash; El valor máximo de la `CardView`de elevación.

Los valores mayores de `cardElevation` aumentar el tamaño de la sombra para realizar `CardView` parecen float más alto por encima del fondo. El `cardElevation` atributo también determina el orden de dibujo de superposición de las vistas; es decir, el `CardView` se dibujará en otra vista que se superponen con un valor superior de elevación y versiones posteriores de todas las vistas que se superponen con un valor menor de elevación.
El `cardMaxElevation` opción es útil para cuando la aplicación cambia dinámicamente a elevación &ndash; impide que la sombra extender más allá del límite que se define con esta configuración.


#### <a name="corner-radius-and-background-color"></a>Radio de redondeo y Color de fondo

`CardView` ofrece los atributos que puede usar para controlar el radio de redondeo y su color de fondo. Estas dos propiedades permiten cambiar el estilo general de la `CardView`:

[![Diagrama de esquina CardView radious y atributos de color de fondo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Estos atributos son explicarse como sigue:

-  `cardCornerRadius` &ndash; El radio de redondeo de las esquinas de la `CardView`.

-  `cardBackgroundColor` &ndash; El color de fondo de la `CardView`.

En este diagrama, `cardCornerRadius` está establecido en un 10dp más redondeado y `cardBackgroundColor` está establecido en `"#FFFFCC"` (amarillo claro).


## <a name="compatibility"></a>Compatibilidad

Puede usar `CardView` en versiones de Android anteriores a Android 5.0 Lollipop. Dado que `CardView` forma parte de la biblioteca de compatibilidad de Android v7, puede usar `CardView` con Android 2.1 (API de nivel 7) y versiones posteriores.
Sin embargo, debe instalar la `Xamarin.Android.Support.v7.CardView` paquete tal como se describe en [requisitos](#requirements)descritos anteriormente.

`CardView` muestra un comportamiento ligeramente diferente en los dispositivos antes de Lollipop (API nivel 21):

-  `CardView` usa una implementación mediante programación de instantáneas que agrega relleno adicional.

-  `CardView` no recorta vistas secundarias que se intersecan con el `CardView`del esquinas redondeadas.

Para ayudarle a administrar estas diferencias de compatibilidad, `CardView` proporciona varios atributos adicionales que se pueden configurar en su diseño:

-   `cardPreventCornerOverlap` &ndash; Establezca este atributo en `true` agregar relleno cuando la aplicación se está ejecutando en anteriores versiones de Android (nivel de API 20 y versiones anterior). Esta configuración evita `CardView` contenido desde que se cruzan con los `CardView`del esquinas redondeadas.

-   `cardUseCompatPadding` &ndash; Establezca este atributo en `true` agregar relleno cuando la aplicación se ejecuta en las versiones de Android a o mayor que el nivel de API 21. Si desea usar `CardView` en dispositivos de círculo previo y que parecen iguales en círculo (o posterior), establezca este atributo en `true`. Cuando este atributo está habilitado, `CardView` agrega relleno adicional para dibujar sombras cuando se ejecuta en dispositivos Lollipop previa. Esto ayuda a salvar las diferencias en el margen de que se presentan cuando las implementaciones de instantáneas mediante programación previa Lollipop están en vigor.

Para obtener más información sobre cómo mantener la compatibilidad con versiones anteriores de Android, consulte [mantener compatibilidad](https://developer.android.com/training/material/compatibility.html).


## <a name="summary"></a>Resumen

Esta guía presentan las nuevas `CardView` widget incluido en Android 5.0 (Lollipop). Se ha demostrado el valor predeterminado `CardView` apariencia y se explica cómo personalizar `CardView` cambiando su elevación, redondeados de las esquinas, relleno de contenido y el color de fondo. Se enumeran los `CardView` atributos de diseño (con diagramas de referencia) y se explica cómo usar `CardView` en dispositivos Android anteriores a Android 5.0 Lollipop. Para obtener más información acerca de `CardView`, consulte el [referencia de clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Vínculos relacionados

- [RecyclerView (ejemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introducción al círculo](~/android/platform/lollipop.md)
- [Referencia de clase CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
