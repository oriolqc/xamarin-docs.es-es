---
title: Introducción al desarrollo de aplicaciones de empresa
description: Este capítulo ofrece una introducción al desarrollo de aplicaciones de empresa y presenta la aplicación móvil de eShopOnContainers.
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 4fbb4047b95fd70f829cd79e4ea26b2958273297
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831177"
---
# <a name="introduction-to-enterprise-app-development"></a>Introducción al desarrollo de aplicaciones de empresa

Independientemente de la plataforma, afrontan diferentes desafíos a los desarrolladores de aplicaciones empresariales:

-   Requisitos de aplicación que se pueden cambiar con el tiempo.
-   Desafíos y nuevas oportunidades de negocio.
-   Comentarios en curso durante el desarrollo que puede afectar significativamente a los requisitos de la aplicación y el ámbito.

Con esto en mente, es importante compilar aplicaciones que se pueden modificar con facilidad o extendidas con el tiempo. Diseño de la capacidad de dichos adaptación puede ser difícil, ya que requiere una arquitectura que permite que las partes individuales de la aplicación para desarrollar y probar de forma aislada sin afectar al resto de la aplicación por separado.

Muchas aplicaciones empresariales son bastante complejas que requieren más de un desarrollador. Puede ser un desafío importante para decidir cómo diseñar una aplicación para que varios desarrolladores puedan trabajar eficazmente en distintas partes de la aplicación de forma independiente, garantizando que las piezas reúnen sin problemas cuando se integra en la aplicación.

El enfoque tradicional para diseñar y crear una aplicación de resultados en lo que se conoce como un *monolítica* app, donde los componentes están estrechamente acoplados sin separación clara entre ellos. Normalmente, este enfoque monolítica da lugar a las aplicaciones que son difíciles y eficaz de mantener, porque pueden ser difícil de resolver los errores sin que ello interrumpa otros componentes en la aplicación, y puede ser difícil para agregar nuevas características o reemplazar las características existentes.

Un remedio eficaz para estos desafíos es dividir una aplicación en componentes discretos y con acoplamiento flexible que pueden integrarse fácilmente entre sí en una aplicación. Este enfoque ofrece varias ventajas:

-   Permite la funcionalidad a desarrollado, probado, ampliado y mantenido por distintas personas o equipos individual.
-   Promueve la reutilización y una separación clara de intereses entre las capacidades horizontal la aplicación, como la autenticación y acceso a datos y las funcionalidades verticales, como la funcionalidad empresarial específica de la aplicación. Esto permite que las dependencias y las interacciones entre componentes de aplicación para administrar más fácilmente.
-   Le ayuda a mantener una separación de roles al permitir que distintas personas o equipos, para centrarse en una tarea específica o parte de la funcionalidad según sus conocimientos. En concreto, proporciona una separación más clara entre la interfaz de usuario y la lógica de negocios de la aplicación.

Sin embargo, hay muchos problemas que deben resolverse al dividir una aplicación en componentes discretos y con acoplamiento flexible. Entre ellas se incluyen las siguientes:

