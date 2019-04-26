---
title: Introducción a tvOS 9
description: Este artículo presenta todas las características disponibles en tvOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: dda197f71b2a2ab3e0d61a838ab85d79b7a078c7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270349"
---
# <a name="introduction-to-tvos-9"></a>Introducción a tvOS 9

_Este artículo presenta todas las características disponibles en tvOS 9 y las API nuevas y modificadas para desarrolladores de Xamarin.tvOS._

Apple ha lanzado la 4ª generación del hardware de Apple TV, que incluye un rediseñada, táctil Habilitar control remoto, ejecute el nuevo sistema operativo de tvOS (basado en iOS 9).

Por primera vez, abre la plataforma de Apple TV para el desarrollador, lo que permite crear aplicaciones sofisticadas y envolventes y liberarlos a través el Store de Apple TV integrada aplicación en un proceso similar a la experiencia de escritura y lanzar aplicaciones para iOS mediante iTunes App tvOS Store.

Si está familiarizado con el desarrollo de Xamarin.iOS, debería encontrar la transición a tvOS bastante simple. La mayoría de las API y características es los mismos, sin embargo, muchas API comunes están disponibles (por ejemplo, WebKit). Además, trabajar con el con el elemento remoto Siri plantea algunos desafíos de diseño que no están presentes en los dispositivos de pantalla táctil en función de iOS.

