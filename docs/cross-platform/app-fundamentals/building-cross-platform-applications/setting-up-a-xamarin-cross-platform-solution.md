---
title: 'Parte 3: configurar una solución de plataforma cruzada de Xamarin'
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: 6e8dfab76881bc972e00abe7cbbea238e335d9a9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>Parte 3: configurar una solución de plataforma cruzada de Xamarin

Independientemente de qué plataformas se usan, todos los proyectos de Xamarin utilizan el mismo formato de archivo de solución (Visual Studio **.sln** formato de archivo). Las soluciones pueden compartirse a través de entornos de desarrollo, incluso cuando no se puede cargar proyectos individuales (por ejemplo, un proyecto de Windows en Visual Studio para Mac).



Al crear un nuevo entre la aplicación de la plataforma, el primer paso es crear una solución en blanco. Esta sección Qué ocurre después: configurar los proyectos para la creación de aplicaciones móviles multiplataforma.

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>Uso compartido de código

Hacer referencia a la [opciones de uso compartido de código](~/cross-platform/app-fundamentals/code-sharing.md) documento para obtener una descripción detallada de cómo implementar el uso compartido de código entre plataformas.

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>Proyectos compartidos

El enfoque más sencillo para uso compartido de archivos de código es usar un [proyecto compartido](~/cross-platform/app-fundamentals/shared-projects.md).

Este método permite compartir el mismo código en proyectos de plataforma diferente y usar las directivas de compilador para incluir las rutas de código diferente, específico de la plataforma.

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>Bibliotecas de clases portables (PCL)

Históricamente un archivo de proyecto de .NET (y el ensamblado resultante) está dirigidos a una versión de un marco concreto. Esto evita que el proyecto o en el ensamblado está compartida por diferentes marcos de trabajo.

Una biblioteca de clases portables (PCL) es un tipo especial de proyecto que puede utilizarse en distintas plataformas CLI como Xamarin.iOS y Xamarin.Android, como WPF, plataforma Universal de Windows y Xbox. La biblioteca sólo puede utilizar un subconjunto de la plataforma .NET completo, limitada las plataformas de destino.

Puede leer más acerca de Xamarin [compatibilidad con bibliotecas de clases Portable](~/cross-platform/app-fundamentals/pcl.md) y siga las instrucciones que se indican para ver cómo la [TaskyPortable ejemplo](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable) funciona.


### <a name="net-standard"></a>.NET Standard

Introducida en 2016, [.NET estándar](~/cross-platform/app-fundamentals/net-standard.md) proyectos proporcionan una manera sencilla de compartir código entre plataformas, generar ensamblados a los que pueden utilizarse en Windows, Xamarin plataformas (iOS, Android, Mac) y Linux.

Bibliotecas estándar de .NET se pueden crear y utilizar como PCLs, excepto en que las API disponibles en cada versión (1.0 a 1.6) se detectan más fácilmente y cada versión es compatible con los números de versión más bajos.



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>Rellenar la solución

Independientemente de qué método se utiliza para compartir código, la estructura general de la solución debe implementar una arquitectura en capas que fomenta la elaboración de uso compartido de código.
El enfoque de Xamarin es código de grupo en dos tipos de proyecto:

-   **Proyecto de núcleo** : escribir código reutilizable en un solo lugar, compartir en diferentes plataformas. Use los principios de encapsulación para ocultar los detalles de implementación siempre que sea posible.
-   **Proyectos de aplicación específico de la plataforma** : use el código reutilizable con como acoplamiento pequeña como sea posible. Características específicas de plataforma se agregan en este nivel, basado en componentes que estén expuestos en el proyecto principal.


 <a name="Core_Project" />


### <a name="core-project"></a>Proyecto de núcleo

Proyectos de código compartido solo deben hacer referencia a ensamblados que están disponibles en todas las plataformas: Internet Explorer. al igual que los espacios de nombres comunes de framework `System`, `System.Core` y `System.Xml`.

Los proyectos compartidos deben implementar tanta funcionalidad ajeno a la interfaz de usuario tal y como es posible, lo que podría incluir las capas siguientes:

-   **Capa de datos** : código que se ocupa del almacenamiento de datos físico p. ej.  [SQLite NET](https://github.com/praeclarum/sqlite-net), como una base de datos alternativo [Realm.io](https://realm.io/products/realm-mobile-database/) o incluso archivos XML. Las clases de la capa de datos son normalmente solo se usa la capa de acceso a datos.
-   **Capa de acceso a datos** : define una API que admite las operaciones de datos necesarios para la funcionalidad de la aplicación, como métodos para tener acceso a listas de datos, elementos de datos individuales y crear, editar y eliminar.
-   **Nivel de acceso de servicio** – servicios de un nivel opcional para proporcionar en la nube a la aplicación. Contiene código que tiene acceso a recursos de red remota (servicios web, descargas de imágenes, etcetera) y posiblemente el almacenamiento en caché de los resultados.
-   **Capa de negocio** : definición de las clases del modelo y las clases de la fachada o el administrador que exponen la funcionalidad a las aplicaciones específicas de la plataforma.


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>Proyectos de aplicación específico de la plataforma

Proyectos específicos de plataforma deben hacer referencia a los ensamblados necesarios para enlazar a los SDK de la plataforma (Xamarin.iOS, Xamarin.Android, Xamarin.Mac o Windows), así como el proyecto de código compartido Core.

Deben implementar los proyectos específicos de plataforma:

-   **Nivel de aplicación** – enlace/conversión entre los objetos de la capa empresarial y la interfaz de usuario y la funcionalidad específica de plataforma.
-   **Capa de interfaz de usuario** : pantallas, controles de interfaz de usuario personalizada, presentación de la lógica de validación.


<a name="Example" />


### <a name="example"></a>Ejemplo

La arquitectura de la aplicación se muestra en este diagrama:

 [ ![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "La arquitectura de la aplicación se muestra en este diagrama")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

Esta captura de pantalla muestra una configuración de la solución con el proyecto de núcleo compartido, iOS y proyectos de aplicación de Android. El proyecto compartido contiene código relacionado con cada uno de los niveles arquitectónicos (código de negocio, servicio, datos y el acceso a datos):

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "El proyecto compartido contiene código relacionado con cada uno de los niveles arquitectónicos (código de negocio, servicio, datos y el acceso a datos)")


 <a name="Project_References" />


## <a name="project-references"></a>Referencias del proyecto

Referencias del proyecto reflejan las dependencias de un proyecto. Principales proyectos limitan sus referencias a ensamblados comunes para que el código sea fácil compartir.
Hacer referencia a proyectos de aplicación específico de la plataforma el código compartido, además de cualquier otro ensamblado específico de la plataforma que necesitan para aprovechar las ventajas de la plataforma de destino.

Los proyectos de aplicación cada una referencia de proyecto compartido y contienen el código de interfaz de usuario necesario para la funcionalidad presente para el usuario, como se muestra en estas capturas de pantalla:

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "La aplicación de los proyectos cada referencia de proyecto compartido") ![ ] (setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "la aplicación de los proyectos cada referencia de proyecto compartido")


Se proporcionan ejemplos específicos de cómo se deben estructurar los proyectos en los casos de estudio.

 <a name="Adding_Files" />


## <a name="adding-files"></a>Agregar archivos

 <a name="Build_Action" />


### <a name="build-action"></a>Acción de compilación

Es importante establecer la acción de compilación correcta para determinados tipos de archivo. Esta lista muestra la acción de compilación para algunos tipos de archivo comunes:

-  **Todos los archivos de C#** : acción de compilación: compilación
-   **Imágenes en Xamarin.iOS & Windows** : acción de compilación: contenido
-   **Archivos XIB y guiones gráficos en Xamarin.iOS** : acción de compilación: interfaz
-   **Imágenes y diseños AXML en Android** : acción de compilación: AndroidResource
-  **Los archivos XAML en proyectos de Windows** : acción de compilación: página
-  **Los archivos XAML de Xamarin.Forms** : acción de compilación: EmbeddedResource


Por lo general el IDE detectará el tipo de archivo y sugerirá la acción de compilación correcta.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Distinción de mayúsculas y minúsculas

Por último, recuerde que algunas plataformas tengan sistemas de archivos entre mayúsculas y minúsculas (p. ej.
iOS y Android), así que asegúrese de utilizar un estándar de nomenclatura de archivo coherente y asegurarse de que los nombres de archivo que se usa en el código coinciden exactamente con el sistema de archivos. Esto es especialmente importante para las imágenes y otros recursos que se hace referencia en el código.
