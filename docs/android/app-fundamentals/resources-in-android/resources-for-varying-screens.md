---
title: "Creación de recursos para diferentes pantallas"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/12/2017
ms.openlocfilehash: aeb7115e3c7521f6679e8802eb759d7e56ba1cfe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="creating-resources-for-varying-screens"></a>Creación de recursos para diferentes pantallas

Android sí se ejecuta en varios dispositivos diferentes, cada uno con una amplia variedad de las densidades de pantalla, tamaños y resoluciones. Android llevará a cabo escalar y cambiar el tamaño para que la aplicación funcione en estos dispositivos, pero esto puede dar lugar a una experiencia de usuario poco óptimo. Por ejemplo, las imágenes pueden aparecer borrosas, imágenes pueden ocupar espacio de pantalla demasiada (o no hay suficientes) lo que hace que la posición de elementos de interfaz de usuario en el diseño se superponen o sea demasiado lejos.

<a name="Concepts" />

## <a name="concepts"></a>Conceptos

Unos términos y conceptos son importantes para comprender para admitir varias pantallas.

- **Tamaño de la pantalla** &ndash; la cantidad de espacio físico para mostrar la aplicación

- **Pantalla densidad** &ndash; el número de píxeles en cualquier área determinada en la pantalla. La unidad de medida típica es puntos por pulgada (PPP).

- **Resolución** &ndash; el número total de píxeles en la pantalla. Al desarrollar aplicaciones, no es tan importante como la densidad y el tamaño de la pantalla resolución.

- **Independiente de la densidad de píxeles (dp)** &ndash; se trata de una unidad virtual de medida para permitir diseños diseñarse independiente de densidad. Para convertir el punto de distribución en píxeles de la pantalla se usa la siguiente fórmula:

    px &equals; dp &times; PPP &divide; 160

- **Orientación** &ndash; orientación de la pantalla se considera horizontal cuando resulta más ancha alta. En cambio, orientación vertical es cuando la pantalla es más alta ancha. Puede cambiar la orientación durante la duración de una aplicación como el usuario gira el dispositivo.

Tenga en cuenta que las tres primeras de estos conceptos están relacionadas entre &ndash; aumentar la resolución sin aumentar la densidad se aumentará el tamaño de pantalla. Sin embargo si se aumentan la densidad y la resolución, a continuación, el tamaño de pantalla puede permanecer sin cambios. Esta relación entre el tamaño de la pantalla, la densidad y la resolución complicar la compatibilidad con la pantalla muy rápidamente.

Con el fin de tratar esta complejidad, prefiere usar el marco de trabajo Android *independiente de la densidad de píxeles (dp)* diseños de pantalla. Mediante el uso de píxeles independientes de densidad, elementos de interfaz de usuario aparecerá para el usuario tenga el mismo tamaño físico en pantallas con densidades diferentes.

<a name="Supporting_Various_Screen_Sizes_and_Densities" />

## <a name="supporting-various-screen-sizes-and-densities"></a>Compatibilidad con distintos tamaños de pantalla y densidades

Android administra la mayor parte del trabajo para representar los diseños correctamente para cada configuración de pantalla. Sin embargo, hay algunas acciones que pueden realizarse en el sistema de Ayuda de.

El uso de independiente de la densidad de píxeles en lugar de píxeles reales en los diseños es suficiente en la mayoría de los casos para garantizar la independencia de la densidad.
Android escalará el drawables en tiempo de ejecución para el tamaño adecuado.
Sin embargo, es posible que esta ampliación hará que se ve borroso almacenar mapas de bits. Para evitar esto, puede ser necesario proporcionar recursos alternativos para las densidades diferentes. Al diseñar dispositivos múltiples y resoluciones de las densidades de pantalla resulte más fácil empezar con la resolución más alta o densidad imágenes y reducir la escala. Esto evitará que cualquier desenfoque o distorsión que puede originarse cuando el cambio de tamaño.

<a name="Declare_the_Screen_Size_the_Application_Supports" />

### <a name="declare-the-screen-size-the-application-supports"></a>Declare el tamaño de pantalla de la aplicación admite

