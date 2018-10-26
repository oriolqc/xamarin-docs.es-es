---
title: Introducción a macOS Sierra
description: Este artículo presenta todas las características disponibles en macOS Sierra y las API nuevas y modificadas para desarrolladores de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 5a944fd8f7dcfdcbb3f025c92b4afac35673416f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111017"
---
# <a name="introduction-to-macos-sierra"></a>Introducción a macOS Sierra

Con el nuevo macOS Sierra, el desarrollador puede aprovechar las ventajas de las nuevas API que permiten al usuario final interactúe con sus aplicaciones y sitios Web no estaban disponibles. Por ejemplo, Apple ahora permite que sitios Web ofrecer a los clientes la opción de pagar forma segura a través de Apple Pay y mejoras para aumentar el marco metálico gráficos de una aplicación y calcular posibles. 

Para obtener más información en macOS Sierra, consulte Apple [macOS + aplicaciones](https://developer.apple.com/macos/) documentación.

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>Novedades en macOS Sierra

Apple ha agregado varias nuevas API y servicios en macOS Sierra, junto con muchas mejoras en las características existentes, incluyendo:

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Sistema de archivos de Apple

Con macOS Sierra, Apple ha lanzado el nuevo sistema de archivos de Apple como un sistema de archivos modernos para iOS, macOS, tvOS y watchOS. El sistema de archivos de Apple se ha optimizado para el almacenamiento SSD y de Flash y proporciona las siguientes características: cifrado de alta seguridad, los metadatos de copia en escritura, espacio compartido, la clonación para archivos y directorios, las instantáneas, ajuste de tamaño de directorio rápida y atomic primitivas seguro para guardar.

Para obtener más información, consulte Apple [Guía de Apple File System](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999).

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Mejoras de Apple Pay

Apple ha realizado varias mejoras en Apple Pay en macOS Sierra que permiten al usuario realizar pagos seguros desde sitios Web.

Con macOS Sierra, varias API nuevas se han agregado que funcionan con macOS Sierra, iOS y watchOS para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

macOS Sierra incluye el nuevo marco ApplePay Javascript que permite al desarrollador a incorporar Apple Pay directamente en iOS y macOS sitios Web basados en Safari. Para los sitios Web que admiten Apple Pay, el usuario puede autorizar el pago con su iPhone o Apple Watch.

Para obtener más información, consulte Apple [ApplePay JS Framework](https://developer.apple.com/reference/applepayjs) referencia.

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>Creación de aplicaciones modernas de macOS

Aplicaciones modernas de macOS como el explorador web Safari de Apple, páginas procesador de textos y números hoja uso abran numerosas tecnologías nuevas para presentar una interfaz de usuario unificada, sensible al contexto que soluciona con elementos de interfaz de usuario tradicionales, como paneles flotantes y varios en Windows.

[![Un ejemplo de una ventana con pestañas de Mac](images/content08.png)](images/content08.png#lightbox)

Nuestro [aplicaciones modernas de creación de macOS](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) guía cubre varias sugerencias, características y técnicas de un desarrollador puede usar para crear una aplicación moderna de macOS en Xamarin.Mac.

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>Uso compartido de datos de CloudKit

Se ha ampliado el marco de trabajo de CloudKit en macOS Sierra para permitir que el usuario rápida y fácilmente compartir registros o conjuntos de registros desde sus bases de datos de iCloud privada.

CloudKit proporciona una completa interfaz de usuario para enviar y aceptar invitaciones de registro compartidas y el usuario tiene control completo de lectura/escritura a través de las personas que tienen acceso a los registros.

Para obtener más información, consulte Apple [CloudKit Framework referencia](https://developer.apple.com/reference/clockkit) y [CloudKit JS Framework referencia](https://developer.apple.com/reference/cloudkitjs).

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Compatibilidad con las extensiones de aplicación de Safari

Extensiones de aplicación de Safari que la aplicación pueda extender el comportamiento del explorador web Safari mientras se integra estrechamente con macOS Sierra. Puesto que las extensiones de aplicación de Safari de macOS funcionan de manera similar a las extensiones de aplicación de Safari de iOS, son fáciles de puerto de un sistema a otro.

Para obtener más información, consulte Apple [Guía de programación de extensión de aplicación de Safari](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319).

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>Mejoras de seguridad y privacidad

Apple ha realizado varias mejoras en seguridad y privacidad en macOS Sierra que le ayudarán a la aplicación a mejorar la seguridad de la aplicación y garantizar la privacidad del usuario final incluido lo siguiente:

- El nuevo `NSAllowsArbitraryLoadsInWebContent` clave se puede agregar a la aplicación `Info.plist` de archivo y permitirá que las páginas web cargar correctamente mientras la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación.
- Common Data Security arquitectura (CDSA) API ha quedado desusado y se debe reemplazar con la API SecKey para generar las claves asimétricas.
- Para todas las conexiones SSL/TLS, el cifrado simétrico de RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que la aplicación dejen de usar SHA-1 y 3DES criptografía tan pronto como sea posible.
- Dado que el Portapapeles nuevo en iOS 10 y macOS Sierra permite al usuario copiar y pegar entre dispositivos, la API se ha ampliado para permitir un Portapapeles se limita a un dispositivo específico y con marca de tiempo se borra automáticamente en un momento dado. Además, mesas de trabajo con nombre ya no se guardan y se deben reemplazar con los contenedores de la mesa de trabajo compartidos.
- Si la aplicación tiene acceso a datos protegidos (por ejemplo, el calendario del usuario), lo _debe_ declarar dicha intención con la clave del valor de cadena propósito adecuado en su `Info.plist` archivo (`NSCalendarUsageDescription` en el caso del calendario).
- Signed desarrollador de aplicaciones que no se entrega a través el Store de la aplicación de Mac ahora puede aprovechar las ventajas de CloudKit, iCloud llaves, iCloud Drive, las notificaciones de inserción remota, los derechos MapKit y VPN.
- macOS Sierra ya no admite la entrega de datos junto con la aplicación de firmante de código en su archivo zip o imagen de disco sin signo o el código externo como no se conoce la ruta de acceso en tiempo de ejecución antes de tiempo de ejecución.

Además, las aplicaciones que se ejecuta en macOS Sierra (o posterior) debe declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de uno o más claves específicas de privacidad de sus `Info.plist` archivos que explican al usuario por qué la aplicación quiere obtener acceso.

Dado que macOS Sierra comparte estos cambios con iOS 10, consulte nuestra iOS 10 [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) guía para obtener más información.

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>Compatibilidad con extensión de controladores de tarjeta inteligente

Con macOS Sierra, puede crear la aplicación `NSExtension` en función de los controladores de tarjeta inteligente que permite el acceso de solo lectura al contenido de ciertos tipos de tarjetas inteligentes. Esta información se presenta a continuación, dentro de la cadena de claves del sistema (reemplazando el método de la arquitectura de seguridad de datos común en desuso).

Para obtener más información, del Declinatoria vea Apple [CryptoTokenKit Framework referencia](https://developer.apple.com/reference/cryptotokenkit).

<a name="Unified-Logging" />

### <a name="unified-logging"></a>Registro unificado

Registro unificado proporciona la aplicación con una sola API de mensajería eficaz en todos los niveles del sistema. Con registro unificado de la aplicación tiene un mayor control sobre varios niveles de registro que incluyen controles de privacidad y la actividad de seguimiento para una depuración más sencilla. 

El registro proporciona correlación automática de los mensajes al seguimiento y registro de actividad se usa juntos.

macOS Sierra incluye una nueva aplicación de consola (en aplicaciones/utilidades) que es capaz de mostrar los datos de registro de varios orígenes, incluidos los dispositivos conectados. También es compatible con las búsquedas guardadas y con tokens y muestra las conexiones entre los mensajes relacionados en varios procesos.

Además, los mensajes de registro se pueden ver y mantienen mediante herramientas de línea de comandos.

Para obtener más información, consulte Apple [referencia registro](https://developer.apple.com/reference/os/1891852-logging).

<a name="Wide-Color" />

### <a name="wide-color"></a>Color amplio

macOS Sierra amplía la compatibilidad con formatos de píxel de rango ampliado y espacios de la amplia gama de colores en todo el sistema incluidos marcos como gráficos de Core, imagen básica, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas a color amplia es aún más disminuido proporcionando este comportamiento a lo largo de la pila de gráficos completa.

Además, `AppKit` se ha modificado para que funcione en el nuevo extendidos **sRGB** espacio de color, lo que facilita la combinación de colores en gamas de color amplia sin pérdida de rendimiento significativas.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores amplios:

- `NSColor` Ahora se utiliza espacio de color sRGB y se dejará de fijar los valores para el `0.0` a `1.0` intervalo. Si la aplicación se basa en el comportamiento de bloqueo anterior, deberá modificarse para macOS Sierra.
- Cuando se usa una API de bajo nivel como gráficos básicos o sistema operativo para proporcionar procesamiento de imágenes, la aplicación debe usar un rango extendido píxeles y el espacio de formato de color que es compatible con los valores de punto flotante de 16 bits. En caso necesario, la aplicación deberá fijar manualmente los valores de componentes de color.
- Gráficos básicos, imagen Core y los sombreadores de rendimiento de sistema operativo proporcionan nuevos métodos para la conversión entre los espacios de dos colores.

Para obtener más información, consulte nuestra [Introducción a Color amplia](~/ios/platform/wide-color.md) guía.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Cambios de marco de trabajo adicionales

Además de los cambios en la plataforma principal y adiciones enumeradas anteriormente, Apple ha realizado muchos cambios de marco secundarias adicionales en macOS Sierra.

Para obtener más información, consulte nuestra [cambios adicionales de Framework](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) guía.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>Interfaces API en desuso

Las API siguientes han quedado obsoletas en macOS Sierra:

- El sistema de archivos estándar HFS ya no se admite.

Consulte Apple [v10.12 OS X de diferencias de API](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html) documentación para obtener una lista completa de cambios y elementos obsoletos.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://developer.xamarin.com/samples/mac/)
- [Novedades en OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
