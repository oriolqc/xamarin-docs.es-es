---
title: Microservicios en contenedores
description: Este capítulo explica cómo usar microservicios y contenedores para generar ágil, escalable y aplicaciones de nube modernas confiable.
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 33be84bc17f72c8b70d117a0742b001f1f763d3d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300775"
---
# <a name="containerized-microservices"></a>Microservicios en contenedores

Desarrollo de aplicaciones cliente / servidor ha resultado en un enfoque en la creación de aplicaciones en capas que usan tecnologías específicas en cada nivel. Estas aplicaciones a menudo se conocen como *monolítica* aplicaciones y se empaquetan en hardware previamente escalada para cargas máximas. Los principales inconvenientes de este enfoque de desarrollo son el estrecho acoplamiento entre los componentes de cada nivel, que no pueden ampliarse fácilmente componentes individuales y el costo de las pruebas. Una sencilla actualización puede tener efectos imprevistos en el resto de la capa, y por lo que un cambio en un componente de aplicación requiere su nivel completo se vuelven a probar y volver a implementar.

Especialmente preocupante en la era de la nube, que no se pueden fácilmente componentes individuales se escala. Una aplicación monolítica contiene funciones específicas del dominio y normalmente se divide en capas funcionales como front-end, lógica empresarial y almacenamiento de datos. Una aplicación monolítica se escala mediante la clonación de toda la aplicación en varios equipos, como se muestra en la figura 8-1.

![](containerized-microservices-images/monolithicapp.png "Enfoque de escalado de la aplicación monolítica")

**Figura 8-1**: Enfoque de escalado de la aplicación monolítica

## <a name="microservices"></a>Microservicios

Los Microservicios ofrecen un enfoque diferente para el desarrollo de aplicaciones y la implementación, un enfoque es adecuado para la agilidad, escalabilidad y requisitos de confiabilidad de aplicaciones modernas en la nube. Una aplicación de microservicios se descompone en los componentes independientes que funcionan conjuntamente para ofrecer una funcionalidad general de la aplicación. El microservicio de término enfatiza que aplicaciones deben estar formadas por servicios lo suficientemente pequeños como para reflejar los conceptos independientes, para que cada microservicio implementa una sola función. Además, cada microservicio tiene contratos bien definidos para que pueden comunicarse y compartir datos con él otros microservicios. Algunos ejemplos típicos de microservicios incluyen carros de la compra, procesamiento, compra subsistemas y procesamiento de pagos.

Microservicios pueden escalar horizontalmente de forma independiente, en comparación con las aplicaciones monolíticas gigantes que se escalan juntos. Esto significa que se puede escalar a un área funcional específica, que requiere más procesamiento de energía o ancho de banda para admitir la demanda, en lugar de innecesariamente escalar horizontalmente otras partes de la aplicación. Figura 8-2 se ilustra este enfoque, donde los microservicios se implementan y escalan de manera independiente, creación de instancias de servicios en las máquinas.

![](containerized-microservices-images/microservicesapp.png "Enfoque de escalado de aplicación de Microservicios")

**Figura 8-2**: Enfoque de escalado de aplicación de Microservicios

Microservicio de escalabilidad horizontal puede ser casi instantánea, lo que permite una aplicación para adaptarse a las cambiantes cargas. Por ejemplo, un único microservicio en la funcionalidad a través de web de una aplicación podría ser el microservicio solo en la aplicación que necesita para escalar horizontalmente para controlar el tráfico entrante adicional.

El modelo clásico de escalabilidad de la aplicación es tener un nivel de carga equilibrada, sin estado con un almacén de datos compartido externo para almacenar datos permanentes. Los microservicios con estado administran sus propios datos persistentes, normalmente almacenarlo de forma local en los servidores en el que se colocan, para evitar la sobrecarga de red de acceso y la complejidad de servicios cruzados operaciones. Esto permite un procesamiento más rápido posible de datos y puede eliminar la necesidad de sistemas de almacenamiento en caché. Además, los microservicios con estado escalables normalmente dividir los datos entre sus instancias para administrar el rendimiento de tamaño y la transferencia de datos más allá del cual puede admitir un único servidor.

