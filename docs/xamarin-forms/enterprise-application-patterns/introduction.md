---
title: Introducción
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a4f1f6ba820221be7553405f570911d3dc66a657
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction"></a>Introducción

Independientemente de la plataforma, los desarrolladores de aplicaciones empresariales enfrentan a varios desafíos:

-   Requisitos de las aplicaciones que pueden cambiar con el tiempo.
-   Desafíos y nuevas oportunidades de negocio.
-   Comentarios en curso durante el desarrollo que puede afectar significativamente al ámbito y los requisitos de la aplicación.

Con esto en mente, es importante compilar aplicaciones que se pueden modificar fácilmente o extendidas con el tiempo. Diseñar para tal adaptabilidad puede ser difícil, ya que requiere una arquitectura que permite que las partes individuales de la aplicación para desarrollar y probar de forma aislada sin afectar al resto de la aplicación por separado.

Muchas aplicaciones empresariales son bastante complejas como para requerir más de un desarrollador de software. Puede ser un desafío importante para decidir cómo diseñar una aplicación para que varios desarrolladores puedan trabajar eficazmente en diferentes partes de la aplicación de forma independiente, asegurándose de que las partes unificación sin problemas cuando se integra en la aplicación.

El enfoque tradicional para diseñar y compilar una aplicación de resultados en lo que se conoce como un *monolítico* aplicación, donde los componentes están estrechamente con ninguna separación clara entre ellos. Normalmente, este enfoque monolítico da lugar a las aplicaciones que son difíciles y eficaz de mantener, dado que pueden ser difícil de resolver los errores sin que ello interrumpa otros componentes en la aplicación, y puede ser difícil para agregar nuevas características o reemplazar las características existentes.

Una solución efectiva para estos desafíos es dividir una aplicación en componentes discretos y con acoplamiento flexible que pueden integrarse fácilmente juntos en una aplicación. Este enfoque ofrece varias ventajas:

-   Permite la funcionalidad individual desarrollado, probado, extendido y mantenida por distintas personas o equipos.
-   Promueve la reutilización y una separación clara de intereses entre capacidades horizontal de la aplicación, como la autenticación y acceso a datos y las capacidades verticales, como las funciones de negocio específicas de aplicación. Esto permite que las dependencias e interacciones entre los componentes de aplicación para administrarlos más fácilmente.
-   Ayuda a mantener una separación de roles proporcionando a distintas personas o equipos, centrarse en una tarea específica o parte de la funcionalidad según su experiencia. En particular, proporciona una separación más nítida entre la interfaz de usuario y la lógica de negocios de la aplicación.

Sin embargo, hay muchos problemas que deben resolverse al crear particiones en una aplicación en componentes discretos y con acoplamiento flexible. Se incluyen los siguientes:

