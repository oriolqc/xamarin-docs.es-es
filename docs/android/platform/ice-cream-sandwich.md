---
title: Características de helado Sandwich
description: En este artículo se describe algunas de las nuevas características disponibles para los desarrolladores de aplicaciones con la API de 4 Android - Sandwich de helado. Abarca varias tecnologías de interfaz de usuario nueva y, a continuación, examina una variedad de nuevas capacidades que ofrece Android 4 para compartir datos entre aplicaciones y dispositivos.
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 3c5412629f6dcd31fb0a82634ef530569eef459a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="ice-cream-sandwich-features"></a>Características de helado Sandwich

_En este artículo se describe algunas de las nuevas características disponibles para los desarrolladores de aplicaciones con la API de 4 Android - Sandwich de helado. Abarca varias tecnologías de interfaz de usuario nueva y, a continuación, examina una variedad de nuevas capacidades que ofrece Android 4 para compartir datos entre aplicaciones y dispositivos._

## <a name="overview"></a>Información general

Android OS versión 4.0 (API nivel 14) representa una revisión principal del sistema operativo Android e incluye una serie de cambios importantes y las actualizaciones, incluyendo:

-   **Interfaz de usuario se actualizó** : varias características nuevas de interfaz de usuario ofrecen a los desarrolladores más potencia y flexibilidad cuando crea el usuario de la aplicación de interfaces. Estas nuevas características incluyen: `GridLayout` , `PopupMenu` , `Switch` widget, y `TextureView` . 
-   **Mejor la aceleración de Hardware** : 2D representación ahora tiene lugar en la GPU para Android todos los controles. Además, aceleración de hardware está activada, de forma predeterminada, en todas las aplicaciones desarrolladas para Android 4.0. 
-   **Nuevas API de datos** : hay nuevo acceso a datos que no estaba previamente oficialmente accesibles, como los datos de calendario y el perfil de usuario del propietario del dispositivo. 
-   **Uso compartido de datos de aplicación** : compartir datos entre aplicaciones y dispositivos es ahora más fácil que nunca a través de tecnologías como la `ShareActionProvider` , lo que facilita el crear una acción de uso compartida de una barra de acciones, y *carretera Android* para *cerca de las comunicaciones de campo (NFC)* , lo que hace que un complemento para compartir datos entre dispositivos muy próximos entre sí. 


En este artículo, vamos a explorar estas características y otros cambios que se realizaron en la API de Android 4.0 y explicaremos cómo usar cada característica con Xamarin.Android.

## <a name="user-interface-features"></a>Características de la interfaz de usuario

Una variedad de nuevas tecnologías de interfaz de usuario están disponibles con Android 4, incluidos:

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  : diseño de cuadrícula 2D admite de los controles. 
-   **[Cambiar el widget](~/android/user-interface/controls/switch.md)**  : permite alternar entre ON u OFF. 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  – permite vídeo y el contenido de OpenGL dentro de una vista. 
-   **[Barra de navegación](~/android/user-interface/controls/navigation-bar.md)**  – contiene botones virtual para la copia principal y multitarea. 


Además, otros elementos de interfaz de usuario se han mejorado, como el `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`, que ahora es más fácil trabajar con y las pestañas, que tienen un aspecto más pulido.

## <a name="sharing-features"></a>Características de uso compartido

Android 4 incluye varias tecnologías nuevas que nos permiten compartir datos a través de los dispositivos y aplicaciones. También proporciona acceso a varios tipos de datos que no estaban disponibles anteriormente, como la información del calendario y perfil de usuario del propietario del dispositivo. En esta sección que examinaremos una variedad de características que ofrece Android 4 esa dirección estas áreas como:

-  **[Haz Android](~/android/platform/android-beam.md)**  : datos permite compartir a través de CNC.
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  – crea un proveedor que permite a los desarrolladores especificar acciones de uso compartidas de la barra de acciones. 
-   **[Perfil de usuario](~/android/user-interface/user-profile.md)**  : proporciona acceso a los datos de perfil del propietario del dispositivo. 
-   **[API de calendario](~/android/user-interface/controls/calendar.md)**  : proporciona acceso a datos del calendario del proveedor de calendario. 

## <a name="x86-emulators"></a>x86 emuladores

ICS no admite el desarrollo con un x86 emulador. x86 emuladores solo son compatibles con Android 2.3.3, nivel de API 10. Vea [configurar el x86 emulador](~/android/get-started/installation/android-emulator/index.md) para obtener más información.

## <a name="summary"></a>Resumen

En este artículo se trata una variedad de las nuevas tecnologías que están disponibles con 4 Android. Revisamos nuevas características de interfaz de usuario como la *GridLayout*, *menú emergente*, y *conmutador* widget. También explicamos algunas de la nueva compatibilidad para controlar el sistema de la interfaz de usuario, y cómo trabajar con el *TextureView*. A continuación, explicamos una variedad de nuevas tecnologías de uso compartidas. Se trata cómo *carretera Android* vamos a compartir información a través de los dispositivos que usan *NFC*, se describe la nueva *API de calendario*y también se ha explicado cómo usar el elemento integrado en  *ShareActionProvider*.
Por último, se examina cómo usar el *ContactsContract* proveedor de acceso a datos de perfil de usuario.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de helado Sandwich](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo (ejemplo)](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [Pestaña diseño Tutorial](~/android/user-interface/layouts/tab-layout/index.md)
- [Sandwich de helado](http://developer.android.com/about/versions/android-4.0-highlights.html)
- [Plataforma 4.0 Android](http://developer.android.com/about/versions/android-4.0.html)