Microservicios también admitan actualizaciones independientes. Este acoplamiento flexible entre microservicios proporciona una evolución de la aplicación rápida y confiable. Su naturaleza distribuida, independiente es compatible con las actualizaciones graduales, donde solo un subconjunto de las instancias de un único microservicio se actualizará en un momento dado. Por lo tanto, si se detecta un problema, una actualización con errores puede revertirá, antes de actualizan todas las instancias con la configuración o código defectuoso. De forma similar, microservicios suelen usar las versiones del esquema, para que los clientes ven una versión coherente cuando se han aplicado las actualizaciones, independientemente de qué microservicio instancia se está comunicando con.

Por lo tanto, aplicaciones de microservicios tienen muchas ventajas respecto a las aplicaciones monolíticas:

-   Cada microservicio es relativamente pequeño, fácil de administrar y desarrollar.
-   Cada microservicio se puede desarrollar e implementar independientemente de otros servicios.
-   Cada microservicio puede tener escaladas horizontalmente independientemente. Por ejemplo, un servicio de catálogo o el servicio de la cesta de compra tenga que ser más de un servicio de pedidos de escala horizontal. Por lo tanto, la infraestructura resultante consumirá más eficazmente los recursos durante el escalado horizontal.
-   Cada microservicio aísla los problemas. Por ejemplo, si hay un problema en un servicio solo afecta a ese servicio. Pueden seguir los otros servicios controlar las solicitudes.
-   Cada microservicio puede usar las tecnologías más recientes. Dado que microservicios son autónomo y ejecutar simultánea, las últimas tecnologías y marcos de trabajo pueden utilizarse, en lugar de verse obligado a usar un marco anterior que podría utilizarse en una aplicación monolítica.

Sin embargo, una solución basada en microservicios también tiene desventajas potenciales:

-   Elegir cómo dividir una aplicación en microservicios puede ser un reto, ya que cada microservicio tiene que ser completamente autónomo, to-end, incluida la responsabilidad de sus orígenes de datos.
-   Los desarrolladores deben implementar la comunicación entre servicios, que agrega complejidad y latencia a la aplicación.
-   Las transacciones atómicas entre varios microservicios normalmente no son posibles. Por lo tanto, los requisitos de negocio deben adoptar la coherencia eventual entre microservicios.
-   En producción, hay una complejidad operativa en la implementación y administración de un sistema en peligro de muchos servicios independientes.
-   Comunicación directa de cliente a microservicio puede dificultar a refactorizar los contratos de microservicios. Por ejemplo, con el tiempo cómo el sistema se divide en servicios es posible que deba cambiar. Un único servicio puede dividir en dos o más servicios, y pueden combinar los dos servicios. Cuando los clientes se comunican directamente con microservicios, este trabajo de refactorización puede interrumpir la compatibilidad con las aplicaciones cliente.

## <a name="containerization"></a>Inclusión en contenedores

Inclusión en contenedores es un enfoque de desarrollo de software en el que una aplicación y su conjunto de dependencias, además de su configuración de entorno que se resuman como archivos de manifiesto de implementación, con control de versiones se empaquetan como una imagen de contenedor, probada como una unidad, y implementar en un sistema operativo host.

Un contenedor es un, recursos controlados y portátil entorno operativo aislado, donde una aplicación puede ejecutarse sin tocar los recursos de otros contenedores o el host. Por lo tanto, un contenedor parece y actúa como un equipo físico recién instalado o una máquina virtual.

Hay muchas similitudes entre los contenedores y las máquinas virtuales, como se muestra en la figura 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Enfoque de escalado de aplicación de Microservicios")

**Figura 8-3**: Comparación de las máquinas virtuales y contenedores

Un contenedor ejecuta un sistema operativo, tiene un sistema de archivos y puede tener acceso a través de una red como si fuese una máquina física o virtual. Sin embargo, la tecnología y los conceptos usados por los contenedores son muy diferentes de las máquinas virtuales. Las máquinas virtuales se incluyen las aplicaciones, las dependencias necesarias y un sistema operativo invitado completo. Los contenedores incluyen la aplicación y sus dependencias, pero comparten el sistema operativo con otros contenedores, que se ejecutan como procesos aislados en el sistema operativo del host (aparte de los contenedores de Hyper-V que se ejecutan dentro de una máquina virtual especial por contenedor). Por lo tanto, los contenedores comparten recursos y suelen requieran menos recursos que las máquinas virtuales.

