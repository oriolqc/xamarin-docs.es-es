---
ms.openlocfilehash: e383bbccd4e76be8a208f5680e5cf21e45a0dbc3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511943"
---

La siguiente línea de comandos para especificar una versión de lanzamiento de la solución **SOLUTION_FILE. sln** para el iPhone. La ubicación del IPA se puede establecer especificando la `IpaPackageDir` propiedad en la línea de comandos:

- En el equipo Mac, mediante **xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

El comando **xbuild** se encuentra normalmente en el directorio **/Library/Frameworks/mono.Framework/Commands**.

- En Windows, con **msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**msbuild** no expandirá `$( )` automáticamente las expresiones pasadas en la línea de comandos. Por esta razón, se recomienda usar una ruta de acceso completa al establecer `IpaPackageDir` en la línea de comandos.

Consulte las [notas de la versión de iOS 9,8](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location) para obtener más `IpaPackageDir` información sobre la propiedad.
