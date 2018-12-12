---
title: Proyectos de biblioteca NuGet multiplataforma (también conocido como Nugetizer 3000)
description: Este documento describe cómo usar la herramienta Nugetizer 3000 para crear automáticamente los paquetes de NuGet para compartir código entre plataformas.
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 6d3f7b316e397705ecb9bd404007dcd9ef5aa183
ms.sourcegitcommit: 2868c968f418cd7cc110f9664f3c3ffb6df1f9af
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2018
ms.locfileid: "53267266"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>Proyectos de biblioteca multiplataforma de NuGet (Nugetizer 3000)

_Crear automáticamente los paquetes de NuGet para compartir código entre plataformas utilizando 'Nugetizer 3000'!_

Es posible crear automáticamente paquetes de NuGet para compartir código entre plataformas utilizando el _Nugetizer 3000_. Esto hace que es posible crear paquetes de NuGet desde proyectos de biblioteca existente o creando un nuevo **proyecto de biblioteca multiplataforma**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

El Nugetizer 3000 se incluye con Visual Studio para Mac &ndash; buscar el **biblioteca > biblioteca Mulitplatform** tipo de proyecto la **archivo > nuevo** ventana:

[![](images/mulitplatform-library-sml.png "Crear ventana nueva biblioteca multiplataforma")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para usar el Nugetizer 3000 en Visual Studio, inicie [descargue y ejecute el instalador de VSIX](http://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Compilar paquetes de NuGet

Una vez configurado, cada compilación del proyecto genera un completo paquete de NuGet, que se puede utilizar para compartir código internamente con otras aplicaciones o cargado en [NuGet.org](https://www.nuget.org).

Hay tres escenarios para usar esta característica:

- [Proyectos de biblioteca existentes](existing-library.md)

  Crear un paquete NuGet de proyectos existentes de PCL (o .NET Standard).

- [Crear un nuevo proyecto de biblioteca multiplataforma](single-codebase.md)

  Crear una nueva biblioteca para compartir código común a través de NuGet, con una PCL o .NET Standard.

- [Creación de nuevos proyectos de biblioteca específica de la plataforma](platform-specific.md)

  Crear una nueva biblioteca y NuGet que incluye código específico de plataforma para iOS y Android y usa un proyecto compartido para contener el código común y proyectos específicos de plataforma para admitir la funcionalidad específica de iOS o Android.

Hacer referencia a la [metadatos guía](metadata.md) para obtener más información sobre los metadatos necesarios y opcionales que se debe agregar a cualquier paquete de NuGet.

## <a name="further-nuget-information"></a>NuGet para obtener más información

Obtenga más información sobre [creación manual de paquetes NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) y cómo [incluir un paquete de NuGet en una aplicación](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Microsoft [documentación de NuGet](https://docs.microsoft.com/nuget/) contiene información más detallada sobre la **.nupkg** formato y el uso de paquetes de NuGet en Visual Studio.

El análisis de diseño para los proyectos de paquete de NuGet (conocido como) NuGetizer 3000) está disponible en el [repositorio NuGet de GitHub](https://github.com/NuGet/Home/wiki/NuGetizer-3000).

## <a name="related-links"></a>Vínculos relacionados

- [Casos de uso NuGetizer 3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Crear manualmente los paquetes de NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentación de NuGet](https://docs.microsoft.com/nuget/)
