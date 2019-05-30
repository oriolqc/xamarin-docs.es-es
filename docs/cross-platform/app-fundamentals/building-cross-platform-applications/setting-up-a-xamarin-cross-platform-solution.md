---
title: 'Parte 3: configuración de una solución multiplataforma de Xamarin'
description: Este documento describe cómo configurar una solución multiplataforma en Xamarin. Describe el código de diversas estrategias como los proyectos compartidos y .NET Standard de uso compartido.
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: d20275bab4e4ce90f902a5e72321701d94b1d416
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354068"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3: configuración de una solución multiplataforma de Xamarin

Independientemente de qué plataformas se utilizan, todos los proyectos de Xamarin usan el mismo formato de archivo de solución (Visual Studio **.sln** formato de archivo). Las soluciones se pueden compartir entre entornos de desarrollo, incluso cuando no se puede cargar proyectos individuales (por ejemplo, un proyecto de Windows en Visual Studio para Mac).



Al crear una nueva aplicación multiplataforma, el primer paso es crear una solución en blanco. Esta sección explica lo que sucede después: cómo configurar los proyectos para compilar aplicaciones móviles multiplataforma.

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>Uso compartido de código

Hacer referencia a la [opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md) documento para obtener una descripción detallada de cómo implementar el código compartido entre plataformas.

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>Proyectos compartidos

El enfoque más simple para uso compartido de archivos de código es usar un [proyecto compartido](~/cross-platform/app-fundamentals/shared-projects.md).

Este método permite compartir el mismo código en proyectos de plataforma diferente y usar directivas de compilador para incluir las rutas de código diferentes, específicas de la plataforma.

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>Bibliotecas de clases portables (PCL)

Históricamente un archivo de proyecto de .NET (y el ensamblado resultante) está dirigidos a una versión específica de framework. Esto evita que el proyecto o en el ensamblado que se comparte por diferentes marcos de trabajo.

Una biblioteca de clases Portable (PCL) es un tipo especial de proyecto que puede usarse en distintas plataformas CLI como Xamarin.iOS y Xamarin.Android, como WPF, plataforma Universal de Windows y Xbox. La biblioteca sólo puede utilizar un subconjunto de completo a .NET framework, limitado por las plataformas de destino.

Puede leer más acerca de Xamarin [compatibilidad con bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md) y siga las instrucciones para ver cómo el [TaskyPortable ejemplo](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) funciona.


### <a name="net-standard"></a>.NET Standard

Incorporado en 2016, [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) proyectos proporcionan una manera fácil compartir código entre plataformas, generar los ensamblados que se pueden usar en Windows, Xamarin plataformas (iOS, Android, Mac) y Linux.

Bibliotecas de .NET standard se pueden crear y utilizar como PCL, excepto en que se descubren más fácilmente las API disponibles en cada versión (de 1.0 a 1.6) y cada versión es compatible con los números más bajos de versión.



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>Rellenar la solución

Independientemente de qué método se utiliza para compartir código, la estructura general de la solución debe implementar una arquitectura por capas que fomenta el uso compartido de código.
Código de grupo en dos tipos de proyecto es el enfoque de Xamarin:

-   **Proyecto de Core** : escribir código reutilizable en un mismo lugar, para compartirse entre diferentes plataformas. Use los principios de encapsulación para ocultar los detalles de implementación siempre que sea posible.
-   **Proyectos de aplicación específico de la plataforma** – consumen el código reutilizable con como poco acoplamiento como sea posible. En este nivel, que se basa en los componentes que se exponen en el proyecto de Core, se agregan características específicas de plataforma.


 <a name="Core_Project" />


### <a name="core-project"></a>Proyecto de Core

Proyectos de código compartido solo deben hacer referencia a ensamblados que están disponibles en todas las plataformas: ie. al igual que los espacios de nombres comunes de framework `System`, `System.Core` y `System.Xml`.

Proyectos compartidos deben implementar tanta funcionalidad que no son de interfaz de usuario, como es posible, lo que podría incluir las siguientes capas:

-   **Capa de datos** : código que se encarga de almacenamiento de datos físicos; por ejemplo.  [SQLite-NET](https://github.com/praeclarum/sqlite-net), como una base de datos alternativo [Realm.io](https://realm.io/products/realm-mobile-database/) o incluso archivos XML. Las clases de capa de datos son normalmente solo se usa la capa de acceso a datos.
-   **Capa de acceso a datos** : define una API que admita las operaciones de datos necesarios para la funcionalidad de la aplicación, tales como métodos para tener acceso a listas de datos, elementos de datos individuales y también crear, editar y eliminarlos.
-   **Capa de acceso de servicio** – servicios de un nivel opcional para proporcionar en la nube a la aplicación. Contiene código que tiene acceso a recursos de red remota (servicios web, descargas de imágenes, etcetera) y, posiblemente, almacenamiento en caché de los resultados.
-   **Capa de negocio** : definición de las clases del modelo y las clases de fachada o de administrador que exponen la funcionalidad a las aplicaciones específicas de la plataforma.


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>Proyectos de aplicación específico de la plataforma

Los ensamblados necesarios para enlazar a los SDK de la plataforma (Xamarin.iOS, Xamarin.Android, Xamarin.Mac o Windows), así como el proyecto de código compartido Core deben hacer referencia a proyectos específicos de plataforma.

Deben implementar los proyectos específicos de plataforma:

-   **Nivel de aplicación** – enlace/conversión entre los objetos de capa de negocio y la interfaz de usuario y la funcionalidad específica de plataforma.
-   **Capa de interfaz de usuario** – pantallas, controles de interfaz de usuario personalizada, presentación de la lógica de validación.


<a name="Example" />


### <a name="example"></a>Ejemplo

La arquitectura de la aplicación se muestra en este diagrama:

 [ ![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "La arquitectura de la aplicación se muestra en este diagrama")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Esta captura de pantalla muestra una configuración de solución con el proyecto de núcleo compartido, iOS y los proyectos de aplicación de Android. El proyecto compartido contiene código relacionado con cada una de las capas de arquitectura (código de negocio, servicio, datos y acceso a datos):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "El proyecto compartido contiene código relacionado con cada una de las capas de arquitectura (código de negocio, servicio, datos y acceso a datos)")


 <a name="Project_References" />


## <a name="project-references"></a>Referencias del proyecto

Las referencias de proyecto reflejan las dependencias de un proyecto. Los proyectos de Core limitan sus referencias a ensamblados de common para que el código es muy fácil compartir.
El código compartido, además de otros ensamblados específicos de la plataforma que necesitan para aprovechar las ventajas de la plataforma de destino hacen referencia a proyectos de aplicación específico de la plataforma.

Los proyectos de aplicación cada referencia de proyecto compartido y contienen el código de interfaz de usuario necesario para la funcionalidad presente al usuario, como se muestra en estas capturas de pantalla:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "La aplicación de los proyectos cada referencia de proyecto compartido") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "la aplicación de los proyectos cada referencia de proyecto compartido")


Se proporcionan ejemplos específicos de cómo se deben estructurar los proyectos en los casos prácticos.

 <a name="Adding_Files" />


## <a name="adding-files"></a>Agregar archivos

 <a name="Build_Action" />


### <a name="build-action"></a>Acción de compilación

Es importante establecer la acción de compilación correcta para determinados tipos de archivo. Esta lista muestra la acción de compilación para algunos tipos de archivo comunes:

-  **Todos los C# archivos** : acción de compilación: Compile
-   **Las imágenes en Xamarin.iOS & Windows** : acción de compilación: Contenido
-   **Los archivos XIB y guiones gráficos en Xamarin.iOS** : acción de compilación: InterfaceDefinition
-   **Imágenes y diseños AXML en Android** : acción de compilación: AndroidResource
-  **Archivos XAML en proyectos de Windows** : acción de compilación: Página
-  **Los archivos XAML de Xamarin.Forms** : acción de compilación: EmbeddedResource


Por lo general el IDE detectará el tipo de archivo y sugerir la acción de compilación correcta.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Distinción de mayúsculas y minúsculas

Por último, recuerde que algunas plataformas tienen sistemas de archivos distingue mayúsculas de minúsculas (p ej.
iOS y Android) así que asegúrese de utilizar un estándar de nomenclatura coherente del archivo y asegúrese de que los nombres de archivo que se usa en el código coinciden exactamente con el sistema de archivos. Esto es especialmente importante para las imágenes y otros recursos que se hace referencia en el código.