Declarar el tamaño de pantalla se asegura de que solo los dispositivos compatibles pueden descargar la aplicación. Esto se consigue estableciendo la [admite pantallas](http://developer.android.com/guide/topics/manifest/supports-screens-element.html) elemento en el **AndroidManifest.xml** archivo. Este elemento se utiliza para especificar los tamaños de pantalla son compatibles con la aplicación. Se considera una pantalla determinada que se deben admitir si la aplicación puede correctamente de diseños para rellenar la pantalla. Mediante el uso de este elemento de manifiesto, la aplicación no aparecerá en [ *Google Play* ](https://play.google.com/) para dispositivos que no cumplen las especificaciones de la pantalla. Sin embargo, la aplicación seguirá ejecutándose en dispositivos con pantallas no compatibles, pero los diseños pueden aparecer borrosos y pixelado.

Para hacer esto en Xamarin.Android, es necesario agregar primero un **AndroidManifest.xml** archivo al proyecto si aún no existe:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Manifiesto de Android](resources-for-varying-screens-images/01-android-manifest-vs-sml.png)](resources-for-varying-screens-images/01-android-manifest-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Manifiesto de Android](resources-for-varying-screens-images/01-android-manifest-xs-sml.png)](resources-for-varying-screens-images/01-android-manifest-xs.png)

-----


