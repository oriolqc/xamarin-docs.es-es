---
title: ¿Cómo puedo ver qué bibliotecas se admiten en una PCL?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: asb3993
ms.author: amburns
ms.date: 07/27/2018
ms.openlocfilehash: 7e1017baf7daed68b5e55319a9ce13a4a2df5f2e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341270"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>¿Cómo puedo ver qué bibliotecas se admiten en una PCL?

- Puede encontrar información general sobre las distintas características compatibles con las diferentes plataformas de destino PCL en la *características compatibles con* parte de esta página: [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- Otra opción consiste en usar el [.NET Portability Analyzer](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) para evaluar si su biblioteca existente se puede convertir en un perfil PCL.

- Una tercera posibilidad es examinar el contenido del perfil real que se usan. Usar perfil 78 por ejemplo, puede ir aquí: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` Y ver todos los ensamblados dentro de él.

Independientemente del método eligió, por favor, tenga en cuenta que algunas funciones debe descargarse a través de NuGet y la biblioteca Microsoft BCL.
