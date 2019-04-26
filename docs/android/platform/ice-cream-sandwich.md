---
title: Características de ICE Cream Sandwich
description: En este artículo se describe algunas de las nuevas características disponibles para los desarrolladores de aplicaciones con la API de 4 Android - Ice Cream Sandwich. Se tratan varias tecnologías nuevas de interfaz de usuario y, a continuación, examina una variedad de nuevas capacidades que ofrece Android 4 para compartir datos entre aplicaciones y dispositivos.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 00b553ae8de0dfcd86d57d1d5e3e2a892d6b5463
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61091185"
---
# <a name="ice-cream-sandwich-features"></a>Características de ICE Cream Sandwich

_En este artículo se describe algunas de las nuevas características disponibles para los desarrolladores de aplicaciones con la API de 4 Android - Ice Cream Sandwich. Se tratan varias tecnologías nuevas de interfaz de usuario y, a continuación, examina una variedad de nuevas capacidades que ofrece Android 4 para compartir datos entre aplicaciones y dispositivos._

## <a name="overview"></a>Información general

Android OS versión 4.0 (API nivel 14) representa una adaptación principales del sistema operativo Android e incluye una serie de cambios importantes y las actualizaciones, incluidos:

-   **Actualiza la interfaz de usuario** : varias características nuevas de interfaz de usuario ofrecen a los desarrolladores más potencia y flexibilidad cuando crea el usuario de la aplicación de interfaces. Estas nuevas características incluyen: `GridLayout` , `PopupMenu` , `Switch` widget, y `TextureView` . 
-   **Mejor aceleración de Hardware** : 2D rendering tiene lugar en la GPU para Android todos los controles. Además, la aceleración de hardware es, de forma predeterminada, en todas las aplicaciones desarrolladas para Android 4.0. 
-   **Nuevas API de datos** : hay nuevo acceso a datos que no estaba anteriormente oficialmente accesibles, como datos de calendario y el perfil de usuario del propietario del dispositivo. 
-   **Uso compartido de datos de aplicación** : compartir datos entre aplicaciones y dispositivos es ahora más fácil que nunca a través de tecnologías, como el `ShareActionProvider` , lo que facilita la creación de una acción de uso compartida de una barra de acciones, y *carretera Android* para *cerca de las comunicaciones de campo (NFC)* , lo que hace que un complemento para compartir datos entre dispositivos muy próximos entre sí. 


En este artículo, vamos a explorar estas características y otros cambios que se realizaron en la API de Android 4.0 y explicaremos cómo usar cada característica con Xamarin.Android.

## <a name="user-interface-features"></a>Características de la interfaz de usuario

Una variedad de nuevas tecnologías de interfaz de usuario están disponibles con 4 Android, incluidos:

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  : diseño de cuadrícula 2D admite de controles. 
-   **[Cambie el widget](~/android/user-interface/controls/switch.md)**  : permite alternar entre ON u OFF. 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  : permite el vídeo y el contenido OpenGL dentro de una vista. 
-   **[Barra de navegación](~/android/user-interface/controls/navigation-bar.md)**  – contiene botones virtuales para la copia principal y la multitarea. 


Además, otros elementos de interfaz de usuario se han mejorado, como el `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, que es ahora más fácil trabajar con y pestañas, que tienen un aspecto más refinado.

## <a name="sharing-features"></a>Características de uso compartido

Android 4 incluye varias tecnologías nuevas que nos permiten compartir datos entre dispositivos y aplicaciones. También proporciona acceso a diversos tipos de datos que no estaban disponibles, como la información de calendario y perfil de usuario del propietario del dispositivo. En esta sección que examinaremos una variedad de características que ofrece Android 4 esa dirección estas áreas como:

-  **[Haz Android](~/android/platform/android-beam.md)**  : datos permite compartir mediante NFC.
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  – crea un proveedor que permite a los desarrolladores especificar acciones de uso compartidas de la barra de acciones. 
-   **[Perfil de usuario](~/android/user-interface/user-profile.md)**  : proporciona acceso a datos de perfil del propietario del dispositivo. 
-   **[API de calendario](~/android/user-interface/controls/calendar.md)**  : proporciona acceso a datos de calendario desde el proveedor de calendario. 

## <a name="x86-emulators"></a>x86 emuladores

ICS no admiten aún el desarrollo con un x86 emulador. x86 emuladores solo son compatibles con Android 2.3.3, 10 de nivel de API. Consulte [configurando el x86 emulador](~/android/get-started/installation/android-emulator/index.md) para obtener más información.

## <a name="summary"></a>Resumen

En este artículo trata una variedad de las nuevas tecnologías que ya están disponibles con Android 4. Revisamos nuevas características de interfaz de usuario, como el *GridLayout*, *menú emergente*, y *conmutador* widget. También hemos analizado algunas de la nueva compatibilidad para controlar el sistema de la interfaz de usuario, y cómo trabajar con el *TextureView*. A continuación, explicamos una variedad de nuevas tecnologías para compartir. Se trata cómo *carretera Android* vamos a compartir información entre dispositivos que usan *NFC*, se describe la nueva *API de calendario*y también se ha explicado cómo usar la compilación en  *ShareActionProvider*.
Por último, examinamos cómo utilizar el *ContactsContract* proveedor para acceder a datos de perfil de usuario.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de ICE Cream Sandwich](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo (sample)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [Pestaña diseño Tutorial](~/android/user-interface/layouts/tab-layout/index.md)
- [ICE Cream Sandwich](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plataforma Android de 4.0](https://developer.android.com/about/versions/android-4.0.html)
