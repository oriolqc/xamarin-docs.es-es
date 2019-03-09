---
title: Introducción a la integración continua con Xamarin
description: Este documento describe la integración continua con Xamarin. Describe el control de versiones y varios entornos de integración continua.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: lobrien
ms.author: laobri
ms.date: 07/19/2017
ms.openlocfilehash: f2db5f4acd57cbf887d9955d9ea61fce4427c1c3
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672409"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introducción a la integración continua con Xamarin

_La integración continua es una práctica de ingeniería de software en el que se compila una compilación automatizada y opcionalmente, prueba una aplicación cuando el código se agrega o cambia los desarrolladores en el repositorio de control de versiones del proyecto. En este artículo se tratan los conceptos generales de la integración continua y algunas de las opciones disponibles para la integración continua con proyectos de Xamarin._

Es habitual en los proyectos de software para los desarrolladores trabajen en paralelo. En algún momento, es necesario integrar todas estas secuencias paralelas de trabajo en una base de código que conforma el producto final. En los comienzos del desarrollo de software, esta integración se realizó al final de un proyecto, que era un proceso difícil y riesgo.

Integración continua (CI) evitar esas complejidades, combina los cambios de todos los desarrolladores en el código base común de forma continua, normalmente cada vez que los desarrolladores se comprueba los cambios en el proyecto comparten repositorio de código. En cada comprobación desencadena una compilación automatizada y ejecuta pruebas automatizadas para comprobar que el código recién introducido no interrumpir el código existente.  De este modo, elementos de configuración presenta errores y problemas inmediatamente y garantiza que todos los miembros del equipo mantenerse al día con los demás trabajo. Esto da como resultado un código base coherente y estable.

Sistemas de integración continua tienen dos partes principales:

- **Control de versiones** : Control de versión (VC), con lo que también se denomina control de código fuente o administración de código fuente, consolida todo el código de un proyecto en un único repositorio compartido y mantiene un historial completo de todos los cambios a todos los archivos. Este repositorio, conoce a menudo como el *mainline* o *maestro* bifurcar, contiene el código fuente que se utilizará en última instancia para la producción de compilación o versión de la aplicación. Existen muchos productos comerciales para esta tarea, que normalmente permiten equipos o usuarios bifurcar una copia del código en ramas secundarias donde puedan hacer grandes cambios o llevar a cabo experimentos sin riesgos para la rama maestra y código abierto. Una vez que se validan los cambios en una rama secundaria, a continuación, pueden todos juntos vuelven a combinar en la bifurcación principal.
- **Servidor de integración continua** : el servidor de integración continua es responsable de recopilar todos los artefactos de un proyecto (código fuente, imágenes, vídeos, las bases de datos, las pruebas automatizadas, etc.), compilar la aplicación y ejecución de pruebas automatizadas. De nuevo, hay muchos código abierto y herramientas de servidor CI comerciales.

Los desarrolladores suelen tengan una copia de una o varias ramas de trabajo en sus estaciones de trabajo, que inicialmente se realiza el trabajo. Una vez completado un conjunto apropiado de trabajo, los cambios son "incorporar" o "confirmados" en la rama adecuada, que se propaga a las copias de trabajo de otros desarrolladores. Se trata cómo un equipo garantiza que está trabajando en el mismo código.

De nuevo, con la integración continua, el acto de confirmación de cambios hace que el servidor CI compilar el proyecto y ejecutar pruebas automatizadas para comprobar la corrección del código fuente. Si hay errores de compilación o errores de pruebas, un servidor CI notifica al desarrollador al cargo (por correo electrónico, mensajería instantánea, Twitter, Growl, etc.) por lo que puede corregir el problema. (Los servidores CI incluso pueden rechazar la confirmación si hay errores, que se denomina un "validada".)

El diagrama siguiente ilustra este proceso:

