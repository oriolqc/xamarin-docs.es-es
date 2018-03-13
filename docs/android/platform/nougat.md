---
title: "Características de nueces"
description: "Cómo empezar a usar Xamarin.Android para desarrollar aplicaciones para Android nueces."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: c666b7d5b680eab3c990950569868eacdb6f30af
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="nougat-features"></a>Características de nueces

_Cómo empezar a usar Xamarin.Android para desarrollar aplicaciones para Android nueces._

Este artículo se proporciona un resumen de las características introducidas en Android nueces, explica cómo preparar Xamarin.Android para el desarrollo de Android nueces y proporciona vínculos a aplicaciones de ejemplo que muestran cómo usar las características de Android nueces de Aplicaciones Xamarin.Android.


## <a name="overview"></a>Información general

[Android nueces](https://developer.android.com/about/versions/nougat/android-7.0.html) es el seguimiento de Google para Android Marshmallow 6.0. Xamarin.Android proporciona compatibilidad para **7.x Android enlaces** en Xamarin Android 7.0 y versiones posteriores. Nueces Android agrega muchas nuevas API para las características de nueces que se describe a continuación; Estas API están disponibles para aplicaciones de Xamarin.Android cuando se usa la versión 7.0 de Xamarin.Android.

[![Imágenes de héroe de tabletas y teléfonos ejecuta Android nueces Android](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Para obtener más información acerca de las API de Android 7.x, consulte [Android 7.1 para desarrolladores](http://developer.android.com/preview/api-overview.html).
Para obtener una lista de problemas conocidos de Xamarin.Android 7.0, consulte el [notas de la versión](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/).

Nueces Android ofrece muchas características nuevas de interés para desarrolladores de Xamarin.Android. Estas características incluyen:

-   **Compatibilidad con varias ventana** &ndash; esta mejora permite a los usuarios abrir dos aplicaciones en la pantalla a la vez.

-   **Mejoras de notificación** &ndash; el sistema de notificaciones rediseñado en Android nueces incluye un *respuesta directa* característica que permite a los usuarios responder rápidamente a los mensajes de texto directamente a partir de la notificación INTERFAZ DE USUARIO. Además, si la aplicación crea notificaciones para recibido mensajes, la nueva *agrupadas notificaciones* característica puede empaquetar las notificaciones juntas como un único grupo cuando se recibe más de un mensaje.

-   **Protector de datos** &ndash; esta característica es un nuevo servicio de sistema que ayuda a reducir el uso de datos móviles aplicaciones; proporciona a los usuarios control sobre cómo las aplicaciones utilizarán datos móviles.

Además, nueces Android ofrece muchas otras mejoras de interés para desarrolladores de aplicaciones como una nueva característica de configuración de seguridad de red, Doze sobre la marcha, atestación, nuevas API configuración rápida, compatibilidad con varias configuraciones regionales, las API ICU4J, WebView mejoras, clave acceso a características del lenguaje Java 8, acceso al directorio de ámbito, una API de puntero personalizado, compatibilidad con la plataforma VR, archivos virtuales y las optimizaciones de procesamiento en segundo plano.

En este artículo se explica cómo empezar a compilar aplicaciones con Android nueces para probar las nuevas características y planear el trabajo de migración o una característica que tenga como destino la plataforma Android nueces nueva.


## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android nueces en aplicaciones basadas en Xamarin, es necesario lo siguiente:

-   **Visual Studio o Visual Studio para Mac** &ndash; si está usando Visual Studio, versión 4.2.0.628 o posterior de Xamarin para Visual Studio es necesaria. Si utiliza Visual Studio para Mac, versión 6.1.0 o una versión posterior de Visual Studio para Mac es necesaria.

-   **Xamarin.Android** &ndash; Xamarin.Android 7.0 o posterior debe instalarse y configurarse con Visual Studio o Visual Studio para Mac.

-   **SDK de Android** -7.0 de Android SDK (API de 24) o posterior debe estar instalado mediante el Administrador de SDK de Android.

-   **Kit de desarrollo de Java** &ndash; el desarrollo de Xamarin Android 7.0 requiere [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si va a desarrollar para el nivel de API 24 o mayor (JDK 8 también admite niveles de API anteriores a 24). La versión de 64 bits de JDK 8 es necesaria si utiliza controles personalizados o el controlador de vista previa de formularios.

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

Tenga en cuenta que las aplicaciones deben generarse con Xamarin C6SR4 o posterior para que funcione de forma confiable con nueces Android. Puesto que nueces Android puede vincular únicamente a [bibliotecas nativas proporcionado por el NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), las aplicaciones existentes mediante las bibliotecas como **Mono.Data.Sqlite.dll** puede bloquearse cuando se ejecuta en nueces Android si no están correctamente volver a generar.



## <a name="getting-started"></a>Introducción

Para empezar a trabajar con Android nueces Xamarin.Android, debe descargar e instalar los paquetes SDK y herramientas más recientes antes de poder crear un proyecto de Android nueces:

1.  Instalar las actualizaciones más recientes de Xamarin.Android desde el Xamarin.

2.  Instalar el **Android 7.0 (API de 24)** paquetes y las herramientas o una versión posterior.

3.  Cree un nuevo proyecto de Xamarin.Android destinado a Android nueces.

4.  Configurar un dispositivo o emulador de Android nueces.

Cada uno de estos pasos se explica en las secciones siguientes:


### <a name="install-xamarin-updates"></a>Instalar actualizaciones de Xamarin

Para agregar compatibilidad con Xamarin para Android nueces, cambiar el canal actualizaciones en Visual Studio o Visual Studio para Mac para el canal estable y aplicar las actualizaciones más recientes. Si también tiene características que están actualmente disponibles únicamente en el canal alfa o la versión Beta, puede cambiar en el canal alfa o Beta (los canales alfa y Beta también proporcionan compatibilidad para Android 7.x). Para obtener información sobre cómo cambiar el canal actualizaciones (versiones), consulte [cambiar el canal actualizaciones](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/).



### <a name="install-the-android-sdk"></a>Instalar el SDK de Android

Para crear un proyecto con Xamarin Android 7.0, primero debe usar el Administrador de Android SDK para instalar **N de Android SDK de plataforma (API de 24)** o una versión posterior. También debe instalar la versión más reciente **herramientas de Android SDK**:

1.  Inicie el Administrador de SDK de Android (en Visual Studio para Mac, utilice **Herramientas > Abra el Administrador de Android SDK&hellip;**; en Visual Studio, use **Herramientas > Android > Administrador de Android SDK**).

2.  Instalar **Android 7.0 (API de 24)** o una versión posterior:

    [![Seleccionar paquetes de Android 7.0 en el Administrador de SDK de Android](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Instalar las herramientas de SDK de Android más recientes:

    [![Seleccionar las herramientas de SDK de Android más recientes en el Administrador de SDK de Android](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Debe instalar la revisión de herramientas de Android SDK 25.2.2 o herramientas de la plataforma SDK posteriores, Android 24.0.3 o herramientas de compilación SDK de Android y posteriores 24.0.2 o una versión posterior.

4.  Compruebe que la **ubicación de Kit de desarrollo de Java** está configurado para 1.8 de JDK:

    [![Configuración de la ruta de acceso de JDK 8 en las opciones de herramientas](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Para ver esta configuración en Visual Studio, haga clic en **Herramientas > Opciones > Xamarin > configuración de Android**. En Visual Studio para Mac, haga clic en **Preferencias > proyectos > ubicaciones de SDK > Android**.



### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información sobre cómo crear proyectos de Xamarin.Android.

Cuando cree un proyecto Android, debe configurar los valores de versión al destino Android 7.0 o posterior. Por ejemplo, para el destino del proyecto para Android 7.0, debe configurar el nivel de API de Android de destino del proyecto a **Android 7.0 (API de 24 - nueces)**. Se recomienda establecer el nivel de marco de trabajo de destino a API 24 o posterior. Para obtener más información acerca de cómo configurar los niveles de nivel de API de Android, consulte [niveles de API de Android descripción](~/android/app-fundamentals/android-api-levels.md).


> [!NOTE]
> Actualmente, debe establecer el **versión mínimo Android** a **Android 7.0 (API de 24 - nueces)** para implementar la aplicación para dispositivos Android nueces o emuladores.



### <a name="configure-an-emulator-or-device"></a>Configurar un dispositivo o emulador

Si usas un emulador, inicie el Administrador de AVD Android y crear un nuevo dispositivo con la configuración siguiente:

-   Dispositivo: Nexus 5 X Nexus 6, Nexus 6P, Reproductor de Nexus, Nexus 9 o píxel C.
-   Destino: Android 7.0 - 24 de nivel de API
-   ABI: x86 o x86\_64

Por ejemplo, este dispositivo virtual se configura para emular un 6 Nexus:

[![Configurar un AVD con dispositivo Nexus 6 y destino Android 7.0, Intel Atom x86 CPU/ABI](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Si está utilizando un dispositivo físico como un Nexus X 5, 6 o 9, puede actualizar el dispositivo a través de automático a través de las actualizaciones de remotamente o descargar una imagen de sistema y flash directamente el dispositivo. Para obtener más información acerca de cómo actualizar manualmente el dispositivo para nueces Android, consulte [imágenes de transmisión sin cables para dispositivos Nexus](https://developers.google.com/android/nexus/ota).

Tenga en cuenta que los dispositivos Nexus 5 no son compatibles con nueces Android.



## <a name="new-features"></a>Características nuevas

Nueces Android presentan una variedad de nuevas características y funcionalidades, como compatibilidad con varias ventanas y mejoras de las notificaciones, el protector de datos. Las siguientes secciones resaltan estas características y se proporcionan vínculos para ayudarle a empezar a trabajar con ellos en la aplicación.



### <a name="multi-window-mode"></a>Modo de varias ventana

Modo de varias ventana permite a los usuarios abrir a la vez dos aplicaciones con compatibilidad de multitarea completa. Estas aplicaciones pueden ejecutar side-by-side (horizontal) o uno-por encima-the-other (vertical) en modo de pantalla dividida.
Los usuarios pueden arrastrar un divisor entre las aplicaciones para cambiar su tamaño y puede cortar y pegar el contenido del entre aplicaciones. Cuando se presentan dos aplicaciones en modo de varias ventana, la actividad seleccionada continúa ejecutándose mientras la actividad no seleccionada está en pausa pero todavía está visible. Modo de varias ventana no modifica el ciclo de vida de actividad Android.

[![Aplicaciones de ejemplo que se ejecutan en modo de varias ventana en vertical y horizontal](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Puede configurar cómo las actividades de la aplicación Xamarin.Android son compatibles con el modo de varias ventana. Por ejemplo, puede configurar atributos que establece el tamaño mínimo y el alto predeterminado y el ancho de la aplicación en modo de varias ventana. Puede usar el nuevo `Activity.IsInMultiWindowMode` propiedad para determinar si la actividad está en modo de varias ventana. Por ejemplo:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

El [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) aplicación de ejemplo incluye código de C# que muestra cómo aprovechar las ventajas de la ventana de varias interfaces de usuario con la aplicación.

Para obtener más información acerca del modo de varias ventana, vea el [compatibilidad con varias ventanas](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Notificaciones mejoradas

Nueces Android presentan un sistema de notificación rediseñado. Ofrece una nueva característica de respuesta directa que permite a los usuarios la rápida respuesta a las notificaciones para los mensajes entrantes de texto directamente en la interfaz de usuario de notificación. A partir de Android 7.0, los mensajes pueden se deben agrupar como un único grupo cuando se recibe más de un mensaje de notificación. Además, los desarrolladores pueden personalizar notificación vistas, aprovechar decoraciones de sistema en las notificaciones y aprovechar las ventajas de nuevas plantillas de notificación al generar las notificaciones.


#### <a name="direct-reply"></a>Respuesta directa

Cuando un usuario recibe una notificación de mensaje entrante, nueces Android permite responder al mensaje dentro de la notificación (en lugar de abrir la aplicación de mensajería para enviar una respuesta).
Esta característica de respuesta en línea permite a los usuarios responder rápidamente a un mensaje de texto o SMS directamente dentro de la interfaz de notificación:

[![Captura de pantalla de una notificación con un campo de respuesta directa en línea](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Para admitir esta característica en la aplicación, debe agregar *acciones de respuesta en línea* a la aplicación a través de un [RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/) objeto para que los usuarios pueden responder a través de texto directamente desde la interfaz de usuario de notificación.
Por ejemplo, el siguiente código compilaciones un `RemoteInput` para recibir la entrada de texto, genera una intención pendiente para la acción de respuesta y crea una acción habilitada entrada remota:

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

El [servicio de mensajería](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) aplicación de ejemplo incluye código de C# que muestra cómo extender las notificaciones con un `RemoteInput` objeto. Para obtener más información sobre la adición de respuesta en línea acciones a la aplicación para Android 7.0 o posterior, consulte el Android [responder a las notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) tema.


#### <a name="bundled-notifications"></a>Notificaciones empaquetadas

Nueces Android pueden agrupar los mensajes de notificación (por ejemplo, mediante el tema del mensaje) y mostrar el grupo en lugar de cada mensaje por separado.
Esto *agrupadas notificaciones* característica hace posible para que los usuarios descartar o archivar un grupo de notificaciones en una acción. El usuario puede Deslizar hacia abajo para expandir la agrupación de notificaciones para ver cada notificación detalladamente:

[![Captura de pantalla ejemplo de notificaciones agrupadas](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Para admitir notificaciones integradas, puede usar su aplicación el [Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/) método para agrupar las notificaciones similar. Para obtener más información acerca de los grupos de notificación integrados en N Android, consulte el Android [agrupación notificaciones](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) tema.


#### <a name="custom-views"></a>Vistas personalizadas

Nueces Android permite crear vistas de notificación personalizada con encabezados de notificación del sistema, acciones y expandibles diseños. Para obtener más información acerca de las vistas de notificación personalizada de Android nueces, vea el Android [notificación mejoras](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) tema.



### <a name="data-saver"></a>Protector de datos

A partir de nueces Android, los usuarios pueden habilitar una nueva *protector de datos* establecer que el uso de datos de fondo de bloques. Esta configuración también indica a la aplicación para utilizar menos datos en primer plano, siempre que sea posible. El [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/) se amplió en nueces Android para que la aplicación puede comprobar si el usuario ha habilitado el protector de datos para que la aplicación pueda realizar un esfuerzo para limitar el uso de sus datos cuando se haya activado el protector de datos.

Para obtener más información acerca de la característica de protector de datos nueva en nueces Android, consulte el Android [optimizar el uso de datos de red](https://developer.android.com/training/basics/network-ops/data-saver.html) tema.



### <a name="app-shortcuts"></a>Accesos directos de la aplicación

7.1 Android introducidas un *accesos directos de la aplicación* característica que permite a los usuarios rápidamente inicio tareas comunes o recomendada con la aplicación.
Para activar el menú de accesos directos, el usuario long-pulsaciones el icono de la aplicación para uno o más segundos &ndash; aparece el menú con una vibración rápida.
Liberar la acción de presionar hace que el menú permanezca:

[![Pantalla de ejemplo de un menú de acceso directo de la aplicación para una aplicación de mensajería](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Esta característica está disponible solo nivel de API 25 o superior.
Para obtener más información acerca de la nueva característica de accesos directos de la aplicación en Android 7.1, consulte el Android [accesos directos de la aplicación](https://developer.android.com/guide/topics/ui/shortcuts.html) tema.


### <a name="sample-code"></a>Código de ejemplo

Varios ejemplos de Xamarin.Android están disponibles para mostrarle cómo aprovechar las características de nueces Android:

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) muestra el uso de la API de varias ventana disponible en nueces Android. Puede cambiar la aplicación de ejemplo en modo de múltiples de windows para ver cómo afecta al ciclo de vida y el comportamiento de la aplicación.

-   [Servicio de mensajería](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) es un servicio sencillo que envía notificaciones utilizando la `NotificationCompatManager`. También extiende la notificación con un `RemoteInput` objeto que se va a permitir que los dispositivos Android nueces responder a través de texto directamente a partir de la notificación sin tener que abrir una aplicación.

-   [Las notificaciones de activas](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/) muestra cómo utilizar el `NotificationManager` API para indicar el número de notificaciones la aplicación se muestra actualmente.

-   [Ámbito de acceso del directorio](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/) muestra cómo utilizar la API de acceso de directorio con ámbito para acceder fácilmente a directorios específicos. Este valor actúa como una alternativa a tener que definir `READ_EXTERNAL_STORAGE` o `WRITE_EXTERNAL_STORAGE` permisos en el manifiesto.

-   [Dirigir el arranque](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/) ilustra cómo almacenar datos en un almacenamiento cifrado de dispositivo que siempre está disponible mientras el dispositivo se arranca ambos antes y después de introducir cualquier credentials(PIN/Pattern/Password) de usuario.


## <a name="summary"></a>Resumen

En este artículo se introdujo nueces Android y se explica cómo instalar y configurar las últimas herramientas y paquetes para el desarrollo de Xamarin.Android en nueces Android. También proporciona una visión general de las características clave disponibles en Android nueces, con vínculos a código fuente de ejemplo que le ayudarán a empezar a trabajar en la creación de aplicaciones para Android nueces.


## <a name="related-links"></a>Vínculos relacionados

- [Android 7.1 para desarrolladores](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notas de la versión de Xamarin para Android 7.0](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
