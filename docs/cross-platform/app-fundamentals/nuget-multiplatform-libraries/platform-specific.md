---
title: Crear nuevos proyectos de biblioteca específico de la plataforma de NuGet
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 901dbe032d62047668f265e8c7f79593b3fbfcce
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Crear nuevos proyectos de biblioteca específico de la plataforma de NuGet

Los proyectos de biblioteca multiplataforma que tienen como destino plataformas específicas, como iOS y Android, funcionan mejor con proyectos compartidos.

NuGet puede contener código específico de iOS y Android, así como código de .NET comunes a ambos.

Varios ensamblados se crean y se integra en un único paquete de NuGet. Estándares de NuGet Asegúrese de que el paquete puede agregarse a todos los tipos de proyecto compatibles, por ejemplo, Xamarin iOS y Android proyectos.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Pasos para crear una biblioteca multiplataforma NuGet

1. Seleccione **archivo > nueva solución** (o haga clic en una solución existente y elija **Agregar > Nuevo proyecto**).

2. Elija **biblioteca multiplataforma** desde el **multiplataforma > biblioteca** sección:

  [![](platform-specific-images/mulitplatform-library-sml.png "Configurar la biblioteca de multiplataforma para un único código base")](platform-specific-images/multiplatform-library.png#lightbox)

3. Escriba un **nombre** y **descripción**y elija **específica de la plataforma**:

  [![](platform-specific-images/specific-configure-sml.png "Configurar la biblioteca específica de la plataforma para iOS y Android")](platform-specific-images/specific-configure.png#lightbox)

4. Complete el asistente. Los proyectos siguientes se agregan a la solución:

  - **Proyecto de Android** : código específico de Android, opcionalmente, puede agregarse a este proyecto.
  - **Proyecto de iOS** : código específico de iOS, opcionalmente, puede agregarse a este proyecto.
  - **Proyecto de NuGet** : No se agregará código para este proyecto. Hace referencia a los otros proyectos y contiene la configuración de metadatos para la salida del paquete NuGet.
  - **Compartir proyecto** : código común debe agregarse a este proyecto, incluido el código específico de la plataforma en `#if` directivas de compilador.

5. Haga doble clic en el proyecto de NuGet y elija **opciones**, a continuación, abra el **paquete NuGet > metadatos** sección y escriba la [metadatos necesarios](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (como así como cualquier opcional metadatos):

  [![](platform-specific-images/specific-metadata-sml.png "Escriba los metadatos necesarios")](platform-specific-images/specific-metadata.png#lightbox)

6. También en el **opciones de proyecto** ventana, abra el **ensamblados de referencia** sección y elija qué perfiles PCL será compatible con la biblioteca compartida a través de "gancho":

  ![](platform-specific-images/specific-reference-assemblies.png "También en la ventana de opciones de proyecto, abra la sección de ensamblados de referencia y elegir qué perfiles PCL será compatible con la biblioteca compartida a través de gancho")

  > [!NOTE]
> "Gancho" significa que los ensamblados PCL solo contendrá la API expuesta la biblioteca (no puede contener el código específico de la plataforma). Si NuGet se agrega a un proyecto de Xamarin, las bibliotecas compartidas se compilará en la PCL, pero los ensamblados específicos de la plataforma contienen el código que utiliza realmente el iOS o un proyecto de Android.

7. Haga doble clic en el proyecto y elija **crear paquetes de NuGet** (o compilar o implementar la solución) y la **.nupkg** se guardará el archivo de paquete de NuGet en la **/bin/** carpeta ( Debug o Release, según la configuración).

  ![](platform-specific-images/create-nuget-package.png "Archivo de paquete de NuGet se guardará en la carpeta bin Debug o Release, dependiendo de la configuración")


## <a name="verifying-the-output"></a>Comprobar la salida

Paquetes de NuGet también son archivos ZIP, por lo que es posible inspeccionar la estructura interna del paquete generado.

Esta captura de pantalla muestra el contenido de un NuGet específica de la plataforma que admita iOS y Android y tenía dos ensamblados de referencia seleccionados:

![](platform-specific-images/nuget-output.png "Archivos contenidos en el paquete de NuGet")


## <a name="related-links"></a>Vínculos relacionados

- [Guía de metadatos](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
