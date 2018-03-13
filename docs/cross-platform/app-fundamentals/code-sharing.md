---
title: "Opciones de código compartido"
description: "Este documento compara los distintos métodos para compartir el código entre los proyectos multiplataforma: proyectos compartidos, bibliotecas de clases Portable y .NET estándar, incluidas las ventajas y desventajas de cada uno."
ms.topic: article
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e7289d92043bdbe9e4ec55776835530f8ccec526
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="sharing-code-options"></a>Opciones de código compartido

_Este documento compara los distintos métodos para compartir el código entre los proyectos multiplataforma: proyectos compartidos, bibliotecas de clases Portable y .NET estándar, incluidas las ventajas y desventajas de cada uno._

Hay tres métodos para compartir código entre aplicaciones multiplataforma:

-   [**Los proyectos compartidos** ](#Shared_Projects) : usar el tipo de proyecto de recurso compartido para organizar el código fuente y usar directivas de compilador #if según sea necesario para administrar los requisitos específicos de la plataforma.
-   [**Bibliotecas de clases Portable** ](#Portable_Class_Libraries) : crear un como destino la biblioteca de clases portables (PCL) en las plataformas que desea admitir y usar Interfaces para proporcionar la funcionalidad específica de la plataforma.
-   [**Bibliotecas estándar de .NET** ](#Net_Standard) : proyectos de .NET estándar funcionan de forma similar a PCLs, requerir el uso de Interfaces que se va a insertar la funcionalidad específica de la plataforma.

El objetivo de una estrategia para compartir el código es compatible con la arquitectura que se muestra en este diagrama, donde un solo código base puede ser utilizado por varias plataformas.

 ![](code-sharing-images/conceptualarchitecture.png "Arquitectura de la aplicación de código compartido")

Este artículo comparan los tres métodos para ayudarle a elegir el tipo de proyecto adecuado para sus aplicaciones.

<a name="Shared_Projects" />

## <a name="shared-projects"></a>Proyectos compartidos

El enfoque más sencillo para uso compartido de archivos de código es usar un [proyecto compartido](~/cross-platform/app-fundamentals/shared-projects.md).

Esta captura de pantalla muestra un archivo de solución que contiene tres proyectos de aplicación (para Android, iOS y Windows Phone), con un **Shared** proyecto que contiene common archivos de código de origen de C#:

 ![](code-sharing-images/sharedsolution.png "Solución de proyecto compartido")

La arquitectura conceptual se muestra en el diagrama siguiente, donde cada proyecto incluye todos los archivos de código fuente compartido:

 ![](code-sharing-images/sharedassetproject.png "Diagrama de proyecto compartidos")


### <a name="example"></a>Ejemplo

Una aplicación multiplataforma que es compatible con iOS, Android y Windows Phone podría necesitar un proyecto de aplicación para cada plataforma. El código común se ubica en el proyecto compartido.

Una solución de ejemplo contendría las carpetas y los proyectos (nombres de proyecto se han elegido para expresividad, los proyectos no es necesario seguir estas directrices de nomenclatura) siguientes:

-   **Shared** : proyecto compartido que contiene el código común a todos los proyectos.
-   **AppAndroid** : proyecto de aplicación de Xamarin.Android.
-   **AppiOS** : proyecto de aplicación de Xamarin.iOS.
-   **AppWinPhone** : proyecto de aplicación de Windows Phone.


De este modo los proyectos de aplicación de tres comparten el mismo código de origen (los archivos de C# compartido). Las modificaciones al código compartido se compartirán entre todos los tres proyectos.


### <a name="benefits"></a>Ventajas

-  Permite compartir código entre varios proyectos.
-  Código compartido se puede bifurcar en función de la plataforma mediante directivas de compilador (p. ej. usar `#if __ANDROID__` , como se describe en el [compilar aplicaciones de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento).
-  Proyectos de aplicación pueden incluir referencias de específico de la plataforma que puede usar el código compartido (como el uso de `Community.CsharpSqlite.WP7` en el ejemplo Tasky para Windows Phone).



### <a name="disadvantages"></a>Desventajas

-  A diferencia de la mayoría de tipos de proyecto, un proyecto compartido no tiene ningún ensamblado 'salida'. Durante la compilación, los archivos se tratan como parte del proyecto que hace referencia y compilados en dicho ensamblado. Si desea compartir su código como un ensamblado, a continuación, bibliotecas de clases Portable o estándar de .NET es la mejor solución.
-  Refactorizaciones que afectan al código dentro de las directivas de compilador 'inactive' no actualizarán el código.


 <a name="Shared_Remarks" />

### <a name="remarks"></a>Comentarios

Una buena solución para los desarrolladores de aplicaciones para escribir código que solo está pensado para uso compartido en su aplicación (y no distribuir a otros desarrolladores).

 <a name="Portable_Class_Libraries" />


## <a name="portable-class-libraries"></a>Bibliotecas de clases portables


Bibliotecas de clases Portable están [explica con detalle aquí](~/cross-platform/app-fundamentals/pcl.md).

 ![](code-sharing-images/portableclasslibrary.png "Diagrama de la biblioteca de clases Portable")


### <a name="benefits"></a>Ventajas

-  Permite compartir código entre varios proyectos.
-  Las operaciones de refactorización siempre actualización todas las referencias.


### <a name="disadvantages"></a>Desventajas

-  No se puede usar las directivas de compilador.
-  Solo un subconjunto de .NET framework está disponible para usarse, determinado por el perfil seleccionado (vea la [Introducción a PCL](~/cross-platform/app-fundamentals/pcl.md) para obtener más información).


### <a name="remarks"></a>Comentarios

Una buena solución si tiene previsto compartir el ensamblado resultante con otros desarrolladores.



<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>Bibliotecas estándar de .NET

Es el estándar de .NET [explica con detalle aquí](~/cross-platform/app-fundamentals/net-standard.md).

![](code-sharing-images/netstandard.png "Diagrama de .NET estándar")

### <a name="benefits"></a>Ventajas

-  Permite compartir código entre varios proyectos.
-  Las operaciones de refactorización siempre actualización todas las referencias.
-  Un área de superficie grande de la biblioteca de clases de Base (BCL) de .NET está disponible a perfiles PCL.

### <a name="disadvantages"></a>Desventajas

 -  No se puede usar las directivas de compilador.

### <a name="remarks"></a>Comentarios

.NET estándar es similar a PCL, pero con un modelo más sencillo para la compatibilidad con la plataforma y un mayor número de clases a partir de la BCL.



## <a name="summary"></a>Resumen

El código de uso compartido de estrategia que elija está determinado por las plataformas de que destino. Elegir un método que se adapte mejor a su proyecto.

PCL o .NET estándar son una buena elección para la creación de bibliotecas de código puede compartirse (especialmente a publicar en NuGet). Los proyectos compartidos funcionan bien para los desarrolladores de aplicaciones que pretende utilizar una gran cantidad de funcionalidad específica de plataformas en sus aplicaciones entre platforma.


## <a name="related-links"></a>Vínculos relacionados

- [Compilar aplicaciones de plataforma cruzada (documento principal)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md)
- [Proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Caso práctico: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Ejemplo tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Ejemplo tasky mediante PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
