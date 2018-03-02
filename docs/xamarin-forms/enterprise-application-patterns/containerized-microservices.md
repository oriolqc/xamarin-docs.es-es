---
title: Microservicios en contenedores
ms.topic: article
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 3ecbd5a301a64417ab5fb27bd8632b6d9790a7ac
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="containerized-microservices"></a>Microservicios en contenedores

Desarrollar aplicaciones cliente / servidor ha resultado en un enfoque en la creación de aplicaciones en capas que usan tecnologías específicas en cada nivel. Estas aplicaciones se conocen a menudo como *monolítico* aplicaciones y se empaquetan en hardware previamente escalada para cargas máximas. El principal inconveniente de este enfoque de desarrollo es el acoplamiento apretado entre los componentes dentro de cada nivel, que los componentes individuales no se puede escalar fácilmente y el costo de las pruebas. Una actualización simple puede tener efectos imprevistos en el resto de la capa, y por lo que un cambio en un componente de aplicación requiere su nivel todo a probar y volver a implementar.

Relativo a especialmente en el tiempo de la nube, que los componentes individuales no pueden ser fácilmente se escala. Una aplicación monolítica contiene funcionalidad específica del dominio y normalmente se divide por capas funcionales como front-end, la lógica de negocios y el almacenamiento de datos. Una aplicación monolítica se escala mediante la clonación de toda la aplicación en varios equipos, como se muestra en la figura 8-1.

![](containerized-microservices-images/monolithicapp.png "Enfoque de escalado de la aplicación monolítico")

**Figura 8-1**: aplicación monolítico enfoque de ajuste de escala

## <a name="microservices"></a>Microservicios

Microservicios ofrecen un enfoque diferente para la implementación y desarrollo de aplicaciones, un enfoque es adecuado para la agilidad, escala y requisitos de confiabilidad de las aplicaciones modernas en la nube. Una aplicación de microservicios se descompone en componentes independientes que funcionan conjuntamente para proporcionar funcionalidad general de la aplicación. El término microservicio hace hincapié en que las aplicaciones deben estar formadas servicios lo suficientemente pequeños como para reflejar los riesgos independientes, por lo que cada microservicio implementa una única función. Además, cada microservicio tiene contratos bien definidos para que otros microservicios puedan comunicarse y compartir datos con él. Algunos ejemplos típicos de microservicios incluyen la cesta de Amazon, procesamiento, compra subsistemas y procesamiento de pago.

Microservicios pueden escalado horizontal de forma independiente, en comparación con gigantes aplicaciones monolíticas que escalan juntos. Esto significa que se puede escalar un área funcional específica, que requiere más procesamiento alimentación eléctrica o red de ancho de banda para admitir la demanda, en lugar de innecesariamente escala horizontal de las otras áreas de la aplicación. Figura 8-2 muestra este enfoque, donde microservicios se implementan y escalar de forma independiente, la creación de instancias de servicios en los equipos.

![](containerized-microservices-images/microservicesapp.png "Enfoque de escalado de aplicación Microservicios")

**Figura 8-2**: enfoque de escala de las aplicaciones Microservicios

Escalabilidad de Microservicio puede ser casi instantánea, lo que permite una aplicación para adaptarse a las cambiantes cargas. Por ejemplo, un microservicio único en la funcionalidad de web a través de una aplicación podría ser el microservicio solo en la aplicación que necesita para escalar horizontalmente para controlar el tráfico entrante adicional.

El modelo clásico de escalabilidad de la aplicación es para un nivel de equilibrio de carga, sin estado con un almacén de datos externo compartido para almacenar datos permanentes. Con estado microservicios administran sus propios datos persistentes, normalmente almacenarla de forma local en los servidores en el que se colocan, para evitar la sobrecarga de red de acceso y la complejidad de servicios cruzados operaciones. Esto permite un procesamiento más rápido posible de datos y puede eliminar la necesidad de almacenamiento en caché de sistemas. Además, escalables microservicios con estado suelen dividir los datos entre sus instancias, administrar el rendimiento de tamaño y la transferencia de datos más allá del cual puede admitir un único servidor.

