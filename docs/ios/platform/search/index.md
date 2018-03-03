---
title: "Nuevas API de búsqueda"
description: "Este artículo incluye el uso de las nuevas API de búsqueda de aplicación proporcionado por iOS 9 para permitir a los usuarios buscar información y funciones dentro de las aplicaciones de Xamarin.iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 2d802a96fcc8dad1d610b99a1cddffdc4398da38
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="new-search-apis"></a>Nuevas API de búsqueda

_Este artículo incluye el uso de las nuevas API de búsqueda de aplicación proporcionado por iOS 9 para permitir a los usuarios buscar información y funciones dentro de las aplicaciones de Xamarin.iOS._

Se ha expandido la búsqueda en iOS 9 para proporcionar acceso a información y funciones dentro de una aplicación Xamarin.iOS excelentes formas nuevas. Mediante las nuevas API de búsqueda de la aplicación, contenido de la aplicación se realiza búsqueda a través de servicios y Safari resultados de la búsqueda, entrega y avisos de Siri y sugerencias. Esto permite a los usuarios obtener acceso rápidamente a las actividades y obtener información detallada dentro de la aplicación.

Además, las nuevas API de búsqueda facilitan la integración de búsqueda en la aplicación sin experiencia en la implementación anterior de búsqueda. Por este motivo, Apple notificaciones que suele tardar unas horas para que el contenido de una aplicación iOS 9 universalmente permite realizar búsquedas mediante la búsqueda de la aplicación.

[ ![](images/intro01.png "Un ejemplo de contenido de la aplicación de iOS 9 universalmente permite realizar búsqueda mediante la búsqueda de la aplicación")](images/intro01.png)

Búsqueda de la aplicación se compone de tres API independientes:

1. [**NSUserActivity** ](nsuseractivity.md) -se trata de una extensión de la API de entrega que Apple publicada en iOS 8. Se utiliza para hacer posible la búsqueda historial de interacción de la aplicación tanto pública y privada) por el usuario.

2. [**Principales Spotlight** ](corespotlight.md) -permite que una aplicación indizar su contenido se muestre en los resultados de búsqueda. Funciona como una API que pueden agregar y quitar los elementos y es la mejor manera de indizar el contenido privado dentro de una aplicación de base de datos.

3. [**WebMarkup** ](web-markup.md) : para las aplicaciones que proporcionan acceso a su contenido mediante una interfaz web (no solo desde dentro de la aplicación). Contenido Web se puede marcar con vínculos especiales que se volverán a rastrear por Apple y proporcionan la vinculación en profundidad a la aplicación en el dispositivo del usuario iOS 9.

## <a name="selecting-an-app-search-approach"></a>Al seleccionar un método de búsqueda de la aplicación

Decidir cuál de estos métodos para implementar depende de los tipos de interacción proporcionada por la aplicación y el tipo de contenido que y se muestra.

Utilice las siguientes directrices:

- [**NSUserActivity** ](nsuseractivity.md) : Utilice este marco de trabajo para proporcionar funciones de búsqueda tanto contenido público y privado y también funciones de búsqueda de puntos de navegación dentro de la aplicación.

- [**Principales Spotlight** ](corespotlight.md) : utilizar este marco de trabajo para proporcionar funciones de búsqueda de datos privados almacenados en el dispositivo.

- [**Web marcado** ](web-markup.md) : Use este marco de trabajo para proporcionar funciones de búsqueda para las aplicaciones que presentan su contenido no solo desde dentro de la aplicación, pero desde el sitio Web de la aplicación también.

Cada una de la búsqueda de la aplicación enfoques son distintos y se pueden utiliza individualmente, sin embargo Apple han diseñado para trabajar conjuntamente. Cuando se usa más de un enfoque para indizar un elemento específico, asegúrese de que usa el mismo **Id. del elemento** en cada enfoque, por lo que esta persona funcionan los vínculos juntos.

