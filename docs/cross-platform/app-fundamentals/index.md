---
title: Compartir código en varias plataformas
description: Este documento incluye vínculos a diversas guías que describen las técnicas para compartir código, incluidas las bibliotecas de clases portables, los proyectos compartidos, .NET Standard y NuGet.
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 3a2c3f98e3ba83db0794a68ff1d62a9845a111c0
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270194"
---
# <a name="sharing-code-on-multiple-platforms"></a>Compartir código en varias plataformas

Estos artículos explican las distintas opciones disponibles para compartir código entre plataformas, incluidos Windows, Android, iOS y mucho más.

## <a name="code-sharing-overviewcode-sharingmd"></a>[Información general de uso compartido de código](code-sharing.md)

Obtenga información sobre el código diferentes opciones disponibles para los proyectos de Xamarin, incluidas las bibliotecas estándar de .NET y proyectos compartidos de uso compartido. También se admiten las bibliotecas de clases portables, pero se consideran en desuso en favor de .NET Standard.

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET standard es la opción preferida para compartir código entre plataformas. Código se compila con una versión específica (2.0 ofrece la mejor compatibilidad con la API con el código existente de .NET Framework) y, a continuación, puede ser consumida por otros proyectos que admiten ese nivel o superior. Se admiten proyectos de .NET standard en Visual Studio 2017 y Visual Studio para Mac.

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)

Proyectos compartidos permiten escribir código común que se hace referencia a un número de proyectos de aplicación diferentes. El código se compila como parte de cada proyecto que hace referencia y puede incluir directivas de compilador para ayudar a incorporar la funcionalidad específica de la plataforma en el código base compartido. Este artículo describe cómo funcionan los proyectos compartidos y cómo crearlos y utilizarlos con proyectos de Xamarin.

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)

Proyectos de biblioteca de clases Portable le permiten crear y distribuir los ensamblados que contienen código compartido que se ejecuta en varias plataformas. Para crear una biblioteca de clases Portable (o "PCL") que seleccione en primer lugar qué plataformas de destino y, después, escribir código contra un subjuego de .NET Framework que está disponible en el perfil definido para esas plataformas. Las PCL se consideran en desuso en las últimas versiones de Visual Studio; los desarrolladores pueden realizar para usar .NET Standard 2.0 en su lugar.

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Proyectos de NuGet: bibliotecas multiplataforma para compartir código](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Paquetes de NuGet pueden generarse automáticamente desde proyectos de PCL o .NET standard; y se pueden empaquetar proyectos compartidos en paquetes de NuGet "gancho" con el tipo de proyecto de NuGet independiente. Esta sección explica cómo crear paquetes de NuGet para cada escenario de uso compartido de código.

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Crear manualmente los paquetes de NuGet para Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Sugerencias para crear paquetes de NuGet que funcionan con la plataforma Xamarin.
