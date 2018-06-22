---
title: Características de seguridad y privacidad de iOS
description: Este documento describe las características de seguridad y privacidad de iOS y explica cómo utilizarlos con Xamarin.iOS. Toma un vistazo a las actualizaciones realizadas en iOS 10 y cómo obtener acceso a los datos privados del usuario.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fd50e42536aabe16de3e44146d28950970dd801d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784421"
---
# <a name="ios-security-and-privacy-features"></a>Características de seguridad y privacidad de iOS

_Este artículo tratan trabajar con seguridad y privacidad en iOS y cómo afectan a una aplicación de Xamarin.iOS._

Apple ha realizado varias mejoras en la seguridad y privacidad en 10 (y versiones posteriores) de iOS que le ayudará a los desarrolladores mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final. En este artículo se tratará la implementación de estas características en una aplicación de Xamarin.iOS.

Los siguientes temas se tratarán en detalle:

- [Mejoras generales](#General-Enhancements)
- [Obtener acceso a los datos privados del usuario](#Accessing-Private-User-Data)
    - [Configuración de claves de privacidad](#Setting-Privacy-Keys)
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Mejoras generales

Se realizaron los siguientes cambios generales para la seguridad y privacidad en iOS 10:

- La arquitectura de seguridad de la placa datos (CDSA) común API ha quedado desusado y se debe reemplazar con la API SecKey para generar las claves asimétricas.
- El nuevo `NSAllowsArbitraryLoadsInWebContent` clave se puede agregar a la de una aplicación `Info.plist` de archivos y le permitirá páginas web cargar correctamente durante la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación. Para obtener más información, vea nuestra [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md) documentación.
- Dado que el nuevo Portapapeles en iOS 10 y macOS Sierra permite al usuario copiar y pegar entre dispositivos, la API se ha ampliado para permitir un Portapapeles limitarse a un dispositivo específico y con marca de tiempo se va a borrar automáticamente en un momento dado. Además, mesas de trabajo con nombre ya no son persistentes y deben reemplazarse con los contenedores de la mesa de trabajo compartidos.
- Para todas las conexiones SSL/TLS, el cifrado simétrico de RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que el desarrollador dejen de usar cifrado SHA-1 y 3DES tan pronto como sea posible.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Obtener acceso a los datos privados del usuario

Aplicaciones que se ejecutan en iOS 10 (o posterior) deben declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de una o varias claves de privacidad de sus `Info.plist` archivos que explican al usuario ¿por qué la aplicación desea tener acceso.

> [!IMPORTANT]
> Aplicaciones que no proporcionan las claves necesarias se terminará automáticamente por el sistema cuando intenten obtener acceso a una de las funciones restringidas o información de usuario, _sin errores_! Si una aplicación inicia por error de forma inesperada en iOS 10, asegúrese de que todos los `Info.plist` se han especificado.

La privacidad siguiente relacionados con las claves están disponibles:

- **Privacidad - uso de música de Apple descripción** (`NSAppleMusicUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la biblioteca multimedia del usuario.
- **Privacidad - descripción de uso de Bluetooth periféricos** (`NSBluetoothPeripheralUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a Bluetooth en el dispositivo del usuario.
- **Privacidad - descripción de uso de calendarios** (`NSCalendarsUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso el calendario del usuario.
- **Privacidad - descripción de uso de la cámara** (`NSCameraUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la cámara del dispositivo.
- **Privacidad - descripción de uso de contactos** (`NSContactsUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a los contactos del usuario.
- **Privacidad - descripción de uso del recurso compartido de mantenimiento** (`NSHealthShareUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a datos de estado del usuario. Para obtener más información, vea de Apple [HKHealthStore Class Reference](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacidad - descripción de uso de actualización de estado** (`NSHealthUpdateUsageDescription`): permite al desarrollador describir por qué la aplicación desea editar los datos de estado del usuario. Para obtener más información, vea de Apple [HKHealthStore Class Reference](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacidad - descripción de uso de HomeKit** (`NSHomeKitUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a datos de configuración de HomeKit del usuario.
- **Privacidad - descripción de uso de la ubicación siempre** (`NSLocationAlwaysUsageDescription`): permite al desarrollador describir por qué la aplicación desea siempre tienen acceso a la ubicación del usuario.
- [Obsoleto] **Privacidad - descripción de uso de la ubicación** (`NSLocationUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la ubicación del usuario. *Nota: Esta clave está desusada en iOS 8 (y versiones posteriores). Use `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` en su lugar.*
- **Privacidad - ubicación cuando Use uso descripción** (`NSLocationWhenInUseUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la ubicación del usuario mientras se está ejecutando.
- [Obsoleto] **Privacidad - descripción de uso de la biblioteca multimedia** -permite al desarrollador describir por qué la aplicación desea el acceso a la biblioteca multimedia del usuario. *Nota: Esta clave está desusada en iOS 8 (y versiones posteriores). Use `NSAppleMusicUsageDescription` en su lugar.*
- **Privacidad - descripción de uso de micrófono** (`NSMicrophoneUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso al micrófono de dispositivos.
- **Privacidad - descripción de uso de movimiento** (`NSMotionUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a acelerómetro del dispositivo.
- **Privacidad - descripción de uso de la biblioteca fotográfica** (`NSPhotoLibraryUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la biblioteca del usuario foto.
- **Privacidad - descripción de uso de avisos** (`NSRemindersUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a los avisos del usuario.
- **Privacidad - descripción de uso de Siri** (`NSSiriUsageDescription`): permite al desarrollador describir por qué la aplicación desea enviar datos de usuario a Siri.
- **Privacidad - descripción de uso de reconocimiento de voz** (`NSSpeechRecognitionUsageDescription`): permite al desarrollador describir por qué la aplicación desea enviar datos de usuario a los servidores de reconocimiento de voz de Apple.
- **Privacidad - descripción de uso del proveedor de TV** (`NSVideoSubscriberAccountUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la cuenta de proveedor de TV del usuario.

Para obtener más información sobre cómo trabajar con `Info.plist` claves, vea de Apple [referencia de clave de lista de información de propiedad](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1).

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Configuración de claves de privacidad

Considere el ejemplo siguiente de obtener acceso a HomeKit en iOS 10 (y versiones posteriores), el programador tiene que agregar el `NSHomeKitUsageDescription` clave a la aplicación `Info.plist` de archivos y proporcionar una cadena para declarar ¿por qué la aplicación desea tener acceso a la base de datos de HomeKit del usuario. Esta cadena se presentará para el usuario la primera vez que ejecuta la aplicación:

[![](security-privacy-images/info01.png "Una alerta de NSHomeKitUsageDescription de ejemplo")](security-privacy-images/info01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Xamarin.iOS actual de Visual Studio no admite la edición de la mejora de seguridad `Info.plist` configuración desde el IDE, por lo que será necesaria la siguiente solución alternativa:

1. Abra el `Info.plist` archivo en un editor de texto externo.
2. Antes de la última `</dict>` nodo, agregue el siguiente nodo: `<key>NSHomeKitUsageDescription</key>`
3. Agregue el siguiente nodo para proporcionar la descripción requerida: `<string>Allows the app to control HomeKit enabled devices.</string>`
4. El `Info.plist` archivo debe ser similar al siguiente: 

    [![](security-privacy-images/info02vs.png "El archivo Info.plist debe ser similar al siguiente")](security-privacy-images/info02vs.png#lightbox)
4. Guarde los cambios en el archivo.
5. Vuelva a Visual Studio y recompilación de la aplicación.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para establecer cualquiera de las claves de privacidad, haga lo siguiente:

1. Haga doble clic en el `Info.plist` un archivo en el **el Explorador de soluciones** para abrirlo y editarlo.
2. En la parte inferior de la pantalla, cambie a la **origen** vista.
3. Agregue un nuevo **entrada** a la lista.
4. En la lista desplegable, seleccione una clave de privacidad (como **privacidad - descripción de uso de HomeKit**): 

    [![](security-privacy-images/info02.png "Seleccione una clave de privacidad")](security-privacy-images/info02.png#lightbox)
5. Escriba una descripción de por qué la aplicación desea tener acceso a la información de usuario o la característica especificada: 

    [![](security-privacy-images/info03.png "Escriba una descripción")](security-privacy-images/info03.png#lightbox)
6. Guarde los cambios en el archivo.

-----

> [!IMPORTANT]
> En el ejemplo proporcionado anteriormente, error al configurar el `NSHomeKitUsageDescription` clave en el `Info.plist` archivo, se crearán en la aplicación _en modo silencioso errores_ (se cierra el sistema en tiempo de ejecución) sin errores cuando se ejecuta en iOS 10 (o posterior).

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata la seguridad y cambios de privacidad que Apple ha realizado en iOS 10 y cómo afectan a una aplicación de Xamarin.iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
