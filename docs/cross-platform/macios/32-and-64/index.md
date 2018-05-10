---
title: Consideraciones sobre las plataformas de 32 y 64 bits
description: Consideraciones al elegir como destino arquitecturas de 32 bits y 64 bits para la aplicación
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 223da6b490e09b2fa27ab3bbf8fa123b5fa8070c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="3264-bit-platform-considerations"></a>Consideraciones sobre las plataformas de 32 y 64 bits

Aunque iOS y Mac OS históricamente se admiten aplicaciones de 32 y 64 bits, Apple gradualmente desusado soporte de 32 bits.

A partir de iOS 11, ya no se iniciarán aplicaciones de 32 bits, y [todos los envíos a la tienda de aplicaciones deben ser compatible con 64 bits](https://developer.apple.com/news/?id=06282017b).

A partir de enero de 2018, [nuevas aplicaciones que se envían a la tienda de aplicaciones Mac deben ser compatible con 64 bits](https://developer.apple.com/news/?id=06282017a), se deben actualizar las aplicaciones existentes mediante de 2018 de junio.

API clásica de Xamarin (`XamMac.dll` y `monotouch.dll`) admite solo las aplicaciones de 32 bits. Sin embargo, usar nuevas aplicaciones Xamarin.iOS y Xamarin.Mac el [API unificada](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` y `Xamarin.Mac`) de forma predeterminada, y puede, por tanto, destino de 32 y 64 bits, según sea necesario.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitación de 64 bits la compilación de Xamarin.iOS aplicaciones

> [!WARNING]
> En esta sección se incluye por motivos históricos y para ayudar a mover los proyectos de Xamarin.iOS anteriores a la API unificada y soporte técnico de 64 bits. Todos los nuevos proyectos de Xamarin.iOS usará la API unificada y destino de 64 bits de forma predeterminada.

Para aplicaciones móviles Xamarin.iOS que se han convertido a la API unificada, los desarrolladores deben actualizar manualmente la configuración de compilación a 64 bits de destino:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En el **solución Pad**, haga doble clic en el proyecto de la aplicación para abrir el **Project Options** ventana.
2. Seleccione **iOS compilación**.
3. Para iPhone simulador, en la **admite arquitecturas** de lista desplegable, seleccione **x86\_64** o **i386 + x86\_64**:

   [![Establecer las arquitecturas admitidas en x86\_64 o i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Dispositivos físicos, seleccione uno de los disponibles **ARM64** combinaciones:

   [![Establecer las arquitecturas admitidas en una de las combinaciones de ARM64](Images/Image02.png "configuración admite arquitecturas en una de las combinaciones de ARM64")](Images/Image02-large.png#lightbox)

5. Haga clic en **Aceptar**.
6. Realice una compilación limpia.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En el **el Explorador de soluciones**, haga clic en proyecto de la aplicación y seleccione **propiedades**.
2. Seleccione **iOS compilación**.
3. Para el simulador de iPhone, establezca **admite arquitecturas** como **x86\_64** o **i386 + x86\_64**: 

   [![Establecer las arquitecturas admitidas en x86_64 o i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Dispositivos físicos, seleccione uno de los disponibles **ARM64** combinaciones:
    
   [![Establecer las arquitecturas admitidas en una de las combinaciones de ARM64](Images/VS01.png "configuración admite arquitecturas en una de las combinaciones de ARM64")](Images/VS01-large.png#lightbox)

5. Guarde los cambios.
6. Realice una compilación limpia.

-----

ARMv7s solo es compatible con el procesador de A6 incluido en el iPhone 5 (o superior). ARMv7 código es más rápido y menor que el ARMv6, solo funciona con iPhone 3GS y versiones posteriores y también necesite Apple cuando el destino es el iPad o una versión de iOS mínimo de 5.0. ARMv6 funciona en todos los dispositivos, pero ya no es compatible con el compilador incluido Xcode 4.5 y versiones posteriores. 

ARM64 serán necesarios por Apple al enviar nuevas o actualización de salir de aplicaciones en iTunes App Store y es necesario para admitir iOS 8 en iPhone 6 u otros dispositivos de 64 bits.

Para obtener información completa sobre las capacidades de diversos dispositivos de iOS, visite de Apple [compatibilidad del dispositivo](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) documento.

### <a name="64-bit-and-binary-size-increases"></a>aumenta el tamaño de 64 bits y binario

Durante la transición de Apple de iOS de 32 bits a 64 bits, aplicaciones tendrá que ejecutar en hardware de 32 bits y 64 bits. Por este motivo, API unificada de Xamarin permite a los desarrolladores centrarse en ambos.

Arquitecturas de 32 bits y 64 bits de destino aumentará significativamente el tamaño de una aplicación. Sin embargo, si lo hace por lo que le permitirá ejecutar código optimizado al mismo tiempo que se admiten los dispositivos más antiguos de los dispositivos nuevos.

> [!IMPORTANT]
> Si recibe el siguiente mensaje de error al enviar una aplicación de iOS en iTunes App Store, _"Advertencia ITMS-9000: falta la compatibilidad con 64 bits. A partir de 1 de febrero de 2015, nueva iOS aplicaciones cargadas en la tienda de aplicaciones deben incluir compatibilidad con 64 bits y compilarse con iOS 8 SDK, se incluye en Xcode 6 o posterior. Para habilitar 64 bits en el proyecto, se recomienda utilizar el valor predeterminado de "Arquitecturas estándares" configuración de compilación de Xcode para generar un único valor binario con el código de 32 bits y 64 bits. "_ Si necesita cambiar las arquitecturas compatibles en uno de los contadores **ARM64** combinación (como se muestra arriba), recompile y vuelva a intentarlo.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partir de enero de 2018, todas las nuevas aplicaciones de Mac enviadas a la tienda de aplicaciones Mac deben admitir 64 bits. Las aplicaciones existentes de Mac App Store y sus actualizaciones deben admitir 64 bits a partir de junio de 2018. Vea [debidas un con anuncios de Apple](https://developer.apple.com/news/?id=06282017a) y [una guía que describe cómo actualizar las aplicaciones Xamarin.Mac a 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md).

Los equipos Mac más modernos admiten aplicaciones de 32 bits y 64 bits.   MacOS 10.6 (nieve Leopard) fue el último sistema operativo para que se ejecute en sistemas de 32 bits.   La mayoría de equipos Mac publicada desde 2010 admite ambos sistemas.

A diferencia de iOS, muchos de los marcos de trabajo nuevos introducidos en versiones recientes de macOS solo se admiten en modo de 64 bits (CloudKit, EventKit, dispositivo, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, sociales, y MapKit, entre otros).

La API unificada permiten a los desarrolladores elegir qué tipo de aplicaciones desean generar: 32 bits o 64 bits.

**las aplicaciones de 32 bits** se ejecutan en equipos Mac de 32 bits y 64 bits, tiene un espacio de direcciones limitado a 32 bits y requieren que todas las bibliotecas de 32 bits.

Normalmente, usará este modo si tiene dependencias de 32 bits que no se ejecutan en modo de 64 bits, si desea tener una descarga más pequeña, o si no hay ninguna ventaja de rendimiento para pasar a 64 bits.

Este modo está limitando ya que su equipo no podrán usar muchos marcos de trabajo disponibles en Mavericks macOS y macOS Yosemite.

**las aplicaciones de 64 bits** solo se ejecutará en dispositivos de Mac de 64 bits.

Para Mac, este es el modo preferido de operación como la mayoría de equipos Mac en uso actualmente admite el modo de 64 bits y tener acceso al conjunto completo de marcos proporcionadas por Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitación de 64 bits la compilación de aplicaciones de Xamarin.Mac

Para obtener información acerca de cómo compilar una aplicación de 64 bits mediante Xamarin.Mac, consulte el [actualizar Xamarin.Mac unificado aplicaciones a 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md) guía.

## <a name="related-links"></a>Vínculos relacionados

- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
