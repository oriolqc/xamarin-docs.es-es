---
title: Unificación de Google Play de servicios de componentes y NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: e0ba5ee9417917b834ab060a94f72d1f071b4912
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unificación de Google Play de servicios de componentes y NuGet

### <a name="history"></a>Historial

Solía haber varios componentes de servicios de reproducción de Google y paquetes de NuGet:

-   Servicios de Google Play (Froyo)
-   Servicios de Google Play (monigote)
-   Servicios de Google Play (ICS)
-   Servicios de Google Play (JellyBean)
-   Servicios de Google Play (KitKat)

Archivos .jar de Google realmente sólo incluye dos servicios de Google Play:

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

La discrepancia existía porque nuestras herramientas no indican correctamente `aapt.exe` ¿cuál era el máximo nivel de API de recursos que se usará para una aplicación determinada. Esto ha conllevado, hemos recibido errores de compilación si se intenta utilizar el enlace de servicios de Google Play (KitKat) en un nivel inferior de la API como Monigote.

### <a name="unifying-google-play-services"></a>Servicios de Google Play y unificador

En las versiones más recientes de Xamarin.Android, ahora podemos indicar `aapt.exe` qué versión de máximo de recursos para usar, por lo que este problema desaparece para nosotros.

Es decir, no hay ningún motivo real para paquetes independientes para Monigote/ICS/JellyBean/KitKat (sin embargo, todavía necesitamos un enlace independiente para Froyo ya que es un archivo .jar diferentes por completo).

Para facilitar las cosas para los desarrolladores, nos hemos ahora y unificada de nuestros componentes NuGet paquetes en dos:

-   Servicios de Google Play (Froyo) (enlaza `google-play-services-froyo.jar`)
-   Servicios de Google Play (enlaza `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>¿Cuál desea utilizar?

En casi todos los casos, se debe utilizar servicios de Google Play. La única razón para usar el paquete (Froyo) es si activamente destino Froyo. La única razón que existe el archivo .jar independiente de Google es porque Froyo es en un pequeño porcentaje de dispositivos, ellos mismos han decidido dejar de admitir, por lo que este archivo .jar es una instantánea inmovilizada no compatible de servicios de Google Play.

### <a name="note-about-gingerbread"></a>Nota sobre Monigote

Monigote no tiene fragmento admite de forma predeterminada y, por este motivo, algunas de las clases en el enlace no se podrán usar en una aplicación en tiempo de ejecución en un dispositivo de monigote. Las clases como `MapFragment` no funcionará en Monigote y su variante de soporte técnico debe usarse en su lugar `SupportMapFragment`. Es al desarrollador para que sepa que se va a usar. Esta incompatibilidad se indica por Google en la documentación de servicios de Google Play.

### <a name="what-happens-to-the-old-componentsnugets"></a>¿Qué ocurre con la antigua componentes/de NuGet?

Puesto que ya no son necesarias, tenemos deshabilitado/Delisted el NuGets/componentes siguientes:

-   Servicios de Google Play (monigote)
-   Servicios de Google Play (JellyBean)
-   Servicios de Google Play (KitKat)

Existente _servicios de reproducción de Google (ICS)_ componente/Nuget ha cambiado a _servicios de Google Play_ y se mantienen actualizadas en el futuro. Todos los proyectos que hacen referencia a uno de los paquetes deshabilitado/Delisted deben actualizarse para utilizar este.

Los componentes deshabilitados seguirán existan y se deberían ser pueden restaurarse para los proyectos que todavía se utilizan en, para evitar la ruptura de ellos. De igual forma los paquetes de NuGet delisted seguirán existan y se pueden restaurar. No se actualizará en el futuro.
