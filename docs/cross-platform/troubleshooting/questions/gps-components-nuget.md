---
title: Unificación de Google Play Services componentes y NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 3f5c5f75ae1c7a44537afa59ff4a15d54b1df50b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351488"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unificación de Google Play Services componentes y NuGet

## <a name="history"></a>Historial

Solía ser varios componentes de Google Play Services y paquetes de NuGet:

-   Servicios de Google Play (Froyo)
-   Servicios de Google Play (monigote)
-   Google Play Services (ICS)
-   Servicios de Google Play (JellyBean)
-   Servicios de Google Play (KitKat)

.Jar de Google realmente sólo incluye dos archivos de Google Play Services:

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

La discrepancia daba porque nuestras herramientas correctamente lo hubiéramos `aapt.exe` ¿cuál era el máximo nivel de API de recursos que se usará para una aplicación determinada. Esto significaba, hemos recibido errores de compilación si se ha intentado utilizar el enlace de Google Play Services (KitKat) en un nivel inferior de la API, como el jengibre.

## <a name="unifying-google-play-services"></a>Unificación de Google Play Services

En las versiones más recientes de Xamarin.Android, ahora podemos indicar `aapt.exe` qué versión de máximo de recursos para usar, por lo que este problema desaparece para nosotros.

Esto significa, no hay ningún motivo real para tener paquetes independientes para Monigote/ICS/JellyBean/KitKat (sin embargo, todavía necesitamos un enlace independiente para Froyo, ya que es un archivo .jar diferentes por completo).

Para facilitar las cosas para los desarrolladores, nos hemos ahora nuestros componentes y unificada NuGet paquetes en dos:

-   Google Play Services (Froyo) (enlaza `google-play-services-froyo.jar`)
-   Google Play Services (enlaza `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>¿Cuál debe usarse?

En casi todos los casos, se deben utilizar servicios de Google Play. La única razón para usar el paquete (Froyo) es si activamente destino Froyo. La única razón que existe el archivo .jar independiente de Google es, ya que es Froyo en un pequeño porcentaje de dispositivos, ellos mismos han decidido dejar de admitir, por lo que este archivo .jar es una instantánea inmovilizada, no compatible de Google Play Services.

### <a name="note-about-gingerbread"></a>Nota sobre Monigote

Monigote no tiene fragmento admiten de forma predeterminada y, por este motivo, algunas de las clases en el enlace no podrá utilizarse en una aplicación en tiempo de ejecución en un dispositivo de jengibre. Las clases como `MapFragment` no funcionará en jengibre y debe usarse en su lugar su variante de soporte técnico `SupportMapFragment`. Depende de saber que se va a usar al desarrollador. Esta incompatibilidad se indica por Google en su documentación de Google Play Services.

### <a name="what-happens-to-the-old-componentsnugets"></a>¿Qué ocurre con la antigua componentes/de NuGet?

Puesto que ya no son necesarias, tenemos deshabilitado/Delisted los siguientes componentes y paquetes de NuGet:

-   Servicios de Google Play (monigote)
-   Servicios de Google Play (JellyBean)
-   Servicios de Google Play (KitKat)

Existente _Google Play Services (ICS)_ componente/Nuget ha cambiado a _Google Play Services_ y se mantendrá al día a partir de ahora. Todos los proyectos que hacen referencia a uno de los paquetes deshabilitado/Delisted deben actualizarse para utilizar este.

Los componentes deshabilitados seguirán existan y deben ser eliminados para los proyectos que todavía se utilizan en, para evitar la interrupción de ellos. Del mismo modo los paquetes de NuGet delisted seguirán existan y se pueden restaurar. No se actualizará en el futuro.
