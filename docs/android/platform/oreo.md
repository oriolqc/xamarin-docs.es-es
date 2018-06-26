---
title: Características de Oreos
description: Cómo empezar a usar Xamarin.Android para desarrollar aplicaciones para la versión más reciente de Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a23072427a74119bfa339fea8a695cd13b775685
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935108"
---
# <a name="oreo-features"></a>Características de Oreos

_Cómo empezar a usar Xamarin.Android para desarrollar aplicaciones para la versión más reciente de Android._

[Android Oreos 8.0](https://developer.android.com/index.html) es la versión más reciente de Android disponible en Google. Android Oreos ofrece muchas características nuevas de interés para los desarrolladores de Xamarin.Android. Estas características incluyen canales de notificación, distintivos de notificación, las fuentes personalizadas en XML, las fuentes descargables, autorrelleno e imagen en imagen (PIP). Android Oreos incluyen nuevas API para estas nuevas capacidades, y estas API están disponibles para las aplicaciones Xamarin.Android cuando usas Xamarin.Android 8.0 y versiones posteriores.

[![Imagen de héroe Oreos Android](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Este artículo está estructurado para ayudarle a empezar a trabajar en el desarrollo de aplicaciones de Xamarin.Android para Android Oreos 8.0. Se explica cómo instalar las actualizaciones necesarias, configure el SDK y crear un emulador (o dispositivo) para las pruebas. También proporciona un resumen de las nuevas características de Android Oreos 8.0, con vínculos a aplicaciones de ejemplo que muestran cómo usar las características de Android Oreos en aplicaciones de Xamarin.Android.


## <a name="requirements"></a>Requisitos

El siguiente es necesaria para usar características Oreos Android en aplicaciones basadas en Xamarin:

-   **Visual Studio** &ndash; si usas Windows, se requiere la versión 15.5 o una versión posterior de Visual Studio.  Si utilizas un equipo Mac, se requiere Visual Studio para Mac versión 7.2.0.

-   **Xamarin.Android** &ndash; Xamarin.Android 8.0 o posterior debe instalarse y configurarse con Visual Studio.

-   **SDK de Android** &ndash; (26 de API) de Android SDK 8.0 o posterior debe estar instalado mediante el Administrador de SDK de Android.



## <a name="getting-started"></a>Introducción

Para empezar a trabajar con Android Oreos Xamarin.Android, debe descargar e instalar los paquetes SDK y herramientas más recientes antes de poder crear un proyecto de Android Oreos:

1. Actualice a la versión más reciente de Visual Studio.

2. Instalar el **8.0.0 Android (26 de API)** o posterior paquetes y herramientas mediante el Administrador de SDK.

3. Cree un nuevo proyecto de Xamarin.Android destinado a Android Oreos (26 de API).

4. Configurar un emulador o dispositivo para probar aplicaciones de Android Oreos.

Cada uno de estos pasos se explica en las secciones siguientes:



### <a name="update-visual-studio-and-xamarinandroid"></a>Actualizar Xamarin.Android y Visual Studio

Para agregar compatibilidad de Android Oreos a Visual Studio, haga lo siguiente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   Si usa Visual Studio de 2017: 

    1. Actualización de Visual Studio 2017 versión 15.5 o posterior (vea [2017 de actualización de Visual Studio](https://docs.microsoft.com/en-us/visualstudio/install/update-visual-studio)).

    2. Use el Administrador de SDK [las instrucciones de instalación](~/android/get-started/installation/android-sdk.md#installation) para instalar el Administrador de SDK de Xamarin.
       El Administrador de SDK de Xamarin debe estar instalado porque Google no proporciona un standlone Administrador de SDK de GUI que admite las API 26.0 y versiones posterior.

-   Si usa Visual Studio 2015, le recomendamos degradar herramientas del SDK a 25 y usar el administrador del emulador de Google antiguo interfaz gráfica de usuario. Herramientas del SDK de 25 aún pueden usarse junto con la API de 26, 27 y versiones más recientes y no afectan al desarrollo de nuevas plataformas. Esto le dará una interfaz para la administración del SDK de Android para versiones anteriores de VS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

-   Actualizar a la versión estable más reciente de Visual Studio de 2017 para Mac, como se explica en [actualizar Visual Studio para Mac](https://docs.microsoft.com/en-us/visualstudio/mac/update).

-----

Para obtener más información sobre la compatibilidad con Xamarin Oreos Android, consulte el [notas de la versión 8.0 de Xamarin.Android](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/).



### <a name="install-the-android-sdk"></a>Instalar el SDK de Android

Para crear un proyecto con Xamarin.Android 8.0, primero debe usar el Administrador de SDK de Android de Xamarin para instalar la plataforma SDK para **8.0 Android - Oreos** o una versión posterior. También debe instalar las herramientas de Android SDK 26.0 o una versión posteriores.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie el Administrador de SDK (en Visual Studio, haga clic en **Herramientas > Android > Administrador de Android SDK**).

2. Instalar el **8.0 Android - Oreos** paquetes. Si está utilizando el emulador de Android SDK, no olvide incluir el **x86** imágenes del sistema que necesita:

    [![Seleccionar paquetes 8.0 Android en el Administrador de SDK de Android](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Instalar **herramientas de Android SDK 26.0.2** o una versión posterior, **herramientas de Platform SDK de Android 26.0.0** o una versión posterior, y **Android SDK y herramientas de compilación 26.0.0** (o posterior):

    [![Seleccionar herramientas SDK de Android 26 en el Administrador de SDK de Android](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie el Administrador de SDK (en Visual Studio para Mac, haga clic en **Herramientas > Administrador de SDK**).

2. Instalar el **8.0 Android - Oreos** paquetes de SDK. Si está utilizando el emulador de Android SDK, no olvide incluir el **x86** imágenes del sistema que necesita:

    [![Seleccionar paquetes 8.0 Android en el Administrador de SDK](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Instalar **herramientas de Android SDK 26.0.2** o una versión posterior, **herramientas de Platform SDK de Android 26.0.0** o una versión posterior, y **Android SDK y herramientas de compilación 26.0.0** (o posterior):

    [![Seleccionar herramientas SDK de Android 26 en el Administrador de SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre cómo crear proyectos de Xamarin.Android.

Cuando cree un proyecto Android, debe configurar los valores de versión al destino Android 8.0 o posterior. Por ejemplo, para el destino del proyecto para Android 8.0, debe configurar el nivel de API de Android de destino del proyecto a **Android 8.0 (API de 26)**. Se recomienda establecer el nivel de marco de destino y 26 de API o posterior. Para obtener más información acerca de cómo configurar los niveles de nivel de API de Android, consulte [niveles de API de Android descripción](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Configurar un dispositivo o emulador

Si se intenta iniciar el valor predeterminado basado en GUI de Google AVD Manager después de instalar Android SDK Tools 26.0 o una versión posterior, es posible que obtenga el siguiente cuadro de diálogo, que le indica que se utilice la herramienta de línea de comandos AVD manager **avdmanager** en su lugar :

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cuadro de diálogo de advertencia de administrador del emulador de Android](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Cuadro de diálogo de advertencia de administrador del emulador de Android](oreo-images/mac/03-avd-warning.png)

-----

Este mensaje se muestra porque Google ya no proporciona un standlone administrador AVD de GUI que admite las API 26.0 y versiones posterior. Para Android Oreos 8.0, debe utilizar el administrador del emulador de Android Xamarin o la línea de comandos `avdmanager` herramienta para crear dispositivos virtuales para Oreos Android.

Para usar el Administrador de dispositivos Android para crear y administrar dispositivos virtuales, consulte [administrar dispositivos virtuales con el Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).
Para crear dispositivos virtuales sin el Administrador de dispositivos Android, siga los pasos descritos en la sección siguiente.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Crear dispositivos virtuales utilicen avdmanager

Usar **avdmanager** para crear un nuevo dispositivo virtual, siga estos pasos:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Abra una ventana del símbolo del sistema y establezca `JAVA_HOME` a la ubicación del SDK de Java en el equipo. Para una instalación típica de Xamarin, puede usar el comando siguiente:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Agregar la ubicación del SDK de Android `bin` carpeta para su `PATH`.
    Para una instalación típica de Xamarin, puede usar el comando siguiente:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Cierre la ventana de símbolo del sistema y abrir una nueva ventana de símbolo del sistema. Crear un nuevo dispositivo virtual mediante la [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Por ejemplo, para crear un AVD denominado **AVD-Oreos-8.0** mediante la x86 imagen de sistema de nivel de API 26, use el siguiente comando:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  Cuando se le pida con **¿desea crear un perfil de hardware personalizado [n]** puede escribir **sin** y acepte el perfil de hardware predeterminado. Si dice **Sí**, **avdmanager** le solicitará que con una lista de preguntas para personalizar el perfil de hardware.

Después de **avdmanager** para crear el dispositivo virtual, se incluirá en el menú desplegable de dispositivo:

[![AVD nueva se agrega al menú desplegable de dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Abra un **Terminal** ventana y cambie a la ubicación del directorio de herramientas de SDK de Android en el equipo Mac. Para una instalación típica de Xamarin, puede usar el comando siguiente:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Crear un nuevo dispositivo virtual mediante la [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Por ejemplo, para crear un AVD denominado **AVD-Oreos-8.0** mediante la x86 imagen de sistema de nivel de API 26, use el siguiente comando:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  Cuando se le pida con **¿desea crear un perfil de hardware personalizado [n]** puede escribir **sin** y acepte el perfil de hardware predeterminado. Si dice **Sí**, **avdmanager** le solicitará que con una lista de preguntas para personalizar el perfil de hardware.

Después de usar **avdmanager** para crear el dispositivo virtual, se incluirá en el menú desplegable de dispositivo:

[![AVD nueva se agrega al menú desplegable de dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Para obtener más información acerca de cómo configurar un emulador de Android para probar y depurar, consulte [depuración en el emulador de Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Si está utilizando un dispositivo físico como un Nexus o un píxel, puede actualizar el dispositivo a través de automático a través de las actualizaciones de remotamente o descargar una imagen de sistema y flash directamente el dispositivo. Para obtener más información acerca de cómo actualizar manualmente el dispositivo para Oreos Android, consulte [generador de imágenes para dispositivos de píxel y Nexus](https://developers.google.com/android/images).



## <a name="new-features"></a>Características nuevas

Android Oreos presentan una variedad de nuevas características y funcionalidades, como canales de notificación, distintivos de notificación, las fuentes personalizadas en XML, las fuentes descargables, autorrelleno e imagen en imagen. Las siguientes secciones resaltan estas características y se proporcionan vínculos para ayudarle a empezar a trabajar con ellos en la aplicación.



### <a name="notification-channels"></a>Canales de notificación

*Canales de notificación* son categorías definidas por la aplicación para notificaciones.
Puede crear un canal de notificación para cada tipo de notificación que necesita para enviar y puede crear canales de notificación para reflejar las opciones realizados por los usuarios de la aplicación. La nueva característica de canal de notificación permite dar control exhaustivo de los usuarios diferentes tipos de notificaciones. Por ejemplo, si está implementando una aplicación de mensajería, puede crear canales de notificación independiente para cada grupo de conversación que se crea un usuario.

[Canales de notificación](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explica cómo crear un canal de notificación y usarlo para el registro de notificaciones locales. Para obtener un ejemplo de código reales, consulte el [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) ejemplo; esta aplicación de ejemplo administra dos canales y establece las opciones de notificación adicionales.



### <a name="notification-badges"></a>Distintivos de notificación

Distintivos de notificación son pequeños puntos que aparecen sobre los iconos de aplicación, tal y como se muestra en esta captura de pantalla:

[![Distintivos de notificación de ejemplo en los iconos de aplicación](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Estos puntos indican que hay nuevas notificaciones para uno o varios canales de notificación en la aplicación asociada a ese icono de la aplicación &ndash; estas son las notificaciones que el usuario aún no se descarta o ha actuado en consecuencia. Los usuarios pueden long-presionar en un icono para Eche un vistazo a las notificaciones asociadas con un distintivo de notificación, descartando o actúa en notificaciones en el menú presionándolos una larga que appeaars.

Para obtener más información sobre identificadores de notificación, vea el desarrollador Android [notificación distintivos](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) tema.



### <a name="custom-fonts-in-xml"></a>Fuentes personalizadas en XML

Presentan Android Oreos *fuentes en XML*, lo que hace posible para incorporar las fuentes personalizadas como recursos. OpenType (**.otf**) y TrueType (**.ttf**) se admiten formatos de fuente. Para agregar fuentes como recursos, realice lo siguiente:

1. Crear un **recursos/fuente** carpeta.

2. Copie los archivos de fuente (ejemplo, **.ttf** y **.otf** archivos) a **recursos/fuente**. 

3. Si es necesario, cambie el nombre de cada archivo de fuente para que se adhiere a las convenciones de nomenclatura de archivos Android (es decir, utilice sólo minúsculas *, a-z*, *0-9*y caracteres de subrayado en nombres de archivo). Por ejemplo, el archivo de fuente `Pacifico-Regular.ttf` se puede cambiar en algo similar a `pacifico.ttf`.

4. Aplicar la fuente personalizada mediante el nuevo `android:fontFamily` atributo en el diseño de XML. Por ejemplo, la siguiente `TextView` declaración usa agregado **pacifico.ttf** recurso de fuente:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

También puede crear un archivo XML familia de fuentes que se describe varias fuentes, así como detalles de estilo y el peso. Para obtener más información, vea el desarrollador Android [fuentes en XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) tema.


### <a name="downloadable-fonts"></a>Fuentes descargables

A partir de Oreos Android, las aplicaciones pueden solicitar fuentes desde un proveedor, en lugar de preciso agruparlos en el APK. Las fuentes se descargan desde la red sólo cuando sea necesario. Esta característica reduce el tamaño APK, conservar el uso de datos de memoria y red de telefonía móvil teléfono. También puede utilizar esta característica en versiones de API de Android 14 y versiones posteriores mediante la instalación del paquete de Android 26 de biblioteca de soporte técnico.

Cuando la aplicación necesita una fuente, se crea un `FontsRequest` objeto (así como especificar la fuente que se va a descargar) y, a continuación, páselo a un `FontsContract` método para descargar la fuente. Los pasos siguientes describen el proceso de descarga de fuentes con más detalle:

1.  Crear una instancia de un [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) objeto. 

2.  Subclase y crea una instancia [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Implemente el [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) método, que se utiliza para controlar la finalización de la solicitud de la fuente.

4.  Implemente el [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) método, que se usa para informar a la aplicación de los errores que tienen lugar durante el proceso de solicitud de la fuente.

5.  Llame a la [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) método para recuperar la fuente desde el proveedor de fuente. 

Cuando se llama a la `RequestFonts` método, comprueba primero si la fuente local se almacena en caché (desde una llamada anterior a `RequestFont`). Si no se almacena, llame al proveedor de fuente, recupera la fuente de forma asincrónica y, a continuación, pasa los resultados a la aplicación mediante la invocación de la `OnTypeFaceRetrieved` método.

El [fuentes descargables](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) ejemplo muestra cómo utilizar la característica de las fuentes descargables que se introdujo en Oreos Android. 

Para obtener más información acerca de cómo descargar las fuentes, consulte el desarrollador Android [fuentes descargables](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) tema.



### <a name="autofill"></a>Autorrelleno

El nuevo _autorrelleno_ framework en Oreos Android facilita a los usuarios controlar las tareas repetitivas, como inicio de sesión y creación de cuentas, las transacciones de tarjetas de crédito. Los usuarios dedican menos tiempo a volver a escribir la información (lo que puede provocar errores de entrada). Antes de que la aplicación puede funcionar con el marco de trabajo de autorrelleno, un servicio de autorrelleno debe estar habilitado en la configuración del sistema (los usuarios pueden habilitar o deshabilitar autorrelleno).

El [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) ejemplo muestra el uso de la plataforma de autorrelleno. Incluye implementaciones de cliente actividades con vistas que se deben autofilled y un servicio que puede proporcionar datos de autorrelleno a actividades de cliente.

Para obtener más información acerca de la nueva característica de autorrelleno y cómo optimizar la aplicación para autorrelleno, vea el desarrollador Android [autorrelleno Framework](https://developer.android.com/guide/topics/text/autofill.html) tema.



### <a name="picture-in-picture-pip"></a>Imagen en imagen (PIP)

Oreos Android hace posible que una actividad Iniciar en modo de imagen en imagen (PIP), superponer la pantalla de otra actividad. Esta característica sirve para reproducción de vídeo.

Para especificar que la actividad de la aplicación puede usar el modo PIP, establezca el marcador siguiente en true en el manifiesto de Android:

```xml
android:supportsPictureInPicture
```

Para especificar cómo debe comportarse la actividad cuando se encuentra en modo PIP, use la nueva [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) objeto. `PictureInPictureParams` Representa un conjunto de parámetros que utiliza para inicializar y actualizar una actividad en el modo PIP (por ejemplo, la actividad preferido relación de aspecto). Los siguientes métodos PIP nuevos se agregaron a `Activity` en Oreos Android:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; coloca la actividad en el modo PIP. La actividad se coloca en la esquina de la pantalla y el resto de la pantalla se rellena con la actividad anterior que estaba en la pantalla.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; actualiza valores de configuración de PIP de la actividad (por ejemplo, un cambio en la relación de aspecto).

El [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) ejemplo muestra el uso básico del modo de imagen en imagen (PiP) para dispositivos de mano introducidas en Oreos. El ejemplo reproduce un vídeo que sigue sin interrupciones al hacia atrás y cambiar entre modos de presentación u otras actividades.



### <a name="other-features"></a>Otras características

Android Oreos contiene muchas otras características nuevas, como los límites de fondo Emoji compatibilidad con API de ubicación, de bibliotecas, amplia gama de colores para aplicaciones, nuevos códecs de audio, mejoras de WebView, compatibilidad de navegación de teclado mejorado y una nueva API de AAudio (audio pro) para audio de baja latencia y alto rendimiento, para obtener más información acerca de estas características, vea el desarrollador Android [API y características de Oreos Android](https://developer.android.com/about/versions/oreo/android-8.0.html) tema.



## <a name="behavior-changes"></a>Cambios de comportamiento

Android Oreos incluyen una serie de sistema y los cambios de comportamiento de API que pueden tener un impacto en la funcionalidad de las aplicaciones existentes. A continuación se describen estos cambios.


### <a name="background-execution-limits"></a>Límites de la ejecución de fondo

Para mejorar la experiencia del usuario, Oreos Android impone limitaciones en lo que pueden hacer aplicaciones mientras se ejecuta en segundo plano. Por ejemplo, si el usuario ve un vídeo o un juego, una aplicación que se ejecuta en segundo plano puede poner en peligro el rendimiento de una aplicación intensiva de vídeo que se ejecuta en primer plano. Como resultado, Oreos Android impone las siguientes restricciones en las aplicaciones que no interactúan directamente con el usuario:

1.  **Limitaciones del servicio en segundo plano** &ndash; cuando una aplicación se ejecuta en segundo plano, tiene una ventana de varios minutos en el que todavía se admite para crear y usar los servicios. Al final de esa ventana, Android, se detiene el servicio de fondo de la aplicación y lo trata como _inactivo_.

2.  **Limitaciones de difusión** &ndash; Android 7.0 (API de 25) coloca las limitaciones en las difusiones que una aplicación se registra para recibir. Android Oreos hace que estas limitaciones más estrictas. Por ejemplo, aplicaciones de Android Oreos ya no pueden registrar receptores de difusión para las difusiones implícitas en sus manifiestos.

Para obtener más información acerca de los nuevos límites de la ejecución de segundo plano, consulte el desarrollador Android [límites de la ejecución de fondo](https://developer.android.com/about/versions/oreo/background.html) tema.


### <a name="breaking-changes"></a>Cambios importantes

Aplicaciones de destino Oreos Android o posterior, deben modificar sus aplicaciones para admitir los cambios siguientes, si procede:

- Android Oreos deja de usar la capacidad de establecer la prioridad de las notificaciones individuales. En su lugar, establece un nivel de importancia recomendada cuando se crea un canal de notificación. El nivel de importancia que se asigne a un canal de notificación se aplica a todos los mensajes de notificación, publicar en él.

- Para aplicaciones destinadas a Android Oreos, `PendingIntent.GetService()` no funciona debido a los nuevos límites colocados en servicios iniciados en segundo plano. Si desea usar Oreos Android, debe usar [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) en su lugar.  


## <a name="sample-code"></a>Código de ejemplo

Varios ejemplos de Xamarin.Android están disponibles para mostrarle cómo aprovechar las características de Oreos Android:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) muestra cómo utilizar el nuevo sistema de canales de notificación que se introdujo en Oreos Android. Este ejemplo administra dos canales de notificación: uno con la importancia de forma predeterminada y el otro con importancia alta.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) muestra el uso básico del modo de imagen en imagen (PiP) para dispositivos de mano introducidas en Oreos. El ejemplo reproduce un vídeo que sigue sin interrupciones al hacia atrás y cambiar entre modos de presentación u otras actividades.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) muestra el uso de la plataforma de autorrelleno. Incluye implementaciones de cliente actividades con vistas que se deben autofilled y un servicio que puede proporcionar datos de autorrelleno a actividades de cliente.

-   [Las fuentes descargables](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) proporciona un ejemplo de cómo usar la característica de las fuentes descargables se ha descrito anteriormente.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) muestra el uso de la biblioteca de compatibilidad de EmojiCompat. Puede usar esta biblioteca para evitar que la aplicación de muestra que faltan emoji caracteres como caracteres de "tofu".

-   [Ubicación actualizaciones pendientes intención](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) muestra el uso de la API de ubicación para obtener actualizaciones sobre la ubicación de un dispositivo con un `PendingIntent`.

-   [Servicio de primer plano de las actualizaciones de ubicación](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) se muestra cómo utilizar la API de ubicación para obtener actualizaciones sobre la ubicación de un dispositivo con un servicio de primer plano enlazado e iniciado.


## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Desarrollo de Oreos 8.0 Android con C#**


## <a name="summary"></a>Resumen

En este artículo se introdujo Oreos Android y se explica cómo instalar y configurar las últimas herramientas y paquetes para el desarrollo de Xamarin.Android en Oreos Android. Proporciona información general de las características clave disponibles en Android Oreos, con vínculos a código fuente de ejemplo para varias características nuevas. Incluyen vínculos a documentación de API y temas de desarrolladores de Android que le ayudarán a empezar a trabajar en la creación de aplicaciones para Android Oreos. También se resaltan los cambios de comportamiento Oreos Android más importantes que podrían afectar a las aplicaciones existentes.


## <a name="related-links"></a>Vínculos relacionados

- [Android Oreos 8.0](https://developer.android.com/index.html)
