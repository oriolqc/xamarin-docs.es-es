---
title: ¿Cómo se puede cambiar a una versión anterior un paquete NuGet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 206336cbcdc85e5e2f3f010e947981cb96e7cd1a
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674745"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>¿Cómo se puede cambiar a una versión anterior un paquete NuGet?

Visual Studio para Mac y Visual Studio tiene características para seleccionar las versiones anteriores de los paquetes e instalarlos automáticamente; de forma similar a cómo de la actualización de paquetes. Estos pasos se describen a continuación.

## <a name="visual-studio"></a>Programa para la mejora

1. Vaya a **Herramientas > Administrador de paquetes NuGet > consola del Administrador de paquetes**
2. Establezca el proyecto en **proyecto predeterminado**
3. Utilice esta sintaxis:

    > Paquete de instalación [nombre del paquete]-[tab para el menú de la versión] de la versión

Puede también copiar/pegar el comando exacto de la página del paquete NuGet. Ejemplo de Xamarin.Forms: [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. En el proyecto, haga clic en la carpeta de paquetes y seleccione **agregar paquetes**
2. En el searchbar, puede usar la sintaxis siguiente para buscar los paquetes necesarios:

    `[PackageName] version:*`

### <a name="examples"></a>Ejemplos 
- Enumera todos los paquetes de Xamarin.Forms: 

    `Xamarin.Forms version:`

- Enumera todos los paquetes de 1.4. x de Xamarin.Forms: 

    `Xamarin.Forms version:1.4`

*Nota: Si agrega un espacio entre `version:` & el número de versión, la búsqueda se comportará como si no se especificó ninguna versión.*
