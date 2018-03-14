---
title: Proyectos de NuGet (Nugetizer 3000)
description: "Crear automáticamente paquetes de NuGet para compartir código entre plataformas con el '3000 Nugetizer'!"
ms.topic: article
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 11/22/2017
ms.openlocfilehash: 49e7c00feb697d25d61a5e09b051c41945c260c6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="nuget-projects-nugetizer-3000"></a>Proyectos de NuGet (Nugetizer 3000)

_Crear automáticamente paquetes de NuGet para compartir código entre plataformas con el '3000 Nugetizer'!_

Es posible crear automáticamente paquetes de NuGet para compartir código entre plataformas a través de la _Nugetizer 3000_. Esto hace que es posible crear paquetes de NuGet desde proyectos de biblioteca existente o creando un nuevo **proyecto de biblioteca de multiplataforma**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

El 3000 Nugetizer se incluye con Visual Studio para Mac 6.2.

[![](images/mulitplatform-library-sml.png "Crear ventana nueva biblioteca multiplataforma")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para usar el 3000 Nugetizer en Visual Studio, inicie [descargue y ejecute el instalador VSIX](http://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Paquetes de NuGet de creación

Una vez configurado, cada compilación del proyecto genera un paquete de NuGet completo, que puede utilizar para compartir código internamente con otras aplicaciones o cargado en [NuGet.org](https://www.nuget.org).

Hay tres escenarios para usar esta característica:

- [Proyectos de biblioteca existentes](existing-library.md)

  Crear un paquete de NuGet desde proyectos existentes de PCL (o estándar. NET).

- [Crear un nuevo proyecto de biblioteca multiplataforma](single-codebase.md)

  Crear una nueva biblioteca para compartir código común a través de NuGet, con una PCL o .NET estándar.

- [Crear nuevos proyectos de biblioteca específico de la plataforma](platform-specific.md)

  Crear una nueva biblioteca y NuGet que incluye código específico de plataforma para iOS y Android y se usa un proyecto compartido para contener el código común y proyectos para plataformas específicas para admitir la funcionalidad específica de iOS o Android.

Hacer referencia a la [metadatos guía](metadata.md) para obtener más información sobre los metadatos obligatorios y opcionales que se debe agregar a los paquetes de NuGet.


## <a name="further-nuget-information"></a>Información adicional de NuGet

Obtenga más información sobre [creación manual de NuGets para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) y cómo [incluye un paquete de NuGet en una aplicación](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Microsoft [documentación de NuGet](https://docs.microsoft.com/nuget/) contiene información más detallada sobre la **.nupkg** formato y el uso de paquetes de NuGet en Visual Studio.

La discusión de diseño para los proyectos de paquete de NuGet (conocido como) NuGetizer 3000) está disponible en la [repositorio de NuGet GitHub](https://github.com/NuGet/Home/wiki/NuGetizer-3000).


## <a name="related-links"></a>Vínculos relacionados

- [Casos de uso de 3000 NuGetizer](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Crear manualmente los paquetes de NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentación de NuGet](https://docs.microsoft.com/nuget/)
- [Notas de la versión](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#NuGetizer_3000)