-   Decida cómo va a proporcionar una separación clara de intereses entre los controles de interfaz de usuario y su lógica. Una de las decisiones más importantes al crear una aplicación de empresa de Xamarin.Forms es si se debe colocar la lógica de negocios en archivos de código subyacente, o si se crea una separación clara de intereses entre los controles de interfaz de usuario y su lógica, para que la aplicación más fácil de mantener y probar. Para obtener más información, consulte [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
-   Determina si se usa un contenedor de inserción de dependencia. Contenedores de inserción de dependencia reducen la dependencia de acoplamiento entre los objetos por lo que proporciona una funcionalidad para construir instancias de clases con sus dependencias y administran su duración en función de la configuración del contenedor. Para obtener más información, consulte [inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
-   Elección entre eventos de plataforma que proporciona y escasamente acoplado basada en mensajes de la comunicación entre los componentes que no son convenientes para vincular mediante referencias de objeto y el tipo. Para obtener más información, vea Introducción a [comunicarse entre débilmente acoplados componentes](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
-   Decidir cómo navegar entre páginas, incluida la forma de invocar el panel de navegación, y donde debe residir la lógica de navegación. Para obtener más información, consulte [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md) (Navegación).
-   Determinación de cómo validar entradas de usuario son correctos. Debe incluir la decisión de cómo validar entradas de usuario y notificar al usuario sobre los errores de validación. Para obtener más información, consulte [validación](~/xamarin-forms/enterprise-application-patterns/validation.md).
-   Decidir cómo realizar la autenticación y cómo proteger los recursos con la autorización. Para obtener más información, consulte [autenticación y autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
-   Determinar cómo obtener acceso a datos remotos de web services, incluido cómo recuperar datos de forma confiable y cómo almacenar en caché los datos. Para obtener más información, consulte [acceso a datos remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
-   Decida cómo va a probar la aplicación. Para obtener más información, consulte [Unit Testing](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Esta guía proporciona instrucciones sobre estos problemas y se centra en la arquitectura para la creación de una aplicación de enterprise multiplataforma con Xamarin.Forms y patrones principales. Las instrucciones pretende ayudar a generar código adaptable, fáciles de mantener y probar, al tratar los escenarios habituales del desarrollo de aplicaciones de enterprise de Xamarin.Forms y al separar las preocupaciones de presentación, lógica de presentación y las entidades mediante la compatibilidad con el Patrón Model-View-ViewModel (MVVM).

## <a name="sample-application"></a>Aplicación de ejemplo

Esta guía incluye una aplicación de ejemplo eShopOnContainers, que es una tienda en línea que incluye la funcionalidad siguiente:

-   La autenticación y autorización en un servicio back-end.
-   Exploración de un catálogo de camisetas, tazas de café y otros productos de marketing.
-   Filtrado del catálogo.
-   Ordenar los elementos del catálogo.
-   Ver historial de pedidos del usuario.
-   Configuración de valores.

### <a name="sample-application-architecture"></a>Arquitectura de aplicación de ejemplo

Figura 1-1 proporciona una descripción general de la arquitectura de la aplicación de ejemplo.

![](introduction-images/architecture.png "arquitectura de alto nivel de eShopOnContainers")

**Figura 1-1**: arquitectura de alto nivel de eShopOnContainers

La aplicación de ejemplo incluye tres aplicaciones de cliente:

-   Una aplicación MVC desarrollado con ASP.NET Core.
-   Una aplicación de página única (SPA) desarrollado con Angular 2 y Typescript. Este enfoque para las aplicaciones web evita realizar una vuelta al servidor con cada operación.
-   Una aplicación móvil desarrollada con Xamarin.Forms, que es compatible con iOS, Android y la plataforma Universal de Windows (UWP).

Para obtener información acerca de las aplicaciones web, consulte [diseño de la arquitectura y desarrollo de aplicaciones Web modernas con ASP.NET Core y Microsoft Azure](https://aka.ms/WebAppEbook).

La aplicación de ejemplo incluye los siguientes servicios de back-end:

-   Un microservicio de identidad, que usa ASP.NET Core Identity y IdentityServer.
-   Un microservicio de catálogo, que es controlada por datos crear, lee, actualizar, eliminar (CRUD) de servicio que utiliza una base de datos de SQL Server mediante Entity Framework Core.
-   Un microservicio de pedidos, que es un servicio basado en dominio que usa patrones de diseño guiado por el dominio.
-   Un microservicio de cesta de la compra, que es un servicio CRUD controladas por datos que usa Redis Cache.

Estos servicios de back-end se implementan como microservicios con ASP.NET Core MVC y se implementan como contenedores únicos dentro de un único host de Docker. Colectivamente, estos servicios de back-end se conocen como aplicación de referencia eShopOnContainers. Las aplicaciones cliente se comunican con los servicios de back-end a través de una interfaz web de Representational State Transfer (REST). Para obtener más información acerca de los microservicios y Docker, consulte [Microservicios en contenedores](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Para obtener información sobre la implementación de los servicios de back-end, consulte [Microservicios de. NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservicios de .NET: Arquitectura para aplicaciones .NET en contenedor).

### <a name="mobile-app"></a>Aplicación móvil

Esta guía se centra en la creación de aplicaciones empresariales de desarrollo multiplataforma con Xamarin.Forms y la aplicación móvil de eShopOnContainers usa como ejemplo. Figura 1-2 se muestran las páginas de la aplicación móvil de eShopOnContainers que proporcionan la funcionalidad que se describen anteriormente.

[![](introduction-images/screenshots.png "La aplicación móvil de eShopOnContainers")](introduction-images/screenshots-large.png#lightbox "la aplicación móvil de eShopOnContainers")

**Figura 1-2**: La aplicación móvil de eShopOnContainers

La aplicación móvil consume los servicios back-end de la aplicación de referencia eShopOnContainers. Sin embargo, puede configurarse para consumir datos de servicios ficticios para aquellos que desean evitar la implementación de los servicios de back-end.

La aplicación móvil de eShopOnContainers ejercita la siguiente funcionalidad de Xamarin.Forms:

-   XAML
-   Controles
-   Enlaces
-   Convertidores de tipos
-   Estilos
-   Animaciones
-   Comandos:
-   comportamientos
-   Desencadenadores
-   Efectos
-   Representadores personalizados
-   MessagingCenter
-   Controles personalizados

Para obtener más información acerca de esta funcionalidad, consulte el [Xamarin.Forms documentación](~/xamarin-forms/index.yml), y [Creating Mobile Apps with Xamarin.Forms](https://aka.ms/xamebook).

Además, las pruebas unitarias se proporcionan para algunas de las clases en la aplicación móvil de eShopOnContainers.

#### <a name="mobile-app-solution"></a>Solución de aplicación móvil

La solución de aplicación móvil de eShopOnContainers organiza el código fuente y otros recursos en proyectos. Todos los proyectos utilizan carpetas para organizar el código fuente y otros recursos en categorías. En la tabla siguiente se describe los proyectos que componen la aplicación móvil de eShopOnContainers:

|Proyecto|DESCRIPCIÓN|
|--- |--- |
|eShopOnContainers.Core|Este proyecto es el proyecto de clases portables (PCL) de la biblioteca que contiene el código compartido y la interfaz de usuario compartida.|
|eShopOnContainers.Droid|Este proyecto contiene código específico de Android y es el punto de entrada para la aplicación de Android.|
|eShopOnContainers.iOS|Este proyecto incluye el código específico de iOS y es el punto de entrada para la aplicación de iOS.|
|eShopOnContainers.UWP|Este proyecto incluye el código específico de plataforma Universal de Windows (UWP) y es el punto de entrada para la aplicación de Windows.|
|eShopOnContainers.TestRunner.Droid|Este proyecto es el ejecutor de pruebas de Android para el proyecto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.iOS|Este proyecto es el ejecutor de pruebas de iOS para el proyecto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.Windows|Este proyecto es el ejecutor de pruebas de la plataforma Universal de Windows para el proyecto eShopOnContainers.UnitTests.|
|eShopOnContainers.UnitTests|Este proyecto contiene pruebas unitarias para el proyecto eShopOnContainers.Core.|

Las clases de la aplicación móvil de eShopOnContainers puede volver a usarse en cualquier aplicación de Xamarin.Forms con poca o ninguna modificación.

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core proyecto

El proyecto PCL eShopOnContainers.Core contiene las siguientes carpetas:

|Carpeta|DESCRIPCIÓN|
|--- |--- |
|Animaciones|Contiene clases que habilitan las animaciones se consuma en XAML.|
|comportamientos|Contiene los comportamientos que se exponen para ver las clases.|
|Controles|Contiene controles personalizados usados por la aplicación.|
|Convertidores de tipos|Contiene los convertidores de valores que se aplican una lógica personalizada a un enlace.|
|Efectos|Contiene el `EntryLineColorEffect` (clase), que se usa para cambiar el color del borde específicas de `Entry` controles.|
|Excepciones|Contiene personalizado `ServiceAuthenticationException`.|
|Extensiones|Contiene métodos de extensión para el `VisualElement` y `IEnumerable` clases.|
|Aplicaciones auxiliares|Contiene clases auxiliares para la aplicación.|
|Modelos|Contiene las clases del modelo de la aplicación.|
|Properties (Propiedades)|Contiene `AssemblyInfo.cs`, un archivo de metadatos de ensamblado. NET.|
|Servicios|Contiene interfaces y clases que implementan los servicios que se proporcionan a la aplicación.|
|Desencadenadores|Contiene el `BeginAnimation` desencadenador, que se utiliza para invocar una animación en XAML.|
|Validaciones|Contiene las clases implicadas en la validación de entrada de datos.|
|ViewModels|Contiene la lógica de aplicación que se expone a las páginas.|
|Vistas|Contiene las páginas de la aplicación.|

##### <a name="platform-projects"></a>Proyectos de plataforma

Los proyectos de plataforma contienen implementaciones de efecto, las implementaciones de representador personalizado y otros recursos específicos de la plataforma.

## <a name="summary"></a>Resumen

Plataformas y herramientas de desarrollo de aplicaciones móviles multiplataforma de Xamarin proporcionan una solución completa para clientes móviles B2E, B2B y B2C apps, y proporciona la capacidad de compartir código entre todas las plataformas de destino (iOS, Android y Windows) y lo que ayuda a reducir el costo total de propiedad. Las aplicaciones pueden compartir su código de lógica usuario interfaz y la aplicación, conservando la apariencia y comportamiento de la plataforma nativa.

Los desarrolladores de aplicaciones empresariales enfrentan a varios desafíos que pueden modificar la arquitectura de la aplicación durante el desarrollo. Por lo tanto, es importante compilar una aplicación para que se pueda modificar o extender con el tiempo. Diseño de la capacidad de dichos adaptación puede ser difícil, pero normalmente implica dividir una aplicación en componentes discretos y con acoplamiento flexible que pueden integrarse fácilmente entre sí en una aplicación.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
