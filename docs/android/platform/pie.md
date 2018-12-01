---
title: Android 9 Pie
description: Cómo empezar a desarrollar aplicaciones para Android circular 9 con Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: cd1c374fa68420e1923ef4dee0bb37a4665f3535
ms.sourcegitcommit: 215cad17324ba3fbc23487ce66cd4e1cc74eb879
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710028"
---
# <a name="android-pie-features"></a>Características de Android circular

_Cómo empezar a desarrollar aplicaciones para Android circular 9 con Xamarin.Android._

[Android circular 9](https://developer.android.com/about/versions/pie/) ahora está disponible en Google. Se están realizando una serie de nuevas características y las API disponible en esta versión, y muchos de ellos son necesarios para aprovechar las nuevas capacidades de hardware en los dispositivos Android más recientes.

![Imagen de hero circular Android](pie-images/01-android-p-logo.png)

En este artículo está estructurado para ayudarle a empezar a trabajar en el desarrollo de aplicaciones de Xamarin.Android para Android circular. Explica cómo instalar las actualizaciones necesarias, configure el SDK y preparar un emulador o dispositivo para las pruebas. También proporciona un resumen de las nuevas características de Android circular y proporciona código fuente de ejemplo que ilustra cómo usar algunas de las características clave de gráfico circular de Android.

Xamarin.Android 9.0 proporciona compatibilidad con Android circular. Para obtener más información sobre la compatibilidad con Xamarin.Android circular Android, consulte el [Android P Developer Preview 3](https://developer.xamarin.com/releases/android/xamarin.android_9/xamarin.android_9.0/#android-p-dp1) notas de la versión.

## <a name="requirements"></a>Requisitos

En la lista siguiente es necesaria para usar las características de Android circular en las aplicaciones basadas en Xamarin:

-   **Visual Studio** &ndash; si usa Windows, actualice a Visual Studio 2017 versión 15,8 o posterior. Si usas un equipo Mac, actualice a Visual Studio 2017 para Mac versión 7.6 o posterior.

-   **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 o posterior debe estar instalado con Visual Studio (Xamarin.Android se instala automáticamente como parte de la **desarrollo móvil con .NET** carga de trabajo).

-   **Kit para desarrolladores de Java** &ndash; el desarrollo de Xamarin Android 9.0 requiere [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (o bien puede probar la versión preliminar de la distribución de Microsoft de la [OpenJDK](~/android/get-started/installation/openjdk.md)). JDK8 se instala automáticamente como parte de la **desarrollo móvil con .NET** carga de trabajo.

-   **SDK de Android** &ndash; API del SDK de Android 28 o posterior debe instalarse a través de Android SDK Manager.

## <a name="getting-started"></a>Introducción

Para empezar a desarrollar aplicaciones de Android circular con Xamarin.Android, debe descargar e instalar los paquetes SDK y herramientas más recientes antes de poder crear su primer proyecto de Android circular:

1. Actualizar a [Visual Studio 2017 versión 15,8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o una versión posterior. Si utiliza Visual Studio para Mac, actualice a [Visual Studio 2017 para Mac versión 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o una versión posterior.

2. Instalar **circular Android (API de 28)** herramientas mediante el Administrador de SDK y paquetes.

3. Cree un nuevo proyecto de Xamarin.Android que tiene como destino **9.0 Android**.

4. Configurar un emulador o dispositivo para probar aplicaciones Android circular.

Cada uno de estos pasos se explica en las secciones siguientes:


### <a name="update-visual-studio"></a>Actualizar Visual Studio

Para agregar compatibilidad con Android circular a Visual Studio, actualice a Visual Studio 2017 versión 15,8 o posterior (para obtener instrucciones, consulte [actualizar Visual Studio 2017 a la versión más reciente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

Para agregar compatibilidad de Android circular en Visual Studio para Mac, actualizar a Visual Studio 2017 para Mac 7.6 o posterior (para obtener instrucciones, consulte [configuración e instalación de Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)).


### <a name="install-the-android-sdk"></a>Instalar el SDK de Android

Para crear un proyecto con Xamarin.Android 9.0, primero debe usar Android SDK Manager para instalar la plataforma SDK para **circular Android (API nivel 28)** o una versión posterior.

1. Inicie el Administrador de SDK. En Visual Studio, haga clic en **Herramientas > Android > Android SDK Manager**. En Visual Studio para Mac, haga clic en **Herramientas > SDK Manager**.

2. En la esquina inferior derecha, haga clic en el icono de engranaje y seleccione **repositorio > Google (no compatible)**:

    [![Configurar el repositorio de Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Instalar el **circular Android** paquetes del SDK, que se enumeran como **28 de plataforma de Android SDK** en el **plataformas** ficha (para obtener más información sobre cómo usar SDK Manager, consulte [ Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)):

    [![Instalación de paquetes de Android circular](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Si usa un emulador, crear un dispositivo virtual que admita **28 de nivel de API**. Para obtener más información acerca de cómo crear dispositivos virtuales, consulte [administración de dispositivos Virtual con el Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).



### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto de Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre cómo crear proyectos de Xamarin.Android.

Al crear un proyecto de Android, debe configurar la configuración de la versión 9.0 o posterior de Android de destino. Por ejemplo, para el destino del proyecto para Android circular, debe configurar el nivel de API de Android de destino del proyecto a **9.0 Android** (28 de API). Se recomienda que establezca también la plataforma de destino nivel y el 28 de API o una versión posterior. Para obtener más información acerca de cómo configurar los niveles de API de Android, consulte [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-a-device-or-emulator"></a>Configurar un dispositivo o emulador

Si utiliza un dispositivo físico, como un Nexus o un píxel, puede actualizar el dispositivo a Android circular siguiendo las instrucciones de [imágenes de fábrica para dispositivos de píxel y Nexus](https://developers.google.com/android/images).

Si usa un emulador, crear un dispositivo virtual de nivel de API 28 y seleccione una imagen x86. Para obtener información sobre cómo usar el Administrador de dispositivos Android para crear y administrar dispositivos virtuales, consulte [administración de dispositivos Virtual con el Administrador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).
Para obtener información sobre cómo usar el emulador de Android para probar y depurar, consulte [depuración en el emulador de Android](~/android/deploy-test/debugging/debug-on-emulator.md).



## <a name="new-features"></a>Características nuevas

Circular Android presenta una serie de nuevas características. Algunas de estas nuevas características están diseñados para aprovechar las nuevas capacidades de hardware que ofrecen los dispositivos Android más recientes, mientras que otros están diseñados para mejorar aún más la experiencia de usuario de Android:

-   **Soporte de recorte de pantalla** &ndash; proporciona API para buscar la ubicación y la forma de la _recorte_ en la parte superior de la pantalla en dispositivos Android más reciente.

-   **Mejoras de notificación** &ndash; mensajes de notificación ahora pueden mostrar imágenes y un nuevo `Person` clase se usa para simplificar los participantes de la conversación.

-   **Posicionamiento interior** &ndash; compatibilidad con la plataforma para el protocolo de tiempo de ida y vuelta de Wi-Fi, lo que permite a las aplicaciones que usan los dispositivos de red Wi-Fi para la navegación en la configuración de interiores.

-   **Compatibilidad con múltiples cámara** &ndash; ofrece la funcionalidad en secuencias acceso simultáneamente desde varios cámaras físicas (como cámaras dual-delantera y trasera de doble).


Las siguientes secciones resaltan estas características y proporcionan ejemplos breves de código que le ayudarán a empezar a trabajar con ellos en la aplicación.

### <a name="display-cutout-support"></a>Soporte de recorte de pantalla

Muchos dispositivos Android más reciente con pantallas de lado a lado tienen un *Mostrar recorte* (o "ranura") en la parte superior de la presentación de la cámara y altavoces.
Captura de pantalla siguiente proporciona un ejemplo de emulador de recortes:

[![Emulador de Android simulando un recorte](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Para administrar cómo la ventana de la aplicación muestra su contenido en los dispositivos con un recorte de pantalla, ha agregado un nuevo gráfico circular Android [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) atributo de diseño de ventana. Este atributo puede establecerse en uno de los siguientes valores:

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; la ventana no debe solaparse con el área de recorte.

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; la ventana se puede ampliar en el área de recorte, pero solo en los bordes cortos de la pantalla. 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; la ventana se puede ampliar en el área de recorte si el recorte está dentro de una barra del sistema.

Por ejemplo, para evitar que la ventana de la aplicación que se superponen con el área de recorte, establezca el modo de recorte de diseño en *nunca*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Los ejemplos siguientes proporcionan ejemplos de estos modos de recorte. Es la primera captura de pantalla de la izquierda de la aplicación en modo no-pantalla completa. En la captura de pantalla del centro, la aplicación deja de pantalla completa con `LayoutInDisplayCutoutMode` establecido en `LayoutInDisplayCutoutModeShortEdges`. Tenga en cuenta que extiende el fondo blanco de la aplicación en el área de recorte de pantalla:

[![Modos de recorte de visualización de ejemplo en el emulador](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

En la captura de pantalla final (por encima de la derecha), `LayoutInDisplayCutoutMode` está establecido en `LayoutInDisplayCutoutModeShortNever` antes de pasar a pantalla completa.
Tenga en cuenta que el fondo blanco de la aplicación no se permite extender en el área de recorte de pantalla.

Si necesita información más detallada sobre el área de recorte en el dispositivo, puede usar el nuevo [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) clase. `DisplayCutout` representa el área de la pantalla que no se puede usar para mostrar el contenido. Puede usar esta información para recuperar la ubicación y la forma del recorte para que la aplicación no intenta mostrar el contenido de esta área que no es funcional.

Para obtener más información acerca de las nuevas características de recorte de P de Android, consulte [soporte de recorte de pantalla](https://developer.android.com/about/versions/pie/android-9.0#cutout).



### <a name="notifications-enhancements"></a>Mejoras de las notificaciones

Android circular presenta las siguientes mejoras para mejorar la experiencia de mensajería:

-   Canales de notificación (introducida en [Android Oreo](~/android/platform/oreo.md)) ahora es compatible con el bloqueo de grupos de canal.

-   El sistema de notificación tiene tres nuevas-no molestar categorías (dando prioridad a las alarmas, sonidos del sistema y los orígenes multimedia). Además, hay siete nuevos-no molestar modos que pueden usarse para suprimir las interrupciones visuales (por ejemplo, distintivos, las luces de notificación, las apariencias de barra de estado y el inicio de las actividades de pantalla completa).

-   Un nuevo [persona](https://developer.android.com/reference/android/app/Person.html) ha agregado la clase para representar el remitente de un mensaje. Uso de esta clase ayuda a optimizar el procesamiento de cada notificación mediante la identificación de las personas implicadas en una conversación (incluidos sus identificadores URI y avatares).

-   Las notificaciones ahora pueden mostrar imágenes. 

El ejemplo siguiente muestra cómo usar las nuevas API para generar una notificación que contiene una imagen. En las capturas de pantalla siguiente, una notificación de texto se registra y va seguida de una notificación con una imagen incrustada. Cuando se expanden las notificaciones (tal y como se muestra en la parte derecha), se muestra el texto de la primera notificación y la imagen incrustada en se amplía la segunda notificación:

[![Notificación de ejemplo con la imagen](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

El ejemplo siguiente muestra cómo incluir una imagen en una notificación de Android circular y muestra el uso de la nueva `Person` clase:

1. Crear un `Person` objeto que representa al remitente. Por ejemplo, se incluyen el nombre del remitente y el icono en `fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Crear un `Notification.MessagingStyle.Message` que contiene la imagen para enviar, pasando la imagen a la nueva [Notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) método.
   Por ejemplo:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Agregar el mensaje a un `Notification.MessagingStyle` objeto. Por ejemplo:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Conecte este estilo en el generador de notificación. Por ejemplo:

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. La notificación de publicación. Por ejemplo:

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Para obtener más información sobre la creación de notificaciones, consulte [notificaciones locales](~/android/app-fundamentals/notifications/local-notifications.md).


### <a name="indoor-positioning"></a>Posicionamiento interiores

Circular Android proporciona compatibilidad con IEEE 802.11mc (también conocido como _Wi-Fi ida y vuelta tiempo_ o _WiFi RTT_), lo que hace posible para las aplicaciones detectar la distancia a uno o más acceso Wi-Fi señala. Con esta información, es posible que la aplicación para aprovechar las ventajas de *posicionamiento interiores* con una precisión de uno o dos medidores. En dispositivos Android que proporcionan compatibilidad de hardware para IEEE 801.11mc, la aplicación puede ofrecer características de navegación como control basado en ubicación de instrucciones de giro por a través de una tienda o electrodomésticos inteligentes:

[![Ejemplo de navegación interior mediante WiFi RTT](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

El nuevo [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) clase y varias clases de aplicación auxiliar que proporciona los medios para medir la distancia a los dispositivos de Wi-Fi. Para obtener más información acerca de las API de posicionamiento interiores introducidas en Android P, consulte [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).


### <a name="multi-camera-support"></a>Compatibilidad con múltiples cámara

Muchos dispositivos Android más recientes tienen doble delantera o dual back cámaras que son útiles para funciones como visión estéreo, efectos visuales mejorados y capacidad de ampliación mejorada. P Android presenta una nueva [multicámara](https://developer.android.com/about/versions/pie/android-9.0#camera) API que hace posible que la aplicación para utilizar un *cámara lógico* (o *lógico multicámara*) que está respaldado por dos o más cámaras físicas.
Para determinar si el dispositivo es compatible con una cámara múltiple lógico, puede mirar las capacidades de cada cámara del dispositivo para ver si admite [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android circular también incluye un nuevo [Configuracióndesesión](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) clase que puede usarse para ayudar a reducir los retrasos durante la captura inicial y elimina la necesidad de iniciar e iniciar la secuencia de la cámara.

Para obtener más información acerca de la cámara múltiples, admite en Android P, vea [actualizaciones de soporte técnico y cámara multicámara](https://developer.android.com/about/versions/pie/android-9.0#camera).


### <a name="other-features"></a>Otras características

Además, circular Android admite varias características nuevas:

-   El nuevo [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) (clase), que puede utilizarse para dibujar y mostrar las imágenes animadas.

-   Un nuevo [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) clase que reemplaza `BitmapFactory`. `ImageDecoder` puede utilizarse para descodificar una `AnimatedImageDrawable`.

-   Compatibilidad con vídeo HDR (alto rango de dinámico) y HEIF (alta eficacia Image File Format) las imágenes.

-   El [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) se ha mejorado para controlar trabajos relacionados con la red de manera más inteligente. El nuevo [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) método de la [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) clase devuelve la red mejor para realizar las solicitudes de red para un trabajo determinado.

Para obtener más información acerca de las características más recientes de Android circular, consulte [API y características 9 Android](https://developer.android.com/about/versions/pie/android-9.0).


## <a name="behavior-changes"></a>Cambios de comportamiento

Cuando la versión de Android de destino se establece en el nivel de API 28, hay varios cambios de plataforma que pueden afectar al comportamiento de la aplicación incluso si no va a implementar las nuevas características que se ha descrito anteriormente. En la lista siguiente es un breve resumen de estos cambios:

-  Las aplicaciones ahora deben solicitar permiso de primer plano antes de usar servicios de primer plano.

-  Si la aplicación tiene más de un proceso, no puede compartir un único [WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) directorio de datos entre procesos.

-  Ya no se permite el acceso directo a directorio de datos de la otra aplicación mediante la ruta de acceso.

Para obtener más información sobre los cambios de comportamiento en aplicaciones destinadas a Android P, consulte [los cambios de comportamiento](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).


## <a name="sample-code"></a>Código de ejemplo

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) es una aplicación de ejemplo de Xamarin.Android para Android gráfico circular que muestra cómo establecer los modos de presentación recorte, cómo usar el nuevo `Person` clase y cómo enviar una notificación que incluye una imagen.


## <a name="summary"></a>Resumen

En este artículo introdujo Android circular y se explica cómo instalar y configurar las últimas herramientas y paquetes para el desarrollo de Xamarin.Android con Android circular. Proporciona información general sobre las características claves disponibles en Android circular, con el ejemplo de código fuente para varias de estas características.
Incluyen vínculos a documentación de API y temas para desarrolladores de Android que le ayudarán a comenzar a crear aplicaciones para Android circular. También se resaltan los cambios de comportamiento circular Android más importantes que pueden afectar a las aplicaciones existentes.


## <a name="related-links"></a>Vínculos relacionados

- [Android circular 9](https://developer.android.com/about/versions/pie/)
