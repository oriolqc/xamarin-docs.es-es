---
title: "Aprovisionamiento automático"
description: "Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso de la firma automática en Visual Studio para Mac para solicitar certificados de desarrollo y perfiles."
ms.topic: article
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 11/17/2017
ms.openlocfilehash: a411c214e35f78ff9d3dd8d4e9122702d66a2156
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="automatic-provisioning"></a>Aprovisionamiento automático

_Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso de la firma automática en Visual Studio para Mac para solicitar certificados de desarrollo y perfiles._

## <a name="requirements"></a>Requisitos

- Visual Studio para Mac 7.3 o posterior
- Xcode 9 o posterior

> [!IMPORTANT]
>  En esta guía se indica cómo se usa Visual Studio para Mac para configurar un dispositivo de Apple para la implementación y cómo se implementa una aplicación. Para conocer los pasos manuales del proceso, también en el caso de Visual Studio para Windows, se recomienda seguir los pasos detallados que encontrará en el [manual de aprovisionamiento](~/ios/get-started/installation/device-provisioning/manual-provisioning.md).

## <a name="enabling-automatic-signing"></a>Habilitación de la firma automática

Antes de iniciar el proceso de firma automática, debe asegurarse de que se haya agregado un Id. de Apple en Visual Studio para Mac, como se describe en la guía de [administración de cuentas de Apple](~/cross-platform/macios/apple-account-management.md). Una vez que haya agregado un Id. de Apple, puede usar cualquier _equipo_ asociado. Así podrá crear certificados, perfiles y otros id. a partir de los datos del equipo. El id. de equipo también se usa para crear el prefijo de un id. de aplicación que se incluirá en el archivo de aprovisionamiento. Todo esto permite a Apple comprobar su identidad.

Para inscribir la aplicación automáticamente para su implementación en un dispositivo iOS, haga lo siguiente:

1. Abra un proyecto de iOS en Visual Studio para Mac.

2. Abra el archivo **Info.plist**.

3. En la sección **Firma**, seleccione **Aprovisionamiento automático**:

    ![Lista desplegable del selector de equipo](automatic-provisioning-images/image2.png)

4. En la lista desplegable **Equipo**, seleccione su equipo.

6. Después de unos segundos se crean un certificado de firma y un perfil de aprovisionamiento:

    ![Certificado y perfil creados correctamente](automatic-provisioning-images/image5.png)

    Si se produce un error en la firma automática, el panel **Firma automática** muestra el motivo de ese error.

## <a name="triggering-automatic-provisioning"></a>Inicio del aprovisionamiento automático

Cuando se ha habilitado la firma automática, Visual Studio para Mac actualiza estos artefactos en caso necesario si sucede algo de lo siguiente:

* Hay un dispositivo iOS conectado al equipo Mac
    - Comprueba automáticamente si el dispositivo está registrado en el portal Apple Developer. Si no es así, lo agrega y genera un nuevo perfil de aprovisionamiento que lo contiene.
* Se ha modificado el Id. de agrupación de la aplicación
    - Actualiza el Id. de aplicación. Se crea un nuevo perfil de aprovisionamiento que contiene este Id. de aplicación.
* Se ha habilitado una capacidad compatible en el archivo Entitlements.plist.
    - Esta capacidad se agrega al Id. de aplicación y se genera un nuevo perfil de aprovisionamiento con el Id. de aplicación actualizado.
    - No todas las capacidades son compatibles de momento. Para más información sobre las que lo son, vea la guía [Working with Capabilities (Trabajar con capacidades)](~/ios/deploy-test/provisioning/capabilities/index.md).


## <a name="related-links"></a>Vínculos relacionados

- [Aprovisionamiento gratis](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribución de aplicaciones)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
- [Guía de distribución de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
