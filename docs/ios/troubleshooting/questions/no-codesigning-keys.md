---
title: '¿Por qué aparece el siguiente error en mi compilación de iOS: no valid iPhone code signing keys found in keychain?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fe267db1f83695b3d0e8f3d828f91e01b56ba8ee
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61419670"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>¿Por qué aparece el siguiente error en mi compilación de iOS: no valid iPhone code signing keys found in keychain?

## <a name="cause-of-the-error"></a>Causa del error
Este mensaje de error se produce cuando el proyecto en cuestión está buscando credenciales válidas de firma de código, pero no puede encontrarlos. Firma de código es necesaria para pruebas e implementaciones en dispositivos iOS físicos; como aplicación y Ad-hoc almacenan compilaciones. 


### <a name="provisioning-devices"></a>Aprovisionamiento de dispositivos
Si aún no lo ha aprovisionado un dispositivo iOS antes, la siguiente guía le llevará a través del proceso completo paso a paso: [Guía de aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>Error al usar el simulador de iOS

> [!NOTE]
> Este problema se ha resuelto en versiones recientes de Xamarin para Visual Studio. Sin embargo, si el problema se produce en la versión más reciente del software, registre un [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de salida del registro de compilación.


Se produjo un error en Xamarin.Visual Studio 3.11 que provocó el proyecto de iOS en una plantilla de Xamarin.Forms para agregar que la firma de código Entitlements.plist Simulator compilaciones; bloquea de manera efectiva las pruebas mediante el simulador.

### <a name="how-to-fix"></a>Cómo corregir
Puede solucionar el problema quitando el `<CodesignEntitlements>` marca en la depuración se basa en el archivo .csproj. Puede hacerlo de la siguiente manera:

*Advertencia: Errores en los archivos .csproj pueden interrumpir el proyecto, por lo que es una buena idea de los archivos de copia de seguridad antes de intentar esto.*

1. Haga clic en el proyecto de iOS en el panel de solución y seleccione **descargar el proyecto**
2. Haga clic de nuevo el proyecto y seleccione **editar .csproj [ProjectName]**
3. Busque los elementos PropertyGroup depurar, deben comenzar con indicadores que tener este aspecto:
   - Depurar: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versión: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. En cada una de las compilaciones que usan el simulador, elimine o comente la siguiente propiedad: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Recargar el proyecto y podrá implementar en el simulador.

### <a name="next-steps"></a>Pasos siguientes
Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, consulte la sección [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo archivar un error nuevo si es necesario. 
