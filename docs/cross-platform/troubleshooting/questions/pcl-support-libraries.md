---
title: ¿Cómo puedo ver qué bibliotecas se admiten en una PCL?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: asb3993
ms.author: amburns
ms.openlocfilehash: 87f65ba2cff2d5990c32aa142f97766a76d6ba05
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919545"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>¿Cómo puedo ver qué bibliotecas se admiten en una PCL?

- Puede encontrar información general sobre las distintas características compatibles con las diferentes plataformas de destino PCL bajo la *características admitidas* parte de esta página: [http://msdn.microsoft.com/library/gg597391.aspx](https://msdn.microsoft.com/library/gg597391.aspx)

- Otra opción consiste en usar la [analizador de portabilidad de .NET](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) para evaluar si se puede convertir la biblioteca existente a un perfil PCL.

- Una tercera posibilidad es examinar el contenido del perfil real que se usan. Usando el perfil 78 por ejemplo, puede ir a continuación: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` y ver todos los ensamblados dentro de él.

El método que eligió, por favor, tenga en cuenta que algunas funciones tiene que ser descargados a través de NuGet y la biblioteca de Microsoft BCL.
