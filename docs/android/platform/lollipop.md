---
title: Características de círculo
description: En este artículo se proporciona una introducción de alto nivel de las nuevas características introducidas en Android 5.0 (Lollipop). Estas características incluyen un nuevo estilo de interfaz de usuario llama a Material de tema, así como nuevas características auxiliares como las animaciones, sombras de vista y tintes pueden dibujar. Android 5.0 también incluye notificaciones mejoradas, dos nuevos widgets de interfaz de usuario, un nuevo programador de trabajos y un puñado de API nuevas para mejorar capacidades multimedia, conectividad, redes y almacenamiento.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d79c0563d1dc9a2cfe75b702300982bb4d38553b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117868"
---
# <a name="lollipop-features"></a>Características de círculo

_En este artículo se proporciona una introducción de alto nivel de las nuevas características introducidas en Android 5.0 (Lollipop). Estas características incluyen un nuevo estilo de interfaz de usuario llama a Material de tema, así como nuevas características auxiliares como las animaciones, sombras de vista y tintes pueden dibujar. Android 5.0 también incluye notificaciones mejoradas, dos nuevos widgets de interfaz de usuario, un nuevo programador de trabajos y un puñado de API nuevas para mejorar capacidades multimedia, conectividad, redes y almacenamiento._

## <a name="lollipop-overview"></a>Información general de círculo

Android 5.0 (Lollipop) presenta un nuevo lenguaje de diseño, *Material Design*, y con él de apoyo convertir de nuevas características para simplificar las aplicaciones y más intuitivo de usar. Con Material Design, Android 5.0 no sólo ofrece teléfonos Android una renovación; También se proporciona un nuevo conjunto de reglas de diseño para tabletas basados en Android, equipos de escritorio, las inspecciones y televisores inteligentes. Estas reglas de diseño hacer hincapié en simplicidad y minimalism al hacer uso de atributos al tacto conocidos (por ejemplo, indicaciones de superficie y borde realistas) para ayudar a los usuarios rápidamente y comprender la interfaz de forma intuitiva.

*Tema de materiales* es la encarnación de estos principios de diseño de interfaz de usuario en Android. En este artículo comienza con la que trata las características de apoyo del Material tema:

-   **Las animaciones** &ndash; *Touch comentarios* animaciones, *transición actividad* animaciones, *ver la transición de estado* animaciones y un *revelar efecto*.

-   **Ver sombras y elevación** &ndash; las vistas tienen ahora un `elevation` propiedad; vistas con mayor `elevation` los valores convierten las sombras mayor en segundo plano.

-   **Características de color** &ndash; *tintes Drawable* permite reutilizar los activos de imagen cambiando su color, y *extracción color destacado* le ayuda a dinámicamente tema de la aplicación en función de los colores de una imagen.

Tema de Material de muchas características ya están integradas en la IU de Android 5.0 experiencia, mientras que otros usuarios deben agregarse explícitamente a las aplicaciones. Por ejemplo, algunas vistas estándares (por ejemplo, botones) ya incluyen las animaciones de comentarios de toque, mientras que las aplicaciones deben habilitar la mayoría de las sombras de vista.

Además de las mejoras de la interfaz de usuario que trajo a través de tema de Material, Android 5.0 también incluye otras características nuevas que se tratan en este artículo:

-   **Mejorado notificaciones** &ndash; notificaciones en Android 5.0 se han actualizado significativamente con un nuevo aspecto para las notificaciones de pantalla de bloqueo y la compatibilidad con un nuevo *Heads-Up* formato de presentación de la notificación.

-   **Nuevos widgets de interfaz de usuario** &ndash; nuevo `RecyclerView` widget facilita que las aplicaciones transmitir grandes conjuntos de datos e información compleja y el nuevo `CardView` widget proporciona un formato simplificado tipo de tarjeta de presentación para mostrar texto y imágenes.

-   **Nuevas API** &ndash; Android 5.0 agrega las nuevas API para compatibilidad con múltiples de red, ha mejorado la conectividad Bluetooth, facilitar la administración de almacenamiento y un control más flexible de dispositivos de cámara y reproductores multimedia. Está disponible para ejecutar las tareas de forma asincrónica en un nuevo trabajo de característica de programación las horas programadas. Esta característica ayuda a mejorar la duración de la batería, por ejemplo, programar tareas tenga lugar cuando el dispositivo está conectado y carga.


## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android 5.0 en aplicaciones basadas en Xamarin, se requiere lo siguiente:

-   **Xamarin.Android** &ndash; Xamarin.Android 4.20 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac. 

-   **SDK de Android** &ndash; Android 5.0 (API 21) o posterior debe estar instalado a través de Android SDK Manager.

