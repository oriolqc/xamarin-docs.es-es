---
title: 'fastlane para iOS: cert'
ms.topic: article
ms.prod: xamarin
ms.assetid: 92B35AB1-7AB7-3D3B-DB31-CC971E0B43AE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: b98375f8a526cd08f7d11f4ea6bb3498db87009c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="fastlane-for-ios--cert"></a>fastlane para iOS: cert

> [!IMPORTANT]
> fastlane recomienda usar la herramienta [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) para generar y mantener certificados. Use `cert` directamente solo si quiere tener un control total y sabe lo suficiente sobre la firma de código. Use esta acción para descargar la identidad de firma de código más reciente.

## <a name="overview"></a>Información general

Tradicionalmente, cada miembro de un equipo de desarrollo realiza el aprovisionamiento de dispositivos mediante Xcode o en el portal para desarrolladores de Apple. Consta de varios pasos:

- Solicitar un certificado de desarrollo
- Agregar un dispositivo al portal
- Creación de un identificador de aplicación
- Crear un perfil de aprovisionamiento
- Descargar perfiles y certificados

Cada uno de estos pasos contiene variables que deben resolverse, que dependen del tipo de aplicación que se está desarrollando. En la guía [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) (Aprovisionamiento de dispositivos) encontrará más información sobre los pasos necesarios para configurar un dispositivo para el desarrollo, ya sea manualmente o mediante Xcode.

En esta guía se presentan las herramientas de Fastlane como alternativa al uso de Xcode y se explica lo siguiente:

- [¿Qué es Cert?](#whatiscert)
- [Uso de Cert](#using)
- [Opciones adicionales](#options)

## <a name="installation"></a>Instalación

Para obtener información sobre la instalación y la actualización de fastlane, consulte la introducción de la guía de [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatiscert" />

## <a name="what-is-cert"></a>¿Qué es Cert?

cert proporciona una interfaz de terminal que crea nuevas identidades de firma de código (lo que se suele conocer como _certificado_ de desarrollador) para entornos de desarrollo y distribución.

<a name="using" />

## <a name="using-cert"></a>Uso de Cert

Para usar la utilidad Cert, escriba el comando siguiente en la CLI de terminal:

    fastlane cert

De forma predeterminada, se creará un certificado de distribución. Para crear un certificado de desarrollo, pase la marca `--development`:

    fastlane cert --development

cert le solicitará su identificador de Apple y la contraseña, por lo que ahora escriba esto:

[ ![](cert-images/fastlane-image1.png "cert le solicitará su identificador de Apple y la contraseña")](cert-images/fastlane-image1.png)

> [!IMPORTANT]
> La primera vez que escriba la contraseña, se guardará en la cadena de claves de macOS local. Como alternativa, puede usar variables de entorno para almacenar el nombre de usuario y la contraseña, o puede usar `export fastlane_DONT_STORE_PASSWORD=1` si no quiere que sus contraseñas se almacenen en la cadena de claves. Para más información sobre cómo administrar credenciales con fastlane, consulte la [guía del administrador de credenciales](https://github.com/fastlane/fastlane/blob/master/credentials_manager/README.md) de fastlane.

El identificador de Apple también se puede pasar como un argumento mediante el comando siguiente:

    fastlane cert -u myemailadress@domain.com

Si el identificador de Apple está conectado a varios equipos, se mostrarán aquí. Seleccione el número que se corresponda con el equipo que quiera usar:

[ ![](cert-images/fastlane-image2.png "Seleccione el equipo que desea usar")](cert-images/fastlane-image2.png)

El identificador de equipo también se puede pasar mediante la marca siguiente:

    fastlane cert -l 2TU993NY9J

fastlane comprobará si alguno de los certificados de firma disponibles está instalado en la máquina local y, en caso de que así sea, lo usará.

Si no hay ningún certificado de firma, Cert hará lo siguiente:

- Crear una clave privada y solicitud de firma.
- Generar, descargar e instalar el certificado.
- Importar el certificado y la clave privada en la cadena de claves.

Cuando se haya alcanzado el número máximo de identidades de firma permitido para la cuenta, se producirá una excepción. Si quiere crear una identidad de firma, debe revocar manualmente uno de los certificados existentes a través del Centro para desarrolladores y volver a intentarlo.

> [!NOTE]
> fastlane no puede descargar identidades de firma existentes del Centro para desarrolladores si no están en la cadena de claves. Esto se debe a que la clave privada solo existe en el equipo, o bien en la versión exportada (*.p12) del certificado, y nunca en el Centro para desarrolladores.

<a name="options" />

## <a name="additional-options"></a>Opciones adicionales

Las siguientes opciones se pueden usar para dar soporte técnico adicional cuando se usa Cert:

- Use la marca `-–help` para obtener una lista de todos los comandos disponibles:

        fastlane cert --help

- Use la marca `-–verbose` para aumentar el nivel de detalle de la salida:

        fastlane cert --development --verbose


## <a name="related-links"></a>Vínculos relacionados

- [fastlane: cert](https://github.com/fastlane/fastlane/blob/master/cert/README.md)
