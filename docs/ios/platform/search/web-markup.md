---
title: Búsqueda con marcado Web en Xamarin.iOS
description: Este documento describe cómo crear los resultados de búsqueda basada en web que vinculan a una aplicación de Xamarin.iOS. Describe cómo habilitar el contenido de web, indexación, por lo que el sitio Web de la aplicación puede detectar, mediante los banners de aplicaciones inteligentes, vínculos universales y mucho más.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 4ee07e4b47ed9e1bdca0efc814ad44e513f68e80
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672370"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Búsqueda con marcado Web en Xamarin.iOS

Para las aplicaciones que proporcionan acceso a su contenido a través de un sitio web (no solo desde dentro de la aplicación), se puede marcar contenido web con vínculos especiales que se rastreará por Apple y proporcionan la vinculación en profundidad en la aplicación en el dispositivo del usuario iOS 9.

Si su aplicación iOS ya admite la vinculación en profundidad móvil y su sitio Web presenta vínculos profundos a contenido dentro de su aplicación, Apple _Applebot_ rastreador web volverá indizar este contenido y lo agrega automáticamente a su índice en la nube:

[![](web-markup-images/webmarkup01.png "Información general del índice en la nube")](web-markup-images/webmarkup01.png#lightbox)

Apple detectarán estos resultados en los resultados de búsqueda de Spotlight y Safari.
Si el usuario puntea en uno de estos resultados (y que tienen instalada la aplicación), a continuación, se le dirigirá al contenido de la aplicación:

[![](web-markup-images/webmarkup02.png "Vinculación de un sitio Web en los resultados de búsqueda profunda")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Habilitar la indización de contenido Web

Hay cuatro pasos necesarios para hacer el contenido de la aplicación se pueda buscar con marcado Web:

1. Asegúrese de que Apple puede detectar y sitio Web de la aplicación de índice si se define como el **soporte** o **Marketing** sitio Web en iTunes Connect.
2. Asegúrese de que el sitio Web de la aplicación contiene el marcado necesario para implementar la vinculación en profundidad móviles. Consulte las secciones siguientes para obtener más detalles.
3. Habilitar vínculo profundo de control en su aplicación iOS.
4. Agregue el marcado de los datos estructurados obtenidos por el sitio Web de la aplicación para proporcionar un resultado sofisticado y atractivo para el usuario final. Aunque este paso no es estrictamente necesaria, se recomienda por Apple.

Las siguientes secciones se pasará a través de estos pasos con detalle.

## <a name="make-your-apps-website-discoverable"></a>Hacer que el sitio Web de la aplicación sea reconocible

Es la manera más fácil tener Apple buscar el sitio Web de la aplicación para usarla como la **soporte** o **Marketing** sitio Web al enviar la aplicación a Apple a través de iTunes Connect.

## <a name="using-smart-app-banners"></a>Uso de Banners de aplicaciones inteligentes

Proporcione un Banner de aplicaciones inteligentes en su sitio Web para presentar un vínculo claro en la aplicación. Si la aplicación ya no está instalada, Safari pedirá automáticamente al usuario que instale la aplicación. En caso contrario, puede pulsar el uso del **vista** vínculo para iniciar la aplicación desde el sitio Web. Por ejemplo, para crear un Banner de aplicaciones inteligentes, puede usar el código siguiente:

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Para obtener más información, consulte Apple [promocionar aplicaciones con Banners aplicación inteligente](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) documentación.

## <a name="using-universal-links"></a>Uso de vínculos universales

Nuevo a iOS 9, vínculos universales ofrecen una alternativa mejor inteligente Banners de aplicación o esquemas de dirección URL personalizadas existentes proporcionando la siguiente:

- **Único** : la misma dirección URL no se pueden solicitar más de un sitio Web.
- **Proteger** : se requiere un certificado firmado por el sitio Web que garantiza que pertenece el sitio Web por el usuario y ha vinculado a la la aplicación.
- **Flexible** : el usuario final puede controlar si la dirección URL inicia el sitio Web o la aplicación.
- **Universal** : la misma dirección URL se puede usar para definir el contenido de su sitio Web y la aplicación.

## <a name="using-twitter-cards"></a>Uso de tarjetas de Twitter

Puede proporcionar vínculos profundos a contenido de la aplicación mediante una tarjeta de Twitter. Por ejemplo:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Para obtener más información, consulte de Twitter [Twitter tarjeta protocolo](http://dev.twitter.com/cards/mobile) documentación.

## <a name="using-facebook-app-links"></a>Uso de vínculos de la aplicación de Facebook

Puede proporcionar vínculos profundos a contenido de la aplicación mediante un Facebook App Link. Por ejemplo:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Para obtener más información, consulte Facebook [aplicación vínculos](http://applinks.org) documentación.

## <a name="opening-deep-links"></a>Abrir vínculos profundos

Deberá agregar compatibilidad para abrir y mostrar vínculos profundos en la aplicación de Xamarin.iOS. Editar el **AppDelegate.cs** archivo e invalidar la `OpenURL` método para controlar el formato de dirección URL personalizado. Por ejemplo:

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

    // Handling a URL in the form http://company.com/appname/?123
    try {
        var components = new NSUrlComponents(url,true);
        var path = components.Path;
        var query = components.Query;

        // Is this a known format?
        if (path == "/appname") {
            // Display the view controller for the content
            // specified in query (123)
            return ContentViewController.LoadContent(query);
        }
    } catch {
        // Ignore issue for now
    }

    return false;
}
```

En el código anterior, estamos buscando una dirección URL que contiene `/appname` y pasando el valor de `query` (`123` en este ejemplo) a un controlador de vista personalizada en nuestra aplicación para mostrar el contenido solicitado al usuario.

## <a name="providing-rich-results-with-structured-data"></a>Proporciona los resultados de enriquecido con datos estructurados

Mediante la inclusión de marcado estructurado de datos puede proporcionar los resultados de búsqueda enriquecida para el usuario final que van más allá de simplemente un título y descripción. Incluir imágenes, datos específicos de la aplicación (como clasificaciones) y acciones para los resultados con el marcado de datos estructurados.

Resultados de Rich son más atractivas y puede ayudar a mejorar la clasificación en la nube en función de índice de búsqueda por incitar más a los usuarios interactuar con ellos.

Una opción para proporcionar marcado estructurado de datos es mediante el uso de Open Graph. Por ejemplo:

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Para obtener más información, consulte el [Open Graph](http://ogp.me) sitio Web.

Otro comunes de marcado estructurado de datos es microdatos formato de schema.org. Por ejemplo:

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

La misma información se puede representar en formato de JSON-LD del schema.org:

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

La siguiente muestra un ejemplo de metadatos desde su sitio Web que proporciona los resultados de búsqueda enriquecida para el usuario final:

[![](web-markup-images/deeplink01.png "Opciones avanzadas de búsqueda resultados a través de marcado estructurado de datos")](web-markup-images/deeplink01.png#lightbox)

Apple actualmente admite los siguientes tipos de esquema de schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - Ofertas
 - Organización
 - PriceRange
 - Receta
 - SearchAction

Para obtener más información sobre estos tipos de esquema, vea [schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Proporcionar acciones con datos estructurados

Determinados tipos de datos estructurados permitirá un resultado de búsqueda poder por el usuario final. Actualmente se admiten las siguientes acciones:

 - Marcar un número de teléfono.
 - Obteniendo dirección de asignación a una dirección determinada.
 - Reproducir un archivo de audio o vídeo.

Por ejemplo, definir una acción para marcar un número de teléfono podría ser similar al siguiente:

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

Cuando el resultado de la búsqueda se presenta al usuario final, se mostrará un icono pequeño de teléfono en el resultado. Si el usuario pulsa el icono, se llamará al número especificado.

El siguiente código HTML agregaría una acción para reproducir un archivo de audio desde el resultado de búsqueda:

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

Por último, el siguiente código HTML agregaría una acción para obtener instrucciones de resultado de la búsqueda:

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

Para obtener más información, consulte Apple [sitio para desarrolladores de aplicación de búsqueda](https://developer.apple.com/ios/search/).



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de la aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
