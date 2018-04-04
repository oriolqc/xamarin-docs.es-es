---
title: Características de marshmallow
description: En este artículo le ayuda a empezar a usar en uso Xamarin.Android para desarrollar aplicaciones para Android Marshmallow 6.0.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: d2150e18a377d61a2e79fabfc845f57cfab8a5c7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="marshmallow-features"></a>Características de marshmallow

_En este artículo le ayuda a empezar a usar en uso Xamarin.Android para desarrollar aplicaciones para Android Marshmallow 6.0._

En este artículo se proporciona un esquema de las nuevas características de Android Marshmallow 6.0, explica cómo preparar Xamarin.Android para el desarrollo de Android Marshmallow y proporciona vínculos a aplicaciones de ejemplo que muestran cómo utilizar nueva Android Marshmallow características en aplicaciones de Xamarin.Android. 


## <a name="overview"></a>Información general

[Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html), es el siguiente Android principal versión después círculo Android.
Xamarin.Android admite Android Marshmallow e incluye:

-   **API 23/Android 6.0 enlaces** &ndash; Android 6.0 agrega muchas nuevas API para las nuevas características que se describen a continuación; estas API están disponibles para las aplicaciones Xamarin.Android cuando el destino 23 de nivel de API. Para obtener más información acerca de la API de Android 6.0, consulte [API de Android 6.0](http://developer.android.com/preview/api-overview.html). 

[![Imágenes de héroe de tabletas y teléfonos ejecuta Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Aunque la versión de Marshmallow se centra principalmente en "polaco y calidad", también proporciona muchas características nuevas de interés para los desarrolladores de Xamarin.Android. Estas características incluyen: 

-   **En tiempo de ejecución permisos** &ndash; esta mejora permite a los usuarios aprobar los permisos de seguridad en caso por caso en tiempo de ejecución. 

-   **Mejoras de autenticación** &ndash; a partir de Android Marshmallow, aplicaciones ahora pueden usar los sensores de huellas digitales para autenticar usuarios y un nuevo *confirmar credencial* característica reduce la necesidad de escribir contraseñas. 

-   **Vinculación de aplicación** &ndash; esta característica ayuda a eliminar la necesidad de tener la **aplicación selector** lista emergente asociando automáticamente aplicaciones con dominios web. 

-   **Dirigir el recurso compartido** &ndash; puede definir *dirigir destinos de recurso compartido* que facilitar el uso compartido rápida e intuitiva para los usuarios; esta característica permite uers compartir contenido con otras aplicaciones. 

-   **Interacciones de voz** &ndash; esta nueva API permite crear características de conversación de voz en su aplicación. 

-   **Modo de presentación de 4 K** &ndash; en Android Marshmallow, la aplicación puede solicitar la resolución de pantalla de un 4 K en hardware lo admite. 

-   **Nuevas características de Audio** &ndash; a partir de Marshmallow, Android ahora es compatible con el protocolo de MIDI. También proporciona nuevas clases para crear objetos de reproducción y captura de audio digital y ofrece nuevos enlaces de API para asociar los dispositivos de audio y de entrada. 

-   **Nuevas características de vídeo** &ndash; Marshmallow proporciona una clase nueva que le ayuda a aplicaciones de la representación de secuencias de audio y vídeo sincronizados; de esta clase también proporciona compatibilidad para que la velocidad de reproducción dinámicas. 

-   **Android for Work** &ndash; Marshmallow incluye controles mejorados para los dispositivos corporativos, usuario único. Se admite la instalación silenciosa y la desinstalación de aplicaciones por el propietario del dispositivo, la aceptación automática de actualizaciones del sistema, la administración de certificados mejorado, seguimiento de datos de uso, la administración de permisos y las notificaciones de estado de trabajo. 

-   **Biblioteca de compatibilidad de diseño material** &ndash; nuevo *biblioteca de compatibilidad de diseño* proporciona los componentes de diseño y patrones que facilita el diseño de Material apariencia y funcionamiento de compilación en la aplicación. 

Además, muchas actualizaciones de biblioteca de Android básica se lanzaron con Android M, y estas actualizaciones proporcionan características nuevas para Android m. y las versiones anteriores de Android.

Además, muchas actualizaciones de biblioteca de Android básica se lanzaron con Android Marshmallow y estas actualizaciones proporcionan características nuevas para Android Marshmallow y versiones anteriores de Android. Este artículo explica cómo empezar a generar aplicaciones con Android Marshmallow y proporciona que una visión general de la nueva característica se resalta en Android 6.0. 

## <a name="requirements"></a>Requisitos

Para usar las nuevas características de Android Marshmallow en aplicaciones basadas en Xamarin, es necesario lo siguiente: 

-   **Xamarin.Android** &ndash; Xamarin.Android 5.1.7.12 o posterior debe estar instalado y configurado con Visual Studio o Xamarin Studio.

-   **Visual Studio para Mac** o **Visual Studio** &ndash; si utiliza Visual Studio para Mac, versión 5.9.7.22 o posterior es necesario. Si está usando Visual Studio, versión 3.11.1537 o posterior de las herramientas de Xamarin para Visual Studio es necesaria. 

-   **SDK de Android** &ndash; (API 23) de Android SDK 6.0 o posterior debe estar instalado mediante el Administrador de SDK de Android.

-   **Kit de desarrollo de Java** &ndash; Xamarin.Android requiere [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o posterior si va a desarrollar para el nivel de API 24 o mayor (JDK 1.8 también admite niveles de API anteriores a 24, incluidos los Marshmallow). La versión de 64 bits de JDK 1.8 es necesaria si utiliza controles personalizados o el controlador de vista previa de formularios.

Se puede seguir usando [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si es desarrollar específicamente para el nivel de API 23 o una versión anterior. 


## <a name="getting-started"></a>Introducción

Para empezar a trabajar con Android Marshmallow Xamarin.Android, debe descargar e instalar los paquetes SDK y herramientas más recientes antes de poder crear un proyecto de Android Marshmallow: 

1.  Instalar las últimas actualizaciones de Xamarin desde el **estable** canal. 

2.  Instale las herramientas y SDK de Android 6.0 Marshmallow paquetes.

3.  Cree un nuevo proyecto de Xamarin.Android destinado a Android 6.0 Marshmallow (API nivel 23). 

4.  Configurar un dispositivo o emulador de Android Marshmallow.

Cada uno de estos pasos se explica en las secciones siguientes:


### <a name="install-xamarin-updates"></a>Instalar actualizaciones de Xamarin

Para actualizar Xamarin por lo que incluye compatibilidad para Android Marshmallow 6.0, cambiar el canal de actualización para **estable** e instalar todas las actualizaciones. Para obtener más información acerca de cómo instalar actualizaciones desde el canal actualizaciones, consulte [cambiar el canal actualizaciones](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/). 


### <a name="install-the-android-60-sdk"></a>Instalar el SDK de Android 6.0

Para crear un proyecto Xamarin.Android para Android Marshmallow, primero debe usar el Administrador de Android SDK para instalar el SDK de Android 6.0:

-   Inicie el Administrador de SDK de Android (en Visual Studio para Mac, utilice **Herramientas > Administrador de SDK**; en Visual Studio, use **Herramientas > Android > Administrador de Android SDK**) e instalar las herramientas de SDK de Android más reciente:

    [![Seleccionar herramientas de SDK de Android en el Administrador de SDK de Android](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

-   Además, instale la versión más reciente **Android 6.0** paquetes de SDK:

    [![Seleccionar paquetes de SDK de Android 6.0 en el Administrador de SDK de Android](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Debe instalar la revisión de herramientas de Android SDK 24.3.4 o una versión posterior.
Para obtener más información sobre cómo usar el Administrador de SDK de Android para instalar el SDK de Android 6.0, consulte [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html).



### <a name="start-a-xamarinandroid-project"></a>Iniciar un proyecto Xamarin.Android

Cree un nuevo proyecto de Xamarin.Android. Si está familiarizado con el desarrollo de Android con Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para obtener información acerca de cómo crear proyectos de Android. 

Cuando cree un proyecto Android, debe configurar los valores de versión Android MarshMallow 6.0 de destino. Para el destino del proyecto para Marshmallow, debe configurar el proyecto para **nivel de API 23 (versión 6.0 de Xamarin.Android soporte técnico)**. Para obtener más información acerca de cómo configurar los niveles de nivel de API de Android, consulte [niveles de API de Android descripción](~/android/app-fundamentals/android-api-levels.md).



### <a name="configure-an-emulator-or-device"></a>Configurar un dispositivo o emulador

Si usas un emulador, inicie el Administrador de AVD Android y crear un nuevo dispositivo con la configuración siguiente:

-   Dispositivo: Nexus 5, 6 o 9.
-   Destino: Android 6.0 - nivel de API 23
-   ABI: x86

Por ejemplo, este dispositivo virtual se configura para emular un 5 Nexus:

[![Configurar un AVD mediante dispositivo Nexus 5, destino de Android 6.0 y Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Si está utilizando un dispositivo físico, como un error de Nexus 5, 6 ó 9, puede instalar una imagen de vista previa de Android Marshmallow. Para obtener más información acerca de cómo actualizar el dispositivo para Android Marshmallow, consulte [imágenes de sistema de Hardware](http://developer.android.com/preview/download.html#images).



## <a name="new-features"></a>Características nuevas

Muchos de los cambios introducidos en Android Marshmallow se centran en la mejora de la experiencia de usuario de Android, aumentando el rendimiento y corrige los errores. Sin embargo, Marshmallow también introdujo algunos cambios importantes a los fundamentos de la plataforma Android. En las siguientes secciones resaltar estas mejoras y se proporcionan vínculos para ayudarle a empezar a trabajar en el uso de las nuevas características de Android Marshmallow en la aplicación. 



### <a name="runtime-permissions"></a>Permisos en tiempo de ejecución

El sistema de permisos Android ha optimizado y simplificado desde Android círculo significativamente. En Android Marshmallow, los usuarios conceder permisos en una base de caso por caso en tiempo de ejecución en lugar de en tiempo de la instalación. Para admitir esta característica en Android Marshmallow y versiones posteriores, al diseñar la aplicación para solicitar al usuario permisos en tiempo de ejecución (en el contexto de donde se necesitan los permisos). Este cambio facilita a los usuarios empezar a usar la aplicación inmediatamente porque simplifica el proceso de instalación y actualización de la aplicación. 

Vea [solicitud de permisos en tiempo de ejecución en Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) para más detalles acerca de cómo implementar permisos en tiempo de ejecución en las aplicaciones de Xamarin.Android (incluidos ejemplos de código).
Xamarin ofrece también una aplicación de ejemplo que ilustra cómo funcionan los permisos en tiempo de ejecución en Android Marshmallow (y versiones posteriores): [RuntimePermissions](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions).

Esta aplicación de ejemplo muestra lo siguiente:

-   Cómo comprobar y solicitar permisos en tiempo de ejecución.
-   Cómo declarar los permisos para dispositivos Android m.

Para usar esta aplicación de ejemplo:

1.  Pulse la **cámara** o **contactos** botones que deben mostrarse los permisos de un cuadro de diálogo de solicitud.
2.  Conceder permiso para ver los fragmentos de cámara o contactos.

Para obtener más información sobre las nuevas características de permisos en tiempo de ejecución en Android Marshmallow, consulte [trabajar con permisos del sistema](https://developer.android.com/preview/features/runtime-permissions.html).



### <a name="authentication-enhancements"></a>Mejoras de la autenticación

Android Marshmallow incluye dos mejoras de autenticación que le ayudan a eliminar la necesidad de contraseñas:

-   **Autenticación de huellas digitales** &ndash; utiliza un recorrido de huellas digitales para autenticar a los usuarios.

-   **Confirmar la credencial** &ndash; autentica a los usuarios en función de cuánto tiempo se ha desbloqueado el dispositivo.

Los vínculos y las aplicaciones de ejemplo que se describe a continuación pueden ayudar a familiarizarse con estas nuevas características.


#### <a name="fingerprint-authentication"></a>Autenticación de huellas dactilares

En los dispositivos que admiten la búsqueda de hardware de huellas digitales, puede usar el nuevo `FingerPrintManager` clase para autenticar un usuario.
Para obtener más información acerca de la característica de autenticación de huellas digitales en Android Marshmallow, consulte [autenticación de huellas dactilares](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin ofrece una aplicación de ejemplo que muestra cómo utilizar registrado las huellas digitales para autenticar un usuario de la aplicación: [FingerprintDialog](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog).

Para usar esta aplicación de ejemplo:

1.  Toque el **compra** para abrir un cuadro de diálogo de autenticación de huellas digitales.
2.  Examinar en la huella dactilar registrada para autenticar.

Tenga en cuenta que esta aplicación de ejemplo requiere un dispositivo con un lector de huellas digitales.
Esta aplicación no almacena la huella dactilar (o su contraseña).



#### <a name="voice-interactions"></a>Interacciones de voz

La nueva característica de interacciones de voz que se introdujo en Android Marshmallow permite a los usuarios de la aplicación usar su propia voz para confirmar las acciones y seleccionar de una lista de opciones. Para obtener más información acerca de las interacciones de voz, consulte [información general de la API de interacción de voz](https://developers.google.com/voice-actions/interaction/). 

Vea [agregar una conversación a la aplicación Android con las interacciones de voz](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) para más detalles acerca de cómo implementar las interacciones de voz en aplicaciones de Xamarin.Android (incluidos ejemplos de código).
Hay una aplicación de ejemplo que muestra cómo utilizar la API de interacción de voz en una aplicación Xamarin.Android: [interacciones de voz](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).



#### <a name="confirm-credential"></a>Confirmar la credencial

Con el nuevo *confirmar credencial* característica de Android Marshmallow, puede liberar a los usuarios de tener que recordar y escribir las contraseñas de específico de la aplicación mediante la autenticación en función de cuánto tiempo se ha desbloqueado el dispositivo.
Para ello, use la nueva `SetUserAuthenticationValidityDurationSeconds` método de la `KeyGenerator`. Use la `KeyGuardManager`del `CreateConfirmDeviceCredentialIntent` método para volver a autenticar al usuario desde dentro de la aplicación. Para obtener más información acerca de esta nueva característica de Android Marshmallow, consulte [confirmar credencial](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin ofrece una aplicación de ejemplo que muestra cómo utilizar las credenciales del dispositivo (por ejemplo, PIN, el patrón o contraseña) en la aplicación: [ConfirmCredential](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential/)

Para usar esta aplicación de ejemplo:

1.  La instalación de una pantalla de bloqueo seguro en el dispositivo (**seguro > seguridad > Screenlock**).
2.  Pulse la **compra** botón y confirme las credenciales de la pantalla de bloqueo seguro.



### <a name="chrome-custom-tabs"></a>Pestañas personalizadas de Chrome

Los desarrolladores de aplicaciones enfrentan a una opción cuando un usuario puntee en una dirección URL: la aplicación puede iniciar un explorador o usar un explorador de la aplicación en función de un `WebView`. Ambas opciones presentan desafíos &ndash; iniciar el explorador es un cambio de contexto intensivo que no es personalizable, mientras `WebView`s no compartir el estado con el explorador. Además, uso de `WebView`s pueden producir una sobrecarga adicional de mantenimiento. 

*Chrome personalizado pestañas* posibilita la elegancia y fácilmente mostrar sitios Web con la potencia de Chrome sin necesidad de dejar su aplicación a los usuarios. Esta característica proporciona la aplicación mayor control sobre la experiencia del usuario web; Asegúrese de transiciones entre nativo y más sencilla de contenido web, sin tener que recurrir a una `WebView`. La aplicación también puede afectar a cómo Chrome busca y se siente personalizando lo siguiente: 

-   Color de barra de herramientas

-   ENTRAR y salir de animaciones

-   Acciones personalizadas en el menú de barra de herramientas y un desbordamiento de Chrome

-   Chrome inicio previo y contenido de la captura previa (para una carga más rápida)

Para aprovechar las ventajas de esta característica en la aplicación Xamarin.Android, descargue e instale el [Android biblioteca de pestañas de soporte técnico personalizada](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Para obtener más información sobre esta característica, consulte [Chrome personalizado pestañas](https://developer.chrome.com/multidevice/android/customtabs).



### <a name="material-design-support-library"></a>Biblioteca de compatibilidad de material de diseño

El círculo Android introducido [Material de diseño](http://www.google.com/design/spec/material-design/introduction.html) como un nuevo lenguaje de diseño para actualizar la experiencia en Android (vea [Material tema](~/android/user-interface/material-theme.md) para obtener información acerca del uso de diseño material en aplicaciones de Xamarin.Android). Con Android Marshmallow, Google introdujo el *Android biblioteca de compatibilidad de diseño* para facilitar la aplicación a los desarrolladores que deben adoptarse en material de diseño apariencia y funcionamiento. Esta biblioteca incluye los siguientes componentes:

-   **CoordinatorLayout** &ndash; nuevo `CoordinatorLayout` widget es similar a pero más eficaces que una `FrameLayout`. Puede usar `CoordinatorLayout` como un contenedor para vistas secundarias o como un diseño de nivel superior y proporciona un `layout_anchor` atributo que puede usarse para vistas de anclaje en relación con otras vistas.

-   **Contraer las barras de herramientas** &ndash; nuevo `CollapsingToolbarLayout` es una contracción barra de la aplicación que es un contenedor de `Toolbar`. (Tenga en cuenta que la *barra de la aplicación* es lo que anteriormente se conocía como el *barra de acciones*.)

-   **Botón de acción de flotante** &ndash; un botón redondo que denota la acción principal en la interfaz de la aplicación.

-   **Flotante etiquetas para editar texto** &ndash; usa un nuevo `TextInputLayout` widget (que encapsula `EditText`) para mostrar una etiqueta de punto flotante cuando una sugerencia está oculto cuando un usuario introduce texto.

-   **Vista de navegación** &ndash; nuevo `NavigationView` widget le permitirá usar el espacio de navegación de una manera que es más fácil para los usuarios se desplacen.

-   **Snackbar** &ndash; nuevo `SnackBar` widget es un mecanismo de comentarios ligera (similar a una notificación del sistema) que muestra un mensaje breve en la parte inferior de la pantalla, que aparecen encima de las demás elementos en la pantalla.

-   **Pestañas material** &ndash; nuevo `TabLayout` widget proporciona un diseño horizontal para mostrar fichas como forma de implementar la exploración de nivel superior en la aplicación.

Para aprovechar la [biblioteca de compatibilidad de diseño](http://developer.android.com/tools/support-library/features.html#design) en la aplicación Xamarin.Android, descargue e instale el Xamarin [Xamarin diseño de la biblioteca de compatibilidad con](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) paquete NuGet.

Vea [maravillosas de Material de diseño con la biblioteca de diseño de compatibilidad con Android](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) para más detalles sobre el uso de la biblioteca de compatibilidad de Material de diseño en las aplicaciones de Xamarin.Android (incluidos ejemplos de código).
Xamarin ofrece una aplicación de ejemplo que hace una demostración de la nueva biblioteca de Android diseño en Xamarin.Android &ndash; [Cheesesquare](https://developer.xamarin.com/samples/monodroid/android5.0/Cheesesquare).
Este ejemplo muestra las siguientes características de la biblioteca de diseño:


-   Contraer la barra de herramientas
-   Botón de acción de punto flotante
-   Delimitación de vista
-   NavigationView
-   Snackbar

Para obtener más información acerca de la biblioteca de diseño, vea [Android biblioteca de compatibilidad de diseño](http://android-developers.blogspot.co.at/2015/05/android-design-support-library.html) en el blog del desarrollador de Android.


### <a name="additional-library-updates"></a>Actualizaciones de biblioteca adicionales

Además de Android Marshmallow, Google ha anunciado actualizaciones relacionadas para Android de varias bibliotecas de core. Xamarin ofrece compatibilidad con Xamarin.Android estas actualizaciones a través de varios paquetes de NuGet de versión de vista previa: 

-   [Servicios de Google Play](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; la última versión de servicios de Google Play incluye la nueva *aplicación invita* característica, lo que permite a los usuarios a compartir su aplicación con amigos. Para obtener más información sobre esta característica, consulte [alcance expanda la aplicación con invita a todas de la aplicación de Google](http://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

-   [Bibliotecas de compatibilidad con Android](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; NuGets estas ofrecen características que solo están disponibles para la API de la biblioteca al proporcionar versiones compatibles con versiones anteriores de framework Android API. 

-   [Android biblioteca Wearable](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; este NuGet incluye enlaces de servicios de Google Play. La versión más reciente de la biblioteca wearable ofrece características nuevas (incluidas una navegación más sencilla para aplicaciones personalizadas) a la plataforma Android desgaste. 


## <a name="summary"></a>Resumen

En este artículo se introdujo Android Marshmallow y se explica cómo instalar y configurar las últimas herramientas y paquetes para el desarrollo de Xamarin.Android en Marshmallow. También proporciona una visión general de las nuevas características de Android Marshmallow más interesantes para el desarrollo de Xamarin.Android.


## <a name="related-links"></a>Vínculos relacionados

- [Android Marshmallow 6.0](http://developer.android.com/about/versions/marshmallow/index.html)
- [Obtener el SDK de Android](https://developer.android.com/sdk/index.html#Other)
- [Introducción a las características](https://developer.android.com/preview/api-overview.html)
- [Notas de la versión](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1.99/)
- [RuntimePermissions (ejemplo)](https://developer.xamarin.com/samples/monodroid/android-m/RuntimePermissions)
- [ConfirmCredential (ejemplo)](https://developer.xamarin.com/samples/monodroid/android-m/ConfirmCredential)
- [FingerprintDialog (ejemplo)](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog)
