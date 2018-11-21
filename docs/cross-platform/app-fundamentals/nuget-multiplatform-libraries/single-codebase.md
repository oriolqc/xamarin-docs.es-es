---
title: Crear una nueva biblioteca multiplataforma para NuGet
description: Este documento describe cómo crear una biblioteca multiplataforma para su uso con NuGet. Esta técnica es adecuada para lógica de negocios y los algoritmos que se pueden expresar completamente en la biblioteca de clases de Base de .NET y, por tanto, se ejecutarán en todas las plataformas de destino sin código específico de plataforma.
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6d695df9c59a5f95441092d6d7b44d5feda941bd
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172059"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Crear una nueva biblioteca multiplataforma para NuGet

Crear un proyecto de biblioteca multiplataforma que use PCL o .NET Standard significa que el paquete NuGet resultante se puede agregar a cualquier proyecto de .NET que admite el perfil de destino, incluidos los proyectos de ASP.NET o aplicaciones de escritorio con WinForms, WPF o UWP.

La biblioteca sólo puede contener código compatible con el perfil PCL o .NET Standard seleccionado, así como los otros paquetes de NuGet que se agregan.
Esto es adecuado para la lógica de negocios y los algoritmos que se pueden expresar completamente en la biblioteca de clases base. NET.

Se crea un único ensamblado y se integra en un paquete de NuGet.

Si posteriormente necesita la funcionalidad específica de la plataforma, [proyectos específicos de plataforma se pueden agregar](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Pasos para crear una biblioteca multiplataforma de NuGet

1. Seleccione **archivo > nueva solución** (o a la derecha, haga clic en una solución existente y elija **Agregar > Nuevo proyecto**).

2. Elija **biblioteca multiplataforma** desde el **Multiplatform > biblioteca** sección:

  [![](single-codebase-images/mulitplatform-library-sml.png "Configurar biblioteca multiplataforma para un único código base")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Escriba un **nombre** y **descripción**y elija **único para todas las plataformas**:

  [![](single-codebase-images/single-configure-sml.png "Configurar biblioteca multiplataforma para un único código base")](single-codebase-images/single-configure.png#lightbox)

4. Complete el asistente. Se crea un proyecto de biblioteca único en la solución.

5. Haga doble clic en el nuevo proyecto de biblioteca y, a continuación, seleccione **opciones**. El **compilar > General** sección permite el **.NET Framework de destino** debe establecerse: elija un perfil de PCL de .NET portátil o una versión de .NET Standard:

  [![](single-codebase-images/single-choose-type-sml.png "Elija PCL o .NET Standard para el tipo de biblioteca")](single-codebase-images/single-choose-type.png#lightbox)

6. También en el **opciones de proyecto** ventana, abra el **paquete NuGet > metadatos** sección y escriba el [metadatos necesarios](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (así como los metadatos opcionales):

  [![](single-codebase-images/single-metadata-sml.png "Escriba los metadatos necesarios")](single-codebase-images/single-metadata.png#lightbox)

7. Haga doble clic en el proyecto de biblioteca y elija **crear paquetes de NuGet** (o compilar o implementar la solución) y el **.nupkg** se guardará el archivo de paquete de NuGet en el **/bin /** carpeta (Debug o Release, según la configuración):

  ![](single-codebase-images/create-nuget-package.png "El archivo de paquete de NuGet se guardará en la carpeta bin Debug o Release, según la configuración")


## <a name="verifying-the-output"></a>Comprobar la salida

Paquetes de NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

Esta captura de pantalla muestra el contenido de NuGet basado en PCL: un único ensamblado PCL se incluye:

![](single-codebase-images/nuget-output.png "Archivos contenidos en el paquete NuGet")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Agregar código específico de plataforma

Proyectos de PCL y basadas en .NET Standard no puede contener referencias específicas de la plataforma (por ejemplo, iOS o Android funcionalidad).

Si un proyecto PCL existente o un proyecto .NET Standard debe expandirse para incluir código específico de plataforma, esto puede hacerse con el botón secundario en el proyecto y seleccionando **Agregar > agregar implementación de la plataforma...** :

[![](single-codebase-images/add-later-sml.png "Agregar menú de la implementación de plataforma")](single-codebase-images/add-later.png#lightbox)

Uno o varios proyectos de plataforma se pueden agregar a la solución y, opcionalmente, se puede convertir la biblioteca PCL o .NET Standard existente a un proyecto compartido:

[![](single-codebase-images/add-later-platforms-sml.png "Agregar opciones de plataformas como iOS, Android y proyecto compartido")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Después de convertir a un proyecto compartido, visite la **opciones de proyecto > paquete de NuGet > ensamblados de referencia**
[sección](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) y asegúrese de que los necesarios están seleccionados los perfiles (para que el NuGet sigue siendo compatible con proyectos en que se había utilizado previamente).


## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
