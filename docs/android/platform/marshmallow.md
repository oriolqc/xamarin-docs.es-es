---
title: Características de marshmallow
description: Este artículo le ayudará a empezar a usar en el uso de Xamarin.Android para desarrollar aplicaciones para Android Marshmallow 6.0.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 0393b9a994c1fd62f51cff01a88aa73f71019d53
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113461"
---
# <a name="marshmallow-features"></a>Características de marshmallow

_Este artículo le ayudará a empezar a usar en el uso de Xamarin.Android para desarrollar aplicaciones para Android Marshmallow 6.0._

En este artículo se proporciona un esquema de las nuevas características de Android Marshmallow 6.0, se explica cómo preparar Xamarin.Android para el desarrollo de Android Marshmallow y proporciona vínculos a aplicaciones de ejemplo que muestran cómo usar Android Marshmallow nuevo características de aplicaciones de Xamarin.Android. 


## <a name="overview"></a>Información general

[Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html), es la siguiente principales de Android versión después de Android Lollipop.
Xamarin.Android admite Android Marshmallow e incluye:

-   **Enlaces de API 23/Android 6.0** &ndash; Android 6.0 agrega muchas nuevas API para las nuevas características que se describen a continuación; estas API están disponibles para las aplicaciones Xamarin.Android cuando el destino es el nivel de API 23. Para obtener más información acerca de la API de Android 6.0, consulte [API de Android 6.0](http://developer.android.com/preview/api-overview.html). 

[![Imágenes de tabletas y teléfonos ejecutan Marshmallow Hero](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Aunque la versión de Marshmallow se centra principalmente en "polaco y calidad", también proporciona muchas características nuevas de interés para los desarrolladores de Xamarin.Android. Entre ellas se incluyen: 

-   **Los permisos en tiempo de ejecución** &ndash; esta mejora permite que los usuarios aprueben en cada caso por los permisos de seguridad en tiempo de ejecución. 

-   **Mejoras de autenticación** &ndash; a partir de Android Marshmallow, las aplicaciones ahora pueden usar los sensores de huellas digitales para autenticar usuarios y un nuevo *confirmar credencial* característica reduce la necesidad de escribir contraseñas. 

-   **Vinculación de la aplicación** &ndash; esta característica ayuda a eliminar la necesidad de tener la **aplicación selector** emergente asociando las aplicaciones con dominios web automáticamente. 

-   **Dirigir el recurso compartido** &ndash; puede definir *dirigir el recurso compartido de destinos* que facilitar el uso compartido rápida e intuitiva para los usuarios; esta característica permite uers compartir contenido con otras aplicaciones. 

-   **Interacciones de voz** &ndash; esta nueva API le permite integrar características de voz de conversación en la aplicación. 

-   **Modo de presentación de 4 K** &ndash; en Android Marshmallow, la aplicación puede solicitar la resolución de pantalla de un 4 K en un hardware lo admite. 

-   **Nuevas características de Audio** &ndash; partir Mallorca, Android admite ahora el protocolo MIDI. También proporciona nuevas clases para crear objetos de reproducción y captura de audio digital y ofrece enlaces de API nuevas para asociar los dispositivos de audio y de entrada. 

-   **Nuevas características de vídeo** &ndash; Marshmallow proporciona una clase nueva que ayuda a las aplicaciones de representación de secuencias de audio y vídeos sincronizados; de esta clase también proporciona compatibilidad para la velocidad de reproducción dinámicas. 

-   **Android for Work** &ndash; Marshmallow incluye controles mejorados para los dispositivos corporativos y de usuario único. Admite la instalación silenciosa y desinstalación de aplicaciones por el propietario del dispositivo, la aceptación automática de actualizaciones del sistema, administración mejorada de certificados, seguimiento de datos de uso, administración de permisos y las notificaciones de estado del trabajo. 

-   **Biblioteca de compatibilidad de diseño material** &ndash; nuevo *diseño Support Library* proporciona componentes de diseño y patrones que resulta más fácil para generar Material Design apariencia en la aplicación. 

Además, muchas actualizaciones de biblioteca de Android de núcleo se lanzaron con Android M, y estas actualizaciones proporcionan las nuevas características para Android M y versiones anteriores de Android.

Además, muchas actualizaciones de biblioteca de Android de núcleo se lanzaron con Android Marshmallow, y estas actualizaciones proporcionan las nuevas características para Android Marshmallow y versiones anteriores de Android. En este artículo se explica cómo empezar a crear aplicaciones con Android Marshmallow, y proporciona que una visión general de la nueva característica se resalta en Android 6.0. 

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android Marshmallow en aplicaciones basadas en Xamarin, se requiere lo siguiente: 

-   **Xamarin.Android** &ndash; Xamarin.Android 5.1.7.12 o posterior debe estar instalado y configurado con Visual Studio o Xamarin Studio.

-   **Visual Studio para Mac** o **Visual Studio** &ndash; si usa Visual Studio para Mac, versión 5.9.7.22 o posterior es necesario. Si usa Visual Studio, versión 3.11.1537 o una versión posterior de las herramientas de Xamarin para Visual Studio es necesario. 

-   **SDK de Android** &ndash; (API 23) de Android SDK 6.0 o posterior debe estar instalado a través de Android SDK Manager.

-   **Kit para desarrolladores de Java** &ndash; requiere Xamarin.Android [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si está desarrollando para el nivel de API 24 o superior (JDK 1.8 también admite los niveles de API anteriores al 24, incluidos Marshmallow). La versión de 64 bits del JDK 1.8 es necesaria si utiliza controles personalizados o la vista previa de formularios.

Aún puede usar [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si está desarrollando contenido específicamente para el nivel de API 23 o una versión anterior. 


## <a name="getting-started"></a>Introducción

Para comenzar a usar Android Marshmallow con Xamarin.Android, debe descargar e instalar los paquetes SDK y herramientas más recientes antes de poder crear un proyecto de Android Marshmallow: 

1.  Instalar las últimas actualizaciones de Xamarin desde el **estable** canal. 

2.  Instale las herramientas y paquetes de SDK de Android 6.0 Marshmallow.

3.  Cree un nuevo proyecto de Xamarin.Android que tiene como destino Android Marshmallow 6.0 (nivel de API 23). 

4.  Configurar un dispositivo o emulador para Android Marshmallow.

Cada uno de estos pasos se explica en las secciones siguientes:


### <a name="install-xamarin-updates"></a>Instalar actualizaciones de Xamarin

Para actualizar Xamarin para que incluya soporte técnico para Android Marshmallow 6.0, cambie el canal de actualización para **estable** e instale todas las actualizaciones. Para obtener más información sobre cómo instalar actualizaciones desde el canal de actualizaciones, consulte [cambiar el canal actualizaciones](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 


### <a name="install-the-android-60-sdk"></a>Instalar el SDK de Android 6.0

Para crear un proyecto de Xamarin.Android para Android Marshmallow, primero debe usar Android SDK Manager para instalar el SDK de Android 6.0:

-   Iniciar Android SDK Manager (en Visual Studio para Mac, use **Herramientas > SDK Manager**; en Visual Studio, use **Herramientas > Android > Android SDK Manager**) e instale la más reciente de Android SDK Tools:

    [![Seleccionar las herramientas de Android SDK de Android SDK Manager](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

-   Además, instale la última versión **Android 6.0** paquetes del SDK:

    [![Seleccionar paquetes de Android SDK 6.0 en el Administrador de SDK de Android](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Debe instalar la revisión de Android SDK Tools 24.3.4 o una versión posterior.
Para obtener más información sobre cómo usar Android SDK Manager para instalar el SDK de Android 6.0, consulte [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).



### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto de Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información acerca de cómo crear proyectos de Android. 

Al crear un proyecto de Android, debe configurar la configuración de la versión Android MarshMallow 6.0 de destino. Para el destino del proyecto para Mallorca, debe configurar el proyecto para **nivel de API 23 (Xamarin.Android 6.0 soporte técnico)**. Para obtener más información acerca de cómo configurar los niveles de nivel de API de Android, consulte [Understanding Android API niveles](~/android/app-fundamentals/android-api-levels.md).



### <a name="configure-an-emulator-or-device"></a>Configurar un dispositivo o emulador

Si usa un emulador, inicie Android AVD Manager y cree un nuevo dispositivo usando la configuración siguiente:

-   Dispositivo: Nexus 5, 6 o 9.
-   Destino: Android 6.0 - nivel de API 23
-   ABI: x86

Por ejemplo, este dispositivo virtual está configurado para emular un dispositivo Nexus 5:

[![Configurar un AVD con dispositivo Nexus 5, el destino de Android 6.0 y Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Si utiliza un dispositivo físico, como un dispositivo Nexus 5, 6 o 9, puede instalar una imagen de vista previa de Android Marshmallow. Para obtener más información acerca de cómo actualizar el dispositivo a Android Marshmallow, consulte [imágenes de sistema de Hardware](http://developer.android.com/preview/download.html#images).



## <a name="new-features"></a>Características nuevas

Muchos de los cambios introducidos en Android Marshmallow se centran en mejorar la experiencia del usuario de Android, aumento del rendimiento y corrección de errores. Sin embargo, Marshmallow también introdujo algunos cambios importantes a los fundamentos de la plataforma Android. Las siguientes secciones resaltan estas mejoras y proporcionan vínculos para ayudarle a empezar a usar las nuevas características de Android Marshmallow en la aplicación. 



### <a name="runtime-permissions"></a>Permisos en tiempo de ejecución

El sistema de permisos de Android ha optimizado y simplificado desde Android Lollipop significativamente. En Android Marshmallow, los usuarios conceder los permisos según el caso por caso en tiempo de ejecución en lugar de en tiempo de la instalación. Para admitir esta característica en Android Marshmallow y versiones posteriores, al diseñar la aplicación para pedir al usuario los permisos en tiempo de ejecución (en el contexto de donde se necesitan los permisos). Este cambio facilita que los usuarios empezar a usar la aplicación inmediatamente porque agiliza el proceso de instalación y actualización de la aplicación. 

Consulte [solicitar permisos en tiempo de ejecución en Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) para obtener más información acerca de cómo implementar permisos en tiempo de ejecución en aplicaciones de Xamarin.Android (incluidos ejemplos de código).
Xamarin ofrece también una aplicación de ejemplo que ilustra cómo funcionan los permisos de tiempo de ejecución en Android Marshmallow (y versiones posteriores): [RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions).

Esta aplicación de ejemplo muestra lo siguiente:

-   Cómo comprobar y solicitar los permisos en tiempo de ejecución.
-   Cómo declarar los permisos para los dispositivos de Android M.

Para usar esta aplicación de ejemplo:

1.  Pulse el **cámara** o **contactos** botones para mostrar los permisos de un cuadro de diálogo de solicitud.
2.  Conceder permiso para ver los fragmentos de cámara o contactos.

Para obtener más información sobre las nuevas características de permisos en tiempo de ejecución en Android Marshmallow, consulte [trabajar con permisos del sistema](https://developer.android.com/preview/features/runtime-permissions.html).



### <a name="authentication-enhancements"></a>Mejoras de la autenticación

Android Marshmallow incluye dos mejoras de autenticación que ayudan a eliminar la necesidad de contraseñas:

-   **Autenticación de huella digital** &ndash; usa un examen de huellas digitales para autenticar usuarios.

-   **Confirmar credencial** &ndash; autentica a los usuarios en función de cuánto se ha desbloqueado el dispositivo.

Los vínculos y las aplicaciones de ejemplo que se describe a continuación pueden ayudarle a familiarizarse con estas nuevas características.


#### <a name="fingerprint-authentication"></a>Autenticación con huella digital

En los dispositivos que admiten la búsqueda de hardware de huellas digitales, puede usar el nuevo `FingerPrintManager` clase para autenticar un usuario.
Para obtener más información sobre la característica de autenticación de huella digital en Android Marshmallow, consulte [autenticación con huella digital](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin ofrece una aplicación de ejemplo que ilustra cómo usar las huellas digitales registradas para autenticar un usuario en la aplicación: [FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog).

Para usar esta aplicación de ejemplo:

1.  Tocar la **compra** botón para abrir un cuadro de diálogo de autenticación de huella digital.
2.  Examinar en la huella digital registrada para autenticarse.

Tenga en cuenta que esta aplicación de ejemplo requiere un dispositivo con un lector de huellas digitales.
Esta aplicación no almacena su huella dactilar (o su contraseña).



#### <a name="voice-interactions"></a>Interacciones de voz

La nueva característica de interacciones de voz introducida en Android Marshmallow permite a los usuarios de la aplicación usar su voz para confirmar las acciones y seleccionar de una lista de opciones. Para obtener más información acerca de las interacciones de voz, consulte [información general de la API de interacción de voz](https://developers.google.com/voice-actions/interaction/). 

Consulte [agregar una conversación a la aplicación Android con interacciones de voz](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) para obtener más información acerca de cómo implementar las interacciones de voz en aplicaciones de Xamarin.Android (incluidos ejemplos de código).
Está disponible una aplicación de ejemplo que ilustra cómo usar la API de interacción de voz en una aplicación de Xamarin.Android: [interacciones de voz](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).



#### <a name="confirm-credential"></a>Confirmar la credencial

Con el nuevo *confirmar credencial* característica de Android Marshmallow, puede liberar los usuarios tengan que recordar y escribir las contraseñas de aplicación específica mediante la autenticación en función de cuánto se ha desbloqueado su dispositivo.
Para ello, use la nueva `SetUserAuthenticationValidityDurationSeconds` método de la `KeyGenerator`. Use la `KeyGuardManager`del `CreateConfirmDeviceCredentialIntent` método para volver a autenticar al usuario desde dentro de la aplicación. Para obtener más información acerca de esta nueva característica de Android Marshmallow, consulte [confirmar credencial](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin ofrece una aplicación de ejemplo que ilustra cómo usar las credenciales del dispositivo (por ejemplo, PIN, el patrón o contraseña) en la aplicación: [ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

Para usar esta aplicación de ejemplo:

1.  Configurar una pantalla de bloqueo seguro en el dispositivo (**Secure > seguridad > Screenlock**).
2.  Pulse el **compra** botón y confirme las credenciales de la pantalla de bloqueo seguro.



### <a name="chrome-custom-tabs"></a>Fichas personalizadas de Chrome

Los desarrolladores de aplicaciones enfrentan a una opción cuando un usuario pulsa una dirección URL: la aplicación puede iniciar un explorador o use un explorador en la aplicación según un `WebView`. Ambas opciones presentan desafíos &ndash; iniciar el explorador es un cambio de contexto intensivo que no es personalizable, mientras `WebView`s no compartir el estado con el explorador. También, uso de `WebView`s puede agregar una sobrecarga de mantenimiento adicionales. 

*Pestañas personalizadas de Chrome* posibilita la elegancia y facilidad mostrar sitios Web con la eficacia de Chrome sin necesidad de dejar la aplicación a los usuarios. Esta característica ofrece más control sobre la experiencia del usuario web; de la aplicación lo que las transiciones entre nativo y contenido más sencilla de web sin tener que recurrir a un `WebView`. La aplicación también puede afectar a cómo Chrome parece y siente personalizando el siguiente: 

-   Color de barra de herramientas

-   ENTRAR y salir de las animaciones

-   Acciones personalizadas en el menú de barra de herramientas y el desbordamiento de Chrome

-   Captura previa de contenido y de inicio previo (para una carga más rápida) de Chrome

Para aprovechar las ventajas de esta característica en la aplicación de Xamarin.Android, descargue e instale el [Android soporte personalizado pestañas biblioteca](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Para obtener más información sobre esta característica, consulte [pestañas personalizadas de Chrome](https://developer.chrome.com/multidevice/android/customtabs).



### <a name="material-design-support-library"></a>Biblioteca de compatibilidad de material Design

Android Lollipop introducida [Material Design](http://www.google.com/design/spec/material-design/introduction.html) como un nuevo lenguaje de diseño para actualizar la experiencia en Android (consulte [Material tema](~/android/user-interface/material-theme.md) para obtener información sobre cómo usar el diseño material en aplicaciones de Xamarin.Android). Con Android Marshmallow, Google introdujo el *Android diseño Support Library* para facilitar la aplicación a los desarrolladores a adoptar el material de diseñan la apariencia y comportamiento. Esta biblioteca incluye los siguientes componentes:

-   **CoordinatorLayout** &ndash; nuevo `CoordinatorLayout` widget es similar a pero más eficaces que una `FrameLayout`. Puede usar `CoordinatorLayout` como un contenedor para las vistas secundarias o como un diseño de nivel superior y se proporciona un `layout_anchor` atributo que se puede usar para las vistas de anclaje en relación con otras vistas.

-   **Contraer las barras de herramientas** &ndash; nuevo `CollapsingToolbarLayout` es una contracción barra de aplicación que es un contenedor para `Toolbar`. (Tenga en cuenta que el *barra de la aplicación* es lo que anteriormente se conocía como el *barra de acciones*.)

-   **Botón de acción de flotante** &ndash; un botón redondo que denota la acción principal en la interfaz de la aplicación.

-   **Flotante etiquetas para editar texto** &ndash; usa un nuevo `TextInputLayout` widget (el ajusta `EditText`) para mostrar una etiqueta flotante cuando se oculta una sugerencia cuando un usuario escribe texto.

-   **Vista de navegación** &ndash; nuevo `NavigationView` widget le permitirá usar el cajón de navegación de manera que es más fácil para los usuarios a navegar.

-   **Snackbar** &ndash; nuevo `SnackBar` widget es un mecanismo de comentarios ligeros (similar a una notificación del sistema) que muestra un mensaje breve en la parte inferior de la pantalla, que aparecen encima de los otros elementos de la pantalla.

-   **Pestañas materiales** &ndash; nuevo `TabLayout` widget proporciona un diseño horizontal para mostrar fichas como forma de implementar la navegación de nivel superior en la aplicación.

Para aprovechar la [diseño Support Library](http://developer.android.com/tools/support-library/features.html#design) en la aplicación de Xamarin.Android, descargue e instale Xamarin [diseño de bibliotecas de compatibilidad con Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) paquete NuGet.

Consulte [hermoso diseño Material con la biblioteca de diseño de compatibilidad con Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) para obtener más detalles sobre el uso de la biblioteca de compatibilidad de diseño de Material en aplicaciones de Xamarin.Android (incluidos ejemplos de código).
Xamarin ofrece una aplicación de ejemplo que demuestra la nueva biblioteca de diseño de Android en Xamarin.Android &ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare).
Este ejemplo muestra las siguientes características de la biblioteca de diseño:


-   Contraer barra de herramientas
-   Botón de acción flotante
-   Delimitación de vista
-   Control NavigationView
-   Snackbar

Para obtener más información acerca de la biblioteca de diseño, vea [Android diseño Support Library](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html) en el blog del desarrollador de Android.


### <a name="additional-library-updates"></a>Actualizaciones de biblioteca adicionales

Además de Android Marshmallow, Google ha anunciado actualizaciones relacionadas para varias bibliotecas de core Android. Xamarin ofrece compatibilidad con Xamarin.Android estas actualizaciones a través de varios paquetes de NuGet de la versión de vista previa: 

-   [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; la versión más reciente de Google Play Services incluye la nueva *invitaciones a la aplicación* característica, lo que permite a los usuarios compartir su aplicación con sus amigos. Para obtener más información sobre esta característica, consulte [alcance expanda la aplicación con invitaciones a la aplicación de Google](http://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

-   [Android Support Libraries](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; estos paquetes de NuGet ofrecen características que solo están disponibles para la API de biblioteca al tiempo que proporciona las versiones compatibles con versiones anteriores de framework Android API. 

-   [Biblioteca ponibles Android](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; este NuGet incluye enlaces de Google Play Services. La versión más reciente de la biblioteca de portátiles aporta nuevas características (incluido una navegación más sencilla para aplicaciones personalizadas) a la plataforma Android Wear. 


## <a name="summary"></a>Resumen

En este artículo introdujo Android Marshmallow y se explica cómo instalar y configurar las últimas herramientas y paquetes para el desarrollo de Xamarin.Android en Marshmallow. También se proporciona información general sobre las nuevas características de Android Marshmallow más interesantes para el desarrollo de Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html)
- [Obtenga el SDK de Android](https://developer.android.com/sdk/index.html#Other)
- [Introducción a las características](https://developer.android.com/preview/api-overview.html)
- [Notas de la versión](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions (ejemplo)](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential (ejemplo)](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog (ejemplo)](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
