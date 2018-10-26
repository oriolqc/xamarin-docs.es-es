---
title: Características de nougat
description: Cómo empezar a usar Xamarin.Android para desarrollar aplicaciones para Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: bf2842679aded21c9677d9cef31910f273518acd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119181"
---
# <a name="nougat-features"></a>Características de nougat

_Cómo empezar a usar Xamarin.Android para desarrollar aplicaciones para Android Nougat._

Este artículo se proporciona un resumen de las características introducidas en Android Nougat, se explica cómo preparar Xamarin.Android para el desarrollo de Android Nougat y proporciona vínculos a aplicaciones de ejemplo que muestran cómo usar las características de Android Nougat en Aplicaciones de Xamarin.Android.


## <a name="overview"></a>Información general

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) es el seguimiento de Google para Android Marshmallow 6.0. Xamarin.Android proporciona compatibilidad para **Android 7.x enlaces** en Xamarin Android 7.0 y versiones posteriores. Android Nougat agrega muchas nuevas API para las características de Nougat que se describe a continuación; Estas API están disponibles para aplicaciones Xamarin.Android cuando se usa en Xamarin.Android 7.0.

[![Imágenes de imagen prominente de tabletas y teléfonos ejecutan Android Nougat Android](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Para obtener más información acerca de las API de Android 7.x, consulte [Android 7.1 para desarrolladores](http://developer.android.com/preview/api-overview.html).
Para obtener una lista de problemas conocidos de Xamarin.Android 7.0, vea el [notas de la versión](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/).

Android Nougat proporciona muchas características nuevas de interés para los desarrolladores de Xamarin.Android. Entre ellas se incluyen:

-   **Compatibilidad con las múltiples ventana** &ndash; esta mejora permite a los usuarios abrir dos aplicaciones en la pantalla a la vez.

-   **Mejoras de notificación** &ndash; el sistema de notificaciones rediseñada en Android Nougat incluye un *respuesta directa* características que permiten a los usuarios responder rápidamente a los mensajes de texto directamente desde la notificación INTERFAZ DE USUARIO. También, si la aplicación crea notificaciones para recibir mensajes, el nuevo *incluye notificaciones* característica puede agrupar las notificaciones como un único grupo cuando se recibe más de un mensaje.

-   **Protector de datos** &ndash; esta característica es un nuevo servicio de sistema que ayuda a reducir el uso de datos móviles, aplicaciones; proporciona a los usuarios control sobre cómo las aplicaciones utilizarán datos móviles.

Además, Android Nougat aporta muchas otras mejoras de interés para los desarrolladores de aplicaciones, como una nueva característica de configuración de seguridad de red, Doze sobre la marcha, atestación, nuevas API configuración rápida, compatibilidad con varias configuraciones regionales, ICU4J API, WebView mejoras, claves acceso a características del lenguaje Java 8, acceso al directorio con ámbito, una API personalizada de puntero, compatibilidad con la plataforma VR, archivos virtuales y las optimizaciones de procesamiento en segundo plano.

En este artículo se explica cómo empezar a crear aplicaciones con Android Nougat probar las nuevas características y planear el trabajo de migración o una característica para tener como destino la nueva plataforma de Android Nougat.


## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android Nougat en aplicaciones basadas en Xamarin, se requiere lo siguiente:

-   **Visual Studio o Visual Studio para Mac** &ndash; si está usando Visual Studio, versión 4.2.0.628 o posterior de Visual Studio Tools para Xamarin es necesaria. Si utiliza Visual Studio para Mac, versión 6.1.0 o una versión posterior de Visual Studio para Mac es necesario.

-   **Xamarin.Android** &ndash; Xamarin.Android 7.0 o posterior debe estar instalado y configurado con Visual Studio o Visual Studio para Mac.

-   **SDK de Android** -Android 7.0 SDK (API 24) o posterior debe estar instalado a través de Android SDK Manager.

-   **Kit para desarrolladores de Java** &ndash; el desarrollo de Xamarin Android 7.0 requiere [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si está desarrollando para el nivel de API 24 o superior (JDK 8 también admite los niveles de API anteriores al 24). La versión de 64 bits de JDK 8 es necesaria si utiliza controles personalizados o la vista previa de formularios.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

Tenga en cuenta que las aplicaciones deben generarse con Xamarin C6SR4 o posterior para trabajar de forma confiable con Android Nougat. Puesto que Android Nougat puede vincular únicamente a [bibliotecas nativas siempre NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), las aplicaciones existentes mediante bibliotecas como **Mono.Data.Sqlite.dll** puede bloquearse cuando se ejecuta en Android Nougat si no están correctamente volver a generar.



## <a name="getting-started"></a>Introducción

Para comenzar a usar Android Nougat con Xamarin.Android, debe descargar e instalar los paquetes SDK y herramientas más recientes antes de poder crear un proyecto de Android Nougat:

1.  Instalar las últimas actualizaciones de Xamarin.Android desde Xamarin.

2.  Instalar el **Android 7.0 (API 24)** paquetes y las herramientas o una versión posterior.

3.  Cree un nuevo proyecto de Xamarin.Android que tiene como destino Android Nougat.

4.  Configurar un dispositivo o emulador de Android Nougat.

Cada uno de estos pasos se explica en las secciones siguientes:


### <a name="install-xamarin-updates"></a>Instalar actualizaciones de Xamarin

Para agregar compatibilidad con Xamarin para Android Nougat, cambiar el canal de actualizaciones en Visual Studio o Visual Studio para Mac para el canal estable y se aplican las actualizaciones más recientes. Si también tiene características que están actualmente disponibles sólo en el canal alfa o Beta, puede cambiar en el canal alfa o Beta (los canales alfa y Beta también proporcionan soporte técnico para Android 7.x). Para obtener información sobre cómo cambiar el canal de actualizaciones (versiones), consulte [cambiar el canal actualizaciones](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).



### <a name="install-the-android-sdk"></a>Instalar el SDK de Android

Para crear un proyecto con Xamarin Android 7.0, primero debe usar Android SDK Manager para instalar **N de Android SDK Platform (API 24)** o una versión posterior. También debe instalar la versión más reciente **Android SDK Tools**:

1.  Iniciar Android SDK Manager (en Visual Studio para Mac, use **Herramientas > Abrir Android SDK Manager&hellip;**; en Visual Studio, use **Herramientas > Android > Android SDK Manager**).

2.  Instalar **Android 7.0 (API 24)** o una versión posterior:

    [![Seleccionar paquetes de Android 7.0 en Android SDK Manager](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Instalar las herramientas de Android SDK más recientes:

    [![Seleccionar las herramientas más recientes de Android SDK de Android SDK Manager](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Debe instalar la revisión de Android SDK Tools 25.2.2 o versiones posteriores, Android Platform SDK herramientas 24.0.3 o posterior y Android SDK Build 24.0.2 o una versión posterior.

4.  Compruebe que la **ubicación de Java Development Kit** está configurado para JDK 1.8:

    [![Configuración de la ruta de acceso de JDK 8 en las opciones de herramientas](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Para ver esta configuración en Visual Studio, haga clic en **Herramientas > Opciones > Xamarin > configuración de Android**. En Visual Studio para Mac, haga clic en **Preferencias > proyectos > ubicaciones de SDK > Android**.



### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto de Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre cómo crear proyectos de Xamarin.Android.

Al crear un proyecto de Android, debe configurar la configuración de versión al destino Android 7.0 o posterior. Por ejemplo, para el destino del proyecto para Android 7.0, debe configurar el nivel de API de Android de destino del proyecto a **Android 7.0 (API 24: Nougat)**. Se recomienda establecer el nivel de marco de destino a la API 24 o versiones posteriores. Para obtener más información acerca de cómo configurar los niveles de nivel de API de Android, consulte [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).


> [!NOTE]
> Actualmente, debe establecer el **versión Android mínima** a **Android 7.0 (API 24: Nougat)** para implementar la aplicación para dispositivos Android Nougat o emuladores.



### <a name="configure-an-emulator-or-device"></a>Configurar un dispositivo o emulador

Si usa un emulador, inicie Android AVD Manager y cree un nuevo dispositivo usando la configuración siguiente:

-   Dispositivo: Nexus 5 X, Nexus 6, Nexus 6P, Nexus Reproductor, Nexus 9 o píxel C.
-   Destino: Android 7.0 - nivel API 24
-   ABI: x86 o x86\_64

Por ejemplo, este dispositivo virtual está configurado para emular un Nexus 6:

[![Configurar un AVD con Intel Atom x86 CPU/ABI, destino de Android 7.0 y dispositivo Nexus 6](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Si utiliza un dispositivo físico como un Nexus 5 X, 6 o 9, puede actualizar el dispositivo a través de automático a través de las actualizaciones de remotamente (OTA) o descargar una imagen de sistema y el dispositivo flash directamente. Para obtener más información acerca de cómo actualizar manualmente el dispositivo a Android Nougat, consulte [imágenes de transmisión sin cables para dispositivos Nexus](https://developers.google.com/android/nexus/ota).

Tenga en cuenta que los dispositivos Nexus 5 no son compatibles con Android Nougat.



## <a name="new-features"></a>Características nuevas

Android Nougat presenta una variedad de nuevas características y capacidades, como la compatibilidad con varias ventanas, mejoras en las notificaciones y protector de datos. Las siguientes secciones resaltan estas características y proporcionan vínculos para ayudarle a empezar a trabajar con ellos en la aplicación.



### <a name="multi-window-mode"></a>Modo Multiventana

El modo multiventana permite a los usuarios abrir dos aplicaciones a la vez con compatibilidad completa multitarea. Estas aplicaciones pueden ejecutar side-by-side (horizontal) o uno-por encima-the-otros (vertical) en modo de pantalla dividida.
Los usuarios pueden arrastrar un divisor entre las aplicaciones para cambiar su tamaño y puede cortar y pegar el contenido del entre aplicaciones. Cuando se presentan dos aplicaciones en modo multiventana, la actividad seleccionada continúa ejecutándose mientras la actividad no seleccionada está en pausa pero todavía está visible. El modo multiventana no modifica el ciclo de vida de la actividad de Android.

[![Aplicaciones de ejemplo que se ejecutan en el modo multiventana en vertical y horizontal](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Puede configurar cómo las actividades de la aplicación de Xamarin.Android son compatibles con el modo multiventana. Por ejemplo, puede configurar los atributos que establece el tamaño mínimo y el alto y ancho predeterminados de la aplicación en el modo multiventana. Puede usar el nuevo `Activity.IsInMultiWindowMode` propiedad para determinar si la actividad está en modo de varias ventana. Por ejemplo:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

El [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) aplicación de ejemplo incluye código de C# que muestra cómo aprovechar las ventajas de la ventana de varias interfaces de usuario con la aplicación.

Para obtener más información sobre el modo multiventana, consulte el [compatibilidad con las múltiples ventanas](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Notificaciones mejoradas

Android Nougat presenta un sistema de notificación rediseñada. Ofrece una nueva característica de respuesta directa a los que hace posible para que los usuarios rápidamente una respuesta a las notificaciones para los mensajes entrantes de texto directamente en el interfaz de usuario de notificación. A partir de Android 7.0, los mensajes se pueden agrupar como un único grupo cuando se recibe más de un mensaje de notificación. Además, los desarrolladores pueden personalizar notification aprovechan decoraciones del sistema en las notificaciones de vistas y aprovechar las ventajas de nuevas plantillas de notificación al generar las notificaciones.


#### <a name="direct-reply"></a>Respuesta directa

Cuando un usuario recibe una notificación de mensaje entrante, Android Nougat permite responder al mensaje dentro de la notificación (en lugar de abrir la aplicación de mensajería para enviar una respuesta).
Esta característica de respuesta en línea permite a los usuarios responder rápidamente a un mensaje de texto o SMS directamente dentro de la interfaz de notificación:

[![Captura de pantalla de una notificación con un campo de respuesta directa en línea](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Para admitir esta característica en su aplicación, debe agregar *acciones de respuesta en línea* a la aplicación a través de un [RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/) objeto para que los usuarios pueden responder mediante texto directamente desde la interfaz de usuario de notificación.
Por ejemplo, el siguiente código compilaciones un `RemoteInput` para recibir datos de entrada de texto, genera una intención pendiente para la acción de respuesta y crea una acción habilitada entrada remota:

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

Esta acción se agrega a la notificación:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

El [Messaging Service](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) aplicación de ejemplo incluye código de C# que muestra cómo extender las notificaciones con un `RemoteInput` objeto. Para obtener más información acerca de cómo agregar respuesta en línea acciones a la aplicación para Android 7.0 o posterior, consulte el Android [responder a las notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) tema.


#### <a name="bundled-notifications"></a>Notificaciones empaquetadas

Android Nougat puede agrupar los mensajes de notificación (por ejemplo, al tema del mensaje) y mostrar el grupo en lugar de cada mensaje independiente.
Esto *incluye notificaciones* característica hace que los usuarios pueden descartar o archivar un grupo de notificaciones en una sola acción. El usuario puede deslizarse hacia abajo para expandir la agrupación de notificaciones para ver cada notificación detalladamente:

[![Captura de pantalla ejemplo de las notificaciones empaquetadas](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Para admitir las notificaciones empaquetadas, la aplicación puede usar el [Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/) método para agrupar las notificaciones similar. Para obtener más información acerca de los grupos de notificación integrados en Android N, consulte el Android [notificaciones unión](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) tema.


#### <a name="custom-views"></a>Vistas personalizadas

Android Nougat permite crear vistas de notificación personalizada con diseños expansibles, acciones y encabezados de notificación del sistema. Para obtener más información acerca de las vistas de notificación personalizada de Android Nougat, vea el Android [notificación mejoras](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) tema.



### <a name="data-saver"></a>Protector de datos

A partir de Android Nougat, los usuarios pueden habilitar un nuevo *protector de datos* establecer que el uso de datos de fondo de bloques. Esta configuración también indica a la aplicación para utilizar menos datos en primer plano siempre que sea posible. El [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/) se ha ampliado en Android Nougat para que la aplicación puede comprobar si el usuario ha habilitado el protector de datos para que la aplicación puede realizar un esfuerzo para limitar el uso de sus datos cuando se habilita el protector de datos.

Para obtener más información acerca de la nueva característica de protector de datos de Android Nougat, vea el Android [optimizar el uso de datos de red](https://developer.android.com/training/basics/network-ops/data-saver.html) tema.



### <a name="app-shortcuts"></a>Métodos abreviados de la aplicación

Android 7.1 introducidas un *accesos directos de la aplicación* característica que permite a los usuarios rápidamente inicio tareas comunes o recomendada con la aplicación.
Para activar el menú de accesos directos, el usuario prolongada presionadas el icono de la aplicación durante un segundo o más &ndash; el menú aparece con una vibración rápido.
Liberación de la prensa, hace que el menú permanezca:

[![Pantalla de ejemplo de un menú contextual de aplicación para una aplicación de mensajería](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Esta característica está disponible solo nivel de API 25 o superior.
Para obtener más información acerca de la nueva característica de accesos directos de la aplicación de Android 7.1, vea el Android [accesos directos de la aplicación](https://developer.android.com/guide/topics/ui/shortcuts.html) tema.


### <a name="sample-code"></a>Código de ejemplo

Existen varios ejemplos de Xamarin.Android para mostrarle cómo aprovechar las características de Android Nougat:

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) muestra el uso de la API de multiventana disponible en Android Nougat. Puede cambiar la aplicación de ejemplo en modo de múltiples de windows para ver cómo afecta al ciclo de vida y el comportamiento de la aplicación.

-   [Servicio de mensajería](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) es un servicio sencillo que envíe notificaciones utilizando el `NotificationCompatManager`. También extiende la notificación con un `RemoteInput` objeto para permitir que los dispositivos Android Nougat responder a través de texto directamente desde la notificación sin tener que abrir una aplicación.

-   [Activas notificaciones](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/) muestra cómo usar el `NotificationManager` API para indicar el número de notificaciones está mostrando actualmente la aplicación.

-   [Ámbito de acceso al directorio](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/) muestra cómo usar la API de acceso de directorio con ámbito para tener acceso fácilmente a los directorios específicos. Esto sirve como una alternativa a tener que definir `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` permisos en el manifiesto.

-   [Dirigir el arranque](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/) ilustra cómo almacenar datos en un almacenamiento cifrado de dispositivo que siempre está disponible mientras el dispositivo se arranca ambos antes y después de introducir cualquier credentials(PIN/Pattern/Password) de usuario.


## <a name="summary"></a>Resumen

En este artículo introdujo Android Nougat y se explica cómo instalar y configurar las últimas herramientas y paquetes para el desarrollo de Xamarin.Android en Android Nougat. También proporciona una visión general de las características claves disponibles en Android Nougat, con vínculos a código fuente de ejemplo que le ayudarán a comenzar a crear aplicaciones para Android Nougat.


## <a name="related-links"></a>Vínculos relacionados

- [Android 7.1 para desarrolladores](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notas de la versión de Xamarin Android 7.0](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
