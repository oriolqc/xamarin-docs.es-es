---
title: tvOS recursos y almacenamiento de datos en Xamarin
description: En este artículo se describe cómo trabajar con recursos y el almacenamiento de datos persistentes en una aplicación de tvOS con Xamarin. Describe los recursos de almacenamiento y de petición de datos de iCloud.
ms.prod: xamarin
ms.assetid: C56B5046-D2C0-4B63-9CE0-ADAA0EFD368A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 8f8ecc115738fb97f71b4ea6b2cdcc5c2714372d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108189"
---
# <a name="tvos-resources-and-data-storage-in-xamarin"></a>tvOS recursos y almacenamiento de datos en Xamarin

_En este artículo se explica cómo trabajar con recursos y almacenamiento de datos persistente en una aplicación Xamarin.tvOS._

<a name="tvOS-Resource-Limitations" />

## <a name="tvos-resource-limitations"></a>Limitaciones de recursos de tvOS

A diferencia de los dispositivos iOS, el nuevo Televisor Apple proporciona un almacenamiento persistente y local extremadamente limitado para tvOS aplicaciones o datos. Para obtener elementos muy pequeños (por ejemplo, las preferencias del usuario), la aplicación tvOS todavía tiene acceso a `NSUserDefaults` con un [límite de 500 KB de datos](https://forums.developer.apple.com/message/50696#50696). Sin embargo, si necesita conservar grandes cantidades de información de la aplicación Xamarin.tvOS, debe almacenar y recuperar los datos de [iCloud](#iCloud-Data-Storage).

Además, tvOS limita el tamaño de una aplicación de Apple TV a 200MB. Si la aplicación necesita recursos más allá de este tamaño, deberá ser empaquetan y cargan con [recursos a petición](#On-Demand-Resources) (hasta 2 GB adicionales). Dadas estas limitaciones, es fundamental que correctamente tiempo la descarga de los recursos adicionales para proporcionar la mejor experiencia para usuarios de su aplicación. Para obtener más información, consulte Apple [Guía de recursos y a petición](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

<a name="Non-Persistent-Downloads" />

## <a name="non-persistent-downloads"></a>Descargas no persistentes

Proporciona un directorio de caché temporal que se descargan a sus activos y recursos adicionales para cada aplicación tvOS. Este directorio se conservarán mientras todavía se está ejecutando la aplicación. Sin embargo, como Apple TV necesita liberar espacio para otras aplicaciones o el uso del sistema, se puede eliminar esta caché.

Como resultado, la aplicación no puede confiar en el contenido descargado previamente que está disponible la próxima vez que se inicie. La aplicación Xamarin.tvOS siempre debe comprobar la existencia de los recursos necesarios y descargarlos según sea necesario.

> [!IMPORTANT]
> Aunque tiene la capacidad de descargar otros recursos y recursos según sea necesario, Apple advierte consumir todo el espacio en memoria caché de la aplicación, ya que puede provocar resultados imprevisibles.




<a name="Managing-Resources" />

## <a name="managing-resources"></a>Administrar recursos

Como se indicó anteriormente, debido a la limitación, el almacenamiento no persistente de la información disponible para las aplicaciones de tvOS, estas restricciones requieren una planificación cuidadosa crear una experiencia de usuario excelente para la aplicación Xamarin.tvOS.

<a name="iCloud-Data-Storage" />

### <a name="icloud-data-storage"></a>Almacenamiento de datos de iCloud

Dado que el almacenamiento en Apple TV es limitado, no solo es muy limitado almacenamiento persistente y local, la aplicación no tiene ninguna garantía de que cualquier información ha descargado anteriormente estará disponible la próxima vez que se ejecute.

Como resultado, la aplicación Xamarin.tvOS debe almacenar los datos de usuario en un Data Store iCloud. Apple proporciona dos opciones de almacenamiento de datos basados en iCloud para las aplicaciones de tvOS:

- **Almacenamiento de clave-valor (KVS) iCloud** : para pequeños fragmentos de información (inferior a 1 MB) que puede requerir la aplicación (por ejemplo, las preferencias del usuario), puede usar el almacenamiento de KVS iCloud. los datos de KVS de iCloud se sincronizarán automáticamente a la nube y todos los dispositivos del usuario que ejecuta la misma aplicación. Para obtener más información, consulte el [almacenamiento de pares clave-valor](~/ios/data-cloud/introduction-to-icloud.md) sección de nuestra [Introducción a iCloud](~/ios/data-cloud/introduction-to-icloud.md) documento o Apple [diseñar para datos de pares clave-valor en iCloud](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/iCloudDesignGuide/Chapters/DesigningForKey-ValueDataIniCloud.html#//apple_ref/doc/uid/TP40012094-CH7) documentación.
- **CloudKit** : para el almacenamiento de grandes piezas de información (superior a 1 MB), usar el marco de Apple CloudKit. A diferencia de KVS almacenamiento de iCloud, CloudKit datos pueden compartirse entre todos los usuarios de la aplicación (así como privada a un único usuario). Forma de obtener más información, consulte nuestra [Introducción a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) documentación o Apple [inicio rápido de CloudKit](https://developer.apple.com/library/prerelease/tvos/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987).

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

<a name="On-Demand-Resources" />

### <a name="on-demand-resources"></a>Recursos y a petición

Los recursos y a petición proporcionar contenido de la aplicación y los recursos (independientes de lote de aplicaciones), que se hospedan en el Store de la aplicación y se descarga según sea necesario por la aplicación. Hasta 2 GB de datos adicionales pueden ser presentado mediante los recursos y a petición. Permiten la descarga de la aplicación inicial a ser más pequeños (tvOS aplicaciones se limitan a un máximo de 200MB), mientras sigue proporcionando enriquecidos activos según sea necesario.

Cuando una aplicación de tvOS solicita recursos y a petición, el sistema administrará automáticamente la descarga y el almacenamiento de este contenido en el directorio de caché de la aplicación. La aplicación debe esperar a este contenido se descargan y se ponen a disposición antes de poder continuar.

Pueden continuar estos recursos en la caché de Apple TV a lo largo de varios lanzamientos de la aplicación, ciclo de lanzamiento, por tanto, acelerar. Sin embargo, la aplicación no puede depender de cualquier contenido descargado previamente que está disponible la próxima vez que se inicie. Consulte la [descargas Non-Persistent](#Non-Persistent-Downloads) sección anterior para obtener más detalles.

Usar Xcode para crear agrupaciones de contenido relacionado (por ejemplo, todos los activos de juego de nivel 2) asociadas con una etiqueta de recurso determinado. Más adelante la aplicación solicitará recursos a petición mediante la especificación de esta etiqueta de recurso. La aplicación debe presentar una interfaz de usuario al usuario que indica que el contenido se está descargando. Para obtener más información, consulte Apple [Guía de recursos y a petición](https://developer.apple.com/library/prerelease/tvos/documentation/FileManagement/Conceptual/On_Demand_Resources_Guide/index.html#//apple_ref/doc/uid/TP40015083).

> [!IMPORTANT]
> Debe tener cuidado para lograr el equilibrio correcto entre el número de veces que la aplicación tiene que descargar los recursos y a petición y el tamaño de las descargas individuales. Usuario que está frustrado después de la aplicación si el juego se interrumpe constantemente para descargar el contenido nuevo o si una sola descarga tarda demasiado tiempo.




<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se trata las limitaciones de almacenamiento de tamaño, recursos y datos realizadas en una aplicación Xamarin.tvOS por el sistema de tvOS. Ha presentado opciones para solucionar estas limitaciones y sugerencias para crear una experiencia de usuario excelente para la aplicación.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicación de tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
