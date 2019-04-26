---
title: ¿Puedo cambiar la ruta de acceso de salida del archivo IPA?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 1c3c3a63de40a63f040870505b086d67fe160773
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421200"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>¿Puedo cambiar la ruta de acceso de salida del archivo IPA?

## <a name="for-cycle-7-and-higher"></a>Ciclo de 7 y versiones posteriores
Sí, puede usar destinos de MSBuild personalizados para lograr esto. La opción más sencilla es probablemente copiar el `.ipa` archivo después de que se haya compilado.

Estos pasos funcionarán para cualquier proyecto de iOS que usa el motor de compilación de MSBuild en Mac o Windows. (Nota: todos los proyectos de Unified API usan el motor de compilación de MSBuild.)

1. Abra el `.csproj` de archivos para el proyecto de aplicación de iOS en un editor de texto y, a continuación, agregue las siguientes líneas al final (inmediatamente antes del cierre `</Project>` etiqueta):
    
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

2. Establezca DestinationFolder en la carpeta de salida deseado. Como es habitual puede usar las propiedades de MSBuild (por ejemplo, $(OutputPath)) dentro de este argumento, si lo desea.

## <a name="notes"></a>Notas
- El `CreateIpaDependsOn` propiedad está definida en el `Xamarin.iOS.Common.targets` archivo que forma parte de Xamarin.iOS. Se comporta como se describió en *propiedades "DependsOn" de invalidación* en [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- Puede usar un **mover** tareas en lugar de un **copia** si su preferido de tareas. Si elige la opción y se va a crear que en Windows, deberá usar el nombre completo de tareas `<Microsoft.Build.Tasks.Move>` para evitar la ambigüedad con el XamarinVS tareas de compilación.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Para las versiones anteriores de Xamarin Studio 6.0.0.5174 | Xamarin para Visual Studio 4.1.0.530

Sí, puede usar destinos de MSBuild personalizados para lograr esto. La opción más sencilla es probablemente copiar el `.ipa` archivo después de que se haya compilado.

Estos pasos funcionarán para cualquier proyecto de iOS que usa el motor de compilación de MSBuild en Mac o Windows. (Nota: todos los proyectos de Unified API usan el motor de compilación de MSBuild.)

1. Abra el `.csproj` de archivos para el proyecto de aplicación de iOS en un editor de texto y, a continuación, agregue las siguientes líneas al final (inmediatamente antes del cierre `</Project>` etiqueta).

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

2. Establecer el `DestinationFolder` a la carpeta de salida deseado. Como es habitual puede usar las propiedades de MSBuild (como `$(OutputPath)`) dentro de este argumento, si lo desea.

## <a name="notes"></a>Notas
- El `CreateIpaDependsOn` propiedad está definida en el `Xamarin.iOS.Common.targets` archivo que forma parte de Xamarin.iOS. Se comporta como se describió en *propiedades "DependsOn" de invalidación* en [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- Puede usar un **mover** tareas en lugar de un **copia** si su preferido de tareas. Si elige la opción y se va a crear que en Windows, deberá usar el nombre completo de tareas `<Microsoft.Build.Tasks.Move>` para evitar la ambigüedad con el XamarinVS tareas de compilación.
