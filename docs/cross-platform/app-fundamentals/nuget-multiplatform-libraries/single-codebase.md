---
title: Crear una nueva biblioteca multiplataforma de NuGet
ms.topic: article
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: a56cc080ac04c45ef3f0fcc6c7c89096a08beddf
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Crear una nueva biblioteca multiplataforma de NuGet

Crear un proyecto de biblioteca multiplataforma que utiliza PCL o estándar de .NET significa que NuGet resultante puede agregarse a cualquier proyecto de .NET que admite el perfil de destino, incluidos los proyectos de ASP.NET o aplicaciones de escritorio con formularios Windows Forms, WPF o UWP.

La biblioteca sólo puede contener código compatible con el perfil seleccionado PCL o estándar. NET, así como cualquier otro NuGets que se agregan.
Esto es adecuado para lógica de negocios y algoritmos que se pueden expresar completamente en la biblioteca de clases base. NET.

Se crea un único ensamblado y se integra en un paquete de NuGet.

Si posteriormente necesita la funcionalidad específica de la plataforma, [proyectos para plataformas específicas pueden agregarse](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Pasos para crear una biblioteca multiplataforma NuGet

1. Seleccione **archivo > nueva solución** (o haga clic en una solución existente y elija **Agregar > Nuevo proyecto**).

2. Elija **biblioteca multiplataforma** desde el **multiplataforma > biblioteca** sección:

  [![](single-codebase-images/mulitplatform-library-sml.png "Configurar la biblioteca de multiplataforma para un único código base")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Escriba un **nombre** y **descripción**y elija **único para todas las plataformas**:

  [![](single-codebase-images/single-configure-sml.png "Configurar la biblioteca de multiplataforma para un único código base")](single-codebase-images/single-configure.png#lightbox)

4. Complete el asistente. Se crea un proyecto de biblioteca único en la solución.

5. Haga doble clic en el nuevo proyecto de biblioteca y, a continuación, seleccione **opciones**. El **generar > General** sección permite la **.NET Framework de destino** establecerse: elija un perfil de PCL Portable de .NET o una versión de .NET estándar:

  [![](single-codebase-images/single-choose-type-sml.png "Elegir PCL o estándar de .NET para el tipo de biblioteca")](single-codebase-images/single-choose-type.png#lightbox)

6. También en el **opciones de proyecto** ventana, abra el **paquete NuGet > metadatos** sección y escriba la [metadatos necesarios](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (así como cualquier metadato opcional):

  [![](single-codebase-images/single-metadata-sml.png "Escriba los metadatos necesarios")](single-codebase-images/single-metadata.png#lightbox)

7. Haga doble clic en el proyecto de biblioteca y elija **crear paquetes de NuGet** (o compilar o implementar la solución) y la **.nupkg** se guardará el archivo de paquete de NuGet en la **/bin/** carpeta (Debug o Release, según la configuración):

  ![](single-codebase-images/create-nuget-package.png "El archivo de paquete de NuGet se guardará en la carpeta bin Debug o Release, dependiendo de la configuración")


## <a name="verifying-the-output"></a>Comprobar la salida

Paquetes de NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

Esta captura de pantalla muestra el contenido de un NuGet basada en PCL: un único ensamblado PCL se incluye:

![](single-codebase-images/nuget-output.png "Archivos contenidos en el paquete de NuGet")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Agregar código específico de plataforma

Proyectos basados en PCL y proyectos basados en .NET estándar no pueden contener referencias específica de la plataforma (por ejemplo, la funcionalidad de Android o iOS).

Si un proyecto estándar de .NET o un proyecto PCL existente debe expandirse para incluir código específico de plataforma, esto puede hacerse mediante el botón secundario en el proyecto y seleccione **Agregar > implementación de la plataforma de agregar...** :

[![](single-codebase-images/add-later-sml.png "Agregar el menú de la implementación de plataforma")](single-codebase-images/add-later.png#lightbox)

Uno o varios proyectos de plataforma se pueden agregar a la solución y, opcionalmente, se puede convertir la biblioteca de .NET estándar o de PCL existente a un proyecto compartido:

[![](single-codebase-images/add-later-platforms-sml.png "Agregar opciones de plataformas como iOS y Android, proyecto compartido")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Después de convertir a un proyecto compartido, visite la **Project Options > paquete NuGet > ensamblados de referencia**
[sección](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) y asegúrese de que los necesarios se seleccionaron perfiles (para que el NuGet sigue siendo compatible con los proyectos en que se había utilizado previamente).


## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