Esta guía le proporcionará una introducción a todas las características disponibles en tvOS 9 y las API nuevas y modificadas para los desarrolladores Xamarin.tvOS. Para obtener más información sobre tvOS, consulte Apple [desarrollar para el nuevo Televisor Apple](https://developer.apple.com/tvos/) documentación.

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Capacidades admitidas y no admitidas

aplicaciones de tvOS que se ejecutan en Apple TV ha admite lo siguiente y características y capacidades:

 - Grupos de aplicaciones
 - Modos en segundo plano
 - Protección de datos
 - Game Center
 - Dispositivos de juego
 - iCloud
 - Adquisición de la aplicación
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

Consulte nuestra [admite ensamblados](~/ios/tvos/internals/assemblies.md) y [admite marcos](~/ios/tvos/internals/frameworks.md) documentación para obtener más información.

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Hardware de Apple TV

El nuevo Televisor Apple tiene las siguientes especificaciones de hardware:

 - procesador de 64 bits A8
 - GB de 32 o 64GB de almacenamiento
 - 2GB de RAM
 - 10/100 Mbps Ethernet
 - WiFi 802.11a/b/g/n/ac
 - resolución de 1.080 p
 - HDMI
 - Puerto C de USB (para los desarrolladores y solo use diagnóstico)
 - Nuevo Siri Remote o Apple TV remoto (según la región)

### <a name="siri-remote"></a>Siri remoto

Según la región, la TV Apple proporcionado remoto se incluirá en cualquier una configuración: Siri Remote o Apple TV remoto.

Siri remoto está actualmente disponible en los siguientes países:

 - Australia
 - Canadá
 - Francia
 - Alemania
 - Japón
 - España
 - Reino Unido
 - Estados Unidos

Todos los demás países recibirán Apple TV remota que reemplaza el botón de Siri con un botón de búsqueda, se abrirá la pantalla de búsqueda de forma predeterminada con la entrada de texto para la búsqueda:

[![](tvos9-images/remote02.png "Siri remoto")](tvos9-images/remote02.png#lightbox)

Para obtener más información, consulte nuestra [Siri Remote y Bluetooth controladores](~/ios/tvos/platform/remote-bluetooth.md) documentación.

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Aprovisionamiento de Apple TV

Al igual que el desarrollo para iOS, el nuevo tvOS requerirá al perfil de aprovisionamiento correcto para el desarrollo y distribución basada en la pertenencia al equipo y las identidades de firma que ya se ha establecido con Apple.

También es necesario tener acceso a características de tvOS como iCloud KVS o almacenes de datos de CloudKit aprovisionamiento correcto. Consulte nuestra [recursos y almacenamiento de datos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obtener información sobre la compatibilidad con iCloud en sus aplicaciones Xamarin.tvOS.

Perfiles de aprovisionamiento se crean y se instala la misma manera que trabajar con aplicaciones de Xamarin.iOS. Por lo tanto, consulte nuestra iOS [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) documentación para obtener más detalles.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Aplicaciones de TV de Apple

El nuevo hardware de Apple TV y tvOS 9 admite dos tipos de aplicaciones: tradicionales y las aplicaciones cliente / servidor.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>Aplicaciones tradicionales

Las aplicaciones tradicionales se adquieren desde el Store de Apple TV App y se instalan directamente en el dispositivo. Estas aplicaciones pueden ser juegos, utilidades o aplicaciones multimedia que se desarrollan utilizando los mismos marcos y técnicas como las aplicaciones de Xamarin.iOS.

Aplicaciones de Apple TV tienen un tamaño máximo de 200MB y pueden descargar los 2GB adicionales de uso de recursos y a petición de contenido. Consulte nuestra [recursos y almacenamiento de datos](~/ios/tvos/app-fundamentals/resources-data-storage.md) para obtener más información.

Consulte nuestra [Hola, Guía de inicio rápido de tvOS](~/ios/tvos/get-started/hello-tvos.md) familiarizarse con las herramientas y los conceptos necesarios para desarrollar aplicaciones de tvOS con Xamarin.tvOS.

<a name="Summary" />

### <a name="client-server-apps"></a>Aplicaciones cliente / servidor

Además de las aplicaciones tradicionales de instaladas, Apple TV resulta fácil crear aplicaciones con las tecnologías web (HTTPS, XML y JavaScript) de streaming de multimedia de cliente-servidor basada en web. Se diseña la interfaz de usuario mediante el lenguaje de marcado TVML de Apple y usar JavaScript para definir los comportamientos de la aplicación mediante TVMLKit.

Para obtener más información, consulte Apple [referencia del lenguaje de marcado de Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064), [TVJS Framework referencia](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [TVMLKit Framework referencia](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [acerca de HTTP Live Streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) y [especificación de creación de HLS para Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) documentación.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Desafíos de la interfaz de usuario

A diferencia de iOS o OS X, Apple TV no tiene una pantalla táctil o mouse que permite al usuario seleccionar directamente e interactuar con una aplicación o su contenido. En su lugar se usuario remoto Siri nuevo o un dispositivo de juego Bluetooth para navegar por la interfaz de usuario de una aplicación. Para obtener más información, consulte nuestra [Siri Remote y Bluetooth controladores](~/ios/tvos/platform/remote-bluetooth.md) documentación.

Además, la experiencia global del usuario es significativamente diferente de aplicaciones iOS o Mac que tienden a ser experiencias de usuario único. Con Apple TV, experiencias de usuario suelen ser más sociales por naturaleza, donde varias personas pueden estar en el sofá interactuar con una sola aplicación y entre sí. Para diseñar una experiencia de aplicación de Apple TV correcta (una nueva aplicación o migrando uno existente), estos cambios deben tenerse en cuenta. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Trabajar con foco e imágenes de paralaje

Como se indicó anteriormente, los usuarios de la aplicación Xamarin.tvOS no interactúa con su interfaz directamente como con iOS donde al pulsar imágenes en la pantalla del dispositivo, pero indirectamente desde a distancia mediante el control remoto de Siri. Para presentar y controlar esta interacción del usuario, Apple TV usa un modelo de enfoque en función. 

Como los cambios de foco, sutiles animaciones y efectos (por ejemplo, el efecto de paralaje en imágenes) se usan para identificar claramente el elemento de interfaz de usuario que tiene actualmente el foco.

Si el usuario realiza un movimiento lento, circular en el equipo remoto Siri, el elemento enfocado se sway en tiempo real en respuesta a este movimiento. Cuando se produce el balanceo, se aplica un brillo iluminada a su imagen parezca la superficie destacar. Después de un período determinado de inactividad, cualquier contenido fuera de foco se atenúa y el elemento enfocado crecerá incluso mayor.

Para obtener más información, consulte nuestra [trabajar con navegación y foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y [trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) documentación.

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>La pantalla principal

La pantalla de Apple TV inicio muestra todas las aplicaciones que están instaladas y proporciona una manera de obtener acceso a las preferencias del usuario:

[![](tvos9-images/home01.png "La pantalla principal")](tvos9-images/home01.png#lightbox)

El usuario navega a una cuadrícula de los iconos de aplicación mediante gestos táctiles en el control remoto de Siri con el foco para seleccionar una aplicación e inícielo. El icono de aplicación es el por primera vez en un sorteo para realizar una impresión excelente en sus usuarios potenciales y debe comunicar el propósito de la aplicación con un solo vistazo.

Cada aplicación debe proporcionar una pequeña y una versión grande de su icono de la aplicación. El icono pequeño que se usará en la pantalla principal de TV de Apple cuando se instala la aplicación. Store de la aplicación utiliza la versión de gran tamaño. El icono grande de la aplicación debe imitar la apariencia y comportamiento de la versión del icono pequeño.

Para obtener más información, consulte nuestra [trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) documentación.

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>El estante superior

Si el usuario ha colocado la aplicación Xamarin.tvOS en la fila superior en la pantalla principal de TV de Apple, se mostrará una imagen de estante superior grande cuando se selecciona la aplicación por el usuario. Esta imagen debe resaltar las características de la aplicación o proporcionar vínculos directos a su contenido.

[![](tvos9-images/topshelf01.png "El estante superior")](tvos9-images/topshelf01.png#lightbox)

La imagen del estante superior o bien puede proporcionarse como una única static `.png` o `.lsr` o bien, se pueden crearse dinámicamente en tiempo de ejecución como una sola fila de elementos puede recibir el foco.

En lugar de mostrar una imagen estática de estante superior, puede contener una fila dinámica o recibir el foco de los elementos o un conjunto dinámico de banners de desplazamiento. Ambos de estos estilos dinámicos permiten resaltar el contenido proporcionado por la aplicación o de salto en sus características más usados.

Para obtener más información, consulte nuestra [trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) documentación y Apple [TVServices Framework referencia](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) para obtener más información sobre cómo agregar una extensión del estante superior a la aplicación para proporcionar contenido dinámico de estante superior.






## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Creación de aplicaciones para tvOS con Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
