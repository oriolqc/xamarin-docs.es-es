---
title: "¿Cómo se puede degradar un paquete de NuGet?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 98d12bd93c50690909ac902a6f2498bcdb94960f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>¿Cómo se puede degradar un paquete de NuGet?

Visual Studio para Mac y Visual Studio tienen unas características para seleccionar las versiones anteriores de los paquetes e instalarlos automáticamente; es similar a la de la actualización de paquetes. Estos pasos se describen a continuación.

## <a name="visual-studio"></a>Programa para la mejora
1. Vaya a **Herramientas > Administrador de paquetes de NuGet > consola de administrador de paquetes**
2. Establezca el proyecto en **proyecto predeterminado**
3. Use esta sintaxis:

    > Paquete de instalación [PackageName]-versión [tab para menú versión]

Puede también copiar/pegar el comando exacto de la página de NuGet del paquete. Ejemplo de Xamarin.Forms: [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac
1. En el proyecto, haga clic en la carpeta de paquetes y seleccione **agregar paquetes**
2. En el searchbar, puede utilizar la sintaxis siguiente para buscar los paquetes necesarios:

    `[PackageName] version:*`

### <a name="examples"></a>Ejemplos 
- Enumera todos los paquetes de Xamarin.Forms: 

    `Xamarin.Forms version:`
- Enumera todos los paquetes de 1.4. x de Xamarin.Forms: 


    `Xamarin.Forms version:1.4`

*Nota: Si agrega un espacio entre `version:` y el número de versión, la búsqueda se comportará como si se ha especificado ninguna versión.*