-   **Kit para desarrolladores de Java** &ndash; requiere Xamarin.Android [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si está desarrollando para el nivel de API 24 o superior (JDK 1.8 también admite los niveles de API anteriores al 24, incluido el círculo). La versión de 64 bits del JDK 1.8 es necesaria si utiliza controles personalizados o la vista previa de formularios.

Aún puede usar [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si está desarrollando contenido específicamente para el nivel de API 23 o una versión anterior.


## <a name="setting-up-an-android-50-project"></a>Configuración de un proyecto Android 5.0

Para crear un proyecto de Android 5.0, debe instalar los paquetes SDK y herramientas más recientes. Utilice los pasos siguientes para configurar un proyecto de Xamarin.Android que tiene como destino Android 5.0:

1. Instale las herramientas de Xamarin.Android y activar su licencia de Xamarin. Consulte [configuración e instalación](~/android/get-started/installation/index.md) para obtener más información acerca de cómo instalar Xamarin.Android.

2. Si utiliza Visual Studio para Mac, instale las actualizaciones más recientes de Android 5.0.

3. Iniciar Android SDK Manager (en Visual Studio para Mac, use **herramientas &gt; abrir Android SDK Manager&hellip;**) e instalar Android SDK Tools 23.0.5 o posterior:

    [![Seleccionar las herramientas de Android SDK de Android SDK Manager](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Además, instale los paquetes más recientes de SDK de Android 5.0 (API 21 o posterior):

    [![Instalación de paquetes de SDK de Android 5.0 en Android SDK Manager](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Para obtener más información sobre cómo usar Android SDK Manager, consulte [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).

4. Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información acerca de cómo crear proyectos de Android. Al crear un proyecto de Android, asegúrese de configurar las opciones de versión para Android 5.0.
   En Visual Studio para Mac, vaya a **opciones de proyecto &gt; compilar &gt; General** y establecer **.NET framework de destino** a **Android 5.0 (Lollipop)** o más adelante:

    ![Establecer el destino Framwework en Android Lollipop 5.0](lollipop-images/target-framework.png)

   En **opciones de proyecto &gt; compilar &gt; Aplicaciónandroid**, establecer la duración mínima y versión de Android para tener como destino **automático: use la versión de framework de destino**:

    ![Si las versiones mínima y Android de destino se establece en automático](lollipop-images/minimum-android-version.png)

5. Configurar un emulador o un dispositivo Android para probar la aplicación. Si usa un emulador, consulte [configuración de Android Emulator](~/android/get-started/installation/android-emulator/index.md) para obtener información sobre cómo configurar un emulador de Android para su uso con Xamarin Studio o Visual Studio. Si se usa un dispositivo Android, consulte [configuración de la versión preliminar del SDK de](https://developer.android.com/preview/setup-sdk.html) para obtener información sobre cómo actualizar el dispositivo para Android 5.0. Para configurar el dispositivo Android para ejecutar y depurar aplicaciones de Xamarin.Android, consulte [configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

Nota: Si va a actualizar un proyecto de Android existente que estaba apuntando a la versión preliminar de L Android, debe actualizar el **.NET Framework de destino** y **Android versión** a los valores que se ha descrito anteriormente.

## <a name="important-changes"></a>Cambios importantes

Anteriormente, las aplicaciones publicadas de Android podrían verse afectadas por cambios en Android 5.0. En concreto, Android 5.0 usa un nuevo tiempo de ejecución y un formato de notificación significativamente modificados.

### <a name="android-runtime"></a>Tiempo de ejecución de Android

Android 5.0, se utiliza el tiempo de ejecución de Android nueva (arte) como el tiempo de ejecución predeterminado en lugar de Dalvik. ARTE implementa varias características nuevas principales:

-   **Compilación ahead of time (AOT)** &ndash; AOT puede mejorar el rendimiento de la aplicación mediante la compilación de código de la aplicación antes de inicia la aplicación en primer lugar. Cuando se instala una aplicación, arte genera una aplicación compilada ejecutable para el dispositivo de destino.

-   **Mejora de la colección de elementos no utilizados (GC)** &ndash; mejoras de GC en imágenes pueden mejorar el rendimiento de la aplicación. Recolección ahora usa una pausa de GC en lugar de dos y completan las operaciones simultáneas de GC en el momento más oportuno.

-   **Depuración de aplicación mejorada** &ndash; arte proporciona más detalles de diagnóstico le ayudan a analizar las excepciones y los informes de bloqueo.

Las aplicaciones existentes deben funcionar sin cambios en el arte &ndash; excepto para las aplicaciones que aprovechan técnicas únicas para el tiempo de ejecución Dalvik anterior, que puede no funcionar en imágenes. Para obtener más información sobre estos cambios, consulte [comportamiento de comprobación de la aplicación en tiempo de ejecución de Android (ART)](http://developer.android.com/guide/practices/verifying-apps-art.html).


### <a name="notification-changes"></a>Cambios de notificación

Las notificaciones se han cambiado significativamente en Android 5.0:

-   **Se traten sonidos y vibración** &ndash; sonidos de notificaciones y las vibraciones ahora se controlan mediante `Notification.Builder` en lugar de `Ringtone`, `MediaPlayer`, y `Vibrator`.

-   **Nueva combinación de colores** &ndash; con arreglo al tema de Material, las notificaciones se representan con texto oscuro en fondos blancos o muy claros. Además, pueden modificar canales alfa en iconos de notificación de Android para coordinar con combinaciones de colores del sistema. 

-   **Las notificaciones de pantalla de bloqueo** &ndash; notificaciones ahora pueden aparecer en la pantalla de bloqueo del dispositivo.

-   **Aviso** &ndash; notificaciones de alta prioridad aparecen ahora en una pequeña ventana flotante (notificación informativa) cuando el dispositivo se desbloquea y se activa la pantalla.

En la mayoría de los casos, la portabilidad de la funcionalidad de notificación de aplicación existente para Android 5.0 requiere los siguientes pasos:

1.  Convertir el código para usar `Notification.Builder` (o `NotificationsCompat.Builder`) para la creación de notificaciones. 

2.  Compruebe que los recursos existentes de notificación están visibles en la combinación de colores de tema de Material nuevo.

3.  Decidir qué deben tener las notificaciones cuando se presentan en la pantalla de bloqueo de visibilidad. ¿Si una notificación no es pública, el contenido que debe aparecer en la pantalla de bloqueo?

4.  Establecer la categoría de las notificaciones, por lo que se controlan correctamente en el nuevo Android 5.0 *no molestar* modo.

Si las notificaciones están presentes los controles de transporte, los medios para mostrar estado de la reproducción, use `RemoteControlClient`, o llamar a `ActivityManager.GetRecentTasks`, consulte [importantes cambios de comportamiento](http://developer.android.com/preview/api-overview.html#Behaviors) para obtener más información acerca de cómo actualizar las notificaciones para Android 5.0.

Para obtener información sobre cómo crear notificaciones en Android, consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md). El [compatibilidad](~/android/app-fundamentals/notifications/local-notifications.md#compatibility) sección de este artículo explica cómo crear notificaciones que son compatibles con hacia abajo con versiones anteriores de Android.


## <a name="material-theme"></a>Tema de materiales

El nuevo tema de Android 5.0 Material aporta cambios genéricos a la apariencia y comportamiento de la interfaz de usuario de Android. Ahora, los elementos visuales usan al tacto superficies que tomar en los gráficos en negrita, tipografía y colores brillantes de diseño de impresión. Ejemplos del tema de Material se representan en las capturas de pantalla siguiente:

[![Capturas de pantalla de la pantalla principal de tema de Material, pantalla de aplicaciones y pantalla de configuración](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 le da la pantalla principal que se muestra en la parte izquierda. La captura de pantalla de center es la primera pantalla de la lista de aplicaciones y la captura de pantalla de la derecha es el **configuración** pantalla. Google [Material Design](https://material.io/guidelines/material-design/introduction.html) especificación explica las reglas de diseño subyacentes promotoras del concepto de tema de Material nuevo.

Tema de materiales incluye tres tipos integrados que puede usar en la aplicación: el `Theme.Material` tema oscuro (predeterminado), el `Theme.Material.Light` tema y el `Theme.Material.Light.DarkActionBar` tema: 

[![Temas capturas de pantalla de oscuro, claro y DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Para obtener más información sobre el uso de características de tema de Material en aplicaciones de Xamarin.Android, consulte [Material tema](~/android/user-interface/material-theme.md).


## <a name="animations"></a>Animaciones

Android 5.0 proporciona animaciones de comentarios de toque, animaciones de transición de la actividad y animaciones de transición de estado de vista para realizar más intuitivo de usar interfaces de aplicación. Además, pueden usar las aplicaciones de Android 5.0 *revelar efecto* animaciones para mostrar u ocultar vistas. Puede usar *curvados movimiento* configuración para configurar la rapidez o lentitud las animaciones se representan.


### <a name="touch-feedback-animations"></a>Animaciones de comentarios de toque

Las animaciones de comentarios de Touch proporcionan a los usuarios comentarios visuales cuando se ha tocado una vista. Por ejemplo, los botones ahora muestran un efecto dominó cuando se han tocado &ndash; se trata de la animación de comentarios de forma predeterminada táctil en Android 5.0. Animación de ripple se implementa mediante el nuevo `RippleDrawable` clase. El efecto dominó puede configurarse para terminar en los límites de la vista o sobrepasa los límites de la vista. Por ejemplo, la secuencia de capturas de pantalla siguiente muestra el efecto dominó en un botón durante la animación táctil:

![Marco de capturas de pantalla de fotogramas de animación de ripple en un botón](lollipop-images/touch-animation.png)

Contacto táctil inicial con el botón se produce en la primera imagen de la izquierda, mientras que la secuencia restante (de izquierda a derecha) muestra cómo el efecto dominó se extiende hasta el borde del botón. Cuando finaliza la animación de ripple, devuelve la vista de aspecto original. La animación de ripple predeterminada tiene lugar en una fracción de segundo, pero la longitud de la animación se puede personalizar para las longitudes larga o más cortas de tiempo.

Para obtener más información sobre touch animaciones de comentarios en Android 5.0, consulte [personalizar comentarios Touch](http://developer.android.com/training/material/animations.html#Touch).


### <a name="activity-transition-animations"></a>Animaciones de transición de actividad

Animaciones de transición de la actividad a los usuarios una sensación de continuidad visual cuando una actividad realiza una transición a otro. Las aplicaciones pueden especificar tres tipos de animaciones de transición:

-   **Escriba transición** &ndash; para cuando una actividad entra en la escena.

-   **Salir de transición** &ndash; para cuando una actividad finaliza la escena.

-   **Comparten la transición del elemento** &ndash; para cuando se cambia una vista que es común a dos actividades como la primera transiciones de actividad a la siguiente.

Por ejemplo, la siguiente secuencia de capturas de pantalla muestra una transición del elemento compartido:

[![Marco de capturas de pantalla de marco de una animación de transición del elemento compartido](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un elemento compartido (como una fotografía de un caterpillar) es una de varias vistas en la primera actividad; amplía para convertirse en la vista sola en la segunda actividad, como las transiciones de actividad primeras al segundo.

#### <a name="enter-transition-animation-types"></a>Escriba los tipos de animaciones de transición

Para las transiciones de ENTRAR, Android 5.0 proporciona tres tipos de animaciones:

-   **Puede ampliar la animación** &ndash; aumenta el tamaño de una vista desde el centro de la escena.

-   **Animación de diapositiva** &ndash; mueve una vista en uno de los bordes de una escena.

-   **Animación de atenuación** &ndash; atenúa una vista en la escena.

#### <a name="exit-transition-animation-types"></a>Tipos de animaciones de transición de salida

Para las transiciones de salida, Android 5.0 proporciona tres tipos de animaciones:

-   **Puede ampliar la animación** &ndash; reduce el tamaño de una vista en el centro de la escena.

-   **Animación de diapositiva** &ndash; mueve una vista a uno de los bordes de una escena.

-   **Animación de atenuación** &ndash; atenúa una vista a partir de la escena.

#### <a name="shared-element-transition-animation-types"></a>Compartir tipos de animaciones de transición de elemento

Transiciones de un elemento compartido admiten varios tipos de animaciones, como:

-   Cambiar los límites de diseño o un clip de una vista.

-   Cambiar la escala y rotación de una vista.

-   Cambiar el tipo de tamaño y la escala de una vista.

Para obtener más información acerca de las animaciones de transición de actividad en Android 5.0, consulte [Personalizar actividad transiciones](http://developer.android.com/training/material/animations.html#Transitions).


### <a name="view-state-transition-animations"></a>Animaciones de transición de estado de vista

Android 5.0 hace posible para las animaciones que se ejecuta cuando cambia el estado de una vista. Puede animar las transiciones de estado de vista mediante una de las técnicas siguientes:

-   Crear recursos drawable animar los cambios de estado asociados con una vista determinada. El nuevo `AnimatedStateListDrawable` clase le permite crear recursos drawable que muestran las animaciones entre los cambios de estado de vista.

-   Definir la funcionalidad de animación que se ejecuta cuando cambia el estado de una vista. El nuevo `StateListAnimator` clase le permite definir una animación que se ejecuta cuando cambia el estado de una vista.

Para obtener más información sobre animaciones de transición de estado de vista en Android 5.0, consulte [animar los cambios de estado de vista](http://developer.android.com/training/material/animations.html#ViewState).


### <a name="reveal-effect"></a>Mostrar el efecto

El *revelar efecto* es un círculo de recorte que radius de los cambios para mostrar u ocultar una vista. Puede controlar este efecto estableciendo el radio del círculo de recorte inicial y final. La siguiente secuencia de capturas de pantalla muestra una animación de efecto de revelar desde el centro de la pantalla:

[![Marco de capturas de pantalla de fotogramas de animación de revelar](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

La secuencia siguiente muestra una animación de efecto de revelar que tiene lugar en la esquina inferior izquierda de la pantalla:

[![Marco de capturas de pantalla de fotogramas de animación de recorte](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Revelar las animaciones se pueden invertir; es decir, el círculo de recorte puede reducir para ocultar la vista en lugar de ampliar para mostrar la vista.

Para obtener más información en el efecto de revelar Android 5.0, consulte [usar el efecto de revelar](http://developer.android.com/training/material/animations.html#Reveal).


### <a name="curved-motion"></a>Movimiento curva

Además de estas características de animación, Android 5.0 también proporciona nuevas API que permiten especificar las curvas de tiempo y el movimiento de animaciones. Android 5.0 utiliza estas curvas para interpolar movimiento temporal y espacial durante las animaciones. Se definen tres curvas en Android 5.0:

-   **Fast\_out\_lineal\_en** &ndash; acelera rápidamente y sigue aumentando hasta el final de la animación.

-   **Fast\_out\_lenta\_en** &ndash; acelera rápidamente y con lentitud desacelera hacia el final de la animación.

-   **Lineal\_out\_lenta\_en** &ndash; empieza con una velocidad máxima y lentamente desacelera al final de la animación.

Puede usar el nuevo `PathInterpolator` clase para especificar cómo realiza la interpolación de movimiento. `PathInterpolator` es un interpolador que atraviesa las rutas de acceso de animación según los puntos de control especificados y curvas de movimiento. Para obtener más información acerca de cómo especificar la configuración de movimiento curvas en Android 5.0, consulte [Use curvas movimiento](http://developer.android.com/training/material/animations.html#CurvedMotion).


## <a name="view-shadows--elevation"></a>Vista sombras & elevación

En Android 5.0, puede especificar el *elevación* de una vista estableciendo un nuevo `Z` propiedad. Una mayor `Z` valor hace que la vista para convertir una sombra más grande en segundo plano, que parezca que la vista a float más alto por encima del fondo. Puede establecer la elevación inicial de una vista mediante la configuración de su `elevation` atributo en el diseño.

El ejemplo siguiente muestra las sombras tinte vacío `TextView` controlar cuando se establece su atributo elevación 2dp, 4dp y 6dp, respectivamente:

[![Capturas de pantalla de sombras de vista más grandes de progessively](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Configuración de la sombra de la vista puede ser estática (como se indicó anteriormente) o puede usarse en las animaciones para hacer que una vista aparecen temporalmente subiendo por encima de fondo de la vista. Puede usar el `ViewPropertyAnimator` clase para animar la elevación de una vista. La elevación de una vista es la suma de su diseño `elevation` configuración más una `translationZ` propiedad que se puede establecer a través de un `ViewPropertyAnimator` llamada al método.

Para obtener más información acerca de las sombras de vista de Android 5.0, consulte [definir sombras y vistas de recorte](http://developer.android.com/training/material/shadows-clipping.html).


## <a name="color-features"></a>Características de color

Android 5.0 proporciona dos características nuevas para la administración de color en las aplicaciones:

-   *Puede dibujar tintes* le permite modificar los colores de los activos de imagen al cambiar un atributo de diseño.

-   *Extracción de color destacado* hace posible para que pueda personalizar dinámicamente el tema de color de la aplicación para coordinar con la paleta de colores de una imagen mostrada.


### <a name="drawable-tinting"></a>Tintes drawable

Reconocen un nuevo Android 5.0 diseños `tint` atributo que puede usar para establecer el color de recursos drawable sin tener que crear varias versiones de estos recursos para mostrar colores diferentes. Para usar esta característica, defina un mapa de bits como una máscara alfa y usará el `tint` atributo para definir el color del recurso. Esto hace posible para que pueda crear activos de una vez y el color de ellos en su diseño para que coincida con el tema.

En el ejemplo siguiente, un recurso de imagen único &ndash; un logotipo blanco con un fondo transparente &ndash; se utiliza para crear variaciones de tono:

![Logotipo de Xamarin en blanco con fondo transparente](lollipop-images/xamarin-logo-white.png)

Este logotipo se muestra sobre un fondo azul circular tal como se muestra en los ejemplos siguientes. La imagen de la izquierda es cómo aparece el logotipo sin un `tint` configuración. En el centro de la imagen, el logotipo `tint` atributo está establecido en gris oscuro. En la imagen a la derecha, `tint` se establece en un gris claro:

![Ejemplos del logotipo anterior con la configuración de un tono diferente](lollipop-images/drawable-tinting.png)

Para obtener más información acerca de tintes pueden dibujar en Android 5.0, consulte [tintes Drawable](http://developer.android.com/training/material/drawables.html#DrawableTint).


### <a name="prominent-color-extraction"></a>Extracción de Color destacado

El nuevo Android 5.0 `Palette` clase le permite extraer los colores de una imagen para que se puede aplicar dinámicamente a una paleta de colores personalizada. La `Palette` clase extrae los seis colores de una imagen y etiqueta estos colores según sus niveles de saturación de color y brillo relativos:

-   Vibrante

-   Vibrante oscuro

-   Luz vibrante

-   Silenciado

-   Desactivado oscuro

-   Luz desactivado

Por ejemplo, en las capturas de pantalla siguiente, un aplicación de visualización de fotografías extrae los colores destacados de la imagen en pantalla y usa estos colores para adaptar la combinación de colores de la aplicación para que coincida con la imagen:

[![Capturas de pantalla de extracciones de color de tema de color rosa, verde y azul](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

En las capturas de pantalla anterior, la barra de acciones se establece en el "vibrante light" extraído color y el fondo se establece en la "vibrante oscuridad" extraída color. En cada ejemplo anterior, se incluye para ilustrar los colores de paleta que se han extraído de la imagen de una fila de cuadrados de color pequeñas.

Para obtener más información acerca de la extracción de color en Android 5.0, consulte [extraer destacado colores de una imagen](http://developer.android.com/training/material/drawables.html#ColorExtract).


## <a name="new-ui-widgets"></a>Nuevos Widgets de interfaz de usuario

Android 5.0 presenta dos nuevos widgets de interfaz de usuario:

-   `RecyclerView` &ndash; Un grupo de vista que muestra una lista de elementos desplazables.

-   `CardView` &ndash; Un diseño básico con esquinas redondeadas.

Ambos widgets incluyen compatibilidad incorporada para características de Material tema; Por ejemplo, `RecyclerView` usa animaciones para agregar o quitar vistas, y `CardView` usa ver sombras para realizar cada tarjeta flotante sobre el fondo. En las capturas de pantalla siguiente se muestran ejemplos de estos nuevos widgets:

[![Capturas de pantalla de las aplicaciones compiladas con RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

La captura de pantalla de la izquierda es un ejemplo de `RecyclerView` como se utiliza en una aplicación de correo electrónico y la captura de pantalla en la derecha es un ejemplo de `CardView` como utiliza en una aplicación de reservas de viajes.


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` es similar a `ListView,` pero es más adecuado para grandes conjuntos de vistas o las listas de elementos que cambian dinámicamente. Al igual que `ListView,` especificar un adaptador para tener acceso al conjunto de datos subyacente. Sin embargo, a diferencia de `ListView,` usas un *Administrador de diseño* colocar los elementos de `RecyclerView`. El Administrador de diseño también se encarga de reciclado de vista; administra la reutilización de las vistas de elementos que ya no son visibles para el usuario.

Cuando se usa un `RecyclerView` widget, debe especificar un `LayoutManager` y un adaptador. Como se muestra en esta ilustración, `LayoutManager` es el intermediario entre el adaptador y el `RecyclerView`:

![Diagrama de RecyclerView con LayoutManager, el adaptador y el conjunto de datos de compatibilidad](lollipop-images/recyclerview-diagram.png)

Las capturas de pantalla siguientes ilustran una `RecyclerView` que contiene 100 elementos (cada elemento consta de un `ImageView` y un `TextView`):

[![Capturas de pantalla de una aplicación de RecyclerView desplazarse a través de las imágenes](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` controla este conjunto de datos de gran tamaño con facilidad &ndash; desplazamiento desde el principio de la lista de finalización de la lista en este ejemplo aplicación tarda solo unos segundos. `RecyclerView` También es compatible con las animaciones; de hecho, las animaciones para agregar y quitar elementos están habilitadas de forma predeterminada. Cuando se agrega un elemento a un `RecyclerView`, atenúa en como se muestra en esta secuencia de capturas de pantalla:

[![Fotogramas por captura de pantalla de marco de una atenuación de elemento de fotos en](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Para obtener más información acerca de `RecyclerView`, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).


### <a name="cardview"></a>CardView

`CardView` es una vista sencilla que simula una tarjeta flotante con esquinas redondeadas. Dado que `CardView` tiene las sombras de vistas integradas, proporciona una manera fácil de agregar profundidad visual a la aplicación. Las capturas de pantalla siguientes muestran tres ejemplos orientados a texto de `CardView`:

[![Capturas de pantalla de ejemplo de aplicaciones con RecyclerView con elementos basados en CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Cada una de las tarjetas en el ejemplo anterior contiene un `TextView`; se establece el color de fondo a través de la `cardBackgroundColor` atributo.

Para obtener más información acerca de `CardView`, consulte [CardView](~/android/user-interface/controls/card-view.md).


## <a name="enhanced-notifications"></a>Notificaciones mejoradas

El sistema de notificación en Android 5.0 se actualizó significativamente con nuevas características y un nuevo formato visual. Las notificaciones tienen un nuevo aspecto en Android 5.0. Por ejemplo, las notificaciones en Android 5.0 ahora usar texto oscuro sobre un fondo claro:

![Ejemplo de una notificación de Android 5.0 sin expandido](lollipop-images/expanded-notification-contracted.png)

Cuando se muestra un icono grande en una notificación (como se muestra en el ejemplo anterior), Android 5.0 presenta el pequeño icono como una notificación sobre el icono de gran tamaño. 

En Android 5.0, las notificaciones también pueden aparecer en la pantalla de bloqueo del dispositivo.
Por ejemplo, esta es una captura de pantalla de ejemplo de una pantalla de bloqueo con una única notificación:

[![Captura de pantalla de notificación que aparece en la pantalla de bloqueo](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Los usuarios pueden puntee una notificación en la pantalla de bloqueo para desbloquear el dispositivo y saltar a la aplicación que se originó dicha notificación, o pasar el dedo para descartarla. Las notificaciones tienen un nuevo *visibilidad* el valor que determina cuánto contenido se puede mostrar en la pantalla de bloqueo. Los usuarios pueden elegir si se permite contenido confidencial que se mostrará en las notificaciones de pantalla de bloqueo.

Android 5.0 presenta un nuevo formato de presentación de notificación de alta prioridad denominado *Heads-Up*. Las notificaciones de aviso se deslice hacia abajo desde la parte superior de la pantalla durante unos segundos y retreat, a continuación, volver a la sombra de notificación en la parte superior de la pantalla. Las notificaciones de aviso hacen posible para el sistema de la interfaz de usuario para colocar información importante delante del usuario sin interrumpir la actividad que se está ejecutando. El ejemplo siguiente muestra una notificación de aviso simple que se muestra encima de una aplicación:

[![Ejemplo de una notificación de pantalla de datos](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Las notificaciones de aviso se utilizan normalmente para los siguientes eventos:

-   Un nuevo mensaje siguiente

-   Una llamada de teléfono entrante

-   Indicación de batería baja

-   Una alarma

Android 5.0 muestra una notificación en formato sobreimpresa solo cuando tiene una configuración de prioridad alta o max.

En Android 5.0, puede proporcionar los metadatos de notificación para ayudar a Android, ordenar y mostrar las notificaciones de una forma más inteligente. Android 5.0 organiza las notificaciones según la prioridad, visibilidad y categoría.
Categorías de notificaciones se utilizan para filtrar las notificaciones que se pueden presentar cuando el dispositivo está en *no molestar* modo.

Para obtener información detallada sobre cómo crear e iniciar las notificaciones con las características más recientes de Android 5.0, consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).


## <a name="new-apis"></a>Nuevas API.

Además de las nuevas características de apariencia y funcionamiento descritas anteriormente, Android 5.0 agrega nuevas API que amplían las capacidades de multimedia existente, el almacenamiento y la funcionalidad o la conectividad inalámbrica. Además, Android 5.0 incluye nuevas API que proporcionan compatibilidad para una nueva característica de programador de trabajo.

### <a name="camera"></a>Cámara

Android 5.0 proporciona varias API nuevas para las capacidades mejoradas de cámara. El nuevo `Android.Hardware.Camera2` espacio de nombres incluye la funcionalidad para tener acceso a los dispositivos de cámara individuales conectados a un dispositivo Android. Además, `Android.Hardware.Camera2` modela cada dispositivo de la cámara como una canalización: acepta una solicitud de captura, captura la imagen y, a continuación, envía el resultado. Este enfoque hace posible que las aplicaciones para poner en cola varias solicitudes de captura en un dispositivo de la cámara.

Las siguientes API permiten estas nuevas características:

-   `CameraManager.GetCameraIdList` &ndash; Le ayuda a obtener acceso mediante programación a los dispositivos de cámara; usa `CameraManager.OpenCamera` para conectarse a un dispositivo de la cámara concreta.

-   `CameraCaptureSession` &ndash; Captura o transmite imágenes desde el dispositivo de la cámara. Implementar un `CameraCaptureSession.CaptureListener` interfaz para controlar nuevos eventos de captura de imagen.

-   `CaptureRequest` &ndash; Define los parámetros de captura.

-   `CaptureResult` &ndash; Proporciona los resultados de una operación de captura de imagen.

Para obtener más información acerca de la cámara nueva API en Android 5.0, consulte [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Reproducción de audio

5.0 actualizaciones de Android el `AudioTrack` clase para la reproducción de audio mejor:

-   `ENCODING_PCM_FLOAT` &ndash; Configura `AudioTrack` para aceptar datos de audio en formato de punto flotante para un mejor intervalo dinámico, mayor altura y una mayor calidad (gracias a una mayor precisión). Además, formato de punto flotante ayuda a evitar el recorte de audio.

-   `ByteBuffer` &ndash; Ahora puede proporcionar datos de audio `AudioTrack` como una matriz de bytes.

-   `WRITE_NON_BLOCKING` &ndash; Esta opción simplifica el almacenamiento en búfer y el subprocesamiento múltiple para algunas aplicaciones.

Para obtener más información acerca de `AudioTrack` mejoras en Android 5.0, consulte [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Control de reproducción multimedia

Android 5.0 presenta la nueva `Android.Media.MediaController` clase, que sustituyen `RemoteControlClient`. `Android.Media.MediaController` Proporciona API de control de transporte simplificada y ofrece un control de subprocesos de reproducción fuera del contexto de interfaz de usuario. Las siguientes nuevas API controlan de control de transporte:

-   `Android.Media.Session.MediaSession` &ndash; Una sesión de control de medios que controla varios controladores. Se llama a `MediaSession.GetSessionToken` para solicitar un token que usa su aplicación para interactuar con la sesión.

-   `MediaController.TransportControls` &ndash; Transportan de identificadores de comandos como **reproducir**, **detener**, y **Skip**.

Además, puede usar el nuevo `Android.App.Notification.MediaStyle` clase para asociar una sesión de medios con contenido enriquecido de notificación (por ejemplo, extraer y mostrar las carátulas de álbum).

Para obtener más información sobre las nuevas características de control de reproducción de medios en Android 5.0, consulte [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Almacenamiento

Android 5.0 actualiza el marco de trabajo de acceso de almacenamiento para que sea más fácil para las aplicaciones trabajar con documentos y directorios:

-   Para seleccionar un subárbol de directorio, puede crear y enviar un `Android.Intent.Action.OPEN_DOCUMENT_TREE` intención. Este intento hace que el sistema mostrar todas las instancias del proveedor que admiten la selección de subárbol; a continuación, el usuario examina y selecciona un directorio.

-   Para crear y administrar documentos nuevos o directorios en cualquier lugar en un subárbol, use la nueva `CreateDocument`, `RenameDocument`, y `DeleteDocument` métodos de `DocumentsContract`.

-   Para obtener las rutas de acceso a directorios de medios en todos los dispositivos de almacenamiento compartido, llame al nuevo `Android.Content.Context.GetExternalMediaDirs` método.

Para obtener más información sobre el almacenamiento de la nueva API en Android 5.0, consulte [almacenamiento](http://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Conectividad e inalámbricas

Android 5.0 agrega las siguientes mejoras de API para la conectividad e inalámbricos:

-   Nuevo *varias redes* API que permiten que las aplicaciones buscar y seleccionar redes con funcionalidades específicas antes de realizar una conexión.

-   Funcionalidad de difusión de Bluetooth que permite que un dispositivo Android 5.0 para que actúe como periféricos Bluetooth de bajo consumo.

-   Mejoras de NFC que resulte más fácil utilizar la funcionalidad de las comunicaciones de cerca de campo para compartir datos con otros dispositivos.

Para obtener más información acerca de la nueva inalámbrica y conectividad API en Android 5.0, consulte [conectividad e inalámbricos](http://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Programación de trabajos

Android 5.0 presenta una nueva `JobScheduler` API que puede ayudar a los usuarios minimizar la batería se agote rápidamente programando ciertas tareas para ejecutarse únicamente cuando el dispositivo está conectado y de carga. Esta característica de programador de trabajo también puede usarse para programar una tarea que se ejecuta cuando las condiciones son más adecuadas para esa tarea, como la descarga de un archivo grande cuando el dispositivo está conectado a través de una red Wi-Fi en lugar de una red de uso medido.

Para obtener más información acerca de la nueva API en Android 5.0 de programación de trabajos, consulte [programación de trabajos](http://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Resumen

Este artículo proporciona información general sobre las nuevas características importantes en Android 5.0 para los desarrolladores de aplicaciones de Xamarin.Android:

-   Tema de materiales

-   Animaciones

-   Elevación y sombras de vista

-   Extracción de características de color, por ejemplo, puede dibujar tintes y destacado color

-   El nuevo `RecyclerView` y `CardView` widgets

-   Mejoras de notificación

-   Nuevas API para la cámara, reproducción de audio, control de medios, almacenamiento, o la conectividad inalámbrica y programación de trabajos

Si está familiarizado con el desarrollo de Xamarin Android, lea [configuración e instalación](~/android/get-started/installation/index.md) que le ayudarán a empezar a trabajar con Xamarin.Android.
[Hello, Android](~/android/get-started/hello-android/index.md) es una excelente introducción para obtener información sobre cómo crear proyectos de Android.



## <a name="related-links"></a>Vínculos relacionados

- [Vista previa para desarrolladores de Android L](http://developer.android.com/preview/index.html)
- [Obtenga el SDK de Android](https://developer.android.com/sdk/index.html#Other)
- [Diseño de materiales](http://developer.android.com/preview/material/index.html)
- [Principios de diseño de materiales](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
