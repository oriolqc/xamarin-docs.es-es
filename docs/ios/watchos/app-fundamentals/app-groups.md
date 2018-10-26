---
title: Trabajar con grupos de aplicaciones de Xamarin de watchOS
description: Este documento describe los grupos de aplicaciones y su uso en una aplicación para watchOS. Describe cómo configurar un grupo de aplicaciones, implementación, Entitlements.plist consideraciones y requisitos de aprovisionamiento.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 78f6c03f73f0e4d8a74f826dd7bc25bbe325d545
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103678"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Trabajar con grupos de aplicaciones de Xamarin de watchOS


Un grupo de aplicaciones permite que aplicaciones diferentes (o una aplicación y sus extensiones) tengan acceso a una ubicación de almacenamiento de archivos compartidos. Los grupos de aplicaciones se pueden usar para datos como:

- Apple Watch [configuración](~/ios/watchos/app-fundamentals/settings.md).
- Compartido [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults).
- Compartido [archivos](~/ios/watchos/app-fundamentals/parent-app.md#files).

## <a name="configure-an-app-group"></a>Configurar un grupo de aplicaciones

La ubicación compartida se configura mediante un [grupo de aplicaciones](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que se configura en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del [Centro para desarrolladores de iOS](https://developer.apple.com/devcenter/ios/). También se debe hacer referencia a este valor en cada archivo **Entitlements.plist** del proyecto.

### <a name="provisioning"></a>Aprovisionamiento

El grupo de aplicaciones tendrá un identificador, que normalmente es el identificador de paquete con un `group.` prefijo. Por ejemplo, podríamos usar el identificador de paquete `com.xamarin.WatchSettings` y el grupo de aplicaciones `group.com.xamarin.WatchSettings`.

[![](app-groups-images/app-group-sml.png "Utilice el com.xamarin.WatchSettings Id. de agrupación y el group.com.xamarin.WatchSettings del grupo de aplicaciones")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Así como configurar el perfil de aprovisionamiento, **habilitar los grupos de aplicación** en el **Entitlements.plist** y escriba el identificador que ha elegido:

[![](app-groups-images/entitlements-sml.png "Configurar el archivo plist y escriba el Id.")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>Implementación

Asegúrese de configurar el grupo de aplicaciones correctamente en su [implementación](~/ios/watchos/deploy-test/index.md#App_Groups) aprovisionamiento.


Para obtener más información, consulte el [las capacidades de grupos de aplicación](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentación.


## <a name="related-links"></a>Vínculos relacionados

- [Apple compartir datos con la aplicación contenedora](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Documento de grupo de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
