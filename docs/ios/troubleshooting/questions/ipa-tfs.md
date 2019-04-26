---
title: ¿Cómo se puede copiar los archivos de salida IPA en la carpeta de entrega TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 74e2f2219dcb0908edce7f109844932639038b25
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421128"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>¿Cómo se puede copiar los archivos de salida IPA en la carpeta de entrega TFS?

Abra el `.csproj` de archivos para el proyecto de aplicación de iOS en un editor de texto y, a continuación, agregue las siguientes líneas al final (inmediatamente antes del cierre `</Project>` etiqueta):

```xml
<PropertyGroup>
    <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
    </CreateIpaDependsOn>
</PropertyGroup>

<Target Name="CopyIpa"
    Condition="'$(OutputType)' == 'Exe'
        And '$(ComputedPlatform)' == 'iPhone'
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>Notas

- Ésta es la misma técnica general tratada en [¿cambiar la ruta de acceso de salida del archivo IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). Son los dos puntos importantes establecer `$(TF_BUILD_BINARIESDIRECTORY)` como carpeta de destino y para agregar una condición extra por lo que `CopyIpa` se ejecutará solo para las versiones de TFS.

- Para obtener una descripción de `TF_BUILD_BINARIESDIRECTORY` vea [variables de compilación predefinidos](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="additional-references"></a>Referencias adicionales

- [Documentación sobre la instalación de TFS para su uso con Xamarin](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Tarea de compilación de DevOps de Azure: Xamarin.Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Tarea de compilación de DevOps de Azure: Xamarin.iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Pasos siguientes

Este documento describe el comportamiento actual a partir de Xamarin 3.11.666 para Visual Studio y host de compilación de Xamarin.iOS 8.10.3 en el equipo Mac. Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, consulte la sección [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo archivar un error nuevo si es necesario.