Microservicios también admiten actualizaciones independientes. Este acoplamiento flexible entre microservicios proporciona una evolución aplicación rápida y confiable. Su naturaleza distribuida, independiente admite actualizaciones sucesivas, donde solo un subconjunto de instancias de una sola microservicio se actualizará en un momento dado. Por lo tanto, si se detecta un problema, una actualización con errores puede puede revertirse, antes de actualizan todas las instancias con el código defectuoso o la configuración. De forma similar, microservicios normalmente usan versiones de esquema, por lo que los clientes verán una versión coherente cuando se están aplicando las actualizaciones, independientemente de qué microservicio instancia se está comunicando con.

Por lo tanto, las aplicaciones de microservicio tienen muchas ventajas respecto a las aplicaciones monolíticas:

-   Cada microservicio es relativamente pequeños, fáciles de administrar y desarrollar.
-   Cada microservicio se pueden desarrollar e implementar independientemente de otros servicios.
-   Cada microservicio puede ser escalado horizontal por separado. Por ejemplo, un servicio de catálogo o el servicio de cesta de compra posible que deba ser escalado horizontal más de un servicio de ordenación. Por lo tanto, la infraestructura resultante más eficazmente consumirá recursos mientras se escala horizontalmente.
-   Cada microservicio aísla los problemas. Por ejemplo, si hay un problema en un servicio solo afecta a ese servicio. Los demás servicios pueden continuar controlar las solicitudes.
-   Cada microservicio puede utilizar las tecnologías más recientes. Dado que microservicios son autónomo y ejecución simultánea, las últimas tecnologías y marcos de trabajo pueden utilizarse, en lugar de verse obligado a utilizar un marco anterior que puede usarse en una aplicación monolítica.

Sin embargo, una solución de microservicio según también tiene desventajas potenciales:

-   Selección de particiones de una aplicación en microservicios puede ser un reto, ya que cada microservicio debe ser completamente autónomo, to-end, incluidas la responsabilidad de sus orígenes de datos.
-   Los programadores deben implementar la comunicación entre servicio, que agrega complejidad y la latencia a la aplicación.
-   Las transacciones atómicas entre varios microservicios normalmente no son posibles. Por lo tanto, deben adoptar la coherencia definitiva entre microservicios requisitos empresariales.
-   En producción, hay una complejidad operativa en la implementación y administración de un sistema en peligro de muchos servicios independientes.
-   Comunicación de cliente a microservicio directa puede dificultar la refactorizar los contratos de microservicios. Por ejemplo, con el tiempo cómo el sistema se crean particiones en servicios deba cambiar. Un único servicio puede dividir en dos o más servicios, y pueden combinar los dos servicios. Cuando los clientes se comunican directamente con microservicios, este trabajo refactorización puede interrumpir la compatibilidad con aplicaciones de cliente.

## <a name="containerization"></a>Inclusión en contenedores

Inclusión en contenedores es un enfoque de desarrollo de software en el que una aplicación y su conjunto de dependencias, además de su configuración de entorno que se extraen como archivos de manifiesto de implementación, con control de versiones se empaquetan como una imagen de contenedor, probada como una unidad, y implementar en un sistema operativo host.

Un contenedor es un recurso controlado y portátil entorno operativo aislado, donde una aplicación puede ejecutarse sin tocar los recursos de otros contenedores, o el host. Por lo tanto, un contenedor busca y actúa como un equipo físico recién instalados o una máquina virtual.

Hay muchas similitudes entre los contenedores y las máquinas virtuales, como se muestra en la figura 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Enfoque de escalado de aplicación Microservicios")

**Figura 8-3**: comparación de máquinas virtuales y contenedores

Un contenedor ejecuta un sistema operativo, tiene un sistema de archivos y puede tener acceso a través de una red como si fuera una máquina física o virtual. Sin embargo, la tecnología y los conceptos que se usan los contenedores son muy diferentes de las máquinas virtuales. Máquinas virtuales se incluyen las aplicaciones, las dependencias necesarias y un sistema operativo invitado completa. Contenedores incluyen la aplicación y sus dependencias, pero compartan el sistema operativo con otros contenedores, que se ejecutan como procesos aislados en el sistema operativo del host (aparte de los contenedores de Hyper-V que se ejecutan dentro de una máquina virtual especial por contenedor). Por lo tanto, contenedores de compartan recursos y suelen requieran menos recursos de las máquinas virtuales.

