---
title: Introducción a macOS Sierra
description: Este artículo detallan todas las API y características disponibles en macOS Sierra nueva y modificada para desarrolladores de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 5ab373d708d47ad7c3dbbf4507284be04a1f9934
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-macos-sierra"></a>Introducción a macOS Sierra

Con el nuevo macOS Sierra, el desarrollador puede sacar partido de nuevas API que permiten al usuario final interactuar con sus aplicaciones y sitios Web de maneras no estaban disponibles. Por ejemplo, Apple ahora permite que sitios Web proporcionar a los clientes la opción de pago segura a través de Apple Pay y mejoras para el aumento de Metal framework gráficos de una aplicación y calcular posibles. 

Para obtener más información sobre macOS Sierra, vea de Apple [macOS + aplicaciones](https://developer.apple.com/macos/) documentación.

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>What's New en macOS Sierra

Apple ha agregado varias nuevas API y servicios en macOS Sierra junto con muchas mejoras en las características existentes, incluyendo:

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Sistema de archivos de Apple

Con macOS Sierra, Apple ha lanzado el nuevo sistema de archivos de Apple como un sistema de archivos modernos para iOS, Mac OS, tvOS y watchOS. El sistema de archivos de Apple se ha optimizado para el almacenamiento SSD y Flash y proporciona las siguientes características: cifrado de alta seguridad, copia en escritura metadatos, del espacio compartido, archivos y directorios, las instantáneas, ajuste de tamaño de directorio rápido y atómicas primitivas de almacenamiento seguro para la clonación.

Para obtener más información, vea de Apple [Guía de sistema de archivos de Apple](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999).

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Mejoras de pago de Apple

Apple ha realizado varias mejoras en Apple Pay en macOS Sierra que permiten al usuario realizar pagos seguros de sitios Web.

Con macOS Sierra, varias nuevas API se han agregado que funcionan con macOS Sierra, iOS y watchOS para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

macOS Sierra incluye el nuevo marco de ApplePay Javascript que permite al desarrollador incorporar Apple Pay directamente en iOS y macOS sitios Web basados en Safari. Para los sitios Web que admiten Apple Pay, el usuario puede autorizar el pago con su iPhone o Apple Watch.

Para obtener más información, vea de Apple [ApplePay JS Framework](https://developer.apple.com/reference/applepayjs) referencia.

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>Creación de macOS modernas aplicaciones

Aplicaciones modernas macOS como explorador web Safari de Apple, procesador de textos de páginas y uso de hoja de propagación de números muchas nuevas tecnologías para crear una interfaz de usuario totalmente unificada y contextuales que realiza inmediatamente con los elementos de interfaz de usuario tradicionales como paneles flotantes y varias abrir en Windows.

[![Un ejemplo de una ventana con pestañas de Mac](images/content08.png)](images/content08.png#lightbox)

Nuestro [macOS compilar modernas aplicaciones](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) guía abarca varias sugerencias, características y técnicas de un desarrollador puede utilizar para compilar una aplicación moderna macOS en Xamarin.Mac.

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>Uso compartido de datos de CloudKit

El marco de trabajo CloudKit se ha expandido en macOS Sierra para permitir que el usuario rápida y fácilmente compartir registros o conjuntos de registros de sus bases de datos de iCloud privada.

CloudKit proporciona una interfaz de usuario completa para enviar y aceptar invitaciones de registro compartidas y el usuario tiene un control completo de lectura/escritura sobre las personas que tienen acceso a los registros.

Para obtener más información, vea de Apple [CloudKit Framework referencia](https://developer.apple.com/reference/clockkit) y [CloudKit JS Framework Reference](https://developer.apple.com/reference/cloudkitjs).

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Compatibilidad con las extensiones de aplicación de Safari

Extensiones de aplicaciones de Safari permite que la aplicación extender el comportamiento del explorador web Safari mientras se integra estrechamente con macOS Sierra. Puesto que las extensiones de aplicación de Safari macOS funcionan de manera similar a las extensiones de aplicación de Safari de iOS, le resultará fáciles al puerto de un sistema a otro.

Para obtener más información, vea de Apple [Guía de programación de extensión de aplicación de Safari](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319).

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>Mejoras de seguridad y privacidad

Apple ha realizado varias mejoras en seguridad y privacidad en macOS Sierra que le ayudará a la aplicación a mejorar la seguridad de la aplicación y garantizar la privacidad del usuario final como los siguientes:

- El nuevo `NSAllowsArbitraryLoadsInWebContent` clave se puede agregar a la aplicación `Info.plist` de archivos y le permitirá páginas web cargar correctamente durante la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación.
- La arquitectura de seguridad de la placa datos (CDSA) común API ha quedado desusado y se debe reemplazar con la API SecKey para generar las claves asimétricas.
- Para todas las conexiones SSL/TLS, el cifrado simétrico de RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no es compatible con SSLv3 y se recomienda que la aplicación deje de uso de la criptografía de SHA-1 y 3DES tan pronto como sea posible.
- Dado que el nuevo Portapapeles en iOS 10 y macOS Sierra permite al usuario copiar y pegar entre dispositivos, la API se ha ampliado para permitir un Portapapeles limitarse a un dispositivo específico y con marca de tiempo se va a borrar automáticamente en un momento dado. Además, mesas de trabajo con nombre ya no son persistentes y deben reemplazarse con los contenedores de la mesa de trabajo compartidos.
- Si la aplicación tiene acceso a datos protegidos (por ejemplo, el calendario del usuario), lo _debe_ declarar dicha intención con la clave del valor de cadena de propósito adecuado en su `Info.plist` archivo (`NSCalendarUsageDescription` en el caso del calendario).
- Desarrollador Signed aplicaciones que no se entregan a través de la tienda de aplicaciones Mac ahora pueden aprovechar de CloudKit, iCloud llaveros, iCloud unidad, notificaciones de inserción remoto, VPN y MapKit derechos.
- macOS Sierra ya no admite la entrega de datos junto con la aplicación de firmante de código en su archivo zip o imagen de disco sin signo o código externo como no se conoce la ruta de acceso en tiempo de ejecución antes de tiempo de ejecución.

Además, las aplicaciones que se ejecutan en Mac OS Sierra (o posterior) debe declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de una o varias claves específicas de privacidad de sus `Info.plist` archivos que explican al usuario ¿por qué la aplicación desea obtener acceso.

Dado que macOS Sierra comparte estos cambios con iOS 10, visite nuestro iOS 10 [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) guía para obtener más información.

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>Compatibilidad con extensiones de controlador de tarjeta inteligente

Con macOS Sierra, puede crear la aplicación `NSExtension` en función de los controladores de tarjetas inteligentes que permite el acceso de solo lectura para el contenido de determinados tipos de tarjetas inteligentes. Esta información se presenta a continuación, dentro de la cadena de claves de sistema (reemplazando el método de arquitectura de seguridad de datos común en desuso).

Para obtener más información, del Declinatoria vea Apple [CryptoTokenKit Framework Reference](https://developer.apple.com/reference/cryptotokenkit).

<a name="Unified-Logging" />

### <a name="unified-logging"></a>Registro unificado

Registro unificado proporciona la aplicación con una única API de mensajería eficaz en todos los niveles del sistema. Con registro unificado de la aplicación tiene un control exhaustivo de varios niveles de registro que incluyen controles de privacidad y la actividad de seguimiento para una depuración más sencilla. 

El registro proporciona correlación automática de los mensajes al seguimiento y registro de actividad se usa juntos.

macOS Sierra incluye una nueva aplicación de consola (en las aplicaciones o utilidades) que es capaz de mostrar los datos de registro de varios orígenes, incluidos los dispositivos conectados. También es compatible con las búsquedas con tokens y se guarda y muestra las conexiones entre los mensajes relacionados en varios procesos.

Además, los mensajes de registro se pueden ver y mantienen mediante herramientas de línea de comandos.

Para obtener más información, vea de Apple [referencia de registro](https://developer.apple.com/reference/os/1891852-logging).

<a name="Wide-Color" />

### <a name="wide-color"></a>Color amplia

macOS Sierra amplía la compatibilidad con formatos de píxel de rango extendido y los espacios de la amplia gama de colores en todo el sistema incluidos los marcos, como los gráficos esenciales, imagen Core, sistema operativo y AVFoundation. Compatibilidad con dispositivos con pantallas de color amplia adicional se ve facilitado por proporcionar este comportamiento en toda la pila completa de gráficos.

Además, `AppKit` se ha modificado para que funcione en el nuevo extendidos **sRGB** espacio de color, lo que facilita la combinación de colores de gamas de color amplia sin pérdida de rendimiento significativas.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores ancho:

- `NSColor` Ahora se utiliza un espacio de colores sRGB y se dejará sujete valores para la `0.0` a `1.0` intervalo. Si la aplicación se basa en el comportamiento de clamp anterior, deberá modificarse para macOS Sierra.
- Cuando se usa una API de bajo nivel, como gráficos de núcleo o sistema operativo para proporcionar procesamiento de imágenes, la aplicación debe usar un rango extendido píxeles y espacio en formato de color que es compatible con los valores de punto flotante de 16 bits. En caso necesario, la aplicación tendrá que sujete manualmente los valores de componente de color.
- Para los gráficos esenciales, imagen Core y los sombreadores de rendimiento de sistema operativo proporcionan nuevos métodos para convertir entre los espacios de dos color.

Para obtener más información, visite nuestro [Introducción a Color amplia](~/ios/platform/wide-color.md) guía.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Cambios de Framework adicionales

Además de los cambios principales framework y adiciones enumeradas anteriormente, Apple ha realizado numerosos cambios de marco secundarias adicionales en macOS Sierra.

Para obtener más información, visite nuestro [Framework modificándola](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) guía.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>Interfaces API en desuso

Las API siguientes han quedado obsoletas en macOS Sierra:

- El sistema de archivos estándar de HFS ya no se admite.

Vea de Apple [v10.12 OS X API Diffs](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html) documentación para obtener una lista completa de elementos obsoletos y los cambios.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de Mac](https://developer.xamarin.com/samples/mac/)
- [' S new en OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
