---
title: Creación de nuevos proyectos de biblioteca específica de la plataforma para NuGet
description: Este documento describe cómo crear un único paquete de NuGet que contiene código específico de plataforma para varias plataformas.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 4be010448d963462ccf06c263ddfad7ba1d9feae
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832038"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Creación de nuevos proyectos de biblioteca específica de la plataforma para NuGet

Los proyectos de biblioteca multiplataforma que tienen como destino plataformas específicas, como iOS y Android, funcionan mejor con proyectos compartidos.

El paquete NuGet puede contener código específico de iOS y Android, así como código de .NET común a ambos.

Varios ensamblados se crean y se compila en un único paquete de NuGet. Los estándares de NuGet garantizan que el paquete se puede agregar a todos los tipos de proyecto compatibles, como los proyectos de Xamarin.iOS y Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Pasos para crear una biblioteca multiplataforma de NuGet

1. Seleccione **archivo > nueva solución** (o a la derecha, haga clic en una solución existente y elija **Agregar > Nuevo proyecto**).

2. Elija **biblioteca multiplataforma** desde el **Multiplatform > biblioteca** sección:

    [![](platform-specific-images/mulitplatform-library-sml.png "Configurar biblioteca multiplataforma para un único código base")](platform-specific-images/multiplatform-library.png#lightbox)

3. Escriba un **nombre** y **descripción**y elija **específico de la plataforma**:

    [![](platform-specific-images/specific-configure-sml.png "Configurar biblioteca específica de la plataforma para iOS y Android")](platform-specific-images/specific-configure.png#lightbox)

4. Complete el asistente. Los siguientes proyectos se agregan a la solución:

    - **Proyecto de Android** – código específico de Android, opcionalmente, se puede agregar a este proyecto.
    - **Proyecto de iOS** – código específico de iOS, opcionalmente, se puede agregar a este proyecto.
    - **Proyecto de NuGet** : se agrega ningún código para este proyecto. Hace referencia a los otros proyectos y contiene la configuración de los metadatos para la salida del paquete NuGet.
    - **Proyecto compartido** – código común se debe agregar a este proyecto, incluido el código específico de plataforma dentro de `#if` directivas de compilador.

5. Haga doble clic en el proyecto de NuGet y elija **opciones**, a continuación, abra el **paquete NuGet > metadatos** sección y escriba el [metadatos necesarios](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (como así como cualquier opcionales metadatos):

    [![](platform-specific-images/specific-metadata-sml.png "Escriba los metadatos necesarios")](platform-specific-images/specific-metadata.png#lightbox)

6. También en el **opciones de proyecto** ventana, abra el **ensamblados de referencia** sección y elegir qué perfiles PCL será compatible con la biblioteca compartida a través de "gancho":

    ![](platform-specific-images/specific-reference-assemblies.png "También en la ventana de opciones de proyecto, abra la sección de los ensamblados de referencia y elegir qué perfiles PCL será compatible con la biblioteca compartida a través de gancho")

    > [!NOTE]
    > "Gancho" significa que los ensamblados PCL contendrá solo la API expuesta por la biblioteca (no puede contener el código específico de plataforma). Cuando se agrega el paquete NuGet a un proyecto de Xamarin, se va a compilar bibliotecas compartidas en la PCL, pero los ensamblados específicos de la plataforma contienen el código que se usa realmente por iOS o Android proyectos.

7. Haga doble clic en el proyecto y elija **crear paquetes de NuGet** (o compilar o implementar la solución) y el **.nupkg** se guardará el archivo de paquete de NuGet en el **/bin /** carpeta ( Debug o Release, según la configuración).

    ![](platform-specific-images/create-nuget-package.png "Archivo de paquete de NuGet se guardará en la carpeta bin Debug o Release, según la configuración")


## <a name="verifying-the-output"></a>Comprobar la salida

Paquetes de NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

Esta captura de pantalla muestra el contenido de NuGet específico de la plataforma que admite iOS y Android y tenía dos ensamblados de referencia seleccionados:

![](platform-specific-images/nuget-output.png "Archivos contenidos en el paquete NuGet")


## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
