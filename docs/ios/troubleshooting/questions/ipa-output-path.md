---
title: ¿Puedo cambiar la ruta de acceso de salida del archivo IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 9c80a209279a2f032eb6c9efcba1398ca0e267a5
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
ms.locfileid: "31882807"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>¿Puedo cambiar la ruta de acceso de salida del archivo IPA?

## <a name="for-cycle-7-and-higher"></a>Ciclo de 7 y versiones posteriores
Sí, puede usar los destinos de MSBuild personalizados para lograr esto. La opción más sencilla es probablemente copiar el `.ipa` archivo después de que se ha creado.

Estos pasos funcionará para cualquier proyecto de iOS que utiliza el motor de compilación de MSBuild en Mac o Windows. (Nota: todos los proyectos de API unificada usar el motor de compilación de MSBuild.)

1. Abra la `.csproj` de archivos para el proyecto de aplicación de iOS en un editor de texto y, a continuación, agregue las siguientes líneas al final (inmediatamente antes del cierre `</Project>` etiqueta):
    
    ```
    <PropertyGroup>
           <CreateIpaDependsOn>
           $(CreateIpaDependsOn);
            CopyIpa
           </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
        Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(IpaPackagePath)"
            DestinationFolder="$(OutputPath)"
        />
    </Target>
    ```

2. Establezca DestinationFolder en la carpeta de salida que desee. Como de costumbre puede utilizar propiedades de MSBuild (por ejemplo, $(OutputPath)) dentro de este argumento, si lo desea.

## <a name="notes"></a>Notas
- El `CreateIpaDependsOn` propiedad se define en el `Xamarin.iOS.Common.targets` archivo que forma parte de Xamarin.iOS. Se comporta tal y como se describe en la sección *reemplazando 'DependsOn' propiedades* en [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- Puede usar un **mover** tareas en lugar de un **copia** si preferida de tareas. Si decide que la opción y se está generando en Windows, debe usar el nombre completo de tareas `<Microsoft.Build.Tasks.Move>` para evitar la ambigüedad con el XamarinVS tareas de compilación.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Para las versiones anteriores de Xamarin Studio 6.0.0.5174 | Xamarin para Visual Studio 4.1.0.530

Sí, puede usar los destinos de MSBuild personalizados para lograr esto. La opción más sencilla es probablemente copiar el `.ipa` archivo después de que se ha creado.

Estos pasos funcionará para cualquier proyecto de iOS que utiliza el motor de compilación de MSBuild en Mac o Windows. (Nota: todos los proyectos de API unificada usar el motor de compilación de MSBuild.)

1. Abra la `.csproj` de archivos para el proyecto de aplicación de iOS en un editor de texto y, a continuación, agregue las siguientes líneas al final (inmediatamente antes del cierre `</Project>` etiqueta).

    ```csharp
    <PropertyGroup>
        <CreateIpaDependsOn>
            $(CreateIpaDependsOn);
            CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
        Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(OutputPath)$(IpaPackageName)"
            DestinationFolder="/Users/macuser/Desktop/"
        />
    </Target>
    ```

2. Establecer el `DestinationFolder` a la carpeta de salida que desee. Como de costumbre puede utilizar propiedades de MSBuild (como `$(OutputPath)`) dentro de este argumento, si lo desea.

## <a name="notes"></a>Notas
- El `CreateIpaDependsOn` propiedad se define en el `Xamarin.iOS.Common.targets` archivo que forma parte de Xamarin.iOS. Se comporta tal y como se describe en la sección *reemplazando "propiedades"DependsOn* en [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- Puede usar un **mover** tareas en lugar de un **copia** si preferida de tareas. Si decide que la opción y se está generando en Windows, debe usar el nombre completo de tareas `<Microsoft.Build.Tasks.Move>` para evitar la ambigüedad con el XamarinVS tareas de compilación.
