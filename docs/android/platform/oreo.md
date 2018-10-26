---
title: Características de oreo
description: Cómo empezar a usar Xamarin.Android para desarrollar aplicaciones para la versión más reciente de Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: e4f0138c0177c046c1e967a3d3625604f73327d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103197"
---
# <a name="oreo-features"></a>Características de oreo

_Cómo empezar a usar Xamarin.Android para desarrollar aplicaciones para la versión más reciente de Android._

[Android Oreo 8.0](https://developer.android.com/index.html) es la versión más reciente de Android está disponible en Google. Android Oreo ofrece muchas características nuevas de interés para los desarrolladores de Xamarin.Android. Estas características incluyen los canales de notificación, distintivos de notificación, las fuentes personalizadas en XML, las fuentes descargables, Autorrellenar y picture in picture (PIP). Android Oreo incluye API nuevas para estas nuevas funcionalidades, y estas API están disponibles para las aplicaciones Xamarin.Android cuando se usa en Xamarin.Android 8.0 y posterior.

[![Imagen de hero Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

En este artículo está estructurado para ayudarle a empezar a trabajar en el desarrollo de aplicaciones de Xamarin.Android para Android 8.0 Oreo. Explica cómo instalar las actualizaciones necesarias, configure el SDK y crear un emulador (o dispositivo) para las pruebas. También proporciona un resumen de las nuevas características en Android 8.0 Oreo, con vínculos a aplicaciones de ejemplo que muestran cómo usar las características de Android Oreo en aplicaciones de Xamarin.Android.


## <a name="requirements"></a>Requisitos

Para usar características de Android Oreo en aplicaciones basadas en Xamarin, es necesario lo siguiente:

-   **Visual Studio** &ndash; si usa Windows, se requiere la versión 15.5 o versiones posterior de Visual Studio.  Si usas un equipo Mac, se requiere Visual Studio para Mac versión 7.2.0.

-   **Xamarin.Android** &ndash; Xamarin.Android 8.0 o posterior debe estar instalado y configurado con Visual Studio.

-   **SDK de Android** &ndash; Android SDK 8.0 (API 26) o posterior debe estar instalado a través de Android SDK Manager.



## <a name="getting-started"></a>Introducción

Para comenzar a usar Android Oreo con Xamarin.Android, debe descargar e instalar los paquetes SDK y herramientas más recientes antes de poder crear un proyecto de Android Oreo:

1. Actualice a la versión más reciente de Visual Studio.

2. Instalar el **8.0.0 Android (API 26)** o posterior herramientas y paquetes a través del Administrador de SDK.

3. Cree un nuevo proyecto de Xamarin.Android que tiene como destino Android Oreo (API 26).

4. Configurar un emulador o dispositivo para probar aplicaciones de Android Oreo.

Cada uno de estos pasos se explica en las secciones siguientes:



### <a name="update-visual-studio-and-xamarinandroid"></a>Actualizar Visual Studio y Xamarin.Android

Para agregar compatibilidad de Android Oreo en Visual Studio, haga lo siguiente:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-   Si usa Visual Studio 2017: 

    1. Actualización a Visual Studio 2017 versión 15.7 o posterior (consulte [actualizar Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Use la [SDK Manager](~/android/get-started/installation/android-sdk.md) para instalar el nivel de API 26.0 o versiones posterior.

-   Si usa Visual Studio 2015, se recomienda degradar a SDK Tools a 25 y el uso de la GUI del Administrador de emulador de Google antiguo. Las herramientas del SDK 25 pueden usarse junto con la API 26, 27 y versiones más recientes y no afectan al desarrollo de nuevas plataformas. Esto le dará una interfaz para administrar el SDK de Android para versiones anteriores de VS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

-   Actualizar a la versión estable más reciente de Visual Studio 2017 para Mac como se explica en [actualizar Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Para obtener más información sobre la compatibilidad con Xamarin para Android Oreo, consulte el [Xamarin.Android 8.0 notas](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/).



### <a name="install-the-android-sdk"></a>Instalar el SDK de Android

Para crear un proyecto con Xamarin.Android 8.0, primero debe usar Android SDK Manager de Xamarin para instalar la plataforma SDK para **Android 8.0 - Oreo** o una versión posterior. También debe instalar de Android SDK Tools 26.0 o versiones posteriores.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Iniciar SDK Manager (en Visual Studio, haga clic en **Herramientas > Android > Android SDK Manager**).

2. Instalar el **Android 8.0 - Oreo** paquetes. Si usa el emulador de Android SDK, no olvide incluir el **x86** imágenes del sistema que necesitará:

    [![Seleccionar paquetes de Android 8.0 en Android SDK Manager](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Instalar **Android SDK Tools 26.0.2** o versiones posteriores, **Android SDK Platform-Tools 26.0.0** o versiones posteriores, y **Android SDK Build-Tools 26.0.0** (o posterior):

    [![Selección de Android SDK Tools 26 en Android SDK Manager](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Iniciar SDK Manager (en Visual Studio para Mac, haga clic en **Herramientas > SDK Manager**).

2. Instalar el **Android 8.0 - Oreo** paquetes del SDK. Si usa el emulador de Android SDK, no olvide incluir el **x86** imágenes del sistema que necesitará:

    [![Seleccionar paquetes de Android 8.0 en SDK Manager](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Instalar **Android SDK Tools 26.0.2** o versiones posteriores, **Android SDK Platform-Tools 26.0.0** o versiones posteriores, y **Android SDK Build-Tools 26.0.0** (o posterior):

    [![Selección de Android SDK Tools 26 en el Administrador de SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto de Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre cómo crear proyectos de Xamarin.Android.

Al crear un proyecto de Android, debe configurar la configuración de versión al destino Android 8.0 o posterior. Por ejemplo, para el destino del proyecto para Android 8.0, debe configurar el nivel de API de Android de destino del proyecto a **Android 8.0 (API 26)**. Se recomienda establecer también el nivel de marco de destino a la API 26 o versiones posteriores. Para obtener más información acerca de cómo configurar los niveles de nivel de API de Android, consulte [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Configurar un dispositivo o emulador

Si se intenta iniciar el valor predeterminado basado en GUI de Google AVD Manager después de instalar Android SDK Tools 26.0 de o versiones posteriores, puede aparecer el cuadro de diálogo de error siguiente, que le indica que se va a usar la herramienta de línea de comandos AVD manager **avdmanager** en su lugar :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cuadro de diálogo de advertencia de Android Emulator Manager](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Cuadro de diálogo de advertencia de Android Emulator Manager](oreo-images/mac/03-avd-warning.png)

-----

Se muestra este mensaje porque Google ya no proporciona una independiente administrador AVD de GUI que admite API 26.0 y versiones posterior. Para Android 8.0 Oreo, debe usar el Administrador de emuladores de Android de Xamarin o la línea de comandos `avdmanager` herramienta para crear dispositivos virtuales para Android Oreo.

Para usar el Administrador de dispositivos Android para crear y administrar dispositivos virtuales, consulte [administración de dispositivos Virtual con el Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).
Para crear dispositivos virtuales sin el Administrador de dispositivos Android, siga los pasos descritos en la sección siguiente.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Crear dispositivos virtuales mediante avdmanager

Para usar **avdmanager** para crear un nuevo dispositivo virtual, siga estos pasos:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Abra una ventana del símbolo del sistema y establezca `JAVA_HOME` a la ubicación del SDK de Java en el equipo. Para una instalación típica de Xamarin, puede usar el comando siguiente:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Agregue la ubicación de Android SDK `bin` carpeta para su `PATH`.
    Para una instalación típica de Xamarin, puede usar el comando siguiente:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Cierre la ventana de símbolo del sistema y abra una nueva ventana de símbolo del sistema. Crear un nuevo dispositivo virtual mediante la [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Por ejemplo, para crear un AVD denominado **AVD-Oreo-8.0** utilizando la x86 imagen de sistema de nivel de API 26, use el siguiente comando:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  Cuando se le pida con **desea crear un perfil de hardware personalizado [n]** puede introducir **ningún** y acepte el perfil de hardware predeterminado. Si dice **Sí**, **avdmanager** le pedirá que con una lista de preguntas para personalizar el perfil de hardware.

Después de **avdmanager** para crear el dispositivo virtual, se incluirá en el menú desplegable del dispositivo:

[![Ha agregado un AVD nuevo al menú desplegable de dispositivos](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Abra un **Terminal** ventana y cambie a la ubicación del directorio de herramientas de Android SDK en el equipo Mac. Para una instalación típica de Xamarin, puede usar el comando siguiente:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Crear un nuevo dispositivo virtual mediante la [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Por ejemplo, para crear un AVD denominado **AVD-Oreo-8.0** utilizando la x86 imagen de sistema de nivel de API 26, use el siguiente comando:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  Cuando se le pida con **desea crear un perfil de hardware personalizado [n]** puede introducir **ningún** y acepte el perfil de hardware predeterminado. Si dice **Sí**, **avdmanager** le pedirá que con una lista de preguntas para personalizar el perfil de hardware.

Después de usar **avdmanager** para crear el dispositivo virtual, se incluirá en el menú desplegable del dispositivo:

[![Ha agregado un AVD nuevo al menú desplegable de dispositivos](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Para obtener más información acerca de cómo configurar un emulador de Android para probar y depurar, consulte [depuración en el emulador de Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Si utiliza un dispositivo físico, como un Nexus o un píxel, puede actualizar el dispositivo a través de automático a través de las actualizaciones de remotamente (OTA) o descargar una imagen de sistema y el dispositivo flash directamente. Para obtener más información acerca de cómo actualizar manualmente el dispositivo para Android Oreo, consulte [imágenes de fábrica para dispositivos de píxel y Nexus](https://developers.google.com/android/images).



## <a name="new-features"></a>Características nuevas

Android Oreo presenta una variedad de nuevas características y funcionalidades, tales como canales de notificación, distintivos de notificación, fuentes personalizadas en XML, las fuentes descargables, Autorrellenar e imagen en imagen. Las siguientes secciones resaltan estas características y proporcionan vínculos para ayudarle a empezar a trabajar con ellos en la aplicación.



### <a name="notification-channels"></a>Canales de notificación

*Canales de notificación* son las categorías definidas por aplicación para notificaciones.
Puede crear un canal de notificación para cada tipo de notificación que necesita para enviar y puede crear canales de notificación para reflejar las elecciones realizadas por usuarios de su aplicación. La nueva característica de los canales de notificación hace posible para que pueda ofrecer un mayor control de los usuarios sobre diferentes tipos de notificaciones. Por ejemplo, si está implementando una aplicación de mensajería, puede crear los canales de notificación por separado para cada grupo de conversación que se crea un usuario.

[Canales de notificación](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) se explica cómo crear un canal de notificación y usarlo para el registro de notificaciones locales. Para obtener un ejemplo de código real, consulte el [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) ejemplo; esta aplicación de ejemplo administra dos canales y establece las opciones de notificación adicional.



### <a name="notification-badges"></a>Distintivos de notificación

Distintivos de notificación son pequeños puntos que aparecen sobre los iconos de aplicación, como se muestra en esta captura de pantalla:

[![Notificaciones de la notificación de ejemplo en iconos de aplicación](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Estos puntos indican que hay nuevas notificaciones para uno o varios canales de notificación en la aplicación asociada a ese icono aplicación &ndash; son notificaciones que el usuario aún no se han descartado o actúa sobre ella. Los usuarios pueden long-presione en un icono para Eche un vistazo a las notificaciones asociadas con un distintivo de notificaciones, descartando o actuar sobre las notificaciones en el menú prolongada presione ese appeaars.

Para obtener más información acerca de las notificaciones de notificación, consulte el desarrollador Android [notificación distintivos](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) tema.



### <a name="custom-fonts-in-xml"></a>Fuentes personalizadas en XML

Android Oreo presenta *fuentes en XML*, lo que hace posible para incorporar las fuentes personalizadas como recursos. OpenType (**.otf**) y TrueType (**.ttf**) se admiten los formatos de fuente. Para agregar fuentes como recursos, realice lo siguiente:

1. Crear un **recursos/fuente** carpeta.

2. Copie los archivos de fuente (ejemplo, **.ttf** y **.otf** archivos) a **recursos/fuente**. 

3. Si es necesario, cambie el nombre de cada archivo de fuente para que se adhiera al archivo Android convenciones de nomenclatura (es decir, utilice sólo minúsculas *z*, *0-9*y caracteres de subrayado en nombres de archivo). Por ejemplo, el archivo de fuentes `Pacifico-Regular.ttf` podría cambiarse en algo similar a `pacifico.ttf`.

4. Aplicar la fuente personalizada con el nuevo `android:fontFamily` atributo en su diseño XML. Por ejemplo, la siguiente `TextView` declaración utiliza el agregado **pacifico.ttf** recurso de fuente:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

También puede crear un archivo XML familia de fuentes que se describe varias fuentes, así como detalles de estilo y grosor. Para obtener más información, vea el desarrollador de Android [fuentes en XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) tema.


### <a name="downloadable-fonts"></a>Fuentes descargables

A partir de Android Oreo, las aplicaciones pueden solicitar las fuentes de un proveedor, en lugar de empaquetado de ellos en el APK. Las fuentes se descargan desde la red según sea necesario. Esta característica reduce el tamaño APK, conservar el uso de datos de memoria y red de telefonía móvil teléfono. También puede usar esta característica en las versiones de API de Android 14 y versiones posteriores mediante la instalación del paquete de Android 26 de biblioteca de soporte técnico.

Cuando la aplicación necesita una fuente, se crea un `FontsRequest` objeto (que especifica la fuente para descargar) y, a continuación, páselo a un `FontsContract` método para descargar la fuente. Los pasos siguientes describen el proceso de descarga de la fuente con más detalle:

1.  Crear una instancia de un [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) objeto. 

2.  Subclase y crear instancias de [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Implemente el [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) método, que se usa para controlar la finalización de la solicitud de la fuente.

4.  Implemente el [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) método, que se usa para informar a la aplicación de los errores que tienen lugar durante el proceso de solicitud de la fuente.

5.  Llame a la [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) método para recuperar la fuente desde el proveedor de fuente. 

Cuando se llama a la `RequestFonts` método, comprueba primero si la fuente local se almacena en caché (de una llamada anterior a `RequestFont`). Si no se almacena, llama al proveedor de fuente, recupera la fuente de forma asincrónica y, a continuación, pasa los resultados a su aplicación mediante la invocación de su `OnTypeFaceRetrieved` método.

El [fuentes descargables](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) ejemplo muestra cómo usar la característica de las fuentes descargables introducida en Android Oreo. 

Para obtener más información acerca de cómo descargar las fuentes, consulte el desarrollador Android [fuentes descargables](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) tema.



### <a name="autofill"></a>Autorrellenar

El nuevo _Autorrellenar_ framework en Android Oreo resulta más fácil para los usuarios controlar tareas repetitivas como inicio de sesión, la creación de cuenta y las transacciones de tarjeta de crédito. Los usuarios dedican menos tiempo en volver a escribir la información (que puede dar lugar a errores de entrada). Antes de la aplicación puede trabajar con el marco de trabajo de relleno automático, un servicio de relleno automático debe estar habilitado en la configuración del sistema (los usuarios pueden habilitar o deshabilitar el relleno automático).

El [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) ejemplo muestra el uso del marco de relleno automático. Incluye implementaciones de cliente de las actividades con vistas que deben ser autofilled y un servicio que puede proporcionar datos de relleno automático para las actividades de cliente.

Para obtener más información acerca de la nueva característica de relleno automático y cómo optimizar la aplicación para el relleno automático, consulte el desarrollador Android [Autorrellenar Framework](https://developer.android.com/guide/topics/text/autofill.html) tema.



### <a name="picture-in-picture-pip"></a>Imagen en imagen (PIP)

Android Oreo hace posible para una actividad Iniciar en modo de imagen en imagen (PIP), superposición de la pantalla de otra actividad. Esta característica está pensada para la reproducción de vídeo.

Para especificar que la actividad de la aplicación puede utilizar el modo PIP, establezca la marca siguiente en true en el manifiesto de Android:

```xml
android:supportsPictureInPicture
```

Para especificar cómo debe comportarse la actividad cuando se encuentra en modo PIP, use la nueva [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) objeto. `PictureInPictureParams` Representa un conjunto de parámetros que usa para inicializar y actualizar una actividad en el modo de PIP (por ejemplo, la actividad preferido relación de aspecto). Se agregaron los siguientes métodos nuevos de PIP a `Activity` en Android Oreo:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; coloca la actividad en el modo PIP. La actividad se coloca en la esquina de la pantalla y el resto de la pantalla se rellena con la actividad anterior que estaba en la pantalla.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; actualiza valores de configuración de la actividad PIP (por ejemplo, un cambio en la relación de aspecto).

El [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) ejemplo muestra el uso básico del modo de imagen en imagen (PiP) para dispositivos de mano introducidas en Oreo. El ejemplo reproduce un vídeo que continúa sin interrupciones mientras se cambia y hacia atrás entre los modos de presentación u otras actividades.



### <a name="other-features"></a>Otras características

Android Oreo contiene muchas otras nuevas características, como los límites de Emoji soporte técnico de biblioteca, API de ubicación, en segundo plano, la amplia gama de colores para las aplicaciones, nuevos códecs de audio, mejoras de WebView, compatibilidad con la navegación mejorada mediante el teclado y una nueva API AAudio (audio pro) para audio de baja latencia y alto rendimiento, para obtener más información acerca de estas características, vea el desarrollador Android [API y características de Android Oreo](https://developer.android.com/about/versions/oreo/android-8.0.html) tema.



## <a name="behavior-changes"></a>Cambios de comportamiento

Android Oreo incluye una variedad de sistema y los cambios de comportamiento de API que pueden tener un impacto en la funcionalidad de las aplicaciones existentes. A continuación se describen estos cambios.


### <a name="background-execution-limits"></a>Límites de ejecución en segundo plano

Para mejorar la experiencia del usuario, Android Oreo impone limitaciones en las aplicaciones que pueden hacerlo mientras se ejecuta en segundo plano. Por ejemplo, si el usuario está viendo un vídeo o un juego, una aplicación que se ejecuta en segundo plano podría comprometer el rendimiento de una aplicación intensiva de vídeo que se ejecutan en primer plano. Como resultado, Android Oreo coloca las siguientes restricciones en las aplicaciones que no interactúan directamente con el usuario:

1.  **Limitaciones del servicio en segundo plano** &ndash; cuando una aplicación se está ejecutando en segundo plano, tiene una ventana de varios minutos en el que todavía se permite crear y usar los servicios. Al final de esa ventana, Android, se detiene el servicio de la aplicación en segundo plano y lo trata como _inactivo_.

2.  **Limitaciones de difusión** &ndash; Android 7.0 (API 25) se colocan las limitaciones en las difusiones que una aplicación se registra para recibir. Android Oreo realiza estas limitaciones más estrictas. Por ejemplo, las aplicaciones de Android Oreo ya no pueden registrar los receptores de difusión para las difusiones implícitas en sus manifiestos.

Para obtener más información acerca de los nuevos límites de ejecución en segundo plano, vea el desarrollador de Android [límites de ejecución en segundo plano](https://developer.android.com/about/versions/oreo/background.html) tema.


### <a name="breaking-changes"></a>Cambios importantes

Aplicaciones que tener como destino Android Oreo o versiones posteriores, deben modificar sus aplicaciones para admitir los siguientes cambios, cuando corresponda:

- Android Oreo admite la capacidad de establecer la prioridad de las notificaciones individuales. En su lugar, establece un nivel de importancia recomendada al crear un canal de notificación. El nivel de importancia que se asigna a un canal de notificación se aplica a todos los mensajes de notificación que publique en él.

- Para aplicaciones destinadas a Android Oreo `PendingIntent.GetService()` no funciona debido a los nuevos límites colocados en los servicios que se inician en segundo plano. Si desea usar Android Oreo, debe usar [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) en su lugar.  


## <a name="sample-code"></a>Código de ejemplo

Existen varios ejemplos de Xamarin.Android para mostrarle cómo aprovechar las características de Android Oreo:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) muestra cómo usar el nuevo sistema de canales de notificación introducido en Android Oreo. En este ejemplo administra dos canales de notificación: uno con la importancia de forma predeterminada y el otro con importancia alta.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) muestra el uso básico del modo de imagen en imagen (PiP) para dispositivos de mano introducidas en Oreo. El ejemplo reproduce un vídeo que continúa sin interrupciones mientras se cambia y hacia atrás entre los modos de presentación u otras actividades.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) muestra el uso del marco de relleno automático. Incluye implementaciones de cliente de las actividades con vistas que deben ser autofilled y un servicio que puede proporcionar datos de relleno automático para las actividades de cliente.

-   [Las fuentes descargables](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) proporciona un ejemplo de cómo usar la característica de fuentes descargables que se ha descrito anteriormente.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) muestra el uso de la biblioteca de compatibilidad de EmojiCompat. Puede usar esta biblioteca para impedir que la aplicación que muestra que faltan emoji caracteres como caracteres de "tofu".

-   [Las actualizaciones de ubicación Pendingintent](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) se muestra el uso de la API de ubicación para obtener actualizaciones sobre la ubicación de un dispositivo mediante un `PendingIntent`.

-   [Servicio de primer plano de las actualizaciones de ubicación](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) muestra cómo usar la API de ubicación para obtener actualizaciones sobre la ubicación de un dispositivo mediante un servicio dependiente y haberse iniciado de primer plano.


## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Desarrollo de Oreo 8.0 Android con C#**


## <a name="summary"></a>Resumen

En este artículo introdujo Android Oreo y se explica cómo instalar y configurar las últimas herramientas y paquetes para el desarrollo de Xamarin.Android en Android Oreo. Proporciona información general sobre las características claves disponibles en Android Oreo, con vínculos a código fuente de ejemplo para varias características nuevas. Incluyen vínculos a documentación de API y temas para desarrolladores de Android que le ayudarán a comenzar a crear aplicaciones para Android Oreo. También se resaltan los cambios de comportamiento de Android Oreo más importantes que pueden afectar a las aplicaciones existentes.


## <a name="related-links"></a>Vínculos relacionados

- [Android Oreo 8.0](https://developer.android.com/index.html)