**AndroidManifest.xml** se agrega a la **propiedades** directory. A continuación, se edita el archivo para incluir [admite pantallas](http://developer.android.com/guide/topics/manifest/supports-screens-element.html):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Agregar admite pantallas](resources-for-varying-screens-images/02-adding-supports-screens-vs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Agregar admite pantallas](resources-for-varying-screens-images/02-adding-supports-screens-xs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-xs.png)

-----


<a name="Provide_Alternate_Layouts_for_Different_Screen_Sizes" />

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Proporcionar diseños alternativos para los diferentes tamaños de pantalla

Aunque Android cambiará de tamaño según el tamaño de pantalla, esto puede no ser suficiente en algunos casos. Puede ser deseable para aumentar el tamaño de algunos elementos de interfaz de usuario en una pantalla más grande, o para cambiar la posición de los elementos de interfaz de usuario para una pantalla más pequeña.

A partir de 13 de nivel de API (Android 3.2), los tamaños de pantalla están en desuso deben usar el software*N*calificador de punto de distribución. Este nuevo calificador declara que necesita la cantidad de espacio en un diseño determinado. Se recomienda encarecidamente que las aplicaciones que están diseñadas para ejecutarse en Android 3.2 o posterior deben utilizar estos calificadores más reciente.

Por ejemplo, si un diseño requiere un mínimo 700dp del ancho de pantalla, el diseño alternativo saldría en una carpeta **sw700dp de diseño**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Carpeta de diseño para el ancho de pantalla de 700dp](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Carpeta de diseño para el ancho de pantalla de 700dp](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


Como norma, incluimos algunos números para varios dispositivos:

- **Teléfono típico** &ndash; 320dp: un teléfono normal

- **Una tableta 5"/"tweener"dispositivo** &ndash; 480dp: por ejemplo, la nota de Samsung

- **Una tableta 7"** &ndash; 600dp: como la Barnes &amp; Nook Noble

- **Una tableta 10"** &ndash; 720dp: como la Motorola Xoom

Para las aplicaciones que los niveles de API de destino hasta 12 (Android 3.1), los diseños de deberían ir en directorios que usan los calificadores **pequeño**/**normal**/**grandes**  / **extra grande** como generalizaciones de los distintos tamaños de pantalla que están disponibles en la mayoría de los dispositivos. Por ejemplo, en la imagen siguiente, hay recursos alternativos para los cuatro tamaños de pantalla diferentes:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Recursos alternativos a cuatro tamaños de pantalla](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Recursos alternativos a cuatro tamaños de pantalla](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

A continuación se ofrece una comparación de cómo se comparan los calificadores de tamaño de pantalla de nivel 13 pre-API anteriores con independiente de la densidad de píxeles:

- es 426dp x 320dp **pequeño**

- es 470dp x 320dp **normal**

- es 640dp x 480dp **grandes**

- es 960dp x 720dp **extra grande**

La pantalla más reciente calificadores en el nivel de API 13 de cambio de tamaño y la tienen mayor prioridad que los calificadores de pantalla anteriores de niveles de API 12 e inferiores.
Para las aplicaciones que abarcarán la antigua y los nuevos niveles de API, puede ser necesario crear recursos alternativos con ambos conjuntos de calificadores como se muestra en la siguiente captura de pantalla:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Recursos alternativos con ambos calificadores](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Recursos alternativos con ambos calificadores](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----


<a name="Provide_Different_Bitmaps_for_Different_Screen_Densities" />

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Obtener mapas de bits diferentes densidades de pantalla diferente

Aunque Android escalará los mapas de bits según sea necesario para un dispositivo, los mapas de bits a sí mismos no pueden escalar elegancia hacia arriba o hacia abajo: se pueden llegar a borrosas o aproximada. Proporciona los mapas de bits adecuados para la densidad de la pantalla se solucionará este problema.

Por ejemplo, la imagen siguiente es un ejemplo de diseño y la apariencia de los problemas que pueden producirse cuando se especifica no se proporcionan recursos de densidad.

![Capturas de pantalla sin recursos de densidad](resources-for-varying-screens-images/06-density-not-provided.png)

Compare esto con un diseño que está diseñado con los recursos específicos de densidad:

![Capturas de pantalla con recursos específicos de la densidad](resources-for-varying-screens-images/07-density-specific-resources.png)

<a name="Create_Varying_Density_Resources_with_Android_Asset_Studio" />

### <a name="create-varying-density-resources-with-android-asset-studio"></a>Crear variables de densidad de recursos con Asset Android Studio

La creación de estos mapas de bits de las densidades distintos puede resultar un poco tediosa. Por lo tanto, Google ha creado una utilidad que puede reducir la parte de la tarea relacionado con la creación de estos mapas de bits que se llama la [ **Android Studio Asset**](https://romannurik.github.io/AndroidAssetStudio/).

[![Asset Android Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png)

Este sitio Web le ayudará a con la creación de mapas de bits que tienen como destino las densidades de pantalla comunes cuatro proporcionando una imagen. Android Studio Asset creará, a continuación, los mapas de bits con algunas de las personalizaciones y, a continuación, permitir que se puede descargar como un archivo zip.

<a name="Tips_for_Multiple_Screens" />

## <a name="tips-for-multiple-screens"></a>Sugerencias para varias pantallas

Android se ejecuta en un número desconcertante de dispositivos, y la combinación de tamaños de pantalla y las densidades de pantalla puede parecer abrumador. Las siguientes sugerencias pueden ayudar a minimizar el esfuerzo necesario para admitir varios dispositivos:

- **Sólo diseño y desarrollo para lo que es necesario** &ndash; hay muchos dispositivos diferentes disponible, pero algunos existen en los factores de forma poco frecuentes que pueden suponer un gran esfuerzo para diseñar y desarrollar para. El [ **tamaño de la pantalla y la densidad de** ](http://developer.android.com/resources/dashboard/screens.html) panel es una página proporcionada por Google que proporciona datos sobre el desglose de la matriz de densidad de tamaño/pantalla de pantalla. Esta división proporciona una visión general acerca de cómo un esfuerzo de desarrollo sobre la compatibilidad de pantallas.

- **Use DP en lugar de píxeles** -píxeles se convierten en problemas como cambios de densidad de la pantalla. No codificar valores de píxel. Evite píxeles en favor de dp (independiente de la densidad de píxeles).

- **Evitar** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **siempre que sea posible** &ndash; que está en desuso en el nivel de API 3 (Android 1.5) y se provocará en diseños complicados. No se recomienda. En su lugar, intente usar los widgets de diseño más flexibles como [ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/), [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), o el nuevo [ **GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/).

- **Elija una orientación de diseño como su valor predeterminado** &ndash; por ejemplo, en lugar de proporcionar los recursos alternativos **diseño tierra** y **diseño-port**, poner los recursos para Panorama en **diseño**y los recursos de vertical a **diseño puerto**.

- **Usar LayoutParams para alto y ancho** : al definir los elementos de interfaz de usuario en un archivo de diseño XML, una aplicación Android con el **wrap_content** y **fill_parent** valores tendrá más éxito Asegúrese de un vistazo adecuado a través de distintos dispositivos que el uso de píxeles o densidad unidades independientes. Estos valores de la dimensión hacen Android recursos de mapa de bits de escala según sea necesario. Por esta misma razón, están mejor reservadas unidades independientes de densidad para saber cuándo especificar los márgenes y relleno de los elementos de interfaz de usuario.

<a name="Testing_Multiple_Screens" />

## <a name="testing-multiple-screens"></a>Probar varias pantallas

Una aplicación de Android debe probarse en todas las configuraciones que serán compatibles. Lo ideal es que los dispositivos se deben probar en los propios dispositivos, pero en muchos casos esto no es posible ni práctico.
En este caso, el uso del emulador y el programa de instalación de dispositivos virtuales Android para cada configuración de dispositivo será útil.

El SDK de Android proporciona algunos emulador máscaras pueden utilizarse para crear de AVD replicará el tamaño, la densidad y la resolución de muchos dispositivos.
Del mismo modo, muchos de los proveedores de hardware proporcionan máscaras para sus dispositivos.

Otra opción es usar los servicios de un servicio de pruebas de otros fabricantes.
Estos servicios se toman un APK, ejecutarla en muchos dispositivos diferentes y, a continuación, proporcionar comentarios cómo la aplicación funcionó correctamente.