No solo con más de un enfoque garantiza que el contenido se encuentra el usuario final, pero también ayuda a mejorar la clasificación de tu artículo desde dentro de la búsqueda.

Durante el proceso de clasificación en gran parte transparente para el desarrollador de la interacción del usuario con un elemento determinado pesa muy tras este rango (por ejemplo, el usuario punteando en un vínculo).
Al proporcionar elementos enriquecidos y proporcionar información útil, puede asegurarse de que un usuario se le enticed para interactuar con el contenido, por tanto, cuando se genera su clasificación.

## <a name="what-content-to-index"></a>El contenido al índice

Apple proporciona las siguientes sugerencias sobre qué contenido y las acciones para proporcionar los índices de búsqueda para la aplicación:

 - Cualquier contenido ve, crea o seleccionada por el usuario desde dentro de la aplicación.
 - Los puntos de navegación y características dentro de la aplicación.
 - Elementos como mensajes nuevos, contenido u otros tipos de elementos mostrados por la aplicación que se han descargado recientemente en el dispositivo.

## <a name="app-search-enhancements"></a>Mejoras en la búsqueda de la aplicación

Noticias destacadas de núcleos en iOS 10 incluye varias mejoras en la búsqueda de la aplicación como:

- **Popularidad de vínculo profundo de Crowdsourced (con privacidad diferencial)** -proporciona una manera para promover el contenido de la aplicación con vínculo profundo en resultados de búsqueda.
- **Buscar en la aplicación** -usar el nuevo `CSSearchQuery` clase para proporcionar capacidad de búsqueda de Spotlight de aplicación similar al funcionan de las aplicaciones de correo electrónico, mensajes y notas.
- **Buscar continuación** : permite a un usuario iniciar una búsqueda de Spotlight o Safari, a continuación, abrir una aplicación y continuar la búsqueda.
- **Visualización de resultados de validación** -de Apple [herramienta de validación de API de búsqueda de aplicación](https://search.developer.apple.com/appsearch-validation-tool) ahora muestra una representación visual de la vinculación profunda y marcado de un sitio Web cuando preforming pruebas.
- **Imagen de aplicación de uso compartido de mensaje** -permite populares imágenes de aplicación proporcionadas para el uso compartido de mensajes (a través de una extensión de la aplicación de mensaje) para que aparezca en las búsquedas de servicios.

Para obtener más información, visite nuestro [mejoras en la aplicación de búsqueda](~/ios/platform/search/app-search-enhancements.md) guía.

### <a name="proactive-suggestions"></a>Sugerencias automático

iOS 10 presenta nuevas formas de contratación conducir a una aplicación al permitir que el sistema de forma proactiva presentar información útil automáticamente al usuario en los momentos adecuados. Al igual que iOS 9 proporcionan la capacidad de Agregar búsqueda en profundidad la aplicación con servicios, entrega y sugerencias de Siri, con iOS 10 que una aplicación puede exponer la funcionalidad que se puede presentar al usuario por el sistema desde dentro de las siguientes ubicaciones:

- El selector de la aplicación
- La pantalla de bloqueo
- CarPlay
- Asignaciones
- Interacciones de Siri
- Sugerencias de QuickType 

Una aplicación expone esta funcionalidad en el sistema mediante un conjunto de tecnologías como [NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/), marcado web, servicios de núcleo, MapKit, el Reproductor de Media y UIKit.

Para obtener más información, visite nuestro [sugerencias automático](~/ios/platform/search/proactive-suggestions.md) guía.

## <a name="summary"></a>Resumen

En este artículo ha cubierto las nuevas características de la API de búsqueda que iOS 9 proporciona para las aplicaciones de Xamarin.iOS. Se tratan [NSUserActivity](nsuseractivity.md), [Core Spotlight](corespotlight.md) y [Web marcado](web-markup.md) métodos para la indización de contenido. Finalizó con una breve explicación de cuándo se debe utilizar un enfoque de búsqueda dados y cuáles deben ser tipos de contenido indizadas.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de la aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
