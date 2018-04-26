---
title: ¿Cómo puedo copiar los archivos de salida de IPA a la carpeta de entrega TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 087a20ea3b573595e6cbd2b40d77de649676391e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>¿Cómo puedo copiar los archivos de salida de IPA a la carpeta de entrega TFS?

Abra la `.csproj` de archivos para el proyecto de aplicación de iOS en un editor de texto y, a continuación, agregue las siguientes líneas al final (inmediatamente antes del cierre `</Project>` etiqueta):

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

-   Se trata de la misma técnica general se describe en [¿se puede cambiar la ruta de acceso de salida del archivo IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). Los dos puntos importantes son el establecimiento `$(TF_BUILD_BINARIESDIRECTORY)` como carpeta de destino y para agregar una condición adicional por lo que `CopyIpa` sólo se ejecutará para versiones de TFS.

-   Para obtener una descripción de `TF_BUILD_BINARIESDIRECTORY` vea [ https://msdn.microsoft.com/library/hh850448.aspx ](https://msdn.microsoft.com/library/hh850448.aspx).

## <a name="additional-references"></a>Referencias adicionales

- [Documentación sobre la instalación de TFS para su uso con Xamarin](https://docs.microsoft.com/vsts/tfvc/overview)
- [Tarea de compilación de TFS: Xamarin.Android](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-android)
- [Tarea de compilación de TFS: Xamarin.iOS](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Pasos siguientes
Este documento describen el comportamiento actual a partir de 3.11.666 de Xamarin para Visual Studio y host de compilación de Xamarin.iOS 8.10.3 en el equipo Mac. Para obtener más ayuda, póngase en contacto con nosotros, o si este problema permanece incluso después de la utilización de la información anterior, vea [qué opciones de soporte técnico están disponibles para Xamarin?](~/cross-platform/troubleshooting/support-options.md) para obtener información sobre las opciones de contacto, sugerencias, así como cómo registre un error nuevo si es necesario. 



