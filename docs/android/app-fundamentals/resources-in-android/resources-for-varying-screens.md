---
title: Creación de recursos para diferentes pantallas
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: df8ee3da8a1341cd1dd879e8e70687d9fbd9957b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117439"
---
# <a name="creating-resources-for-varying-screens"></a>Creación de recursos para diferentes pantallas

Android sí se ejecuta en muchos dispositivos diferentes, cada uno con una amplia variedad de densidades de pantalla, tamaños de pantalla y resoluciones. Android llevará a cabo el escalado y el cambio de tamaño para que la aplicación funcione en estos dispositivos, pero esto puede dar lugar a una experiencia de usuario deficiente. Por ejemplo, las imágenes podrían aparecer borrosas o puede colocarse según lo previsto en una vista.


## <a name="concepts"></a>Conceptos

Unos términos y conceptos son importantes para comprender para admitir varias pantallas.

- **Tamaño de pantalla** &ndash; la cantidad de espacio físico para mostrar la aplicación

- **Densidad de pantalla** &ndash; el número de píxeles en cualquier área determinada en la pantalla. La unidad de medida típica es puntos por pulgada (PPP).

- **Resolución** &ndash; el número total de píxeles en la pantalla. Al desarrollar aplicaciones, no es tan importante como la densidad y el tamaño de la pantalla resolución.

- **Independiente de la densidad de píxeles (dp)** &ndash; una unidad virtual de medida para permitir que los diseños diseñarse independientes de densidad. Esta fórmula se utiliza para convertir dp en píxeles de pantalla:

    px &equals; dp &times; PPP &divide; 160

- **Orientación** &ndash; orientación de la pantalla se considera horizontal cuando resulta más ancha alta. En cambio, orientación vertical es cuando la pantalla es más alta ancha. Puede cambiar la orientación durante la vigencia de una aplicación como el usuario gira el dispositivo.

Tenga en cuenta que las primeras tres de estos conceptos están relacionadas entre &ndash; aumento de la resolución sin aumentar la densidad, aumentará el tamaño de la pantalla. Sin embargo si se aumentan la densidad y la resolución, a continuación, el tamaño de pantalla puede permanecer sin cambios. Esta relación entre el tamaño de la pantalla, la densidad y la resolución complicar la compatibilidad con pantalla rápidamente.

Para ayudar a tratar con esta complejidad, prefiere usar el marco de trabajo Android *independientes de la densidad de píxeles (dp)* para diseños de pantalla. Mediante el uso de píxeles independientes de densidad, los elementos de interfaz de usuario aparecerá para el usuario tenga el mismo tamaño físico en pantallas con densidades diferentes.


## <a name="supporting-various-screen-sizes-and-densities"></a>Compatibilidad con distintos tamaños de pantalla y densidades

Android administra la mayor parte del trabajo para representar los diseños correctamente para cada configuración de pantalla. Sin embargo, hay algunas acciones que pueden considerarse como el sistema de ayuda.

El uso de independiente de la densidad de píxeles en lugar de píxeles reales en los diseños es suficiente en la mayoría de los casos para garantizar la independencia de la densidad.
Android escalará lo recursos drawable en tiempo de ejecución para el tamaño adecuado.
Sin embargo, es posible que el escalado hará que se ve borroso de mapas de bits. Para solucionar este problema, proporcionar recursos alternativos para las densidades diferentes. Al diseñar dispositivos varias densidades de pantalla y resoluciones, resultará más fácil empezar con la resolución más alta o densidad imágenes y, a continuación, reducir verticalmente.


### <a name="declare-the-supported-screen-size"></a>Declara el tamaño de pantalla compatibles

