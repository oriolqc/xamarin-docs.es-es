---
title: "¿Por qué se realiza la compilación de iOS por error con: ningún código de iPhone válido claves de firma que se encuentra en las llaves?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 6a853bc7186647dbdae1d5d12f3b185d302a8088
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>¿Por qué se realiza la compilación de iOS por error con: ningún código de iPhone válido claves de firma que se encuentra en las llaves?

## <a name="cause-of-the-error"></a>Causa del error
Este mensaje de error se produce cuando se examina el proyecto en cuestión unas credenciales válidas de firma de código, pero no puede encontrarlos. Firma de código es necesaria para las pruebas y las implementaciones en los dispositivos de E/s físicas; así como aplicaciones y actualizaciones Ad hoc almacenan compilaciones. 


### <a name="provisioning-devices"></a>Aprovisionamiento de dispositivos
Si no ha proporcionado un dispositivo iOS antes, en la guía siguiente le guiará por el proceso completo paso a paso: [Guía de aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>Error al usar el simulador de iOS

> [!NOTE]
> Este problema se ha resuelto en versiones recientes de Xamarin para Visual Studio. Sin embargo, si el problema se produce en la versión más reciente del software, registre una [nuevo error](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con el control de versiones completo completa y la información de los resultados de registro de compilación.


Se produjo un error en Xamarin.Visual Studio 3.11 que provocó el proyecto de iOS en una plantilla de Xamarin.Forms para agregar que la codesign Entitlements.plist Simulator compilaciones; bloqueando de forma efectiva realizar pruebas con el simulador.

### <a name="how-to-fix"></a>Cómo corregir
Puede solucionar el problema quitando el `<CodesignEntitlements>` indicador de la depuración se basa en el archivo .csproj. Puede hacerlo de la siguiente manera:

*Advertencia: Errores en los archivos .csproj pueden interrumpir el proyecto, por lo que es una buena idea de los archivos de copia de seguridad antes de intentar esto.*

1. Haga clic en el proyecto de iOS en el panel de la solución y seleccione **descargar el proyecto**
2. Haga clic en el proyecto nuevo y seleccione **editar .csproj [ProjectName]**
3. Busque los elementos PropertyGroup depurar, deben comenzar con las marcas que este aspecto:
   - Depuración: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versión: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. En cada una de las compilaciones que usan el simulador, eliminar o marque como comentario la siguiente propiedad: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Volver a cargar el proyecto y podrá implementar en el simulador.

### <a name="next-steps"></a>Pasos siguientes
Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, vea [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo registre un error nuevo si es necesario. 
