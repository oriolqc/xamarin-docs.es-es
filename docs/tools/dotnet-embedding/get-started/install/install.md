---
title: Instalación de inserción de .NET
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 4/18/2018
ms.openlocfilehash: 1675889dceb1d364abe74461b32aa4c895a144a0
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
---
# <a name="installing-net-embedding"></a>Instalación de inserción de .NET

## <a name="installing-net-embedding-from-nuget"></a>Instalar .NET incrustación de NuGet

Elija **Agregar > Agregar paquetes de NuGet...**  e instalar **Embeddinator 4000** desde el Administrador de paquetes de NuGet:

![Administrador de paquetes de NuGet](images/visualstudionuget.png)

Este modo se instalará **Embeddinator 4000.exe** y **objcgen** en el **paquetes/Embeddinator-4000/tools** directory.

En general, se debe seleccionar la versión más reciente de Embeddinator-4000 para descargar. Compatibilidad con Objective-C requiere 0,4 o una versión posterior.

## <a name="running-manually"></a>Ejecutar manualmente

Ahora que está instalado NuGet, puede ejecutar las herramientas a mano.

- Abra un Terminal (macOS) o un símbolo del sistema (Windows)
- Cambie el directorio a la raíz de la solución
- Las herramientas se instalan en:
    - **. / paquetes/Embeddinator-4000. [Versión] / tools/objcgen** (Objective-C)
    - **. / paquetes/Embeddinator-4000. [VERSION]/tools/Embeddinator-4000.exe** (Java/C) 
- En Mac OS, **objcgen** se puede ejecutar directamente. 
- En Windows, **Embeddinator 4000.exe** se puede ejecutar directamente.
- En Mac OS, **Embeddinator 4000.exe** debe ejecutarse con **mono**: 
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Cada invocación del comando será necesario un número de parámetros que se enumeran en la documentación específica de la plataforma.

## <a name="automatic-binding-generation"></a>Generación automática de enlace

Existen dos enfoques para ejecutar automáticamente .NET incrustar parte del proceso de compilación.

- Destinos de MSBuild personalizados
- Pasos posteriores a la compilación

Aunque este documento describirá ambos, el uso de un destino de MSBuild personalizado es preferible. Pasos posteriores a la compilación no ejecutará necesariamente al compilar desde la línea de comandos.

### <a name="custom-msbuild-targets"></a>Destinos de MSBuild personalizados

Para personalizar la compilación con destinos de MSbuild, cree primero un **Embeddinator 4000.targets** archivo junto a su csproj similar al siguiente:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

Aquí, el texto del comando debe rellenarse con una de las invocaciones de incrustación de .NET enumeradas en la documentación específica de la plataforma.

Para usar este destino:

- Cierre el proyecto en Visual Studio de 2017 o Visual Studio para Mac
- Abra la biblioteca csproj en un editor de texto
- Agregue esta línea en la parte inferior, por encima de la última `</Project>` línea:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Vuelva a abrir el proyecto

### <a name="post-build-steps"></a>Pasos posteriores a la compilación

Los pasos para agregar un paso posterior a la compilación para ejecutar .NET incrustar varían en función del IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

En Visual Studio para Mac, vaya a **Project Options > compilar > comandos personalizados** y agregue un **después de compilar** paso.

Configurar el comando no aparece en la documentación específica de la plataforma.

> [!NOTE]
> Asegúrese de utilizar el número de versión instalada de NuGet

Si va a hacer el desarrollo en curso en el proyecto de C#, también puede agregar un comando personalizado para limpiar el **salida** directorio antes de ejecutar la incrustación. NET:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Acción de compilación personalizada](images/visualstudiocustombuild.png)

> [!NOTE]
> El enlace generado se colocará en el directorio indicado por la `--outdir` o `--out` parámetro. El nombre de enlace generado puede variar según algunas plataformas tienen limitaciones en los nombres de paquete.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Básicamente configuraremos lo mismo, pero los menús de Visual Studio de 2017 son un poco diferentes. Los comandos de shell también son ligeramente diferentes.

Vaya a **Project Options > eventos de compilación** y escriba el comando no aparece en la documentación específica de la plataforma en la **línea de comandos del evento posterior a la compilación** cuadro. Por ejemplo:

![Incrustar en Windows de .NET](images/visualstudiowindows.png)