Declarar el tamaño de pantalla, se garantiza que solo los dispositivos compatibles pueden descargar la aplicación. Esto se consigue estableciendo la [admite pantallas](http://developer.android.com/guide/topics/manifest/supports-screens-element.html) elemento en el **AndroidManifest.xml** archivo. Este elemento se utiliza para especificar qué tamaños de pantalla son compatibles con la aplicación. Se considera una pantalla dada se admite si la aplicación puede colocar correctamente sus diseños para rellenar la pantalla. Mediante el uso de este elemento del manifiesto, la aplicación no se mostrará en [ *Google Play* ](https://play.google.com/) para dispositivos que no cumplen las especificaciones de la pantalla. Sin embargo, la aplicación se ejecutará en dispositivos con pantallas no compatibles, pero los diseños que pueden aparecer borrosos y pixelada.

Pantalla compatibles descubiertos seises se declaran en el **Properites/AndroidManifest.xml** archivo de la solución:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Manifiesto de Android](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Manifiesto de Android](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

Editar **AndroidManifest.xml** para incluir [admite pantallas](http://developer.android.com/guide/topics/manifest/supports-screens-element.html):

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="HelloWorld.HelloWorld">
      <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="27" />
      <supports-screens android:resizable="true"
                        android:smallScreens="true"
                        android:normalScreens="true"
                        android:largeScreens="true" />
      <application android:allowBackup="true"
                   android:icon="@mipmap/ic_launcher"
                   android:label="@string/app_name"
                   android:roundIcon="@mipmap/ic_launcher_round"
                   android:supportsRtl="true" android:theme="@style/AppTheme">
  </application>
</manifest>
```

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Proporcionar diseños alternativos para distintos tamaños de pantalla


Diseños alternativos permiten personalizar una vista para un tamaño de pantalla específico, cambiar la posición o el tamaño de los elementos de interfaz de usuario del componente.

A partir de la API de nivel de 13 (Android 3.2), los tamaños de pantalla están en desuso en favor de la sw*N*calificador dp. Este nuevo calificador declara que necesita la cantidad de espacio en un diseño determinado. Se recomienda que las aplicaciones que están diseñadas para ejecutarse en Android 3.2 o posterior deben usar estos calificadores más reciente.

Por ejemplo, si un diseño requiere un mínimo 700 dp del ancho de pantalla, el diseño alternativo iría en una carpeta **diseño sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Carpeta de diseño para el ancho de pantalla de 700 dp](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Carpeta de diseño para el ancho de pantalla de 700 dp](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


Como norma, estos son algunos números para varios dispositivos:

- **Teléfono típico** &ndash; 320 dp: un teléfono normal

- **Una tableta 5"/"tweener"dispositivo** &ndash; 480 dp: por ejemplo, la nota de Samsung

- **Un equipo Tablet PC 7"** &ndash; 600 dp: como la Barnes &amp; Nook Noble

- **Una tableta 10"** &ndash; 720 dp: por ejemplo, el Motorola Xoom

Para las aplicaciones que los niveles de API de destino hasta 12 (Android 3.1), los diseños deben ir en los directorios que usan los calificadores **pequeño**/**normal**/**grandes**  / **extra grande** como generalizaciones de los diferentes tamaños de pantalla que están disponibles en la mayoría de los dispositivos. Por ejemplo, en la imagen siguiente, hay recursos alternativos para los cuatro tamaños de pantalla diferentes:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos para cuatro tamaños de pantalla](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos para cuatro tamaños de pantalla](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

La siguiente es una comparación de cómo se comparan los calificadores de tamaño de pantalla de nivel 13 pre-API anteriores independiente de la densidad de píxeles:

- 426 dp x 320 dp es **pequeño**

- 470 dp x 320 dp es **normal**

- 640 dp x 480 dp es **grandes**

- 960 dp x 720 dp es **extra grande**

La pantalla más reciente tamaño calificadores en el nivel de API 13 y seguridad tienen una mayor prioridad que los calificadores de pantalla anterior de los niveles de API 12 y versiones inferiores.
Para las aplicaciones que abarquen la antigua y los nuevos niveles de API, puede ser necesario crear recursos alternativos con ambos conjuntos de calificadores como se muestra en la captura de pantalla siguiente:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Recursos alternativos con calificadores de ambos](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Recursos alternativos con calificadores de ambos](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----



### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Proporcionar distintos mapas de bits para densidades de pantalla diferentes

Aunque Android escalará los mapas de bits según sea necesario para un dispositivo, los propios mapas de bits no pueden escalar elegantemente arriba o hacia abajo: es posible que estén borrosa o aproximada. Que proporciona los mapas de bits adecuado para la densidad de pantalla se solucionará este problema.

Por ejemplo, la imagen siguiente es un ejemplo de diseño y la apariencia de los problemas que pueden producirse cuando densidad-especificar los recursos no se proporcionan.

![Capturas de pantalla sin recursos de densidad](resources-for-varying-screens-images/06-density-not-provided.png)

Compare esto con un diseño que se ha diseñado con recursos específicos de la densidad:

![Capturas de pantalla con los recursos específicos de densidad](resources-for-varying-screens-images/07-density-specific-resources.png)


### <a name="create-varying-density-resources-with-android-asset-studio"></a>Creación de diferentes recursos de densidad con Android Asset Studio

La creación de estos mapas de bits de varias densidades puede ser un poco tediosa. Por lo tanto, Google ha creado una utilidad que puede reducir algunos de las relacionados con la creación de estos mapas de bits llamado las tediosas tareas el [ **Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

Este sitio Web le ayudará con la creación de mapas de bits que tienen como destino las densidades de pantalla comunes cuatro proporcionando una imagen. Android Asset Studio creará, a continuación, los mapas de bits con algunas personalizaciones y permitirles descargar como archivo zip.


## <a name="tips-for-multiple-screens"></a>Sugerencias para varias pantallas

Android se ejecuta en un número de dispositivos desconcertante, y la combinación de tamaños de pantalla y densidades de pantalla puede parecer abrumador. Las siguientes sugerencias pueden ayudar a minimizar el esfuerzo necesario para admitir varios dispositivos:

- **Solo se diseñan y desarrollan por lo que necesita** &ndash; hay muchos dispositivos diferentes ahí fuera, pero algunos existen en factores de forma poco frecuentes que pueden tardar un esfuerzo significativo para diseñar y desarrollar para. El [ **tamaño de pantalla y la densidad** ](http://developer.android.com/resources/dashboard/screens.html) panel es una página de Google que proporciona datos sobre el desglose de la matriz de densidad de pantalla o el tamaño de pantalla. Este desglose proporciona información sobre cómo el esfuerzo de desarrollo sobre la compatibilidad con pantallas.

- **Utilice DPs en lugar de píxeles** -píxeles se convierten en problemáticos como cambios de densidad de pantalla. No codificar valores en píxeles. Evite los píxeles en favor de dp (independientes de la densidad de píxeles).

- **Evitar** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **siempre que sea posible** &ndash; que está en desuso en el nivel 3 (1.5 Android) de la API y provocará en diseños frágiles. No se recomienda. En su lugar, intente usar los widgets de diseño más flexibles, como [ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/), [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), o en el nuevo [ **GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/).

- **Elija una orientación de diseño como su valor predeterminado** &ndash; por ejemplo, en lugar de proporcionar los recursos alternativos **diseño land** y **diseño-port**, coloque los recursos para vista horizontal en **diseño**y los recursos de vertical a **diseño-port**.

- **Usar LayoutParams correspondientes al alto y ancho** : al definir los elementos de interfaz de usuario en un archivo de diseño XML, una aplicación Android mediante el **wrap_content** y **fill_parent** valores tendrá más éxito Asegúrese de un vistazo adecuado en diferentes dispositivos que el uso de píxeles o unidades independientes de densidad. Estos valores de dimensión provocar Android para escalar los recursos de mapa de bits según corresponda. Por esta misma razón, están mejor reservadas unidades independientes de densidad para cuando especificando los márgenes y relleno de los elementos de interfaz de usuario.


## <a name="testing-multiple-screens"></a>Las pruebas de varias pantallas

Una aplicación de Android se deba probar en todas las configuraciones que serán compatibles. Lo ideal es que los dispositivos deben probarse en los propios dispositivos, pero en muchos casos esto no es posible ni práctico.
En este caso, el uso del emulador y el programa de instalación de dispositivos virtuales Android para cada configuración del dispositivo será útil.

El SDK de Android proporciona algunas máscaras pueden utilizarse para crear AVD de emulador replicará el tamaño, la densidad y la resolución de muchos dispositivos.
Del mismo modo, muchos de los proveedores de hardware proporcionan máscaras para sus dispositivos.

Otra opción es usar los servicios de un servicio de prueba de terceros.
Estos servicios se tome un APK, ejecutarla en muchos dispositivos diferentes y, a continuación, proporcionar comentarios cómo la aplicación funcionó.
