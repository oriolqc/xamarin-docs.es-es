---
title: Instalación de inserción de .NET
description: Este documento describe cómo instalar la incrustación. NET. Explica cómo ejecutar las herramientas a mano, cómo generar enlaces automáticamente, cómo usar destinos de MSBuild personalizados y los pasos posteriores a la compilación.
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 2a572748c21d2a640add3346d1162f4b6bdc8e99
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668444"
---
# <a name="installing-net-embedding"></a>Instalación de inserción de .NET

## <a name="installing-net-embedding-from-nuget"></a>Instalación de inserción de .NET de NuGet

Elija **Agregar > Agregar paquetes de NuGet...**  e instale **Embeddinator 4000** desde el Administrador de paquetes de NuGet:

![Administrador de paquetes de NuGet](images/visualstudionuget.png)

Esto instalará **Embeddinator 4000.exe** y **objcgen** en el **paquetes/Embeddinator-4000/tools** directory.

En general, se debe seleccionar la versión más reciente de los 4000 Embeddinator para la descarga. Compatibilidad con Objective-C requiere 0.4 o versiones posteriores.

## <a name="running-manually"></a>Ejecución manual

Ahora que está instalado el paquete NuGet, puede ejecutar las herramientas de forma manual.

- Abra un Terminal (macOS) o el símbolo del sistema (Windows)
- Cambie el directorio a la raíz de la solución
- Las herramientas se instalan en:
    - **. / paquetes/Embeddinator-4000. [Versión] / tools/objcgen** (Objective-C)
    - **. / paquetes/Embeddinator-4000. [VERSION]/tools/Embeddinator-4000.exe** (Java/C)
- En macOS, **objcgen** se puede ejecutar directamente.
- En Windows, **Embeddinator 4000.exe** se puede ejecutar directamente.
- En macOS, **Embeddinator 4000.exe** debe ejecutarse con **mono**:
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Cada invocación de comando tendrá un número de parámetros que aparece en la documentación específica de la plataforma.

## <a name="automatic-binding-generation"></a>Generación automática de enlace

Existen dos enfoques para ejecutar automáticamente la inserción de .NET de la parte del proceso de compilación.

- Destinos de MSBuild personalizados
- Pasos posteriores a la compilación

Aunque este documento describe ambos, es preferible usar un destino de MSBuild personalizado. Pasos posteriores a la compilación no se ejecutará necesariamente al compilar desde la línea de comandos.

### <a name="custom-msbuild-targets"></a>Destinos de MSBuild personalizados

Para personalizar una compilación con destinos de MSbuild, cree primero un **Embeddinator 4000.targets** archivo junto al archivo csproj de similar al siguiente:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

En este caso, el texto del comando debe rellenarse con una de las invocaciones de inserción de .NET que aparece en la documentación específica de la plataforma.

Para usar este destino:

- Cierre el proyecto en Visual Studio 2017 o Visual Studio para Mac
- Abra la biblioteca csproj en un editor de texto
- Agregue esta línea al final, encima de la última `</Project>` línea:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Vuelva a abrir el proyecto

### <a name="post-build-steps"></a>Pasos posteriores a la compilación

Los pasos para agregar un paso posterior a la compilación para ejecutar la inserción de .NET varían según el IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

En Visual Studio para Mac, vaya a **opciones de proyecto > compilar > comandos personalizados** y agregue un **después de compilar** paso.

Configurar el comando aparece en la documentación específica de la plataforma.

> [!NOTE]
> Asegúrese de utilizar el número de versión que instala desde NuGet

Si va a hacer desarrollo continuo en el C# proyecto, también puede agregar un comando personalizado para limpiar el **salida** directorio antes de ejecutar la inserción de .NET:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Acción de compilación personalizada](images/visualstudiocustombuild.png)

> [!NOTE]
> El enlace generado se colocará en el directorio indicado por el `--outdir` o `--out` parámetro. El nombre de enlace generado puede variar según algunas plataformas tienen limitaciones en los nombres de paquete.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Básicamente, configuraremos lo mismo, pero los menús de Visual Studio 2017 son un poco diferentes. Los comandos de shell también son ligeramente diferentes.

Vaya a **opciones de proyecto > eventos de compilación** y escriba el comando aparece en la documentación específica de la plataforma en la **línea de comandos del evento posterior a la compilación** cuadro. Por ejemplo:

![Inserción de Windows de .NET](images/visualstudiowindows.png)
