---
title: Características de círculo
description: Este artículo proporciona una introducción de alto nivel de las nuevas características introducidas en Android 5.0 (círculo). Estas características incluyen un nuevo estilo de interfaz de usuario denominado Material de tema, así como nuevas características auxiliares como las animaciones, vista sombras y tintes pueden dibujar. Android 5.0 también incluye notificaciones mejoradas, dos nuevos widgets de interfaz de usuario, un nuevo programador de trabajo y una serie de nuevas API para mejorar el almacenamiento, redes, conectividad y capacidades multimedia.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: cdef611525abbe4f066959c0ac56380b1c617747
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773620"
---
# <a name="lollipop-features"></a>Características de círculo

_Este artículo proporciona una introducción de alto nivel de las nuevas características introducidas en Android 5.0 (círculo). Estas características incluyen un nuevo estilo de interfaz de usuario denominado Material de tema, así como nuevas características auxiliares como las animaciones, vista sombras y tintes pueden dibujar. Android 5.0 también incluye notificaciones mejoradas, dos nuevos widgets de interfaz de usuario, un nuevo programador de trabajo y una serie de nuevas API para mejorar el almacenamiento, redes, conectividad y capacidades multimedia._

## <a name="lollipop-overview"></a>Información general de círculo

Android 5.0 (círculo) presenta un nuevo lenguaje de diseño, *Material de diseño*, y con él una compatibilidad con conversión de las nuevas características para crear aplicaciones más sencilla e intuitiva para usar. Con Material de diseño, Android 5.0 no solo ofrece teléfonos Android renueva; También proporciona un nuevo conjunto de reglas de diseño para tabletas basados en Android, equipos de escritorio, inspecciones y televisores inteligentes. Estas reglas de diseño resaltan la simplicidad y minimalism al hacer uso de atributos al tacto conocidos (por ejemplo, indicaciones de superficie y borde realistas) para ayudar a los usuarios rápidamente y comprender intuitivamente la interfaz.

*Tema material* es la representación de estos principios de diseño de la interfaz de usuario en Android. Este artículo comienza al cubrir características auxiliares del Material tema:

-   **Las animaciones** &ndash; *Touch comentarios* animaciones, *transición de actividad* animaciones, *ver la transición de estado* animaciones y un *revelar efecto*.

-   **Ver las sombras y elevación** &ndash; vistas tienen ahora un `elevation` propiedad; vistas con mayor `elevation` valores proyectar sombras más grandes en el fondo.

-   **Características de color** &ndash; *tintes pueden dibujar* permite reutilizar los activos de imagen cambiando su color y *extracción de color destacado* le ayuda a dinámicamente tema de la aplicación según los colores en una imagen.

El tema Material muchas características ya están integradas en la interfaz de usuario de 5.0 Android experiencia, mientras que otros usuarios deben agregarse explícitamente a las aplicaciones. Por ejemplo, algunas vistas estándares (como los botones) ya incluyen táctil comentarios animaciones, mientras que las aplicaciones deben habilitar la mayoría de las sombras de vista.

Además de las mejoras de la interfaz de usuario imperiosa a través de tema de Material, Android 5.0 también incluye otras características nuevas que se tratan en este artículo:

-   **Mejorado notificaciones** &ndash; notificaciones en Android 5.0 se ha actualizado significativamente con un nuevo aspecto, compatibilidad con las notificaciones de la pantalla de bloqueo y un nuevo *aviso* formato de presentación de notificación.

-   **Nuevos widgets de interfaz de usuario** &ndash; nuevo `RecyclerView` widget resulta más fácil para las aplicaciones transmitir grandes conjuntos de datos e información compleja y la nueva `CardView` widget proporciona un formato de presentación de tipo tarjeta simplificado para mostrar texto y imágenes.

-   **Nuevas API** &ndash; Android 5.0 agrega nuevas API para compatibilidad con múltiples de red, mejorar la conectividad Bluetooth, administración de almacenamiento más sencilla y un control más flexible de dispositivos de cámara y reproductores multimedia. Un nuevo trabajo de característica de programación está disponible para ejecutar tareas de forma asincrónica en las horas programadas. Esta característica ayuda a mejorar la duración de la batería, por ejemplo, programar tareas tenga lugar cuando el dispositivo está conectado y está cargando.


## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android 5.0 en aplicaciones basadas en Xamarin, es necesario lo siguiente:

-   **Xamarin.Android** &ndash; Xamarin.Android 4.20 o posterior debe instalarse y configurarse con Visual Studio o Visual Studio para Mac. 

-   **SDK de Android** &ndash; Android 5.0 (API de 21) o posterior debe estar instalado mediante el Administrador de SDK de Android.

