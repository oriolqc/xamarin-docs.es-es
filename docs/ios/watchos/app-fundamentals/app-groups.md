---
title: Trabajar con grupos de aplicaciones
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 341f6b4f5049a39817f2923901472f35b254bead
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-app-groups"></a>Trabajar con grupos de aplicaciones


Un grupo de aplicaciones permite que aplicaciones diferentes (o una aplicación y sus extensiones) tengan acceso a una ubicación de almacenamiento de archivos compartidos. Los grupos de aplicaciones se pueden usar para datos como:

- Apple Watch [configuración](~/ios/watchos/app-fundamentals/settings.md).
- Compartido [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults).
- Compartido [archivos](~/ios/watchos/app-fundamentals/parent-app.md#files).

## <a name="configure-an-app-group"></a>Configurar un grupo de aplicaciones

La ubicación compartida se configura mediante un [grupo de aplicaciones](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que se configura en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del [Centro para desarrolladores de iOS](https://developer.apple.com/devcenter/ios/). También se debe hacer referencia a este valor en cada archivo **Entitlements.plist** del proyecto.

### <a name="provisioning"></a>Aprovisionamiento

El grupo de aplicación tendrá un identificador, que normalmente es el identificador de paquete con un `group.` prefijo. Por ejemplo, podríamos usar el identificador de paquete `com.xamarin.WatchSettings` y el grupo de aplicaciones `group.com.xamarin.WatchSettings`.

[![](app-groups-images/app-group-sml.png "Utilice la com.xamarin.WatchSettings de Id. del lote y el group.com.xamarin.WatchSettings de grupo de aplicaciones")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Además de configurar el perfil de aprovisionamiento, **habilitar los grupos de aplicación** en el **Entitlements.plist** y escriba el identificador que ha elegido:

[![](app-groups-images/entitlements-sml.png "Configurar la plist y escriba el Id.")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>Implementación

Asegúrese de configurar el grupo de aplicaciones correctamente en su [implementación](~/ios/watchos/deploy-test/index.md#App_Groups) aprovisionamiento.


Para obtener más información, consulte el [las capacidades de grupo de aplicaciones de](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentación.


## <a name="related-links"></a>Vínculos relacionados

- [Datos de uso compartido de Apple con la aplicación que lo contiene](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Documentos de grupo de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
