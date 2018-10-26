---
title: las características de privacidad y seguridad de iOS
description: Este documento describe las características de seguridad y privacidad de iOS y explica cómo usarlos con Xamarin.iOS. Da un vistazo a las actualizaciones realizadas en iOS 10 y cómo tener acceso a datos privados del usuario.
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 1c88a5c16dbe5559f0b551a97a27ebb893991187
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120507"
---
# <a name="ios-security-and-privacy-features"></a>las características de privacidad y seguridad de iOS

_En este artículo se explica cómo trabajar con seguridad y privacidad en iOS y cómo afectan a una aplicación de Xamarin.iOS._

Apple ha realizado varias mejoras en seguridad y privacidad en iOS 10 (y versiones posteriores) que le ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y garantizar la privacidad del usuario final. En este artículo se tratará la implementación de estas características en una aplicación de Xamarin.iOS.
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>Mejoras generales

Se realizaron los siguientes cambios generales para la seguridad y privacidad en iOS 10:

- Common Data Security arquitectura (CDSA) API ha quedado desusado y se debe reemplazar con la API SecKey para generar las claves asimétricas.
- El nuevo `NSAllowsArbitraryLoadsInWebContent` clave se puede agregar a la de una aplicación **Info.plist** de archivo y permitirá que las páginas web cargar correctamente mientras la protección de seguridad de transporte de Apple (ATS) todavía está habilitada para el resto de la aplicación. Para obtener más información, consulte nuestra [App Transport Security](~/ios/app-fundamentals/ats.md) documentación.
- Dado que el Portapapeles nuevo en iOS 10 y macOS Sierra permite al usuario copiar y pegar entre dispositivos, la API se ha ampliado para permitir un Portapapeles se limita a un dispositivo específico y con marca de tiempo se borra automáticamente en un momento dado. Además, mesas de trabajo con nombre ya no se guardan y se deben reemplazar con los contenedores de la mesa de trabajo compartidos.
- Para todas las conexiones SSL/TLS, el cifrado simétrico de RC4 ahora está deshabilitado de forma predeterminada. Además, la API de transporte seguro ya no admite SSLv3 y se recomienda que el desarrollador dejen de usar SHA-1 y 3DES criptografía tan pronto como sea posible.

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>Obtener acceso a los datos privados del usuario

Aplicaciones que se ejecutan en iOS 10 (o posterior) deben declarar estáticamente su intención de obtener acceso a características específicas o información de usuario mediante la especificación de una o varias claves de privacidad de sus **Info.plist** archivos que explican al usuario por qué la aplicación quiere obtener acceso.

> [!IMPORTANT]
> Las aplicaciones que no se pueden proporcionar las claves necesarias se terminará automáticamente por el sistema cuando intenten tener acceso a una de las funciones restringidas o información de usuario, _sin errores_! Si una aplicación comienza a rechazar inesperadamente en iOS 10, asegúrese de que todos los **Info.plist** se han especificado.

La privacidad siguiente relacionadas con las claves están disponibles:

- **Privacidad: descripción del uso de Apple Music** (`NSAppleMusicUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la biblioteca multimedia del usuario.
- **Privacidad: descripción de uso de periféricos Bluetooth** (`NSBluetoothPeripheralUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a Bluetooth en el dispositivo del usuario.
- **Privacidad: descripción de uso de calendarios** (`NSCalendarsUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso al calendario del usuario.
- **Privacidad: descripción de uso de la cámara** (`NSCameraUsageDescription`): permite al desarrollador describir por qué la aplicación desee tener acceso a la cámara del dispositivo.
- **Privacidad: descripción de uso de contactos** (`NSContactsUsageDescription`): permite al desarrollador describir por qué la aplicación desee tener acceso a los contactos del usuario.
- **Privacidad: descripción de uso compartido de salud** (`NSHealthShareUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a datos de estado del usuario. Para obtener más información, consulte Apple [referencia de clase HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacidad: descripción de uso de actualización de salud** (`NSHealthUpdateUsageDescription`): permite al desarrollador describir por qué la aplicación desea editar los datos de estado del usuario. Para obtener más información, consulte Apple [referencia de clase HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore).
- **Privacidad: descripción de uso de HomeKit** (`NSHomeKitUsageDescription`): permite al desarrollador describir por qué la aplicación desee tener acceso a datos de configuración de HomeKit del usuario.
- **Privacidad: descripción de uso de la ubicación siempre** (`NSLocationAlwaysUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener siempre acceso a la ubicación del usuario.
- [En desuso] **Privacidad: descripción de uso de la ubicación** (`NSLocationUsageDescription`): permite al desarrollador describir por qué la aplicación desee tener acceso a la ubicación del usuario. *Nota: Esta clave está desusada en iOS 8 (y versiones posteriores). Use `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` en su lugar.*
- **Privacidad: ubicación cuando en el uso de la descripción de uso** (`NSLocationWhenInUseUsageDescription`): permite al desarrollador describir por qué la aplicación desee tener acceso a la ubicación del usuario mientras se está ejecutando.
- [En desuso] **Privacidad: descripción de uso de la biblioteca multimedia** -permite al desarrollador describir por qué la aplicación desea acceder a la biblioteca multimedia del usuario. *Nota: Esta clave está desusada en iOS 8 (y versiones posteriores). Use `NSAppleMusicUsageDescription` en su lugar.*
- **Privacidad: descripción de uso de micrófono** (`NSMicrophoneUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso al micrófono de dispositivos.
- **Privacidad: descripción de uso de Motion** (`NSMotionUsageDescription`): permite al desarrollador describir por qué la aplicación desee tener acceso a acelerómetro del dispositivo.
- **Privacidad: descripción de uso de biblioteca de fotos** (`NSPhotoLibraryUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la biblioteca de fotos del usuario.
- **Privacidad: descripción de uso de recordatorios** (`NSRemindersUsageDescription`): permite al desarrollador describir por qué la aplicación desee tener acceso a los avisos del usuario.
- **Privacidad: descripción de uso de Siri** (`NSSiriUsageDescription`): permite al desarrollador describir por qué la aplicación desea enviar datos de usuario a Siri.
- **Privacidad: descripción de uso del reconocimiento de voz** (`NSSpeechRecognitionUsageDescription`): permite al desarrollador describir por qué la aplicación desea enviar datos de usuario a los servidores de reconocimiento de voz de Apple.
- **Privacidad: descripción de uso del proveedor de TV** (`NSVideoSubscriberAccountUsageDescription`): permite al desarrollador describir por qué la aplicación desea tener acceso a la cuenta del proveedor de TV del usuario.

Para obtener más información sobre cómo trabajar con **Info.plist** claves, consulte Apple [referencia de clave de lista de propiedades de información](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1).

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>Configuración de claves de privacidad

Tomemos el ejemplo siguiente de obtener acceso a HomeKit en iOS 10 (y versiones posteriores), el desarrollador tendrá que agregar el `NSHomeKitUsageDescription` clave a la aplicación **Info.plist** de archivos y proporcione una cadena para declarar ¿por qué la aplicación desee tener acceso a HomeKit del usuario base de datos. Esta cadena se le presentará el usuario la primera vez que ejecute la aplicación:

![Una alerta de ejemplo NSHomeKitUsageDescription](security-privacy-images/info01.png "una alerta de ejemplo NSHomeKitUsageDescription")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin.iOS para Visual Studio actualmente no admite la edición del **Info.plist** editor de manifiestos de las claves de privacidad desde dentro de iOS de forma predeterminada. En su lugar, deberá usar el editor de PList genérico, por lo que haga lo siguiente:

1. Haga doble clic en el **Info.plist** de archivos en el **el Explorador de soluciones** y seleccione **abrir con...** .
2. Seleccione el **Editor de PList genérico** en la lista de programas para abrir el archivo, a continuación, haga clic en **Aceptar**.

    ![Seleccione el Editor de PList genérico](security-privacy-images/InfoEditorSelectionVs.png "seleccione el Editor de PList genérico")
3. Haga clic en el **+** botón en la última fila en el editor para agregar una nueva entrada a la lista. Esto se llamará "Propiedad personalizada", con el tipo establecido en `String` y un valor vacío.
4. Haga clic en el nombre de propiedad, y aparecerá una lista desplegable.
5. En la lista desplegable, seleccione una clave de privacidad (como **privacidad: descripción de uso de HomeKit**): 

    ![Seleccione una clave de privacidad](security-privacy-images/InfoPListEditorSelectKey.png "seleccionar una clave de privacidad")
6. ¿Por qué la aplicación desea acceder a la información de usuario o función determinada, escriba una descripción en la columna de valor: 

    ![Escriba una descripción](security-privacy-images/InfoPListSetValue.png "escriba una descripción")
7. Guarde los cambios en el archivo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para establecer cualquiera de las claves de privacidad, haga lo siguiente:

1. Haga doble clic en el archivo **Info.plist**, en el **Explorador de soluciones**, para abrirlo para su edición.
2. En la parte inferior de la pantalla, cambie a la **origen** vista.
3. Agregue un nuevo **entrada** a la lista.
4. En la lista desplegable, seleccione una clave de privacidad (como **privacidad: descripción de uso de HomeKit**): 

    ![Seleccione una clave de privacidad](security-privacy-images/info02.png "seleccionar una clave de privacidad")
5. Escriba una descripción de por qué la aplicación desea acceder a la información de usuario o función determinada: 

    ![Escriba una descripción](security-privacy-images/info03.png "escriba una descripción")
6. Guarde los cambios en el archivo.

-----

> [!IMPORTANT]
> En el ejemplo proporcionado anteriormente, no se pudo establecer el `NSHomeKitUsageDescription` clave en el **Info.plist** archivo daría lugar a la aplicación _con errores en modo silencioso_ (que se cierra el sistema en tiempo de ejecución) sin errores cuando se ejecuta en iOS 10 ( o superior).

<a name="Summary" />

## <a name="summary"></a>Resumen

Este artículo trata la seguridad y los cambios de privacidad que Apple ha realizado en iOS 10 y cómo afectan a una aplicación de Xamarin.iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