La ventaja de un enfoque de desarrollo e implementación orientada a servicios de contenedor es que elimina la mayoría de los problemas que surgen de configuraciones de entorno incoherentes y los problemas que se incluyen con ellas. Además, contenedores permiten la funcionalidad de ampliación rápida de aplicaciones mediante creación de instancias de nuevos contenedores según sea necesario.

Los conceptos clave al crear y trabajar con contenedores son:

-   Host de contenedor: La máquina física o virtual configurado para contenedores del host. El host de contenedor ejecutará uno o varios contenedores.
-   Imagen de contenedor: Una imagen consta de una unión de filesystems superpuesto apilados unos encima de otros y es la base de un contenedor. Una imagen no tiene estado y nunca cambia tal y como se implementa en diferentes entornos.
-   Contenedor: Un contenedor es una instancia en tiempo de ejecución de una imagen.
-   Imagen del sistema operativo de contenedor: Contenedores se implementan a partir de imágenes. La imagen de sistema operativo del contenedor es la primera capa de potencialmente muchas capas de imagen que componen un contenedor. Un sistema operativo de contenedor es inmutable y no se puede modificar.
-   Repositorio de contenedor: Cada vez que se crea una imagen de contenedor, la imagen y sus dependencias se almacenan en un repositorio local. Estas imágenes se pueden reutilizar muchas veces en el host de contenedor. Las imágenes de contenedor también pueden almacenarse en un registro público o privado, como [Docker Hub](https://hub.docker.com/), de modo que pueden usarse en hosts de contenedor diferentes.

Las empresas están adoptando cada vez más contenedores cuando aplicaciones basadas en la implementación de microservicio y Docker se ha convertido en la implementación de contenedor estándar que se ha optado por la mayoría de las plataformas de software y los proveedores de nube.

La aplicación de referencia eShopOnContainers usa Docker para hospedar cuatro microservicios en contenedores de back-end, como se muestra en la figura 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "hacer referencia a eShopOnContainers microservicios de back-end de aplicación")

**Figura 8-4**: eShopOnContainers hacen referencia a microservicios de back-end de aplicación

La arquitectura de los servicios back-end en la aplicación de referencia se descompone en varios subsistemas autónomos en forma de colaborar microservicios y contenedores. Cada microservicio proporciona una única área de funcionalidad: un servicio de identidad, un servicio de catálogo, un servicio de ordenación y un servicio de la cesta de compra.