-   Decidir cómo proporcionar una separación clara de intereses entre los controles de interfaz de usuario y su lógica. Una de las decisiones más importantes al crear una aplicación de empresa de Xamarin.Forms es si se debe colocar la lógica de negocios en archivos de código subyacente, o si se crea una separación clara de intereses entre los controles de interfaz de usuario y la lógica para que la aplicación más fácil de mantener y pueden someterse a prueba. Para obtener más información, consulte [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
-   Determinar si se debe usar un contenedor de inyección de dependencia. Contenedores de inyección de dependencia reducen la dependencia de acoplamiento entre objetos por lo que proporciona una funcionalidad para construir instancias de clases con sus dependencias insertados y administran su duración en función de la configuración del contenedor. Para obtener más información, consulte [inyección de dependencia](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
-   Elegir entre los eventos de plataforma que proporciona y flexible de acoplamiento comunicación basada en mensajes entre los componentes que son poco práctico vincular por referencias de objeto y el tipo. Para obtener más información, consulte Introducción a [comunicarse entre débilmente acoplados componentes](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
-   Decidir cómo navegar entre páginas, incluido cómo invocar la navegación, y donde debe residir la lógica de navegación. Para obtener más información, consulte [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md) (Navegación).
-   Determinar cómo validar proporcionados por el usuario son correctos. Debe incluir la decisión de cómo validar proporcionados por el usuario y cómo notificar al usuario sobre los errores de validación. Para obtener más información, consulte [validación](~/xamarin-forms/enterprise-application-patterns/validation.md).
-   Decidir cómo realizar la autenticación y cómo proteger los recursos con autorización. Para obtener más información, consulte [autenticación y autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
-   Determinar cómo obtener acceso a datos remotos de web services, incluido cómo recuperar datos de forma confiable y cómo almacenar en caché los datos. Para obtener más información, consulte [obtiene acceso a datos remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
-   Decidir cómo probar la aplicación. Para obtener más información, consulte [Unit Testing](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Esta guía proporciona instrucciones acerca de estos problemas y se centra en la arquitectura para la creación de una aplicación empresarial de multiplataforma usando Xamarin.Forms y patrones principales. Tiene como objetivo ayudar a generar código comprobable, adaptable y fácil de mantener, arreglando comunes escenarios de desarrollo de aplicaciones de empresa de Xamarin.Forms y mediante la separación de las preocupaciones de presentación, lógica de presentación y entidades a través de la compatibilidad con las instrucciones del Modelo de Model-View-ViewModel (MVVM).

## <a name="sample-application"></a>Aplicación de ejemplo

Esta guía incluye una aplicación de ejemplo, eShopOnContainers, que es una tienda en línea que incluye la funcionalidad siguiente:

-   Autenticación y autorización en un servicio back-end.
-   Examinar un catálogo de camisetas, tazas de café y otros elementos de marketing.
-   Filtrar el catálogo.
-   Ordenar los elementos del catálogo.
-   Ver el historial de pedidos del usuario.
-   Configuración de valores.

### <a name="sample-application-architecture"></a>Arquitectura de la aplicación de ejemplo

Figura 1-1 proporciona una descripción general de la arquitectura de la aplicación de ejemplo.

![](introduction-images/architecture.png "arquitectura de alto nivel eShopOnContainers")

**Figura 1-1**: arquitectura de alto nivel eShopOnContainers

La aplicación de ejemplo incluye tres aplicaciones de cliente:

-   Una aplicación de MVC desarrollado con ASP.NET Core.
-   Una aplicación de página única (SPA) desarrollado con 2 Angular y Typescript. Este enfoque para las aplicaciones web evita realizar un envío y recepción en el servidor con cada operación.
-   Una aplicación móvil se desarrolló con Xamarin.Forms, que es compatible con la plataforma Universal de Windows (UWP), iOS y Android.

Para obtener información acerca de las aplicaciones web, consulte [diseño de la arquitectura y desarrollo de aplicaciones de Web modernas con ASP.NET Core y Microsoft Azure](http://aka.ms/WebAppEbook).

La aplicación de ejemplo incluye los siguientes servicios de back-end:

-   Un microservicio de identidad, que usa ASP.NET Core Identity y IdentityServer.
-   Un microservicio de catálogo, que es una creación controladas por datos, lee, actualizar y eliminar servicio (CRUD) que utiliza una base de datos de SQL Server mediante Entity Framework Core.
-   Una ordenación microservicio, que es un servicio controlada por el dominio que usa patrones de diseño basado en dominio.
-   Microservicio de la cesta de compra, que es un servicio CRUD controladas por datos que usa la caché en Redis.

Estos servicios back-end se implementan como microservicios con MVC de ASP.NET Core y se implementan como únicos contenedores dentro de un único host de Docker. Colectivamente, estos servicios back-end se denominan aplicaciones hacen referencia a la eShopOnContainers. Las aplicaciones cliente se comunican con los servicios back-end a través de una interfaz web de Representational State Transfer (REST). Para obtener más información acerca de microservicios y Docker, consulte [Microservicios en contenedores](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Para obtener información acerca de la implementación de los servicios back-end, vea [Microservicios. NET: arquitectura de aplicaciones de .NET en contenedores](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>Aplicación móvil

Esta guía se centra en la creación de aplicaciones de empresa entre plataformas con Xamarin.Forms y utiliza la aplicación móvil eShopOnContainers como ejemplo. Figura 1-2 muestra las páginas de la aplicación móvil eShopOnContainers que proporcionan la funcionalidad que se describen anteriormente.

[![](introduction-images/screenshots.png "La aplicación móvil eShopOnContainers")](introduction-images/screenshots-large.png#lightbox "la aplicación móvil eShopOnContainers")

**Figura 1-2**: la aplicación móvil eShopOnContainers

La aplicación móvil consume los servicios back-end proporcionados por la aplicación de referencia de eShopOnContainers. Sin embargo, puede configurarse para consumir datos desde los servicios ficticios para quienes desea evitar la implementación de los servicios back-end.

La aplicación móvil eShopOnContainers ejercita la funcionalidad de Xamarin.Forms siguiente:

-   XAML
-   Controles
-   Enlaces
-   Convertidores de
-   Estilos
-   Animaciones
-   Comandos
-   comportamientos
-   Desencadenadores
-   Efectos
-   Representadores personalizados
-   MessagingCenter
-   Controles personalizados

Para obtener más información acerca de esta funcionalidad, consulte la [Xamarin.Forms documentación](~/xamarin-forms/index.yml), y [crear aplicaciones móviles con Xamarin.Forms](https://aka.ms/xamebook).

Además, las pruebas unitarias se proporcionan para que algunas de las clases en la aplicación móvil eShopOnContainers.

#### <a name="mobile-app-solution"></a>Solución de aplicación móvil

La solución de aplicación móvil eShopOnContainers organiza el código fuente y otros recursos en proyectos. Todos los proyectos usar carpetas para organizar el código fuente y otros recursos en categorías. En la tabla siguiente se describe los proyectos que forman la aplicación móvil eShopOnContainers:

|Proyecto|Descripción|
|--- |--- |
|eShopOnContainers.Core|Este proyecto es el proyecto de biblioteca (PCL) de clases portable que contiene el código compartido y la interfaz de usuario compartida.|
|eShopOnContainers.Droid|Este proyecto contiene código específico de Android y es el punto de entrada para la aplicación de Android.|
|eShopOnContainers.iOS|Este proyecto contiene código específico de iOS y es el punto de entrada para la aplicación de iOS.|
|eShopOnContainers.UWP|Este proyecto contiene código específico de plataforma Universal de Windows (UWP) y es el punto de entrada para la aplicación de Windows.|
|eShopOnContainers.TestRunner.Droid|Este proyecto es el ejecutor de pruebas de Android para el proyecto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.iOS|Este proyecto es el ejecutor de pruebas de iOS para el proyecto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.Windows|Este proyecto es el ejecutor de pruebas de la plataforma Universal de Windows para el proyecto eShopOnContainers.UnitTests.|
|eShopOnContainers.UnitTests|Este proyecto contiene pruebas unitarias para el proyecto eShopOnContainers.Core.|

Las clases de la aplicación móvil eShopOnContainers puede volver a usar en cualquier aplicación de Xamarin.Forms con poca o ninguna modificación.

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core proyecto

El proyecto PCL eShopOnContainers.Core contiene las siguientes carpetas:

|Carpeta|Descripción|
|--- |--- |
|Animaciones|Contiene clases que habilitan las animaciones a ser consumidos en XAML.|
|comportamientos|Contiene los comportamientos que se exponen para ver las clases.|
|Controles|Contiene controles personalizados usados por la aplicación.|
|Convertidores de|Contiene los convertidores de valores que aplican la lógica personalizada a un enlace.|
|Efectos|Contiene el `EntryLineColorEffect` (clase), que se utiliza para cambiar el color del borde específicas de `Entry` controles.|
|Excepciones|Contiene la custom `ServiceAuthenticationException`.|
|Extensiones|Contiene métodos de extensión para la `VisualElement` y `IEnumerable` clases.|
|Aplicaciones auxiliares|Contiene clases de aplicación auxiliar para la aplicación.|
|Modelos|Contiene las clases del modelo de la aplicación.|
|Propiedades|Contiene `AssemblyInfo.cs`, un archivo de metadatos de ensamblado. NET.|
|Servicios|Contiene interfaces y clases que implementan los servicios que se proporcionan a la aplicación.|
|Desencadenadores|Contiene el `BeginAnimation` desencadenador, que se utiliza para invocar una animación en XAML.|
|Validaciones|Contiene las clases implicadas en la validación de entrada de datos.|
|ViewModels|Contiene la lógica de aplicación que se expone a las páginas.|
|Vistas|Contiene las páginas de la aplicación.|

##### <a name="platform-projects"></a>Proyectos de plataforma

Los proyectos de plataforma contienen implementaciones de efecto, las implementaciones de representador personalizado y otros recursos específicos de la plataforma.

## <a name="summary"></a>Resumen

Plataformas y herramientas de desarrollo de aplicaciones móviles multiplataforma de Xamarin proporcionan una solución completa para clientes móviles B2E, B2B y B2C aplicaciones, ofrece la capacidad de compartir código entre todas las plataformas de destino (iOS, Android y Windows) y le ayuda a reducir el costo total de propiedad. Las aplicaciones pueden compartir su código de lógica usuario interfaz y la aplicación, conservando la apariencia y funcionamiento de la plataforma nativa.

Los desarrolladores de aplicaciones empresariales enfrentan a varios desafíos que pueden modificar la arquitectura de la aplicación durante el desarrollo. Por lo tanto, es importante compilar una aplicación para que se puede modificar o extender con el tiempo. Diseñar para tal adaptabilidad puede ser difícil, pero normalmente implica crear particiones de una aplicación en componentes discretos y con acoplamiento flexible que pueden integrarse fácilmente juntos en una aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
