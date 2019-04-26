---
title: API de búsqueda en Xamarin.iOS
description: En este artículo se describe el uso de las nuevas API de búsqueda de aplicación proporcionado por iOS 9 para permitir a los usuarios buscar información y las funciones dentro de las aplicaciones de Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: b2968399279fe3e9d160471bbcae08ae091be93e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075663"
---
# <a name="search-apis-in-xamarinios"></a>API de búsqueda en Xamarin.iOS

_En este artículo se describe el uso de las API de búsqueda de aplicación proporcionado por iOS 9 para permitir a los usuarios buscar información y las funciones dentro de las aplicaciones de Xamarin.iOS._

Se ha ampliado la búsqueda en iOS 9 para proporcionar excelentes nuevas formas de acceder a información y características dentro de una aplicación de Xamarin.iOS. Mediante las nuevas API de búsqueda de la aplicación, se podrán buscar a través de Spotlight y Safari resultados de la búsqueda, entrega y avisos de Siri y sugerencias de contenido de la aplicación. Esto permite a los usuarios acceder rápidamente a las actividades y la información en profundidad dentro de la aplicación.

Además, las nuevas API de búsqueda facilitan la integración de búsqueda en la aplicación sin experiencia en la implementación anterior de búsqueda. Por este motivo, notificaciones de Apple que suele tardar unas horas en que el contenido de una aplicación iOS 9 universalmente mediante la búsqueda de la aplicación de búsqueda.

[![](images/intro01.png "Un ejemplo de contenido de la aplicación de iOS 9 universalmente mediante la búsqueda de la aplicación de búsqueda")](images/intro01.png#lightbox)

Búsqueda de la aplicación se compone de tres API independientes:

1. [**NSUserActivity** ](nsuseractivity.md) -esta es una extensión de la API de entrega que Apple se lanzó en iOS 8. Se usa para que permita la búsqueda en el historial de interacción de aplicaciones tanto de forma pública y privada) por el usuario.

2. [**Noticias destacadas de núcleo** ](corespotlight.md) -permite que una aplicación indizar su contenido se presente en los resultados de búsqueda. Funciona como una base de datos que se pueden agregar y quitar elementos, y es la mejor manera de indizar el contenido privado dentro de una aplicación de API.

3. [**WebMarkup** ](web-markup.md) : para las aplicaciones que proporcionan acceso a su contenido mediante una interfaz web (no solo desde dentro de la aplicación). , Se puede marcar contenido Web con vínculos especiales que se rastreará por Apple y proporcionan la vinculación en profundidad a la aplicación en el dispositivo del usuario iOS 9.

## <a name="selecting-an-app-search-approach"></a>Selección de un método de búsqueda de la aplicación

Decidir cuál de estos métodos para implementar depende de los tipos de interacción de la aplicación y el tipo de contenido que presenta.

Utilice las siguientes directrices:

- [**NSUserActivity** ](nsuseractivity.md) : Use este marco de trabajo para proporcionar funciones de búsqueda para el público y privado contenido y también las funciones de búsqueda de puntos de navegación dentro de la aplicación.

- [**Noticias destacadas de núcleo** ](corespotlight.md) : utilizar este marco de trabajo para proporcionar funciones de búsqueda de datos privados almacenados en el dispositivo.

- [**Web marcado** ](web-markup.md) : usar este marco de trabajo para proporcionar funciones de búsqueda para las aplicaciones que presenten su contenido no solo desde dentro de la aplicación, pero desde el sitio Web de la aplicación también.

Cada uno de la búsqueda de la aplicación enfoques son distintos y pueden utilizar individualmente, sin embargo, Apple diseñado que trabajen juntos. Cuando se usa más de un enfoque para indexar un elemento específico, asegúrese de que usa el mismo **Id. del elemento** en cada enfoque, así que esa persona funcionan los vínculos entre sí.

Con más de un enfoque no solo garantiza que el contenido se encuentra el usuario final, pero también ayuda a mejorar la clasificación de su elemento desde dentro de la búsqueda.

