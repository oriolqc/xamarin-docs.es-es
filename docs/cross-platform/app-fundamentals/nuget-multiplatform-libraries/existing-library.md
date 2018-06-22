---
title: Crear un NuGet de proyectos de biblioteca existente
description: Este documento describe cómo crear un paquete de NuGet desde un proyecto de biblioteca existente, lo que permite el código se comparta con otros desarrolladores.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 7f407b22d1793d585ae40aeae8c2d9b7616784e6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34779988"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Crear un NuGet de proyectos de biblioteca existente

Bibliotecas PCL existente o estándar de .NET se pueden convertir en NuGets a través de la **Project Options** ventana:

1. Haga doble clic en el proyecto de biblioteca en el **solución Pad** y elija **opciones**.

2. Vaya a la **paquete NuGet > metadatos** todos los valores especificar y la sección la [la información necesaria](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en el **General** ficha:

  [![](existing-library-images/existing-metadata-sml.png "Escriba los metadatos necesarios")](existing-library-images/existing-metadata.png#lightbox)

3. Si lo desea, [agregar metadatos adicionales](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) en el **detalles** ficha.

4. Una vez que se ha configurado los metadatos, puede haga doble clic en el proyecto y elija **crear paquetes de NuGet** y **.nupkg** se guardará el archivo de paquete de NuGet en la **/bin/** carpeta (Debug o Release, dependiendo de la configuración).

  ![](existing-library-images/create-nuget-package.png "Elija crear paquetes de NuGet desde el menú contextual.")

5. Para crear el paquete de NuGet en _cada_ compilar o implementar, vaya a la **paquete NuGet > compilar** sección y graduación **crear un paquete de NuGet al compilar el proyecto**:

    [![](existing-library-images/existing-tickbox-sml.png "Pasos para crear un paquete de NuGet")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> Creación de NuGet paquete puede ralentizar el proceso de compilación. Si esta casilla no está marcada, todavía puede generar un paquete de NuGet manualmente en cualquier momento desde el menú contextual del proyecto (se muestra en el paso 4 anterior).

## <a name="verifying-the-output"></a>Comprobar la salida

Paquetes de NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

Esta captura de pantalla muestra el contenido de un NuGet basada en PCL: un único ensamblado PCL se incluye:

![](existing-library-images/nuget-output.png "Archivos contenidos en el paquete de NuGet")


## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
