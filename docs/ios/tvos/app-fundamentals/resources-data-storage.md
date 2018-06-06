---
title: tvOS recursos y el almacenamiento de datos en Xamarin
description: En este artículo se describe cómo trabajar con recursos y el almacenamiento de datos persistentes en una aplicación de tvOS compilada con Xamarin. Se trata de recursos de almacenamiento y a petición de datos de iCloud.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: aa14ac08763a48b5f4f9a9522e8b10c652e513c4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788841"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>tvOS recursos y el almacenamiento de datos en Xamarin

_Este artículo explica cómo trabajar con recursos y el almacenamiento de datos persistentes en una aplicación Xamarin.tvOS._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>tvOS limitaciones de recursos

A diferencia de los dispositivos iOS, el nuevo Televisor de Apple proporciona un almacenamiento persistente y local extremadamente limitado para tvOS aplicaciones o datos. Para obtener elementos muy pequeños (por ejemplo, las preferencias del usuario), la aplicación tvOS todavía tiene acceso a `NSUserDefaults` con un [límite de 500 KB de datos](https://forums.developer.apple.com/message/50696#50696). Sin embargo, si la aplicación Xamarin.tvOS necesita almacenar grandes cantidades de información, debe almacenar y recuperar los datos de [iCloud](#iCloud-Data-Storage).

Además, tvOS limita el tamaño de una aplicación de TV de Apple a 200MB. Si la aplicación necesita recursos más allá de este tamaño, deben ser empaquetar y cargar mediante [recursos a petición](#On-Demand-Resources) (hasta 2 GB adicionales). Dadas estas limitaciones, es fundamental que correctamente tiempo la descarga de recursos adicionales para proporcionar la mejor experiencia para los usuarios de la aplicación. Para obtener más información, vea de Apple [Guía de recursos a petición](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Descargas no persistentes

Cada aplicación tvOS se proporciona un directorio de caché temporal que se descargan a sus recursos adicionales y los activos en. Este directorio se conservarán siempre y cuando la aplicación se está ejecutando. Sin embargo, como Apple TV necesita liberar espacio para otras aplicaciones o el uso del sistema, se puede eliminar esta caché.

Como resultado, la aplicación no puede confiar en que están disponibles la próxima vez que se inicia el contenido descargado previamente. La aplicación Xamarin.tvOS siempre debe comprobar la existencia de los recursos necesarios y descargarlos según sea necesario.

> [!IMPORTANT]
> Mientras tiene la capacidad para descargar otros activos y recursos según sea necesario, Apple advierte de consumir todo el espacio en memoria caché de la aplicación, ya que puede provocar resultados imprevisibles.




<a name="Managing-Resources" />

## <a name="managing-resources"></a>Administrar recursos

Como se indicó anteriormente, debido a la limitación de almacenamiento no persistente de la información disponible para las aplicaciones tvOS, estas restricciones requieren un planeamiento cuidadoso para crear una experiencia óptima al usuario para la aplicación Xamarin.tvOS.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Almacenamiento de datos de iCloud

Dado que se limita el almacenamiento en Apple TV, no solo es muy limitado un almacenamiento persistente y local, la aplicación no tiene ninguna garantía de que cualquier información que descargaron estará disponible la próxima vez que se ejecute.

Como resultado, la aplicación Xamarin.tvOS debe almacenar los datos de usuario en un almacén de datos de iCloud. Apple proporciona dos opciones de almacenamiento de datos basados en iCloud para las aplicaciones de tvOS:

- **iCloud almacenamiento de información de clave y valor (KVS)** : para pequeños fragmentos de información (inferior a 1 MB) que puede requerir la aplicación (por ejemplo, las preferencias del usuario), puede usar el almacenamiento de KVS iCloud. datos de KVS iCloud se sincronizan automáticamente a la nube y todos los dispositivos del usuario ejecutan la misma aplicación. Para obtener más información, consulte el [almacenamiento de información de clave y valor](~/ios/data-cloud/introduction-to-icloud.md) sección de nuestro [Introducción a iCloud](~/ios/data-cloud/introduction-to-icloud.md) documento o de Apple [diseñar para datos de valores de clave en iCloud](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) documentación.
- **CloudKit** : para el almacenamiento de grandes piezas de información (superior a 1 MB), use CloudKit Framework de Apple. A diferencia de iCloud almacenamiento KVS, CloudKit datos pueden compartirse entre todos los usuarios de la aplicación (como las que se va a privada a un único usuario). Formulario obtener más información, consulte nuestro [Introducción a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) documentación o de Apple [inicio rápido de CloudKit](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987).

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Recursos de petición

Recursos de petición ofrecen contenido de las aplicaciones y los activos (independientes de la agrupación de aplicaciones), que se hospedan en la tienda de aplicaciones y se descarga según sea necesario por la aplicación. Hasta 2 GB de datos adicionales pueden proporcionarse mediante recursos a petición. Permiten la descarga de la aplicación inicial más pequeño (tvOS aplicaciones se limitan a un máximo de 200MB), mientras sigue proporcionando enriquecidos activos según sea necesario.

Cuando una aplicación tvOS solicita recursos a petición, el sistema administra automáticamente la descarga y el almacenamiento de este contenido en el directorio de caché de la aplicación. La aplicación debe esperar para que este contenido se descargan y están disponibles para poder continuar.

Pueden seguir estos recursos en la caché de la televisión de Apple a lo largo de varias inicia de la aplicación, por lo tanto aceleración ciclo de inicio. Sin embargo, la aplicación no puede confiar en cualquier contenido descargado anteriormente que están disponibles la próxima vez que se inicia. Consulte la [descarga Non-Persistent](#Non-Persistent-Downloads) sección anterior para obtener más detalles.

Utilice Xcode para crear paquetes de contenido relacionado (por ejemplo, todos los activos de juego nivel 2) asociados a una determinada etiqueta de recurso. Más adelante la aplicación solicitará recursos a petición mediante la especificación de esta etiqueta de recurso. La aplicación debe presentar una interfaz de usuario para el usuario que indica que el contenido se descarga. Para obtener más información, vea de Apple [Guía de recursos a petición](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

> [!IMPORTANT]
> Debe tener cuidado para lograr el equilibrio adecuado entre el número de veces que la aplicación tiene que descargar los recursos a petición y el tamaño de las descargas individuales. Usuario puede quedar frustrado con la aplicación si juega se interrumpe constantemente para descargar contenido nuevo o si una única descarga tarda demasiado tiempo.




<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata las limitaciones de almacenamiento de datos, tamaño y recursos colocadas en una aplicación de Xamarin.tvOS por el sistema tvOS. Ha presentado opciones para solucionar estas limitaciones y sugerencias para crear una experiencia óptima al usuario para la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guías de interfaz humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones tvos](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