Durante el proceso de clasificación en gran parte transparente para el desarrollador, interacción del usuario con un determinado elemento pesa mucho tras este rango (por ejemplo, el usuario punteando en un vínculo).
Al proporcionar elementos enriquecidos, informativos, puede asegurarse de que un usuario se le enticed para interactuar con el contenido, elevando así su clasificación.

## <a name="what-content-to-index"></a>El contenido al índice

Apple proporciona las siguientes sugerencias sobre qué contenido y las acciones para proporcionar los índices de búsqueda para en la aplicación:

 - Cualquier contenido ve, crea o seleccionada por el usuario desde dentro de la aplicación.
 - Puntos de navegación y las características dentro de la aplicación.
 - Cosas como los mensajes nuevos, contenido u otros tipos de elementos mostrados por la aplicación que recientemente se han descargado en el dispositivo.

## <a name="app-search-enhancements"></a>Mejoras en las búsquedas de aplicaciones

Core Spotlight en iOS 10 proporciona varias mejoras para la búsqueda de la aplicación, como:

- **Popularidad de vínculo profundo de Micromecenazgo (con privacidad diferencial)** -proporciona una manera para promover el contenido de la aplicación con vínculo profundo en resultados de búsqueda.
- **Buscar en la aplicación** -usar el nuevo `CSSearchQuery` clase para proporcionar capacidad de búsqueda de Spotlight en aplicación similar a cómo funcionan las aplicaciones de correo electrónico, mensajes y notas.
- **Buscar continuación** : permite que un usuario iniciar una búsqueda de Spotlight o Safari, a continuación, abrir una aplicación y continuar esa búsqueda.
- **Visualización de resultados de validación** -Apple [herramienta de validación de API de búsqueda de aplicación](https://search.developer.apple.com/appsearch-validation-tool) ahora muestra una representación visual de marcado de un sitio Web y vínculos profundos cuando realice las pruebas.
- **Aplicación de uso compartido de imágenes del mensaje** -permite imágenes populares de aplicación proporcionadas para el uso compartido de mensajes (a través de una extensión de la aplicación de mensaje) para que aparezca en las búsquedas de Spotlight.

Para obtener más información, consulte nuestra [mejoras en la aplicación de búsqueda](~/ios/platform/search/app-search-enhancements.md) guía.

### <a name="proactive-suggestions"></a>Sugerencias proactivas

iOS 10 presenta nuevas formas de conducción engagement para una aplicación, ya que permite el sistema de forma proactiva presentar información útil automáticamente al usuario en los momentos adecuados. Igual que iOS 9 proporciona la capacidad de agregar una búsqueda en profundidad a la aplicación mediante Spotlight, entrega y sugerencias de Siri, con iOS 10, que una aplicación puede exponer la funcionalidad que se puede presentar al usuario por el sistema desde dentro de las siguientes ubicaciones:

- El modificador de la aplicación
- La pantalla de bloqueo
- CarPlay
- Asignaciones
- Interacciones de Siri
- Sugerencias de QuickType 

Una aplicación expone esta funcionalidad al sistema mediante un conjunto de tecnologías como [NSUserActivity](xref:Foundation.NSUserActivity), marcado web, Core Spotlight, MapKit, Media Player y UIKit.

Para obtener más información, consulte nuestra [sugerencias proactivas](~/ios/platform/search/proactive-suggestions.md) guía.

## <a name="summary"></a>Resumen

Este artículo trata las nuevas características de la API de búsqueda que iOS 9 proporciona aplicaciones de Xamarin.iOS. Se tratan [NSUserActivity](nsuseractivity.md), [Core Spotlight](corespotlight.md) y [marcado Web](web-markup.md) métodos para la indización de contenido. Finalizó con una breve explicación de cuándo se debe utilizar un enfoque de búsqueda determinada y cuáles deben ser tipos de contenido indizado.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de la aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
