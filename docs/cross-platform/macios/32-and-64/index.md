---
title: Consideraciones de la plataforma de 32 o 64 bits
description: Este documento describen diversas consideraciones a tener en cuenta al elegir como destino arquitecturas de 32 bits y 64 bits para una aplicación Xamarin.iOS o Xamarin.Mac.
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 31eb0bfae58ecdca40548e46d1d9d95828be67b4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120104"
---
# <a name="3264-bit-platform-considerations"></a>Consideraciones de la plataforma de 32 o 64 bits

IOS y macOS históricamente han admitido las aplicaciones de 32 y 64 bits, mientras que Apple ha dejado gradualmente de compatibilidad con 32 bits.

A partir de iOS 11, ya no se iniciarán las aplicaciones de 32 bits, y [todos los envíos a la aplicación de Store deben ser compatible con 64 bits](https://developer.apple.com/news/?id=06282017b).

A partir de enero de 2018, [nuevas aplicaciones enviadas al Store de la aplicación Mac deben ser compatible con 64 bits](https://developer.apple.com/news/?id=06282017a), y las aplicaciones existentes deben actualizarse antes de junio de 2018.

API clásica de Xamarin (`XamMac.dll` y `monotouch.dll`) admite solo las aplicaciones de 32 bits. Sin embargo, usan nuevas aplicaciones de Xamarin.iOS y Xamarin.Mac el [Unified API](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` y `Xamarin.Mac`) de forma predeterminada, y puede, por tanto, destino de 32 y 64 bits, según sea necesario.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitación de 64 bits la compilación de aplicaciones de Xamarin.iOS

> [!WARNING]
> En esta sección se incluye por motivos históricos y para ayudar a mover proyectos de Xamarin.iOS antiguos a Unified API y compatible con 64 bits. Todos los nuevos proyectos de Xamarin.iOS usará la API unificada y 64 bits de destino de forma predeterminada.

Para aplicaciones móviles de Xamarin.iOS que se han convertido a Unified API, los desarrolladores deben actualizar manualmente la configuración de compilación para 64 bits de destino:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **panel de solución**, haga doble clic en el proyecto de la aplicación para abrir el **opciones de proyecto** ventana.
2. Seleccione **compilación de iOS**.
3. Para el iPhone simulador, en el **arquitecturas compatibles** lista desplegable, seleccione **x86\_64** o **i386 + x86\_64**:

   [![Establecer las arquitecturas admitidas en x86\_64 o i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. Para los dispositivos físicos, seleccione uno de los disponibles **ARM64** combinaciones:

   [![Establecer las arquitecturas admitidas en una de las combinaciones de ARM64](Images/Image02.png "configuración compatible con arquitecturas a una de las combinaciones de ARM64")](Images/Image02-large.png#lightbox)

5. Haga clic en **Aceptar**.
6. Realice una compilación limpia.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **el Explorador de soluciones**, haga clic en el proyecto de la aplicación y seleccione **propiedades**.
2. Seleccione **compilación de iOS**.
3. Para el simulador de iPhone, establezca **arquitecturas compatibles** como **x86\_64** o **i386 + x86\_64**: 

   [![Establecer las arquitecturas admitidas en x86_64 o i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. Para los dispositivos físicos, seleccione uno de los disponibles **ARM64** combinaciones:
    
   [![Establecer las arquitecturas admitidas en una de las combinaciones de ARM64](Images/VS01.png "configuración compatible con arquitecturas a una de las combinaciones de ARM64")](Images/VS01-large.png#lightbox)

5. Guarde los cambios.
6. Realice una compilación limpia.

-----

ARMv7s solo es compatible con el procesador de A6 incluido en el iPhone 5 (o superior). ARMv7 código es más rápido y más pequeño que el ARMv6 solo funciona con el iPhone 3GS y versiones posteriores y es requerido por Apple cuando el destino es el iPad o una versión de iOS mínima de 5.0. ARMv6 funciona en todos los dispositivos, pero ya no es compatible con el compilador enviado con Xcode 4.5 y versiones posteriores. 

ARM64 es necesario para admitir el 8 de iOS en iPhone 6 u otros dispositivos de 64 bits y será necesario por Apple al enviar nuevos o actualización de salir de aplicaciones en iTunes App Store.

Para información completa sobre las capacidades de diversos dispositivos de iOS, eche un vistazo de Apple [compatibilidad del dispositivo](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) documento.

### <a name="64-bit-and-binary-size-increases"></a>aumenta el tamaño de 64 bits y binario

Durante la transición de Apple de iOS de 32 bits a 64 bits, aplicaciones tendrá que ejecutar en hardware de 32 bits y 64 bits. Por este motivo, Unified API de Xamarin permite a los desarrolladores centrarse en ambos.

Como destino arquitecturas de 32 bits y 64 bits aumentará significativamente el tamaño de una aplicación. Sin embargo, si lo hace, permitirá que los dispositivos más recientes ejecutar código optimizado mientras se continúa admitiendo los dispositivos más antiguos.

> [!IMPORTANT]
> Si recibe el siguiente mensaje de error al enviar una aplicación de iOS a iTunes App Store, _"Advertencia ITMS-9000: falta la compatibilidad con 64 bits. A partir de iOS nuevo el 1 de febrero de 2015, las aplicaciones que se cargan en el Store de la aplicación deben incluir compatibilidad con 64 bits y compilarse con iOS 8 SDK, incluido en Xcode 6 o posterior. Para habilitar 64 bits en el proyecto, se recomienda usar el valor predeterminado la configuración de "Estándares architectures" de la compilación de Xcode para compilar un solo archivo binario con el código de 32 bits y 64 bits. "_ Debe cambiar las arquitecturas admitidas en uno de los disponibles **ARM64** combinación (como se muestra arriba), recompile y vuelva a intentarlo.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partir de enero de 2018, todas las nuevas aplicaciones de Mac enviadas al Store de la aplicación Mac deben admitir 64 bits. Las aplicaciones existentes de Mac App Store y sus actualizaciones deben admitir 64 bits a partir de junio de 2018. Consulte [announcment de Apple](https://developer.apple.com/news/?id=06282017a) y [una guía que describe cómo actualizar las aplicaciones de Xamarin.Mac a 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md).

Equipos Mac más modernos admiten aplicaciones de 32 bits y 64 bits.   MacOS 10.6 (Leopard nieve) fue el último sistema operativo se ejecute en sistemas de 32 bits.   La mayoría de equipos Mac publicada desde 2010 admite ambos sistemas.

A diferencia de iOS, muchos de los marcos nuevos que se introdujeron en versiones recientes de macOS solo se admiten en modo de 64 bits (CloudKit, EventKit, dispositivo, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, Social, y MapKit, entre otros).

Unified API permiten a los desarrolladores elegir qué tipo de aplicaciones desean producir: 32 bits o 64 bits.

**las aplicaciones de 32 bits** se ejecuten en equipos Mac de 32 bits y 64 bits, tiene un espacio de direcciones limitado a 32 bits y requiere que todas las bibliotecas son de 32 bits.

Normalmente, usará este modo si tiene dependencias de 32 bits que no se ejecutan en modo de 64 bits, si desea tener una descarga más pequeña, o si no hay ninguna ventaja de rendimiento para pasar a 64 bits.

Como ya no podrá usar muchos marcos de trabajo disponibles en macOS Mavericks y macOS Yosemite limite este modo.

**las aplicaciones de 64 bits** solo se ejecutará en dispositivos de Mac de 64 bits.

Para Mac, este es el modo preferido de operación como la mayoría de equipos Mac en uso hoy en día admite el modo de 64 bits y tener acceso al conjunto completo de marcos proporcionadas por Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitación de 64 bits la compilación de aplicaciones de Xamarin.Mac

Para obtener información sobre la creación de una aplicación de 64 bits con Xamarin.Mac, consulte el [aplicaciones actualizando unificadas de Xamarin.Mac a 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md) guía.

## <a name="related-links"></a>Vínculos relacionados

- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
