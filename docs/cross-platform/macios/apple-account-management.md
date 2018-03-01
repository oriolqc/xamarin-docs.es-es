---
title: "Administración de cuentas de Apple"
ms.topic: article
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/05/2017
ms.openlocfilehash: 0cf7456cec2e934516e15ac6cbc57109e6b57a79
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="apple-account-management"></a>Administración de cuentas de Apple

La interfaz de administración de la cuenta de Apple proporciona una manera de ver todos los equipos de desarrollo asociados con un identificador de Apple. También permite ver más detalles sobre cada equipo para ver una lista de _identidades firma_ y _perfiles de aprovisionamiento_ que están instalados en su equipo.

Autenticación de su identificador de Apple se realiza en la línea de comandos con [fastlane](https://fastlane.tools/). FastLane debe instalarse en el equipo para que pueda autenticarse correctamente. Para obtener más información sobre fastlane y cómo instalarlo se detalla en la [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) guías.

El cuadro de diálogo de cuenta de Apple en Visual Studio para Mac le permite hacer lo siguiente:

* **Crear y administrar certificados** 
* **Crear y administrar perfiles de aprovisionamiento** 

Obtener información sobre cómo hacer esto se describe en esta guía.

También puede utilizar las herramientas de firma de paquete de iOS para hacer lo siguiente:

* **Agregar una nueva identidad de firma a un perfil existente** 
* **Aprovisionar nuevos dispositivos** 

Para obtener más información sobre el uso de estas características, consulte la [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) guía.
️
## <a name="requirements"></a>Requisitos

Administración de cuentas de Apple está disponible en Visual Studio para Mac. No está actualmente disponible en Visual Studio para Windows.

Debe tener una cuenta de desarrollador de Apple para usar esta característica. Obtener más información sobre las cuentas de desarrollador de Apple está disponible en la [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) guía.

- Asegúrese de que está conectado a internet. Esto es porque fastlane se comunica directamente con el portal para desarrolladores de Apple.
- Asegúrese de que tiene [instaladas las herramientas de fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).
- Asegúrese de tener las últimas herramientas de fastlane de [https://download.fastlane.tools](https://download.fastlane.tools).
- Antes de comenzar, asegúrese de que acepte los contratos de licencia de usuario en el [portal para desarrolladores de](https://developer.apple.com/account/).

# <a name="adding-an-apple-developer-account"></a>Agregar una cuenta de desarrollador de Apple

1. Para abrir el cuadro de diálogo de administración de cuenta, vaya a **Visual Studio > Preferencias > cuenta de desarrollador de Apple**:

    ![Opciones de cuenta de desarrollador de Apple](apple-account-management-images/image1.png)

2. Presione el  **+**  botón para mostrar el inicio de sesión en el cuadro de diálogo, como se describe a continuación: 

    ![cuadro de diálogo de FastLane.](apple-account-management-images/image2.png)

4. Escriba su ID de Apple y la contraseña y haga clic en el **inicio de sesión** botón. Esto guardará sus credenciales en la cadena de claves segura en este equipo. [FastLane](~/ios/deploy-test/provisioning/fastlane/index.md) se utiliza para controlar las credenciales de forma segura y pasarlas al portal para desarrolladores de Apple.
 
5. Seleccione **permitir siempre que** en el cuadro de diálogo de alerta para permitir que Visual Studio usar las credenciales:

    ![](apple-account-management-images/image4.png)

6. Una vez que su cuenta se ha agregado correctamente, verá el identificador de Apple y a cualquier equipo que forma parte de su identificador de Apple.

    ![](apple-account-management-images/image5.png)

7. Seleccione cualquier equipo y presione la **ver detalles...** . Esto mostrará una lista de todas las identidades de firma y perfiles de aprovisionamiento que están instalados en su equipo:

    ![](apple-account-management-images/image6.png)

<a name="managing">
    
## <a name="managing-signing-identities-and-provisioning-profiles"></a>Administración de identidades de firma y perfiles de aprovisionamiento

El cuadro de diálogo de detalles del equipo muestra una lista de identidades de firma, organizadas por tipo. El **estado** columna le informa de si el certificado es: 

* **Válido** : la identidad de firma (el certificado y la clave privada) está instalada en su equipo y no ha expirado.

* **No en las llaves** : hay una identidad de firma válida en el servidor de Apple. Para instalar este en su equipo, debe exportarse desde otro equipo. No se puede descargar la identidad de firma desde el Portal para desarrolladores de Apple como no contendrá la clave privada.

* **Clave privada no está presente** : se instala un certificado sin clave privada en la cadena de claves.

* **Caducado** : el certificado ha expirado. Debe quitar esto de la cadena de claves.

  ![](apple-account-management-images/image7.png)

### <a name="create-a-signing-identities"></a>Crear identidades de firma

Para crear una nueva identidad de firma, seleccione la **crear un nuevo certificado** botón de lista desplegable y seleccione el tipo que necesite. Si tiene los permisos correctos una firma nueva identidad aparecerá después de unos segundos.

Si una opción en la lista desplegable está atenuada y no está seleccionada, como se muestra a continuación, significa que no tiene los permisos de equipo correcto para crear este tipo de certificado.

![](apple-account-management-images/image8.png)

### <a name="download-provisioning-profiles"></a>Descargar perfiles de aprovisionamiento

El cuadro de diálogo de detalles de equipo también muestra una lista de todos los perfiles de aprovisionamiento conectado a la cuenta de desarrollador. Puede descargar todos los perfiles de aprovisionamiento en el equipo local presionando el **descargar todos los perfiles** botón

![](apple-account-management-images/image9.png)

## <a name="ios-bundle-signing"></a>iOS Bundle Signing

Para obtener información sobre cómo implementar la aplicación en un dispositivo, consulte la [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md) guía.


## <a name="troubleshooting"></a>Solución de problemas

#### <a name="view-details-dialog-is-empty"></a>Cuadro de diálogo de detalles de la vista está vacía

Actualmente, esta es un problema conocido, relacionado con el error [&#53906;](https://bugzilla.xamarin.com/show_bug.cgi?id=53906). Asegúrese de que está usando la versión estable más reciente de Visual Studio para Mac

#### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>Si experimenta problemas al iniciar sesión en su cuenta, por favor, pruebe lo siguiente:

* Abra la aplicación de la cadena de claves y bajo la categoría, seleccione *contraseñas*. Busque `deliver.`y elimine todas las entradas.

#### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"Error al agregar la cuenta. Por favor, inicie sesión con una contraseña específico de la aplicación"

Esto es porque la autenticación multifactor 2 está habilitado en su cuenta. Asegúrese de que está usando la versión estable más reciente de Visual Studio para Mac

#### <a name="failed-to-create-new-certificate"></a>No se pudo crear un nuevo certificado
"Ha alcanzado el límite para los certificados de este tipo de"

![](apple-account-management-images/image10.png)

El número máximo de certificados permitidos se han generado. Para solucionar este problema, vaya a la [Centro para desarrolladores de Apple](https://developer.apple.com/account/ios/certificate/distribution) y revocar uno de los certificados de producción.

#### <a name="known-issues"></a>Problemas conocidos:

* A veces, el cuadro de diálogo Detalles de la vista puede tardar una cantidad excesiva de tiempo para capturar los perfiles y las identidades de firma.
* A menudo el foco puede no devolver a Visual Studio para Mac después de escribir los detalles, haciendo que su cuenta no va a agregar. Si este es el caso, vuelva a intentar el proceso.
* Los perfiles de aprovisionamiento creados en Visual Studio para Mac no tendrán en cuenta los derechos seleccionados en los proyectos (Entitlements.plist). Esta funcionalidad se agregará en futuras versiones del IDE.
* El destino predeterminado de los perfiles de aprovisionamiento de distribución será App Store. Los perfiles In House o Ad Hoc deben crearse manualmente.