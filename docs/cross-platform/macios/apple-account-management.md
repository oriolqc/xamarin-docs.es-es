---
title: Administración de cuentas de Apple
description: Este documento describe cómo usar las características de administración de la cuenta de Apple en Visual Studio para Mac y Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 1e353aceaf0e2c0525b82c0ccb7e7bcb73df3075
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106070"
---
# <a name="apple-account-management"></a>Administración de cuentas de Apple

La interfaz de administración de la cuenta de Apple proporciona una manera de ver todos los equipos de desarrollo asociados con un identificador de Apple. También permite ver más detalles acerca de cada equipo, mostrando una lista de _identidades de firma_ y _perfiles de aprovisionamiento_ que están instalados en el equipo.

Autenticación de su identificador de Apple se realiza en la línea de comandos con [fastlane](https://fastlane.tools/). FastLane debe instalarse en el equipo para que pueda autenticarse correctamente. Más información sobre cómo instalarlo y de fastlane se detalla en el [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) guías.

El cuadro de diálogo de cuenta de Apple le permite hacer lo siguiente:

* **Crear y administrar certificados**
* **Crear y administrar perfiles de aprovisionamiento**

Información sobre cómo hacer esto se describe en esta guía.

> [!NOTE]
> Las herramientas de Xamarin para la administración de la cuenta de Apple solo muestran información acerca de las cuentas de desarrollador de Apple pago. Para obtener información sobre cómo probar una aplicación en un dispositivo sin una cuenta de desarrollador de Apple pago, consulte el [aprovisionamiento gratuito para aplicaciones Xamarin.iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) guía.

También puede usar las herramientas de aprovisionamiento automático de iOS para crear y administrar identidades de firma, los identificadores de aplicación y perfiles de aprovisionamiento automáticamente. Para obtener más información sobre el uso de estas características, consulte el [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) guía.

## <a name="requirements"></a>Requisitos

Administración de cuentas de Apple está disponible en Visual Studio para Mac y Visual Studio 2017 (versión 15.7 y versiones posterior)

Debe tener una cuenta de desarrollador de Apple para usar esta característica. Obtener más información sobre las cuentas de desarrollador de Apple está disponible en el [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) guía.

- Asegúrese de que está conectado a internet. Esto es porque fastlane se comunica directamente con el portal para desarrolladores de Apple.
- Asegúrese de que tiene [instaladas las herramientas de fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Asegúrese de que tiene las últimas herramientas de fastlane desde [ https://download.fastlane.tools ](https://download.fastlane.tools).
- Antes de comenzar, asegúrese de que acepte los contratos de licencia de usuario en el [portal para desarrolladores](https://developer.apple.com/account/).

## <a name="adding-an-apple-developer-account"></a>Agregar una cuenta de desarrollador de Apple

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Para abrir el cuadro de diálogo de administración de cuenta, vaya a **Visual Studio > Preferencias > cuenta de desarrollador de Apple**:

    ![Opciones de la cuenta de desarrollador de Apple](apple-account-management-images/image1.png)

2. Presione el **+** botón para mostrar el inicio de sesión en el cuadro de diálogo, como se muestra a continuación: 

    ![cuadro de diálogo de FastLane.](apple-account-management-images/image2.png)

4. Escriba su Id. de Apple y la contraseña y haga clic en el **sesión** botón. Esto guardará sus credenciales en la cadena de claves seguro en este equipo. [FastLane](~/ios/deploy-test/provisioning/fastlane/index.md) se usa para administrar las credenciales de forma segura y pasarlas al portal para desarrolladores de Apple.
 
5. Seleccione **permitir siempre** en el cuadro de diálogo de alerta para permitir que Visual Studio usar las credenciales:

    ![Permitir siempre el cuadro de diálogo de alerta](apple-account-management-images/image4.png)

6. Una vez que su cuenta se ha agregado correctamente, verá el identificador de Apple y los equipos que forman parte de su identificador de Apple.

    ![Cuadro de diálogo de cuenta de Apple developer con las cuentas agregadas](apple-account-management-images/image5.png)

7. Seleccione cualquier equipo y presione la **ver detalles...** . Esto mostrará una lista de todas las identidades de firma y perfiles de aprovisionamiento que están instalados en el equipo:

    ![Vista Detalles de la pantalla que muestra las identidades de firma y perfiles en el equipo de aprovisionamiento](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Antes de empezar a agregar el ID de Apple para Visual Studio 2017, asegúrese de que es el entorno de desarrollo [emparejado con un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Para abrir la ventana de administración de cuenta, vaya a **Herramientas > Opciones > Xamarin > cuentas de Apple**:

    ![Pantalla de opciones de cuentas de Apple](apple-account-management-images/prov1.png)

1. Seleccione el **agregar** botón y escriba su Id. de Apple y la contraseña:

    ![cuadro de diálogo Nombre de usuario y contraseña](apple-account-management-images/prov1a.png)

1. Una vez que su cuenta se ha agregado correctamente, verá el identificador de Apple y los equipos que forman parte de su identificador de Apple.
 
1. Seleccione cualquier equipo y presione la **ver detalles...** . Esto mostrará una lista de todas las identidades de firma y perfiles de aprovisionamiento que están instalados en el equipo:

    ![cuadro de diálogo Nombre de usuario y contraseña](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>Administración de identidades de firma y perfiles de aprovisionamiento

El cuadro de diálogo de detalles del equipo muestra una lista de identidades de firma, organizados por tipo. El **estado** columna le advierte si el certificado es: 

* **Válido** : la identidad de firma (tanto el certificado y la clave privada) está instalada en el equipo y no ha expirado.

* **No en la cadena de claves** : hay una identidad de firma válida en el servidor de Apple. Para instalarlo en su equipo, debe exportarse desde otra máquina. No se puede descargar la identidad de firma desde el Portal para desarrolladores de Apple como no contendrá la clave privada.

* **Falta la clave privada** : se instala un certificado sin la clave privada en la cadena de claves.

* **Caducado** : el certificado ha expirado. Esto se debe quitar de la cadena de claves.

  ![información del cuadro de diálogo de detalles de equipo](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>Crear identidades de firma

Para crear una nueva identidad de firma, seleccione el **Create Certificate** botón desplegable y seleccione el tipo que necesite. Si tiene los permisos correctos una firma nueva identidad aparecerá después de unos segundos.

Si una opción en la lista desplegable está atenuada y no está seleccionada, significa que no tiene los permisos de equipo correcto para crear este tipo de certificado.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![crear opciones de certificado](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![crear opciones de certificado](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>Descargar perfiles de aprovisionamiento

El cuadro de diálogo de detalles del equipo también muestra una lista de todos los perfiles de aprovisionamiento conectado a su cuenta de desarrollador. Puede descargar todos los perfiles de aprovisionamiento en el equipo local presionando el **descargar todos los perfiles** botón

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Descargue la sección perfiles de aprovisionamiento](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Descargue la sección perfiles de aprovisionamiento](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>iOS Bundle Signing

Para obtener información sobre la implementación de la aplicación en un dispositivo, consulte el [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) guía.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="view-details-dialog-is-empty"></a>Cuadro de diálogo de detalles de la vista está vacía

Esto es actualmente un problema conocido, relacionados con errores [53906 #](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Asegúrese de que utiliza la versión estable más reciente de Visual Studio para Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Si experimenta problemas al iniciar sesión en su cuenta, pruebe lo siguiente:

* Abra la aplicación de cadena de claves y en la categoría, seleccione *contraseñas*. Busque `deliver.`y elimine todas las entradas.

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Error al agregar la cuenta. Inicie sesión con una contraseña específicos de la aplicación"

Esto es porque la autenticación de 2 fases está habilitada en su cuenta. Asegúrese de que utiliza la versión estable más reciente de Visual Studio para Mac

### <a name="failed-to-create-new-certificate"></a>No se pudo crear un nuevo certificado
"Ha alcanzado el límite para los certificados de este tipo"

![cuadro de diálogo de límite de certificado](apple-account-management-images/image10.png)

El número máximo de certificados permitidos se han generado. Para solucionar este problema, vaya a la [Centro para desarrolladores de Apple](https://developer.apple.com/account/ios/certificate/distribution) y revocar uno de los certificados de producción.

## <a name="known-issues"></a>Problemas conocidos

* El destino predeterminado de los perfiles de aprovisionamiento de distribución será App Store. Los perfiles In House o Ad Hoc deben crearse manualmente.
