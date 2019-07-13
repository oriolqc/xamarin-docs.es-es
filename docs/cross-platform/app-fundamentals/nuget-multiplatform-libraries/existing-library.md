---
title: Creación de NuGet de proyectos de biblioteca existentes
description: Este documento describe cómo crear un paquete de NuGet desde un proyecto de biblioteca existente, permitiendo que el código se puede compartir con otros desarrolladores.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 6e043334d3ca45a573423ebdfdf1ec9149167b55
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864695"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Creación de NuGet de proyectos de biblioteca existentes

Las bibliotecas PCL existente o .NET Standard se pueden convertir en paquetes de NuGet a través de la **opciones de proyecto** ventana:

1. Haga doble clic en el proyecto de biblioteca en el **panel de solución** y elija **opciones**.

2. Vaya a la **paquete NuGet > metadatos** sección y especifique toda la [información requerida](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en el **General** pestaña:

   [![](existing-library-images/existing-metadata-sml.png "Escriba los metadatos necesarios")](existing-library-images/existing-metadata.png#lightbox)

3. Si lo desea, [agregar metadatos adicionales](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en el **detalles** ficha.

4. Después de configurar los metadatos, puede hacer doble clic en el proyecto y elija **crear paquetes de NuGet** y **.nupkg** se guardará el archivo de paquete de NuGet en el **/bin /** carpeta (Debug o Release, según la configuración).

   ![](existing-library-images/create-nuget-package.png "Elija crear paquetes de NuGet en el menú contextual")

5. Para crear el paquete de NuGet en _cada_ de compilación o implementación, vaya a la **paquete NuGet > compilar** sección y graduación **crear un paquete NuGet al compilar el proyecto**:

    [![](existing-library-images/existing-tickbox-sml.png "Pasos para crear un paquete de NuGet")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> Crear el paquete NuGet paquete puede ralentizar el proceso de compilación. Si no está activada esta casilla, todavía se puede generar un paquete NuGet manualmente en cualquier momento desde el menú contextual del proyecto (se muestra en el paso 4 anterior).

## <a name="verifying-the-output"></a>Comprobar la salida

Paquetes de NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

Esta captura de pantalla muestra el contenido de NuGet basado en PCL: un único ensamblado PCL se incluye:

![](existing-library-images/nuget-output.png "Archivos contenidos en el paquete NuGet")


## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