-   **Kit de desarrollo de Java** &ndash; Xamarin.Android requiere [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si va a desarrollar para el nivel de API 24 o mayor (JDK 1.8 también admite niveles de API anteriores a 24, incluidos el círculo). La versión de 64 bits de JDK 1.8 es necesaria si utiliza controles personalizados o el controlador de vista previa de formularios.

Se puede seguir usando [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si es desarrollar específicamente para el nivel de API 23 o una versión anterior.


## <a name="setting-up-an-android-50-project"></a>Cómo configurar un proyecto Android 5.0

Para crear un proyecto de Android 5.0, debe instalar los paquetes SDK y herramientas más recientes. Utilice los pasos siguientes para configurar un proyecto Xamarin.Android destinado a Android 5.0:

1. Instalar las herramientas de Xamarin.Android y activar su licencia de Xamarin. Vea [configuración e instalación](~/android/get-started/installation/index.md) para obtener más información acerca de cómo instalar Xamarin.Android.

2. Si se utiliza Visual Studio para Mac, instale las actualizaciones más recientes de Android 5.0.

3. Inicie el Administrador de SDK de Android (en Visual Studio para Mac, utilice **herramientas &gt; abrir el Administrador de Android SDK&hellip;**) e instalar herramientas de Android SDK 23.0.5 o una versión posterior:

    [![Seleccionar herramientas de SDK de Android en el Administrador de SDK de Android](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Además, instale los paquetes más recientes de SDK de Android 5.0 (API 21 o posterior):

    [![Instalar paquetes de SDK de Android 5.0 en el Administrador de SDK de Android](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Para obtener más información acerca de cómo utilizar el Administrador de Android SDK, vea [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).

4. Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información acerca de cómo crear proyectos de Android. Cuando cree un proyecto Android, asegúrese de configurar las opciones de versión para Android 5.0.
   En Visual Studio para Mac, vaya a **opciones de proyecto &gt; generar &gt; General** y establecer **.NET framework de destino** a **Android 5.0 (círculo)** o más adelante:

    ![Establecer el Framwework de destino en el círculo Android 5.0](lollipop-images/target-framework.png)

   En **opciones de proyecto &gt; generar &gt; aplicación Android**, establecer la duración mínima y tener como destino la versión Android a **automático: versión de framework de destino de uso**:

    ![Establecer las versiones mínima y Android de destino automático](lollipop-images/minimum-android-version.png)

5. Configurar un emulador o un dispositivo Android para probar la aplicación. Si está utilizando un emulador, consulte [el programa de instalación de emulador Android](~/android/get-started/installation/android-emulator/index.md) para obtener información sobre cómo configurar un emulador de Android para su uso con Xamarin Studio o Visual Studio. Si está utilizando un dispositivo Android, consulte [configuración el SDK de vista previa](https://developer.android.com/preview/setup-sdk.html) para obtener información sobre cómo actualizar el dispositivo para Android 5.0. Para configurar un dispositivo Android para ejecutar y depurar aplicaciones de Xamarin.Android, consulte [establecer dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

Nota: Si va a actualizar un proyecto de Android existente que se dirige a la vista previa de L Android, debe actualizar el **.NET Framework de destino** y **versión Android** a los valores que se ha descrito anteriormente.

## <a name="important-changes"></a>Cambios importantes

Anteriormente las aplicaciones publicadas Android pudieron verse afectadas por los cambios en Android 5.0. En concreto, Android 5.0 usa un nuevo tiempo de ejecución y un formato de notificación cambiado significativamente.

### <a name="android-runtime"></a>En tiempo de ejecución de Android

Android 5.0 utiliza el tiempo de ejecución Android nueva (prediseñadas) como el tiempo de ejecución predeterminado en lugar de Dalvik. ARTE implementa varias características nuevas principales:

-   **Compilación de anticipado (AOT)** &ndash; AOT puede mejorar el rendimiento de la aplicación mediante la compilación de código de la aplicación antes de que se inició la aplicación por primera vez. Cuando se instala una aplicación, carátulas genera una aplicación compilada ejecutable para el dispositivo de destino.

-   **Mejora de la colección de elementos no utilizados (GC)** &ndash; mejoras de GC en imágenes también pueden mejorar el rendimiento de la aplicación. Recolección de elementos ahora usa una pausa de GC en lugar de dos y completan las operaciones simultáneas de GC en el momento más oportuno.

-   **Depuración de aplicaciones mejorada** &ndash; prediseñadas proporcionan más detalles de diagnóstico para ayudar a analizar las excepciones e informes de bloqueo.

Las aplicaciones existentes deben funcionar sin cambios en el material gráfico &ndash; excepto para las aplicaciones que aprovechan técnicas únicas para el tiempo de ejecución de Dalvik anterior, que puede no funcionar en imágenes. Para obtener más información sobre estos cambios, consulte [comprobar el comportamiento de aplicación en el tiempo de ejecución Android (prediseñadas)](http://developer.android.com/guide/practices/verifying-apps-art.html).


### <a name="notification-changes"></a>Cambios de notificación

Las notificaciones han cambiado significativamente en Android 5.0:

-   **Sonidos y vibración se tratan de forma diferente** &ndash; sonidos de notificación y vibración ahora es administradas por `Notification.Builder` en lugar de `Ringtone`, `MediaPlayer`, y `Vibrator`.

-   **Nueva combinación de colores** &ndash; según el tema de Material, las notificaciones se representan con texto oscuro en fondos blancos o muy claros. Además, se pueden modificar canales alfa en iconos de notificación de Android para coordinar con combinaciones de colores del sistema. 

-   **Las notificaciones de la pantalla de bloqueo** &ndash; notificaciones pueden aparecer ahora en la pantalla de bloqueo del dispositivo.

-   **Aviso** &ndash; notificaciones de alta prioridad aparecen ahora en una ventana flotante pequeña (notificaciones de aviso) cuando el dispositivo se desbloquea y se activa la pantalla.

En la mayoría de los casos, migrar la funcionalidad de notificación de aplicación existente para Android 5.0 requiere los pasos siguientes:

1.  Convertir el código para usar `Notification.Builder` (o `NotificationsCompat.Builder`) para la creación de notificaciones. 

2.  Compruebe que están visibles en la nueva combinación de colores de tema de Material sus activos existentes de notificación.

3.  Decidir qué visibilidad deben tener las notificaciones cuando se van a mostrar en la pantalla de bloqueo. ¿Si una notificación no es pública, el contenido que debe aparecer en la pantalla de bloqueo?

4.  Establecer la categoría de las notificaciones por lo que estos se controlen correctamente en el nuevo Android 5.0 *no molestar* modo.

Si las notificaciones de presentan los controles de transporte, medios de mostrar estado de la reproducción, use `RemoteControlClient`, o llamar a `ActivityManager.GetRecentTasks`, consulte [cambios de comportamiento importantes](http://developer.android.com/preview/api-overview.html#Behaviors) para obtener más información acerca de cómo actualizar las notificaciones para Android 5.0.

Para obtener información sobre cómo crear las notificaciones en Android, consulte [notificaciones Local](~/android/app-fundamentals/notifications/local-notifications.md). El [compatibilidad](~/android/app-fundamentals/notifications/local-notifications.md#compatibility) sección de este artículo explica cómo crear notificaciones que son compatibles con hacia abajo con versiones anteriores de Android.


## <a name="material-theme"></a>Tema de materiales

El nuevo tema de Material Android 5.0 proporciona cambios importantes a la apariencia y funcionamiento de la interfaz de usuario de Android. Ahora, los elementos visuales usan superficies al tacto que llevar a cabo en negrita gráficos, tipografía y colores brillantes de diseño de impresión. Ejemplos de Material de tema se muestran en las capturas de pantalla siguiente:

[![Capturas de pantalla de la página de inicio del tema de Material, la pantalla de aplicaciones y la pantalla de configuración](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 le da la pantalla principal que se muestra en la parte izquierda. La captura de pantalla de center es la primera pantalla de la lista de aplicaciones y la captura de pantalla de la derecha es el **configuración** pantalla. Google [Material de diseño](https://material.io/guidelines/material-design/introduction.html) especificación explica las reglas de diseño subyacentes detrás el nuevo concepto de tema de Material.

Tema material incluye tres tipos integrados que puede usar en la aplicación: la `Theme.Material` el tema oscuro (valor predeterminado), el `Theme.Material.Light` tema y el `Theme.Material.Light.DarkActionBar` tema: 

[![Temas de capturas de pantalla de oscuro, claro y DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Para obtener más información sobre el uso de características de tema de Material en Xamarin.Android aplicaciones, consulte [Material tema](~/android/user-interface/material-theme.md).


## <a name="animations"></a>Animaciones

Android 5.0 proporciona táctil comentarios animaciones, animaciones de transición de actividad y animaciones de transición de estado de vista para realizar más intuitiva para usar interfaces de aplicación. Además, pueden utilizar aplicaciones de Android 5.0 *revelar efecto* animaciones para mostrar u ocultar vistas. Puede usar *curvada movimiento* opciones para configurar la rapidez o lentitud animaciones se representan.


### <a name="touch-feedback-animations"></a>Touch animaciones de comentarios

Táctil comentarios animaciones proporcionan a los usuarios sugerencias visuales cuando haya ha trabajado con una vista. Por ejemplo, los botones ahora muestran un efecto dominó cuando son modificadas &ndash; se trata de la animación de comentarios de táctil predeterminada en Android 5.0. Animación de ripple se implementa mediante la nueva `RippleDrawable` clase. El efecto dominó puede configurarse para terminar en los límites de la vista o se extienden más allá de los límites de la vista. Por ejemplo, la siguiente secuencia de capturas de pantalla muestra el efecto dominó en un botón durante animación táctil:

![Marco de capturas de pantalla de fotogramas de animación de rizado en un botón](lollipop-images/touch-animation.png)

Contacto del toque inicial con el botón se realiza en la primera imagen de la izquierda, mientras la secuencia restante (de izquierda a derecha) muestra cómo el efecto dominó se extiende hasta el borde del botón. Cuando finaliza la animación de ripple, la vista devuelve a su apariencia original. La animación de ripple predeterminada tiene lugar en una fracción de segundo, pero la longitud de la animación se puede personalizar para mayor o menos periodos de tiempo.

Para obtener más información sobre touch animaciones de comentarios en Android 5.0, consulte [personalizar comentarios táctil](http://developer.android.com/training/material/animations.html#Touch).


### <a name="activity-transition-animations"></a>Animaciones de transición de actividad

Animaciones de transición de actividad proporcionar a los usuarios una idea de continuidad visual cuando una actividad realiza una transición a otro. Las aplicaciones pueden especificar tres tipos de animaciones de transición:

-   **Escriba transición** &ndash; para cuando una actividad entra en la escena.

-   **Salir de transición** &ndash; para una actividad cuando cierra la escena.

-   **Elemento transición compartida** &ndash; para cuando se cambia una vista que es común a dos actividades como la primera transiciones de actividad a la siguiente.

Por ejemplo, la siguiente secuencia de capturas de pantalla muestra una transición de un elemento compartido:

[![Marco de capturas de pantalla de fotogramas de animación de transición de un elemento compartido](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Un elemento compartido (como una fotografía de un caterpillar) es una de varias vistas en la primera actividad; amplía para que sea la única vista en la segunda actividad como las transiciones de actividad primeras al segundo.

#### <a name="enter-transition-animation-types"></a>Escriba tipos de animaciones de transición

Para entrar transiciones, Android 5.0 proporciona tres tipos de animaciones:

-   **Puede ampliar la animación** &ndash; aumenta el tamaño de una vista desde el centro de la escena.

-   **Animación de diapositiva** &ndash; mueve una vista en uno de los bordes de una escena.

-   **Animación de atenuación** &ndash; fundido de una vista de la escena.

#### <a name="exit-transition-animation-types"></a>Tipos de animaciones de transición de salida

Para las transiciones de salida, Android 5.0 proporciona tres tipos de animaciones:

-   **Puede ampliar la animación** &ndash; reduce el tamaño de una vista en el centro de la escena.

-   **Animación de diapositiva** &ndash; mueve una vista a uno de los bordes de una escena.

-   **Animación de atenuación** &ndash; fundido de una vista de la escena.

#### <a name="shared-element-transition-animation-types"></a>Tipos de animación de transición de elemento compartidas

Elemento compartido transiciones admiten varios tipos de animaciones, como:

-   Cambiar los límites de diseño o un clip de una vista.

-   Cambiar la escala y la rotación de una vista.

-   Cambie el tipo de tamaño y la escala de una vista.

Para obtener más información acerca de las animaciones de transición de actividad en Android 5.0, consulte [personalizar transiciones de actividad](http://developer.android.com/training/material/animations.html#Transitions).


### <a name="view-state-transition-animations"></a>Animaciones de transición de estado de vista

Android 5.0 permite a las animaciones que se ejecuta cuando cambia el estado de una vista. Puede animar las transiciones de estado de vista mediante una de las técnicas siguientes:

-   Crear drawables que animar los cambios de estado asociadas a una vista determinada. El nuevo `AnimatedStateListDrawable` clase le permite crear drawables que se muestran las animaciones entre los cambios de estado de vista.

-   Definir la funcionalidad de animación que se ejecuta cuando cambia el estado de una vista. El nuevo `StateListAnimator` clase le permite definir una animación que se ejecuta cuando cambia el estado de una vista.

Para obtener más información acerca de animaciones de transición de estado de vista en Android 5.0, consulte [animar cambios de estado de vista](http://developer.android.com/training/material/animations.html#ViewState).


### <a name="reveal-effect"></a>Mostrar el efecto

El *revelar efecto* es un círculo de recorte que radius de cambios para mostrar u ocultar una vista. Puede controlar este efecto estableciendo el radio inicial y final de la circunferencia de recorte. La siguiente secuencia de capturas de pantalla muestra una animación de efecto reveal desde el centro de la pantalla:

[![Marco de capturas de pantalla de fotogramas de animación de mostrar](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

La secuencia siguiente muestra una animación de efecto de revelar que tiene lugar en la esquina inferior izquierda de la pantalla:

[![Marco de capturas de pantalla de fotogramas de animación de recorte](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Mostrar las animaciones se pueden invertir; es decir, el círculo de recorte puede reducir su tamaño para ocultar la vista en lugar de ampliar para mostrar la vista.

Para obtener más información sobre el efecto de reveal Android 5.0 en, consulte [utilizar el efecto revelar](http://developer.android.com/training/material/animations.html#Reveal).


### <a name="curved-motion"></a>Movimiento curva

Además de estas características de animación, Android 5.0 proporciona nuevas API que permiten especificar las curvas de tiempo y el movimiento de animaciones. Android 5.0 utiliza explícitamente estas curvas para interpolar movimiento temporal y espacial durante las animaciones. Se definen tres curvas en Android 5.0:

-   **Fast\_out\_lineal\_en** &ndash; acelera rápidamente y continúa avanzando hasta el final de la animación.

-   **Fast\_out\_lenta\_en** &ndash; acelera rápidamente y lenta se ralentiza hacia el final de la animación.

-   **Lineal\_out\_lenta\_en** &ndash; empieza con una velocidad máxima y lenta reduce la velocidad al final de la animación.

Puede usar el nuevo `PathInterpolator` clase para especificar cómo realiza la interpolación de movimiento. `PathInterpolator` es un interpolador que atraviesa las rutas de acceso de animación según los puntos de control especificados y curvas de movimiento. Para obtener más información acerca de cómo especificar la configuración de movimiento curva en Android 5.0, consulte [Use curvas movimiento](http://developer.android.com/training/material/animations.html#CurvedMotion).


## <a name="view-shadows--elevation"></a>Vista sombras & elevación

En Android 5.0, puede especificar el *elevación* de una vista estableciendo un nuevo `Z` propiedad. Mayor `Z` valor hace que la vista para convertir una sombra más grande en segundo plano, por lo que la vista parece float más alto por encima del fondo. Puede establecer la elevación inicial de una vista mediante la configuración de su `elevation` atributo en el diseño.

En el ejemplo siguiente se muestra las sombras tinte vacío `TextView` controlar cuando se establece el atributo de elevación 2dp, 4dp y 6dp, respectivamente:

[![Capturas de pantalla de sombras de vista más grandes de progessively](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Ver la configuración de instantáneas puede ser estática (como se muestra arriba) o bien se pueden usar en las animaciones para hacer que una vista parece temporalmente por encima del fondo de la vista. Puede usar el `ViewPropertyAnimator` clase para animar la elevación de una vista. La elevación de una vista es la suma de su diseño `elevation` configuración más una `translationZ` propiedad que se puede establecer a través de un `ViewPropertyAnimator` llamada al método.

Para obtener más información acerca de las sombras de vista en Android 5.0, consulte [definir sombras y recorte vistas](http://developer.android.com/training/material/shadows-clipping.html).


## <a name="color-features"></a>Características de color

Android 5.0 proporciona dos nuevas características para la administración de color en las aplicaciones:

-   *Puede dibujar tintes* le permite modificar los colores de activos de imagen al cambiar un atributo de diseño.

-   *Extracción de color destacado* permite personalizar dinámicamente el tema de color de la aplicación para coordinar con la paleta de colores de una imagen mostrada.


### <a name="drawable-tinting"></a>Tintes pueden dibujar

Android 5.0 diseños reconocen un nuevo `tint` atributo que se puede utilizar para establecer el color de drawables sin tener que crear varias versiones de estos recursos que mostrar colores diferentes. Para usar esta característica, defina un mapa de bits como una máscara alfa y usará el `tint` atributo para definir el color del recurso. Esto permite crear los activos de una vez y les de color en el diseño para que coincida con el tema.

En el ejemplo siguiente, un único elemento de imagen &ndash; un logotipo blanco con un fondo transparente &ndash; se utiliza para crear un tono variaciones:

![Logotipo de blanco Xamarin con fondo transparente](lollipop-images/xamarin-logo-white.png)

Este logotipo se muestra sobre un fondo azul circular tal como se muestra en los ejemplos siguientes. La imagen de la izquierda es el modo en que el logotipo aparece sin una `tint` configuración. En el centro, el logotipo de la imagen `tint` atributo está establecido en un color gris oscuro. En la imagen a la derecha, `tint` se establece en un color gris claro:

![Ejemplos del logotipo anterior con la configuración de un tono diferente](lollipop-images/drawable-tinting.png)

Para obtener más información acerca de tintes pueden dibujar en Android 5.0, consulte [tintes pueden dibujar](http://developer.android.com/training/material/drawables.html#DrawableTint).


### <a name="prominent-color-extraction"></a>Extracción de Color destacado

El nuevo Android 5.0 `Palette` clase le permite extraer los colores de una imagen para que se puedan aplicar dinámicamente una paleta de colores personalizada. La `Palette` clase extrae seis colores de una imagen y las etiquetas estos colores según sus niveles relativos de saturación de color y el brillo:

-   Vibrante

-   Vibrante oscuro

-   Luz vibrante

-   Silenciado

-   Silenciado oscuro

-   Luz silenciado

Por ejemplo, en las capturas de pantalla siguiente, un aplicación de visualización de fotografías extrae los colores destacados de la imagen en pantalla y usa estos colores para adaptar la combinación de colores de la aplicación para que coincida con la imagen:

[![Capturas de pantalla de extracciones de color verde, azul y rosa tema](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

En las capturas de pantalla anterior, la barra de acciones se establece en la "vibrante luz" extraída color y el fondo se establece en la "vibrante oscuridad" extraída color. En cada ejemplo anterior, una fila de cuadrados de color pequeñas se incluye para ilustrar la paleta de colores que se han extraído de la imagen.

Para obtener más información acerca de la extracción de color en Android 5.0, consulte [extraer prominentes colores de una imagen](http://developer.android.com/training/material/drawables.html#ColorExtract).


## <a name="new-ui-widgets"></a>Nuevos Widgets de interfaz de usuario

Android 5.0 presenta dos nuevos widgets de interfaz de usuario:

-   `RecyclerView` &ndash; Un grupo de vista que muestra una lista de elementos desplazables.

-   `CardView` &ndash; Diseño básico con esquinas redondeadas.

Ambos widgets incluyen compatibilidad preparadas para características de tema de Material; Por ejemplo, `RecyclerView` usa animaciones para agregar o quitar vistas, y `CardView` utiliza ver sombras para cada tarjeta hacer que parezca que float por encima del fondo. En las capturas de pantalla siguiente se muestran ejemplos de estos nuevos widgets:

[![Capturas de pantalla de aplicaciones compiladas con RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

La captura de pantalla de la izquierda es un ejemplo de `RecyclerView` tal y como se utiliza en una aplicación de correo electrónico y la captura de pantalla de la derecha es un ejemplo de `CardView` tal y como se usa en una aplicación de reservas de viajes.


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` es similar a `ListView,` , pero es más adecuado para grandes conjuntos de vistas o listas con elementos que cambian de forma dinámica. Al igual que `ListView,` especifique un adaptador para tener acceso al conjunto de datos subyacente. Sin embargo, a diferencia de `ListView,` utiliza un *del Administrador de diseño* colocar los elementos de `RecyclerView`. El Administrador de diseño también se ocupa de vista reciclado; administra la reutilización de las vistas de elementos que ya no son visibles para el usuario.

Cuando se usa un `RecyclerView` widget, debe especificar un `LayoutManager` y un adaptador. Como se muestra en esta ilustración, `LayoutManager` es el intermediario entre el adaptador y el `RecyclerView`:

![Diagrama de RecyclerView incluye soporte técnico LayoutManager, el adaptador y el conjunto de datos](lollipop-images/recyclerview-diagram.png)

Las capturas de pantalla siguientes ilustran una `RecyclerView` que contiene 100 elementos (cada elemento consta de un `ImageView` y `TextView`):

[![Capturas de pantalla de una aplicación de RecyclerView desplazarse a través de imágenes](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` controla este conjunto de datos de gran tamaño con facilidad &ndash; desplazamiento desde el principio de la lista al final de la lista en este ejemplo aplicación tarda solo unos segundos. `RecyclerView` también admite animaciones; de hecho, se habilitan las animaciones para agregar y quitar elementos de forma predeterminada. Cuando se agrega un elemento a una `RecyclerView`, que ésta desaparezca en tal y como se muestra en esta secuencia de capturas de pantalla:

[![Marco de captura de pantalla de marco de una atenuación de elemento de la foto en](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Para obtener más información acerca de `RecyclerView`, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).


### <a name="cardview"></a>CardView

`CardView` es una vista sencilla que simula una tarjeta flotante con esquinas redondeadas. Dado que `CardView` tiene sombras de vistas integradas, proporciona una manera sencilla de agregar profundidad visual a la aplicación. Las capturas de pantalla siguientes muestran tres ejemplos orientados a texto de `CardView`:

[![Capturas de pantalla de ejemplo de aplicaciones con RecyclerView con elementos basados en CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Cada una de las tarjetas en el ejemplo anterior contiene un `TextView`; se establece el color de fondo a través de la `cardBackgroundColor` atributo.

Para obtener más información acerca de `CardView`, consulte [CardView](~/android/user-interface/controls/card-view.md).


## <a name="enhanced-notifications"></a>Notificaciones mejoradas

El sistema de notificación de Android 5.0 se ha actualizado significativamente con un nuevo formato visual y nuevas características. Las notificaciones tienen un nuevo aspecto en Android 5.0. Por ejemplo, las notificaciones en Android 5.0 ahora usan texto oscuro sobre un fondo claro:

![Ejemplo de una notificación de Android 5.0 sin expandido](lollipop-images/expanded-notification-contracted.png)

Cuando se muestra un icono grande en una notificación (como se muestra en el ejemplo anterior), Android 5.0 presenta del icono pequeño, como un distintivo sobre el icono de gran tamaño. 

En Android 5.0, las notificaciones también pueden aparecer en la pantalla de bloqueo del dispositivo.
Por ejemplo, aquí tiene una captura de pantalla de ejemplo de una pantalla de bloqueo con una única notificación:

[![Captura de pantalla de notificación que aparecen en la pantalla de bloqueo](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Los usuarios hacer doble punteo en una notificación en la pantalla de bloqueo para desbloquear el dispositivo y saltar a la aplicación que se originó dicha notificación, o pase a descartar la notificación. Las notificaciones tienen un nuevo *visibilidad* el valor que determina la cantidad de contenido que se puede mostrar en la pantalla de bloqueo. Los usuarios pueden elegir si desea permitir contenido confidencial que se mostrará en las notificaciones de la pantalla de bloqueo.

Android 5.0 presenta un nuevo formato de presentación de notificación de alta prioridad denominado *aviso*. Las notificaciones de aviso deslice hacia abajo desde la parte superior de la pantalla durante unos segundos y, a continuación, retreat hacia el tono de notificación en la parte superior de la pantalla. Las notificaciones de aviso hacen posible para el sistema de la interfaz de usuario para colocar información importante delante del usuario sin interrumpir la actividad que se está ejecutando. En el ejemplo siguiente se muestra una notificación de aviso simple que se muestra encima de una aplicación:

[![Ejemplo de una notificación de pantalla de datos](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Las notificaciones de aviso se usan normalmente para los siguientes eventos:

-   Un nuevo mensaje siguiente

-   Una llamada telefónica entrante

-   Indicación de batería baja

-   Una alarma

Android 5.0 muestra una notificación en formato de aviso únicamente cuando tenga un valor de prioridad alta o max.

En Android 5.0, puede proporcionar metadatos de la notificación para ayudar a Android ordenar y mostrar las notificaciones de forma más inteligente. Android 5.0 organiza las notificaciones de acuerdo con la prioridad, la visibilidad y la categoría.
Categorías de notificaciones que se usan para filtrar qué notificaciones se pueden presentar cuando el dispositivo esté en *no molestar* modo.

Para obtener información detallada sobre cómo crear e iniciar las notificaciones con las últimas características de Android 5.0, consulte [notificaciones Local](~/android/app-fundamentals/notifications/local-notifications.md).


## <a name="new-apis"></a>Nuevas API.

Además de las nuevas características de apariencia y funcionamiento que se ha descrito anteriormente, Android 5.0 agrega nuevas API que amplían las capacidades de multimedia existente, el almacenamiento y la funcionalidad y la conectividad inalámbrica. Además, Android 5.0 incluye nuevas API que proporcionan compatibilidad para una nueva característica de programador de trabajo.

### <a name="camera"></a>Cámara

Android 5.0 proporciona varias API nuevas capacidades mejoradas de cámara. El nuevo `Android.Hardware.Camera2` espacio de nombres incluye la funcionalidad de acceso a los dispositivos de cámara individual está conectado a un dispositivo Android. Además, `Android.Hardware.Camera2` modelos de cada dispositivo de cámara como una canalización: acepta una solicitud de captura, captura la imagen y, a continuación, muestra el resultado. Este enfoque permite a las aplicaciones en cola varias solicitudes de captura en un dispositivo de la cámara.

Las siguientes API permiten estas nuevas características:

-   `CameraManager.GetCameraIdList` &ndash; Le ayuda a obtener acceso mediante programación a dispositivos de cámara; utiliza `CameraManager.OpenCamera` para conectarse a un dispositivo de cámara específica.

-   `CameraCaptureSession` &ndash; Captura o imágenes desde el dispositivo de la cámara se transmita por secuencias. Implementar un `CameraCaptureSession.CaptureListener` interfaz para controlar nuevos eventos de captura de imagen.

-   `CaptureRequest` &ndash; Define los parámetros de captura.

-   `CaptureResult` &ndash; Proporciona los resultados de una operación de captura de imagen.

Para obtener más información acerca de la cámara nuevas API de Android 5.0, consulte [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Reproducción de audio

Android 5.0 actualizaciones el `AudioTrack` clase para reproducción de audio mejor:

-   `ENCODING_PCM_FLOAT` &ndash; Configura `AudioTrack` para aceptar los datos de audio en formato de punto flotante para un mejor intervalo dinámico, mayor espacio disponible y mayor calidad (gracias a una mayor precisión). Además, formato de punto flotante ayuda a evitar el recorte de audio.

-   `ByteBuffer` &ndash; Ahora puede proporcionar datos de audio para `AudioTrack` como una matriz de bytes.

-   `WRITE_NON_BLOCKING` &ndash; Esta opción simplifica el almacenamiento en búfer y el subprocesamiento múltiple para algunas aplicaciones.

Para obtener más información acerca de `AudioTrack` mejoras en Android 5.0, consulte [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Control de reproducción de medios

Android 5.0 presenta la nueva `Android.Media.MediaController` de la clase, que sustituyen `RemoteControlClient`. `Android.Media.MediaController` proporciona las API de control de transporte simplificado y ofrece un control de subprocesos de reproducción fuera del contexto de la interfaz de usuario. Las siguientes API de administrar el control de transporte:

-   `Android.Media.Session.MediaSession` &ndash; Una sesión de control de medios que controla varios controladores. Se llama a `MediaSession.GetSessionToken` para solicitar un token que usa su aplicación para interactuar con la sesión.

-   `MediaController.TransportControls` &ndash; Transportan de identificadores de comandos como **reproducir**, **detener**, y **omitir**.

Además, puede utilizar el nuevo `Android.App.Notification.MediaStyle` clase para asociar una sesión de medios con contenido de la notificación enriquecidos (por ejemplo, extraer y mostrar la carátula del álbum).

Para obtener más información sobre las nuevas características de control de reproducción de multimedia en Android 5.0, consulte [Media](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Almacenamiento

Android 5.0 actualiza el marco de trabajo de acceso de almacenamiento para que sea más fácil para las aplicaciones trabajar con directorios y documentos:

-   Para seleccionar un subárbol de un directorio, puede crear y enviar un `Android.Intent.Action.OPEN_DOCUMENT_TREE` intención. Esta intención hace que el sistema mostrar todas las instancias del proveedor que admiten la selección de subárbol; el usuario, a continuación, examina y selecciona un directorio.

-   Para crear y administrar documentos nuevos o directorios en cualquier lugar en un subárbol, use la nueva `CreateDocument`, `RenameDocument`, y `DeleteDocument` métodos de `DocumentsContract`.

-   Para obtener las rutas de acceso a directorios de medios en todos los dispositivos de almacenamiento compartido, llame al nuevo `Android.Content.Context.GetExternalMediaDirs` método.

Para obtener más información acerca de las API de Android 5.0 un almacenamiento nuevo, consulte [almacenamiento](http://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Conectividad e inalámbricas

Android 5.0 agrega las siguientes mejoras de API de inalámbrica y la conectividad:

-   Nueva *redes múltiples* las API que permiten a las aplicaciones buscar y seleccionar redes con funcionalidades específicas antes de realizar una conexión.

-   Funcionalidad de difusión de Bluetooth que permite a un dispositivo Android 5.0 para que actúe como un periférico de Bluetooth de baja energía.

-   Mejoras de NFC que resulten más fácil usar la funcionalidad de las comunicaciones de datos en proximidad para compartir datos con otros dispositivos.

Para obtener más información acerca de la conectividad de las API de Android 5.0 e inalámbricos nuevo, consulte [inalámbrica y la conectividad](http://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Programación de trabajos

Android 5.0 presenta una nueva `JobScheduler` API que puede ayudar a los usuarios minimizar la purga de la batería: programar ciertas tareas para ejecutarse únicamente cuando el dispositivo está conectado y está cargando. Esta característica de programador de trabajo también puede utilizarse para programar una tarea que se ejecuta cuando las condiciones son más adecuadas para esa tarea, como una descarga un archivo grande cuando el dispositivo está conectado a través de una red Wi-Fi en lugar de una red de uso medido.

Para obtener más información sobre el nuevo trabajo de programación de las API de Android 5.0, consulte [programar trabajos](http://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Resumen

Este artículo proporciona información general sobre nuevas características importantes en Android 5.0 para los desarrolladores de aplicaciones de Xamarin.Android:

-   Tema de materiales

-   Animaciones

-   Vista sombras y elevación

-   Características de color, por ejemplo, puede dibujar tintes y prominentes extracción de color

-   El nuevo `RecyclerView` y `CardView` widgets

-   Mejoras de notificación

-   Nuevas API de cámara, reproducción de audio, control de medios, almacenamiento, y la conectividad inalámbrica y programación de trabajos

Si está familiarizado con el desarrollo de Xamarin para Android, lea [configuración e instalación](~/android/get-started/installation/index.md) que le ayudarán a empezar a trabajar con Xamarin.Android.
[Hello, Android](~/android/get-started/hello-android/index.md) es una excelente introducción para obtener información sobre cómo crear proyectos de Android.



## <a name="related-links"></a>Vínculos relacionados

- [Vista previa de desarrollador de Android L](http://developer.android.com/preview/index.html)
- [Obtener el SDK de Android](https://developer.android.com/sdk/index.html#Other)
- [Diseño de materiales](http://developer.android.com/preview/material/index.html)
- [Principios de diseño de materiales](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