[![](intro-to-ci-images/intro01-small.png "Este diagrama ilustra este proceso")](intro-to-ci-images/intro01.png#lightbox)

Aplicaciones móviles presentan desafíos únicos para la integración continua. Las aplicaciones pueden requerir sensores, como GPS o de la cámara que solo están disponibles en los dispositivos físicos. Además, simuladores o emuladores son sólo una aproximación de hardware y pueden ocultar u ocultar problemas. Al final, es necesario probar una aplicación móvil en hardware real para estar seguros de que está realmente listo para los clientes.

El [App Center Test](https://docs.microsoft.com/appcenter/test-cloud) corrige este problema concreto al probar aplicaciones directamente en cientos de dispositivos físicos. Los desarrolladores escribir pruebas de aceptación automatizadas, que permiten realizar pruebas de interfaz de usuario eficaz. Una vez que estas pruebas se cargan en App Center, el servidor CI puede ejecutarlos automáticamente como parte de un proceso de CI como se muestra en el diagrama siguiente:

[![](intro-to-ci-images/intro02-small.png "Una vez que estas pruebas se cargan en App Center, el servidor CI puede ejecutar automáticamente como parte de un proceso de CI como se muestra en este diagrama")](intro-to-ci-images/intro02.png#lightbox)

## <a name="version-control"></a>Control de versiones

### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps y Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/) y [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) son herramientas de colaboración de Microsoft para la integración continua de compilación servicios, seguimiento de tareas, ágil planeación y herramientas de informes y control de versiones. Con control de versiones, DevOps de Azure y TFS puede trabajar con su propio sistema (Control de versiones de Team Foundation o TFVC) o con los proyectos hospedados en GitHub.

- Visual Studio Team Services proporciona servicios a través de la nube. Su principal ventaja es que no requiere ningún hardware dedicado o la infraestructura y puede obtenerse acceso desde cualquier lugar a través de los exploradores web y herramientas de desarrollo conocidas como Visual Studio, lo que resulta atractivo para los equipos que están geográficamente distribuido. Es gratis para los equipos de desarrolladores de cinco o menos, después se pueden adquirir las licencias adicionales para dar cabida a un equipo en crecimiento.
- TFS está diseñado para servidores de Windows en el entorno local y tener acceso a través de una red local o una conexión VPN a la red. Su principal ventaja es totalmente controlar la configuración de los servidores de compilación y puede instalar cualquier software adicional o servicios son necesarios. TFS tiene una edición Express básico gratuita para equipos pequeños.

TFS y DevOps de Azure están estrechamente integradas con Visual Studio y permiten a los desarrolladores realizar muchas tareas de CI desde dentro de la comodidad de un único IDE y control de versiones. El complemento de Team Explorer Everywhere para Eclipse (ver abajo) también está disponible. Visual Studio para Mac tiene [una vista previa de TFVC disponible](/visualstudio/mac/tf-version-control/).

[Canalizaciones de Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/) tiene compatibilidad directa para los proyectos de Xamarin en los que crear una definición de compilación para cada plataforma de destino (Android, iOS y Windows). Se necesita la licencia Xamarin correspondiente para cada definición de compilación. También es posible conectarse a una variable local, servidor a Azure DevOps de compilación de TFS compatible con Xamarin para este propósito. Con esta configuración, las compilaciones que están en cola en DevOps de Azure se delegarán en el servidor local. Para obtener más información, consulte [de compilación y lanzamiento agentes](https://docs.microsoft.com/azure/devops/pipelines/agents/agents). Como alternativa, puede usar otra herramienta de compilación como Jenkins o ciudad del equipo.

Un resumen completo de todas las características de Application Lifecycle Management (ALM) de Visual Studio, Azure DevOps y Team Foundation Server, vea [DevOps con aplicaciones de Xamarin](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps).

### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) reúne toda la eficacia de Team Foundation Server y Visual Studio Team Services para los equipos de desarrollo fuera de Visual Studio. Permite a los desarrolladores conectarse a proyectos de equipo local o en la nube desde Eclipse o el cliente de línea de comandos multiplataforma para OS X y Linux. Team Explorer Everywhere proporciona completa acceso al control de versiones (incluido Git), los elementos de trabajo y funcionalidades de compilación para plataformas que no sean Windows.

### <a name="git"></a>Git

[GIT](http://git-scm.com) es una solución de control de versión de código abierto populares que originalmente se desarrolló para administrar el código fuente para el kernel de Linux. Es un sistema muy rápido y flexible que es popular entre los proyectos de software de todos los tamaños. Fácilmente escala de los desarrolladores con acceso Internet deficiente a equipos grandes que abarcan todo el mundo. GIT también facilita la bifurcación muy, que a su vez pueden fomentar secuencias paralelas de desarrollo con el mínimo riesgo.

GIT puede funcionar completamente a través de una combinación de exploradores, o [clientes GUI](http://git-scm.com/downloads/guis) que se ejecutan en Windows, Mac OSX y Linux. Es gratis para los repositorios públicos; repositorios privados requieren una [plan de pago](https://github.com/pricing).

Visual Studio 2015 y Visual Studio para Mac proporcionan compatibilidad nativa para Git; las versiones anteriores, Microsoft proporciona un [extensión descargable para Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c). Como se mencionó anteriormente, Visual Studio Team Services y TFS puede usar Git para el control de versiones en lugar de TFVC.

### <a name="subversion"></a>Subversion

[Subversion](http://subversion.apache.org) (SVN) es un sistema de control de versiones de código abierto muy usado que ha estado en uso desde el año 2000. SVN se ejecuta en todas las versiones actuales de Windows, OS X, FreeBSD, Linux y Unix. Visual Studio para Mac tiene compatibilidad nativa para SVN. Existen extensiones de terceros que proporcionan compatibilidad SVN a Visual Studio.

## <a name="continuous-integration-environments"></a>Entornos de integración continua

Cómo configurar un entorno de integración continua, significa que la combinación de un sistema de control de versiones con un servicio de compilación.  Por último, dos las más comunes son:

- [Las canalizaciones de Azure](https://docs.microsoft.com/azure/devops/pipelines/) es el sistema de compilación de TFS y de DevOps de Azure. Que está totalmente integrado con Visual Studio, lo que hace conveniente para los desarrolladores a desencadenar compilaciones, ejecutar pruebas automáticamente y ver los resultados.
- Jenkins es un servidor de CI de código abierto con un rico ecosistema de complementos para admitir todos los tipos de desarrollo de software. Se ejecuta en Windows y Mac OS X. Jenkins no está integrado con cualquier IDE específico. En su lugar, se configura y administra a través de una interfaz web. Integración continua Jenkins también es fácil de instalar y configurar lo que resulta atractivo para equipos pequeños.

Puede usar TFS/Azure DevOps por sí solo o puede usar Jenkins en combinación con DevOps de Azure/TFS o Git, como se describe en las secciones siguientes.

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services y Team Foundation Server

Como se explicó, Visual Studio Team Services y Team Foundation Server proporciona ambas versiones controlar y crear servicios. Servicios de compilación siempre requieren una licencia de Xamarin Business o Enterprise para cada plataforma de destino.

Con Visual Studio Team Services, cree una definición de compilación independiente para cada plataforma de destino y especifique la licencia correspondiente no existe. Una vez configuradas, Azure DevOps le ejecución compila y prueba en la nube. Consulte [canalizaciones de Azure](https://docs.microsoft.com/azure/devops/pipelines/) para obtener más detalles.

Con Team Foundation Server, configure una máquina de compilación como sigue para plataformas de destino específico:

- **Android y Windows:** Instale Visual Studio y las herramientas de Xamarin (para Android y Windows ambos) y configure con las licencias de Xamarin. También es necesario mover el SDK de Android en una ubicación compartida en el servidor de agente de compilación de TFS de donde pueda encontrarlo. Para obtener más información, consulte [configurar TFVC](https://docs.microsoft.com/azure/devops/repos/tfvc/overview).
- **iOS y Xamarin:** Instale Visual Studio y las herramientas de Xamarin en el servidor de Windows con la licencia apropiada. A continuación, instale Visual Studio para Mac en un equipo Mac OS X accesible desde la red, que servirá como host de compilación y crear el paquete de aplicación final (IPA para iOS, la aplicación para OS X).

El siguiente diagrama muestra esta topología:

[![](intro-to-ci-images/intro03-small.png "Diagrama que muestra esta topología.")](intro-to-ci-images/intro03.png#lightbox)

También es posible crear un vínculo a un servidor TFS local a un Visual Studio Team Services proyecto para que las compilaciones de DevOps de Azure se delegan en el servidor local. Para obtener más información, consulte [de compilación y lanzamiento agentes](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/).

### <a name="visual-studio-team-services-and-jenkins"></a>Jenkins y visual Studio Team Services

Si usa Jenkins para compilar sus aplicaciones, puede almacenar el código en Visual Studio Team Services o Team Foundation Server y seguir usando Jenkins para las compilaciones de CI. Puede desencadenar una compilación de Jenkins cuando inserte código en el repositorio de Git del proyecto de equipo o cuando proteja código en TFVC. Para obtener más información, consulte [Jenkins con Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins).

[![](intro-to-ci-images/intro04-small.png "Si usa Jenkins para compilar sus aplicaciones, puede almacenar el código en Visual Studio Team Services o Team Foundation Server y seguir usando Jenkins para las compilaciones de CI")](intro-to-ci-images/intro04.png#lightbox)

### <a name="git-and-jenkins"></a>GIT y Jenkins

Otro entorno común de CI puede ser completamente OS X que se basa. Este escenario implica el uso de Git para control de código fuente y Jenkins para el servidor de compilación. Ambos se ejecutan en un único equipo Mac OS X con Visual Studio para Mac instalados. Esto es muy similar al entorno de Jenkins que se describe en la sección anterior + Visual Studio Team Services:

[![](intro-to-ci-images/intro05-small.png "Esto es muy parecido al entorno de Jenkins que se describe en la sección anterior + Visual Studio Team Services")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins es [no compatible con Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**
