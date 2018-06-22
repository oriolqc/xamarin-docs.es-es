---
title: Búsqueda con marcado Web en Xamarin.iOS
description: Este documento describe cómo crear los resultados de la búsqueda basada en web que estén vinculadas a una aplicación de Xamarin.iOS. Describe cómo habilitar el contenido de web indización, por lo que el sitio Web de la aplicación puede detectar, mediante banners aplicación inteligente, vínculos universal y mucho más.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 438a65de3eb78f849493e3478bce5522a325d0cd
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787999"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Búsqueda con marcado Web en Xamarin.iOS

Para las aplicaciones que proporcionan acceso a su contenido a través de un sitio web (no solo desde dentro de la aplicación), contenido web se puede marcar con vínculos especiales que se volverán a rastrear por Apple y proporcionan la vinculación en profundidad en la aplicación en el dispositivo del usuario iOS 9.

Si su aplicación iOS ya es compatible con la vinculación profunda móvil y su sitio Web presenta vínculos profundos a contenido dentro de su aplicación, Apple _Applebot_ rastreador web volverá indizar este contenido y lo agrega automáticamente a su índice en la nube:

[![](web-markup-images/webmarkup01.png "Información general del índice en la nube")](web-markup-images/webmarkup01.png#lightbox)

Apple detectarán estos resultados en los resultados de búsqueda de Spotlight y Safari.
Si el usuario puntea en uno de estos resultados (y que tienen instalada la aplicación), a continuación, se tomará el contenido de la aplicación:

[![](web-markup-images/webmarkup02.png "Profundidad de vinculación de un sitio Web en los resultados de búsqueda")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Habilitar la indización de contenido Web

Hay cuatro pasos necesarios para exigir el contenido de la aplicación permite realizar búsquedas mediante Web marcado:

1. Asegúrese de que Apple puede detectar y ajustar el sitio Web de la aplicación que lo define como el **compatibilidad** o **Marketing** sitio Web en iTunes Connect.
2. Asegúrese de que el sitio Web de la aplicación contiene el marcado necesario para implementar la vinculación profunda móviles. Vea las secciones siguientes para obtener más detalles.
3. Habilitar vínculo profundo de control en la aplicación de iOS.
4. Agregue marcado de los datos estructurados obtenidos por el sitio Web de la aplicación para proporcionar un resultado interesante y completa para el usuario final. Aunque este paso no es obligatorio, se recomienda encarecidamente por Apple.

En las siguientes secciones se pasará a través de estos pasos detalladamente.

## <a name="make-your-apps-website-discoverable"></a>Hacer que pueda detectar sitios Web de la aplicación

Es la manera más fácil que Apple buscar el sitio Web de la aplicación para usarla como la **compatibilidad** o **Marketing** sitio Web al enviar su aplicación a Apple a través de iTunes Connect.

## <a name="using-smart-app-banners"></a>Uso de Banners de aplicación inteligente

Proporcione un Banner de aplicación inteligentes en el sitio Web para presentar un vínculo claro en la aplicación. Si la aplicación ya no está instalada, Safari le pedirá automáticamente al usuario que instale la aplicación. En caso contrario, puede puntear en el uso del **vista** vínculo para iniciar la aplicación desde el sitio Web. Por ejemplo, para crear un Banner de aplicación inteligente, puede usar el código siguiente:

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Para obtener más información, vea de Apple [promoción de aplicaciones con Banners de aplicación inteligente](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) documentación.

## <a name="using-universal-links"></a>Uso de vínculos Universal

Nuevo en iOS 9, vínculos Universal proporcionar una alternativa mejor que inteligente pancartas de aplicación o esquemas de dirección URL personalizadas existentes escribiendo lo siguiente:

- **Único** : la misma dirección URL no se pueden solicitar más de un sitio Web.
- **Proteger** : se requiere un certificado de firma para el sitio Web que garantiza que es propietario del sitio Web por parte del usuario y ha vinculado a la la aplicación.
- **Flexible** : el usuario final puede controlar si la dirección URL inicia el sitio Web o la aplicación.
- **Universal** : la misma dirección URL se puede utilizar para definir el contenido de su sitio Web y la aplicación.

## <a name="using-twitter-cards"></a>Uso de tarjetas de Twitter

Puede proporcionar vínculos profundos a contenido de la aplicación con una tarjeta de Twitter. Por ejemplo:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Para obtener más información, vea de Twitter [Twitter tarjeta protocolo](http://dev.twitter.com/cards/mobile) documentación.

## <a name="using-facebook-app-links"></a>Uso de vínculos de la aplicación de Facebook

Puede proporcionar vínculos profundos a contenido de la aplicación mediante un Facebook App Link. Por ejemplo:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Para obtener más información, vea Facebook [aplicación vínculos](http://applinks.org) documentación.

## <a name="opening-deep-links"></a>Abrir vínculos profundos

Debe agregar compatibilidad para abrir y mostrar vínculos profundos en la aplicación Xamarin.iOS. Editar la **AppDelegate.cs** archivo e invalide el `OpenURL` método para controlar el formato de dirección URL personalizado. Por ejemplo:

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

En el código anterior, que estamos buscando una dirección URL que contiene `/appname` y pasando el valor de `query` (`123` en este ejemplo) a un controlador de vista personalizada en nuestra aplicación para mostrar el contenido solicitado al usuario.

## <a name="providing-rich-results-with-structured-data"></a>Proporciona los resultados de enriquecido con datos estructurados

Mediante la inclusión de marcado de datos estructurados pueden proporcionar resultados de búsqueda para el usuario final que van más allá de simplemente un título y una descripción. Incluir imágenes, datos específicos de aplicación (como clasificaciones) y las acciones a resultados con formato de datos estructurados.

Los resultados completos se ocupa más y puede ayudar a mejorar la clasificación en la nube según el índice de búsqueda incitando más a los usuarios interactuar con ellos.

Es una opción para proporcionar el marcado de datos estructurados mediante el uso de Open Graph. Por ejemplo:

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Para obtener más información, consulte el [Open Graph](http://ogp.me) sitio Web.

Otro formato común para el marcado de datos estructurados es formato de microdatos de schema.org. Por ejemplo:

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

A continuación muestra un ejemplo de metadatos desde el sitio Web que proporciona los resultados de búsqueda para el usuario final:

[![](web-markup-images/deeplink01.png "Opciones avanzadas de búsqueda resultados a través de marcado de datos estructurados")](web-markup-images/deeplink01.png#lightbox)

Apple admite actualmente los siguientes tipos de esquema de schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - Ofertas
 - Organización
 - PriceRange
 - Solo puede conducir al
 - SearchAction

Para obtener más información sobre estos tipos de esquema, vea [schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Proporcionar acciones con los datos estructurados

Determinados tipos de datos estructurados permitirá un resultado de búsqueda como las que el usuario final. Actualmente se admiten las siguientes acciones:

 - Marcar un número de teléfono.
 - Obtener la dirección de mapa para una dirección determinada.
 - Reproducir un archivo de audio o vídeo.

Por ejemplo, la definición de una acción para marcar un número de teléfono podría ser similar al siguiente:

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

Cuando este resultado de la búsqueda se presenta al usuario final, se mostrará un icono pequeño de teléfono en el resultado. Si el usuario puntea el icono, se llamará al número especificado.

El siguiente código HTML agregar una acción para reproducir un archivo de audio de resultado de la búsqueda:

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

Por último, el código HTML siguiente agregaría una acción para obtener instrucciones de resultado de la búsqueda:

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

Para obtener más información, vea de Apple [sitio para desarrolladores de búsqueda de aplicación](http://developer.apple.com/ios/search/).



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de la aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