La ventaja de un enfoque de desarrollo e implementación orientada a servicios de contenedor es que elimina la mayoría de los problemas que surgen de configuraciones de entorno incoherentes y los problemas que se incluyen con ellas. Además, los contenedores permiten funcionalidad de escalado vertical de aplicaciones rápida mediante la creación de instancias de nuevos contenedores según sea necesario.

Los conceptos clave al crear y trabajar con contenedores son:

-   Host de contenedor: La máquina física o virtual configurado para hospedar contenedores. El host de contenedor ejecutará uno o varios contenedores.
-   Imagen de contenedor: Una imagen consta de una unión de los sistemas de archivos en capas apilada unas sobre otras y es la base de un contenedor. Una imagen no tiene estado y nunca cambia tal como se implementa en diferentes entornos.
-   Contenedor: Un contenedor es una instancia en tiempo de ejecución de una imagen.
-   Imagen de sistema operativo de contenedor: Los contenedores se implementan a partir de imágenes. La imagen de sistema operativo del contenedor es la primera capa de potencialmente muchas capas de imagen que componen un contenedor. Un sistema operativo de contenedor es inmutable y no se puede modificar.
-   Repositorio de contenedor: Cada vez que se crea una imagen de contenedor, la imagen y sus dependencias se almacenan en un repositorio local. Estas imágenes se pueden reutilizar muchas veces en el host de contenedor. Las imágenes de contenedor también pueden almacenarse en un registro público o privado, como [Docker Hub](https://hub.docker.com/), de modo que pueden usarse en hosts de contenedor diferente.

Las empresas están adoptando cada vez más contenedores cuando las aplicaciones basadas en la implementación de microservicios y Docker se ha convertido en la implementación de contenedor estándar que se ha optado por la mayoría de plataformas de software y los proveedores de nube.

La aplicación de referencia eShopOnContainers usa Docker para hospedar cuatro microservicios en contenedores de back-end, como se muestra en la figura 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "microservicios de back-end de aplicación de referencia eShopOnContainers")

**Figura 8-4**: microservicios de back-end de aplicación de referencia eShopOnContainers

La arquitectura de los servicios de back-end de la aplicación de referencia se descompone en varios subsistemas autónomos en forma de microservicios de colaboración y contenedores. Cada microservicio proporciona una única área de funcionalidad: un servicio de identidad, un servicio de catálogo, un servicio de pedidos y un servicio de la cesta de compras.

