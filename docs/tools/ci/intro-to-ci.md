---
title: Introducción a la integración continua con Xamarin
description: Este documento describe la integración continua con Xamarin. Se trata de control de versiones y varios entornos de integración continua.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: topgenorth
ms.author: toopge
ms.date: 07/19/2017
ms.openlocfilehash: 67fc32fc9f79d54274642fbab2d0c2f8afd14d8c
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066512"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introducción a la integración continua con Xamarin

_La integración continua es una práctica de ingeniería de software en el que se compila una compilación automatizada y opcionalmente, prueba una aplicación cuando el código se agrega o cambia los desarrolladores en el repositorio de control de versiones del proyecto. Este artículo describe los conceptos generales de la integración continua y algunas de las opciones disponibles para la integración continua con los proyectos de Xamarin._

Es frecuente en los proyectos de software para que los desarrolladores trabajan en paralelo. En algún momento, es necesario integrar todos estos paralelo flujos de trabajo en un código base que conforma el producto final. En los primeros días de desarrollo de software, esta integración se realiza al final de un proyecto, lo cual era un proceso difícil y riesgo.

Integración continua (CI) evitar esas complejidades, combina los cambios de todos los desarrolladores en el código base común de forma continua, normalmente cada vez que los desarrolladores Compruebe cambios en el proyecto comparten repositorio de código. En cada comprobación desencadena una compilación automatizada y ejecuta pruebas automatizadas para comprobar que el código recién introducido no interrumpe cualquier código existente.  De esta manera, CI presenta errores y problemas inmediatamente y se asegura de que todos los miembros del equipo mantengan al día con sus respectivos trabajos. Esto da como resultado un código base estable y coherente.

Sistemas de integración continua tienen dos partes principales:

-  **Control de versiones** : Control de versión (VC), con lo que también se denomina control de código fuente o administración de código fuente, consolida todo el código de un proyecto en un solo repositorio compartido y mantiene un historial completo de cada cambio a todos los archivos. Este repositorio conoce a menudo como el *principales* o *maestro* crear una bifurcación, contiene el código fuente que se utilizará en última instancia para generar la producción o la versión de la aplicación. Hay muchos código abierto y productos comerciales para esta tarea, que normalmente permite que los equipos o usuarios para bifurcar una copia del código en las bifurcaciones secundarias donde puede realizar grandes cambios o realizar experimentos sin riesgos para la bifurcación principal. Una vez que se validan los cambios en una bifurcación secundaria, a continuación, pueden todos juntos vuelve a combinar en la bifurcación principal.
-  **Servidor de integración continua** : el servidor de integración continua es responsable de recopilar todos los artefactos del proyecto (código fuente, imágenes, vídeos, las bases de datos, las pruebas automatizadas, etc.), compilar la aplicación y ejecutar las pruebas automatizadas. Una vez más, hay muchos código abierto y herramientas de servidor CI comercial.

Los desarrolladores suelen tengan una copia de trabajo de una o varias ramas de sus estaciones de trabajo, que inicialmente se realiza el trabajo. Una vez completado un conjunto adecuado de trabajo, los cambios son "los elementos verificados a" o "confirmados" en la bifurcación adecuada, que se propaga a las copias de trabajo de otros desarrolladores. Se trata cómo un equipo se asegura de que está trabajando en el mismo código.

De nuevo, con la integración continua, el acto de confirmar los cambios hace que el servidor de integración continua compilar el proyecto y ejecutar pruebas automatizadas para comprobar la corrección del código fuente. Si hay errores de compilación o errores de prueba, un servidor CI notifica al desarrollador al cargo (a través de correo electrónico, mensajería instantánea, Twitter, Growl, etc.) para que pueda corregir el problema. (Servidores CI incluso pueden rechazar la confirmación si hay errores, que se denomina una "protección controlada".)

El siguiente diagrama ilustra este proceso:

[![](intro-to-ci-images/intro01-small.png "Este diagrama ilustra este proceso")](intro-to-ci-images/intro01.png#lightbox)

Aplicaciones móviles presentan desafíos únicos para la integración continua. Las aplicaciones pueden requerir sensores como GPS o de la cámara que solo están disponibles en los dispositivos físicos. Además, simuladores o emuladores son solo una aproximación del hardware y pueden ocultar o interferir con problemas. Al final, es necesario para probar una aplicación móvil en hardware real para estar seguro de que es realmente preparado para el cliente.

El [aplicación Center Test](https://docs.microsoft.com/appcenter/test-cloud) para abordar este problema concreto mediante la comprobación de aplicaciones directamente en cientos de dispositivos físicos. Los desarrolladores escribir pruebas de aceptación automatizadas, lo que permite para las pruebas de interfaz de usuario eficaz. Una vez que estas pruebas se cargan en el centro de la aplicación, el servidor CI puede ejecutarlos de forma automática como parte de un proceso de integración continua tal como se muestra en el diagrama siguiente:

[![](intro-to-ci-images/intro02-small.png "Una vez que estas pruebas se cargan en el centro de la aplicación, el servidor CI puede ejecutarlos de forma automática como parte de un proceso de integración continua como se muestra en este diagrama")](intro-to-ci-images/intro02.png#lightbox)

# <a name="components-of-continuous-integration"></a>Componentes de integración continua

Hay un amplio ecosistema de comerciales y de código abierto herramientas diseñadas para admitir elementos de configuración. Esta sección explica algunas de las más comunes.

## <a name="version-control"></a>Control de versiones

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services y Team Foundation Server

[Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/) (VSTS) y [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) son herramientas de colaboración de Microsoft para la integración continua de compilación servicios, tareas de seguimiento, planeación de agile e informes herramientas y versión control. Con control de versiones, VSTS y TFS puede trabajar con su propio sistema (Control de versiones de Team Foundation o TFVC) o con los proyectos que se hospeda en GitHub.

 - Visual Studio Team Services proporciona servicios a través de la nube. Su principal ventaja es que no requiere ningún hardware dedicado o la infraestructura y puede tener acceso desde cualquier lugar a través de los exploradores web y herramientas de desarrollo conocidas como Visual Studio, lo que atractivo para que los equipos que se encuentran geográficamente distribuir. Es gratuito para los equipos de desarrolladores de cinco o menos, después pueden adquirir las licencias adicionales para dar cabida a un equipo en crecimiento.
 - TFS está diseñado para servidores de Windows local y acceder a través de una red local o una conexión VPN a la red. Tiene la ventaja principal es totalmente controlan la configuración de los servidores de compilación y puede instalar cualquier software adicional o servicios son necesarios. TFS tiene una edición Express principiantes gratuita para los equipos pequeños.

TFS y VSTS se integran estrechamente con Visual Studio y permiten a los desarrolladores realizar muchas tareas de CI desde dentro de la comodidad de un único IDE y control de versiones. El complemento de Team Explorer Everywhere para Eclipse (ver abajo) también está disponible. Visual Studio para Mac no ofrece ninguna compatibilidad de TFS o VSTS.

Sistema de compilación del servicio de Visual Studio Team tiene compatibilidad directa para los proyectos de Xamarin, en el que crear una definición de compilación para cada plataforma que se va a destino (Android, iOS y Windows). Se necesita la licencia de Xamarin adecuada para cada definición de compilación. También es posible conectarse a una variable local, servidor para Visual Studio Team Services con este fin de compilación de TFS compatible con Xamarin. Con esta configuración, se delegarán compilaciones que están en cola en VSTS al servidor local. Para obtener más información, consulte [implementar y configurar un servidor de compilación](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts). Como alternativa, puede usar otra herramienta de compilación como Jenkins o una ciudad de equipo.

Obtener un resumen completo de todas las características de Application Lifecycle Management (ALM) de Visual Studio, Visual Studio Team Services y Team Foundation Server, vea [Application Lifecycle Management con aplicaciones Xamarin](https://msdn.microsoft.com/library/mt162217(v=vs.140).aspx) en MSDN.


### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](http://msdn.microsoft.com/library/gg413285.aspx) reúne toda la eficacia de Team Foundation Server y Visual Studio Team Services para los equipos de desarrollo fuera de Visual Studio. Permite que los desarrolladores para conectarse a proyectos de equipo local o en la nube desde Eclipse o el cliente de línea de comandos multiplataforma para OS X y Linux. Team Explorer Everywhere completa proporciona acceso al control de versiones (incluida la Git), los elementos de trabajo y crear capacidades para plataformas distintas de Windows.


### <a name="git"></a>Git

[GIT](http://git-scm.com) es una solución de control de versión de código abierto que se desarrolló originalmente para administrar el código fuente para el kernel de Linux. Es un sistema muy rápido y flexible que es popular entre los proyectos de software de todos los tamaños. Se escala fácilmente de desarrolladores únicos con el acceso inadecuado de Internet a los equipos grandes que abarcan todo el mundo. GIT también facilita la bifurcación muy, que a su vez puede recomendar a los flujos paralelos de desarrollo con el mínimo riesgo.

GIT puede funcionar completamente a través de los exploradores web, o [los clientes de la interfaz gráfica de usuario](http://git-scm.com/downloads/guis) que se ejecutan en Linux, Mac OSX y Windows. Es gratuito para repositorios públicos; los repositorios privados requieren un [plan de pago](https://github.com/pricing).

Visual Studio 2015 y Visual Studio para Mac proporcionan compatibilidad nativa con Git; para las versiones anteriores, Microsoft proporciona un [extensión descargable para Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c). Como se mencionó anteriormente, Visual Studio Team Services y TFS puede usa Git para el control de versiones en lugar de TFVC.


### <a name="subversion"></a>Subversion

[Subversion](http://subversion.apache.org) (SVN) es un sistema de control de versiones de código abierto populares que está en uso desde el año 2000. SVN se ejecuta en todas las versiones actuales de OS X, Windows, FreeBSD, Linux y Unix. Visual Studio para Mac tiene compatibilidad nativa con SVN. Existen extensiones de terceros que proporcionan compatibilidad SVN para Visual Studio.


## <a name="continuous-integration-environments"></a>Entornos de integración continua

Configuración de un entorno de integración continua significa que la combinación de un sistema de control de versiones con un servicio de compilación.  Por último, dos las más comunes son:

- [Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview) es el sistema de compilación de Visual Studio Team Services y TFS. Se integra estrechamente con Visual Studio, que hace conveniente para los desarrolladores desencadenar genera, automáticamente ejecutar pruebas y ver los resultados.
- Jenkins es un servidor de integración continua de código abierto que con un rico ecosistema de complementos para admitir todos los tipos de desarrollo de software. Se ejecuta en Windows y Mac OS X. Jenkins no está integrado con cualquier IDE específico. En su lugar, se configura y administra a través de una interfaz web. Integración continua Jenkins también es fácil de instalar y configurar lo que resulta atractivo para los equipos pequeños.

Puede usar TFS/VSTS por sí solo o en combinación con TFS/VSTS o Git, tal como se describe en las secciones siguientes sirven Jenkins.

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services y Team Foundation Server

Tal y como se ha explicado, Visual Studio Team Services y Team Foundation Server proporciona tanto versión controlar la compilación y servicios. Servicios de compilación siempre requieren una licencia de Xamarin Business o Enterprise para cada plataforma de destino.

Con Visual Studio Team Services, crear una definición de compilación independiente para cada plataforma de destino y escriba la licencia correspondiente no existe. Una vez configurado, VSTS ejecutará compilaciones y pruebas en la nube. Vea [Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview) para obtener más detalles.

Con Team Foundation Server, configure una máquina de compilación como se indica a continuación para plataformas de destino específicas:

- **Android y Windows:** instalar Visual Studio y la Xamarin tools (para Android y Windows ambos) y configurar con las licencias de Xamarin. También es necesario mover el SDK de Android en una ubicación compartida en el servidor de agente de compilación de TFS de donde pueda encontrarlo. Para obtener más información, consulte [configurar TFVC](https://docs.microsoft.com/vsts/tfvc/overview).
- **iOS y Xamarin:** instalar Visual Studio y las herramientas de Xamarin en el servidor de Windows con la licencia apropiada. A continuación, instale Visual Studio para Mac en un equipo Mac OS X accesible desde la red, que servirá como host de compilación y crear el paquete de aplicación final (IPA para iOS, aplicaciones para OS X).

El siguiente diagrama ilustra esta topología:

[![](intro-to-ci-images/intro03-small.png "Este diagrama muestra la topología de este")](intro-to-ci-images/intro03.png#lightbox)

También es posible vincular un servidor TFS local a un proyecto de Visual Studio Team Services para que las compilaciones VSTS se delegan al servidor local. Para obtener más información, consulte [implementar y configurar un servidor de compilación](http://msdn.microsoft.com/library/ms181712.aspx) en MSDN.

### <a name="visual-studio-team-services-and-jenkins"></a>Jenkins y visual Studio Team Services

Si usas Jenkins para compilar las aplicaciones, puede almacenar el código en Visual Studio Team Services o Team Foundation Server y seguir usando Jenkins para las compilaciones de integración continua. Puede desencadenar una compilación Jenkins al insertar código en el repositorio de Git del proyecto de equipo o cuando registre código en TFVC. Para obtener más información, consulte [Jenkins con Visual Studio Team Services](https://docs.microsoft.com/en-us/vsts/service-hooks/services/jenkins?view=vsts).

[![](intro-to-ci-images/intro04-small.png "Si utiliza Jenkins para compilar las aplicaciones, puede almacenar el código en Visual Studio Team Services o Team Foundation Server y continuar usando las compilaciones de integración continua Jenkins")](intro-to-ci-images/intro04.png#lightbox)

### <a name="git-and-jenkins"></a>GIT y Jenkins

Otro entorno de elemento de configuración común puede ser completamente OS X que se basa. Este escenario implica el uso de Git para el control de código fuente y Jenkins para el servidor de compilación. Ambos se ejecutan en un único equipo de Mac OS X con Visual Studio para Mac instalado. Esto es muy similar a la Visual Studio Team Services + entorno Jenkins mencionadas en la sección anterior:

[![](intro-to-ci-images/intro05-small.png "Esto es muy similar a la Visual Studio Team Services + entorno Jenkins mencionadas en la sección anterior")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Nota: Jenkins es [no compatible con Xamarin](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**


# <a name="summary"></a>Resumen

Este documento introdujo el concepto de integración continua y las ventajas que aporta a los equipos de desarrollo de software. La importancia del control de versiones tratada junto con los roles y responsabilidades del servidor de compilación. El documento entraba explicar algunas de las herramientas que se pueden usar para el control de código fuente y servidores de compilación. También presentamos aplicación Center Test, que ayuda a los desarrolladores publicar excelentes aplicaciones mediante la ejecución de pruebas automatizadas que resultarán la calidad y la funcionalidad de sus aplicaciones. Obtener información más detallada sobre cómo enviar aplicaciones y pruebas al centro de aplicación pueden encontrarse [aquí](https://docs.microsoft.com/appcenter/test-cloud). Por último, para ayudar a entender cómo todas estas herramientas y componentes encajan entre sí, se describen varios entornos diferentes de elementos de configuración que las organizaciones pueden establecer para la integración continua. Para obtener más información con proyectos de Xamarin en Visual Studio Team Services y Team Foundation Server, vea [configurar TFVC](https://docs.microsoft.com/vsts/tfvc/overview) y esto [introducción de integración continua](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1). Del mismo modo, si usas Jenkins, consulte [utilizando Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md) para obtener información detallada sobre cómo configurar la integración continua.
