---
title: Introducción a tvOS 9
description: Este artículo detallan todas las características disponibles en tvOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: c3c278666c5d57d00b4038ae6d3f2d7925e88537
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789029"
---
# <a name="introduction-to-tvos-9"></a>Introducción a tvOS 9

_Este artículo detallan todas las características disponibles en tvOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS._

Apple ha lanzado la generación 4th del hardware de Apple TV, que incluye un rediseñada, táctil Habilitar control remoto, ejecuta el nuevo sistema operativo tvOS (basado en iOS 9).

En la primera vez, tvOS, se abrirá la plataforma de TV de Apple para el desarrollador, lo que permite crear aplicaciones enriquecidas y atractiva y liberarlos a través de la tienda de aplicaciones de Apple TV integrada en un proceso similar a la experiencia de escritura y la liberación de aplicaciones para iOS mediante iTunes App Almacén.

Si está familiarizado con el desarrollo de Xamarin.iOS, debe buscar la transición a tvOS bastante simple. La mayoría de las API y características es los mismos, sin embargo, no están disponibles (por ejemplo, WebKit) muchas API comunes. Además, trabajar con el con el control remoto Siri presenta algunos desafíos de diseño que no están presentes en los dispositivos de iOS en función de pantalla táctil.

Esta guía le proporcionará una introducción a todas las características disponibles en tvOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS. Para obtener más información sobre tvOS, vea de Apple [desarrollar para el nuevo Televisor de Apple](https://developer.apple.com/tvos/) documentación.

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Capacidades admitidas y no admitidas

tvOS aplicaciones que se ejecutan en el Televisor de Apple se admiten los siguientes tipos capacidades y características:

 - Grupos de aplicaciones
 - Modos en segundo plano
 - Protección de datos
 - Game Center
 - Dispositivos de juego
 - iCloud
 - En la aplicación de compras
 - Uso compartido de cadenas de claves

No se admiten las siguientes características y capacidades:

 - Apple Pay
 - Espacio aislado de la aplicación
 - Dominios asociados
 - HealthKit
 - HomeKit
 - Inter-App Audio
 - Asignaciones
 - VPN personal
 - Notificaciones push
 - Cartera
 - Configuración inalámbrica de accesorios

Visite nuestro [admite ensamblados](~/ios/tvos/internals/assemblies.md) y [admite marcos](~/ios/tvos/internals/frameworks.md) documentación para obtener más información.

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Hardware de TV de Apple

El nuevo Televisor de Apple tiene las siguientes especificaciones de hardware:

 - procesador de 64 bits A8
 - 32 o 64GB de almacenamiento
 - 2GB de RAM
 - 10/100 Mbps Ethernet
 - 802.11a/b/g/n/ac Wi-Fi
 - resolución de 1.080 p
 - HDMI
 - Puerto de C de USB (para el desarrollador y uso del diagnóstico solo)
 - Nueva Siri remoto o Apple TV remoto (basado en la región)

### <a name="siri-remote"></a>Siri remoto

En función de la región, la TV Apple proporcionado remoto aparecerá en cualquier configuraciones uno: Siri remoto o Apple TV remoto.

La instancia remota de Siri está disponible actualmente en los países siguientes:

 - Australia
 - Canadá
 - Francia
 - Alemania
 - Japón
 - España
 - Reino Unido
 - Estados Unidos

Todos los otros países recibirán Apple TV remoto que reemplaza al botón Siri con un botón de búsqueda, se abrirá la pantalla de búsqueda predeterminada con la entrada de texto para buscar:

[![](tvos9-images/remote02.png "Siri remoto")](tvos9-images/remote02.png#lightbox)

Para obtener más información, vea nuestra [Siri remoto y controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentación.

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Aprovisionamiento de TV de Apple

Al igual que desarrollar para iOS, el nuevo tvOS requerirá el perfil de aprovisionamiento correcto para el desarrollo y distribución basada en la pertenencia al equipo y la firma de identidades que ya se ha establecido con Apple.

También es necesario obtener acceso a características de tvOS como iCloud KVS o almacenes de datos CloudKit aprovisionamiento correcto. Visite nuestro [recursos y el almacenamiento de datos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obtener información sobre la compatibilidad de iCloud en las aplicaciones de Xamarin.tvOS.

Perfiles de aprovisionamiento se crean y se instala de la misma manera que trabajar con aplicaciones de Xamarin.iOS. Por lo tanto, visite nuestro iOS [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) documentación para obtener más detalles.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Aplicaciones de TV de Apple

El nuevo hardware de Apple TV y tvOS 9 admite dos tipos de aplicaciones: tradicionales y las aplicaciones cliente / servidor.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>Aplicaciones tradicionales

Las aplicaciones tradicionales se adquieren de TV App Store de Apple y se instalan directamente en el dispositivo. Estas aplicaciones pueden juegos, utilidades o aplicaciones multimedia que se desarrollan utilizando los mismos marcos de trabajo y técnicas como aplicaciones de Xamarin.iOS.

Aplicaciones de Apple TV tienen un tamaño máximo de 200MB y pueden descargar 2GB adicionales de contenido mediante los recursos a petición. Visite nuestro [recursos y el almacenamiento de datos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obtener más información.

Consulte nuestro [Hello, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md) para familiarizarse con las herramientas y conceptos necesarios para desarrollar aplicaciones de tvOS mediante Xamarin.tvOS.

<a name="Summary" />

### <a name="client-server-apps"></a>Aplicaciones cliente / servidor

Además de las aplicaciones tradicionales instaladas, Apple TV resulta muy sencillo crear medios de cliente-servidor basada en web transmisión por secuencias aplicaciones que usan tecnologías de web (HTTPS, XML y JavaScript). Se diseñe la interfaz de usuario mediante el lenguaje de marcado TVML de Apple y usar JavaScript para definir los comportamientos de la aplicación mediante TVMLKit.

Para obtener más información, vea de Apple [referencia del lenguaje de marcado de Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064), [TVJS Framework referencia](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [TVMLKit Framework referencia](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [acerca de HTTP Live Streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) y [especificación de creación de HLS para Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) documentación.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Desafíos de la interfaz de usuario

A diferencia de iOS u OS X, Apple TV no tiene una pantalla táctil o el mouse (ratón) que permite al usuario seleccionar directamente e interactuar con una aplicación o su contenido. En su lugar se usuario remoto Siri nuevo o un dispositivo de juego Bluetooth para navegar por la interfaz de usuario de una aplicación. Para obtener más información, vea nuestra [Siri remoto y controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentación.

Además, la experiencia global del usuario es significativamente diferente de iOS o las aplicaciones de Mac que tienden a ser experiencias de usuario único. Con Apple TV, experiencias de usuario suelen ser más sociales por naturaleza, donde varias personas pueden estar sentado en el sofá interactuar con una única aplicación y entre sí. Para diseñar una experiencia de aplicación de Apple TV correcta (una nueva aplicación o migrar una existente), estos cambios se deben considerar con detenimiento. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Trabajar con el foco y Parallax imágenes

Como se mencionó anteriormente, los usuarios de la aplicación Xamarin.tvOS no pueden interactuar con su interfaz directamente como con iOS que pulse imágenes en la pantalla del dispositivo, pero indirectamente de a través de la habitación mediante el mando a distancia Siri. Para presentar y controlar esta interacción del usuario, Apple TV utiliza un modelo según el foco. 

Como los cambios de foco, sutiles animaciones y efectos (por ejemplo, el efecto Parallax en imágenes) sirven para identificar claramente el elemento de la interfaz de usuario que tiene actualmente el foco.

Si el usuario realiza un movimiento lento, circular en el servidor remoto Siri, el elemento con foco se influir hora de elegir en tiempo real en respuesta a este movimiento. Cuando se realiza el balanceo, un brillo iluminada se aplica a la imagen parezca que la superficie destacar. Después de un período determinado de inactividad, cualquier contenido fuera de foco, éste se atenúa y el elemento enfocado crecerá incluso mayor.

Para obtener más información, vea nuestra [trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y [trabajar con imágenes de iconos y](~/ios/tvos/app-fundamentals/icons-images.md) documentación.

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>La pantalla principal

En la pantalla Inicio TV de Apple muestra todas las aplicaciones que están instaladas y proporciona una manera de tener acceso a las preferencias del usuario:

[![](tvos9-images/home01.png "La pantalla principal")](tvos9-images/home01.png#lightbox)

El usuario navega a una cuadrícula de los iconos de aplicación con gestos táctiles en el mando a distancia Siri con el foco para seleccionar una aplicación y ejecutarlo. El icono de aplicación es el su primer la oportunidad de realizar una impresión excelente en sus usuarios potenciales y debe comunicar el propósito de la aplicación de un vistazo.

Cada aplicación debe proporcionar una pequeña y una versión grande de su icono de la aplicación. Cuando se instala la aplicación, se utilizará el icono pequeño en la pantalla Inicio TV de Apple. Se usa la versión de gran tamaño mediante la tienda de aplicaciones. El icono grande de la aplicación debe imitar la apariencia y funcionamiento de la versión del icono pequeño.

Para obtener más información, vea nuestra [trabajar con imágenes de iconos y](~/ios/tvos/app-fundamentals/icons-images.md) documentación.

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>Estante superior

Si el usuario ha colocado la aplicación Xamarin.tvOS en la fila de la parte superior de la pantalla Inicio TV de Apple, se mostrará una imagen de estante superior grande cuando se selecciona la aplicación por el usuario. Esta imagen debe resaltar las características de la aplicación o proporcionar vínculos directos a su contenido.

[![](tvos9-images/topshelf01.png "Estante superior")](tvos9-images/topshelf01.png#lightbox)

La imagen de estante superior pueden ser proporcionada como una sola static `.png` o `.lsr` o bien, se pueden crearse dinámicamente en tiempo de ejecución como una sola fila de elementos puede recibir el foco.

En lugar de mostrar una imagen estática de estante superior, puede contener una fila dinámica o un conjunto dinámico de desplazamiento banners o elementos puede recibir el foco. Ambos de estos estilos dinámicos le permiten resaltar el contenido proporcionado por la aplicación o un salto en sus características más usados.

Para obtener más información, vea nuestra [trabajar con imágenes de iconos y](~/ios/tvos/app-fundamentals/icons-images.md) documentación y de Apple [TVServices Framework referencia](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) para obtener más información sobre cómo agregar una extensión de estante superior a la aplicación para proporcionar contenido dinámico de estante superior.






## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Compilar aplicaciones tvos con Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
