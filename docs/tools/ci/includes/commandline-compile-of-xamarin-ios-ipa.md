---
ms.openlocfilehash: ef16224b9ef060ff8643211b8e1f0c767c518bdf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841484"
---

La siguiente línea de comandos para especificar una versión de lanzamiento de la solución **SOLUTION_FILE.sln** para iPhone. Se puede establecer la ubicación del IPA especificando el `IpaPackageDir` propiedad en la línea de comandos:

- En el equipo Mac, mediante **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

El **xbuild** comando normalmente se encuentra en el directorio **/Library/Frameworks/Mono.framework/Commands**.

- En Windows, con **msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**MSBuild** no se expandirá automáticamente `$( )` expresiones pasan por la línea de comandos. Por esta razón se recomienda usar una ruta de acceso completa al establecer el `IpaPackageDir` en la línea de comandos.


Consulte la [notas de la versión para iOS 9.8](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location) para obtener más detalles sobre el `IpaPackageDir` propiedad.