Cada microservicio tiene su propia base de datos, lo que le permite totalmente se separa de los otros microservicios. En caso necesario, la coherencia entre bases de datos de diferentes microservicios se logra mediante eventos de nivel de aplicación. Para obtener más información, consulte [comunicación entre Microservicios](#communication_between_microservices).

Para obtener más información acerca de la aplicación de referencia, vea [Microservicios. NET: arquitectura de aplicaciones de .NET en contenedores](https://aka.ms/microservicesebook).

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>Comunicación entre cliente y Microservicios

La aplicación móvil eShopOnContainers se comunica con el uso de microservicios en contenedores de back-end *dirigir el cliente a microservicio* comunicación, que se muestra en la figura 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Enfoque de escalado de aplicación Microservicios")

**Figura 8-5**: cliente a microservicio comunicación directa

Con la comunicación de cliente a microservicio directa, la aplicación móvil realiza solicitudes a cada microservicio directamente a través de su extremo público con un puerto TCP diferente por microservicio. En producción, el punto de conexión normalmente asignaría al equilibrador de carga de microservicio, que distribuye las solicitudes entre las instancias disponibles.

> [!TIP]
> Considere la posibilidad de utilizar la comunicación de puerta de enlace de API. Comunicación de cliente a microservicio directa puede tener desventajas al crear un microservicio grande y complejo en función de aplicación, pero resulta más adecuado para una pequeña aplicación. Al diseñar un microservicio grande según la aplicación con decenas de microservicios, considere la posibilidad de utilizar la comunicación de puerta de enlace de API. Para obtener más información, consulte [Microservicios. NET: arquitectura de aplicaciones de .NET en contenedores](https://aka.ms/microservicesebook).

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Comunicación entre Microservicios

Una aplicación en función de microservicios es un sistema distribuido, ejecuta potencialmente en varias máquinas. Lo habitual es que cada instancia de servicio sea un proceso. Por lo tanto, los servicios deben interactúan mediante un protocolo de comunicación entre procesos, como HTTP, TCP, Advanced Message Queuing Protocol (AMQP) o protocolos binarios, según la naturaleza de cada servicio.

Los dos enfoques comunes para la comunicación de microservicio a microservicio son basado en HTTP comunicación REST al consultar los datos y la mensajería asincrónica ligera al comunicarse las actualizaciones a través de varios microservicios.

Mensajería basada en orientada a eventos comunicación asincrónica es fundamental para propagar cambios a través de varios microservicios. Con este enfoque, un microservicio publica un evento cuando se produce algún importantes sucede, por ejemplo, cuando actualiza una entidad de negocio. Otros microservicios suscriben a estos eventos. A continuación, cuando un microservicio recibe un evento, actualiza sus propia entidades empresariales, que a su vez podrían dar lugar a más eventos que se está publicados. Esta publicación y suscripción funcionalidad normalmente se logra con un bus de eventos.

Un bus de eventos permite la comunicación entre microservicios, sin necesidad de los componentes para tenga explícitamente entre sí, tal como se muestra en la figura 8-6 de publicación y suscripción.

![](containerized-microservices-images/eventbus.png "Publicación y suscripción con un bus de eventos")

**Figura 8-6:** suscribirse a la publicación con un bus de eventos

Desde una perspectiva de la aplicación, el bus de eventos es simplemente una publicación-suscribirse expuesta a través de una interfaz de canal. Sin embargo, puede variar la manera en que se implementa el bus de eventos. Por ejemplo, podría utilizar una implementación de bus de eventos RabbitMQ, Service Bus de Azure u otros bus de servicio como NServiceBus y MassTransit. Figura 8-7 muestra cómo se utiliza un bus de eventos en la aplicación de referencia de eShopOnContainers.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Comunicación asincrónica orientada a eventos en la aplicación de referencia")

**Figura 8-7:** comunicación asincrónica orientada a eventos en la aplicación de referencia

El bus de eventos eShopOnContainers, implementado mediante RabbitMQ, se proporciona uno a varios asincrónica funcionalidad de publicación y suscripción. Esto significa que después de publicar un evento, puede haber varios suscriptores escuchando el mismo evento. Figura 8-9 ilustra esta relación.

![](containerized-microservices-images/eventdrivencommunication.png "Comunicación de uno a varios")

**Figura 8-9**: uno a varios comunicación

Este método de comunicación de uno a varios utiliza eventos para implementar las transacciones de negocio que abarcan varios servicios, garantizar la coherencia final entre los servicios. Una transacción eventual coherente consta de una serie de pasos distribuidas. Por lo tanto, cuando el perfil de usuario microservicio recibe el comando UpdateUser, actualiza los detalles del usuario en su base de datos y publica el evento UserUpdated en el bus de eventos. El microservicio de la cesta de compra y la ordenación microservicio se ha suscrito para recibir este evento y en respuesta actualizar su información de comprador en sus respectivas bases de datos.

> [!NOTE]
> El bus de eventos eShopOnContainers, implementado mediante RabbitMQ, está pensado para usarse solo como una prueba de concepto. Para los sistemas de producción, se deben considerar las implementaciones del bus de eventos alternativo.

Para obtener información acerca de la implementación del bus de eventos, vea [Microservicios. NET: arquitectura de aplicaciones de .NET en contenedores](https://aka.ms/microservicesebook).

## <a name="summary"></a>Resumen

Microservicios ofrecen un enfoque de implementación que es adecuado para los requisitos de la agilidad, la escala y la confiabilidad de las aplicaciones modernas en la nube y el desarrollo de aplicaciones. Una de las principales ventajas de microservicios es que puede ser escalado horizontal de forma independiente, lo que significa que se puede escalar un área funcional específica que requiere más procesamiento alimentación eléctrica o red de ancho de banda para admitir la demanda, sin ajuste de escala innecesariamente en áreas de la aplicación que no experimentan una mayor demanda.

Un contenedor es un recurso controlado y portátil entorno operativo aislado, donde una aplicación puede ejecutarse sin tocar los recursos de otros contenedores, o el host. Las empresas están adoptando cada vez más contenedores cuando aplicaciones basadas en la implementación de microservicio y Docker se ha convertido en la implementación de contenedor estándar que se ha optado por la mayoría de las plataformas de software y los proveedores de nube.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