Cada microservicio tiene su propia base de datos, lo que permite separarlo totalmente de los otros microservicios. En caso necesario, la coherencia entre las bases de datos de los diferentes microservicios se logra mediante eventos de nivel de aplicación. Para obtener más información, consulte [comunicación entre Microservicios](#communication_between_microservices).

Para obtener más información acerca de la aplicación de referencia, consulte [Microservicios de. NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservicios de .NET: Arquitectura para aplicaciones .NET en contenedor).

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>Comunicación entre cliente y Microservicios

La aplicación móvil de eShopOnContainers se comunica con el uso de microservicios de back-end *dirigir el cliente a microservicio* comunicación, que se muestra en la figura 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Enfoque de escalado de aplicación de Microservicios")

**Figura 8-5**: Comunicación directa de cliente a microservicio

Con la comunicación directa de cliente a microservicio, la aplicación móvil realiza solicitudes a cada microservicio directamente a través de su punto de conexión público, con un puerto TCP diferente por microservicio. En producción, el punto de conexión normalmente asignaría al equilibrador de carga del microservicio, que distribuye las solicitudes entre las instancias disponibles.

> [!TIP]
> Considere el uso de comunicación de la puerta de enlace de API. Comunicación directa de cliente a microservicio puede tener inconvenientes al crear un microservicio grande y complejo basada en la aplicación, pero es más que adecuado para una pequeña aplicación. Al diseñar un microservicio de gran tamaño en función de la aplicación con decenas de microservicios, considere el uso de comunicación de la puerta de enlace de API. Para obtener más información, consulte [Microservicios de. NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservicios de .NET: Arquitectura para aplicaciones .NET en contenedor).

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Comunicación entre Microservicios

Una aplicación basadas en microservicios es un sistema distribuido, ejecuta potencialmente en varias máquinas. Lo habitual es que cada instancia de servicio sea un proceso. Por lo tanto, los servicios deben interactuar mediante un protocolo de comunicación entre procesos, como HTTP, TCP, Advanced Message Queuing Protocol (AMQP) o protocolos binarios, según la naturaleza de cada servicio.

Los dos enfoques comunes para la comunicación de microservicio a microservicio son basado en HTTP comunicación REST al consultar datos y mensajería asincrónica ligera al comunicar actualizaciones entre varios microservicios.

Mensajería basada en eventos comunicación asincrónica controlada es fundamental para propagar cambios entre varios microservicios. Con este enfoque, un microservicio publica un evento cuando algo que notables sucede, por ejemplo, cuando actualiza una entidad de negocio. Otros microservicios se suscriben a estos eventos. A continuación, cuando un microservicio recibe un evento, actualiza sus propias entidades empresariales, lo que a su vez podrían provocar más eventos que se está publicados. Esto publicación-suscripción funcionalidad normalmente se logra con un bus de eventos.

Un bus de eventos permite la comunicación entre microservicios, sin necesidad de los componentes para tenga explícitamente entre sí, tal como se muestra en la figura 8-6 de publicación y suscripción.

![](containerized-microservices-images/eventbus.png "Publicación y suscripción con un bus de eventos")

**Figura 8-6:** Publicación y suscripción con un bus de eventos

Desde una perspectiva de la aplicación, el bus de eventos es simplemente un publish-suscribirse expuesta a través de una interfaz de canal. Sin embargo, la manera en que se implementa el bus de eventos puede variar. Por ejemplo, podría usar una implementación de bus de eventos RabbitMQ, Azure Service Bus u otros buses de servicio como NServiceBus, MassTransit. Figura 8-7 se muestra cómo se utiliza un bus de eventos en la aplicación de referencia eShopOnContainers.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Comunicación asincrónica controlada por eventos en la aplicación de referencia")

**Figura 8-7:** Comunicación asincrónica controlada por eventos en la aplicación de referencia

El bus de eventos de eShopOnContainers implementado utilizando RabbitMQ, proporciona uno a varios asincrónica funcionalidad de publicación y suscripción. Esto significa que después de publicar un evento, puede haber varios suscriptores a la escucha para el mismo evento. Figura 8-9 se ilustra esta relación.

![](containerized-microservices-images/eventdrivencommunication.png "Comunicación de uno a varios")

**Figura 8-9**: Comunicación de uno a varios

Este enfoque de comunicación de uno a varios utiliza eventos para implementar transacciones de negocio que abarquen varios servicios, lo que garantiza la coherencia final entre los servicios. Una transacción eventual coherente consta de una serie de pasos distribuidas. Por lo tanto, cuando el microservicio de perfil de usuario recibe el comando UpdateUser, actualiza los detalles del usuario en su base de datos y publica el evento UserUpdated en el bus de eventos. El microservicio de cesta de la compra y el microservicio de pedidos se han suscrito para recibir este evento y en respuesta actualizar su información de comprador en sus respectivas bases de datos.

> [!NOTE]
> El bus de eventos de eShopOnContainers implementado utilizando RabbitMQ, está pensado para usarse solo como una prueba de concepto. Para los sistemas de producción, se deben considerar las implementaciones del bus de eventos alternativo.

Para obtener información acerca de la implementación del bus de eventos, vea [Microservicios de. NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservicios de .NET: Arquitectura para aplicaciones .NET en contenedor).

## <a name="summary"></a>Resumen

Los Microservicios ofrecen un enfoque al desarrollo de aplicaciones e implementación que se adapte a los requisitos de agilidad, escalabilidad y confiabilidad de aplicaciones modernas en la nube. Una de las principales ventajas de los microservicios es que pueden ser escaladas horizontalmente de forma independiente, lo que significa que se puede escalar un área funcional específica que requiere más procesamiento de energía o ancho de banda para admitir la demanda, sin escalado innecesariamente áreas de la aplicación que no está experimentando una mayor demanda.

Un contenedor es un, recursos controlados y portátil entorno operativo aislado, donde una aplicación puede ejecutarse sin tocar los recursos de otros contenedores o el host. Las empresas están adoptando cada vez más contenedores cuando las aplicaciones basadas en la implementación de microservicios y Docker se ha convertido en la implementación de contenedor estándar que se ha optado por la mayoría de plataformas de software y los proveedores de nube.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
