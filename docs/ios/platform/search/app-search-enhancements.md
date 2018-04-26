---
title: Mejoras en la búsqueda de la aplicación
description: Este artículo tratan las mejoras de Apple ha realizado en la búsqueda de la aplicación en iOS 10 y cómo implementarlas en Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 0df51429ea9655b0a72d9f4c1e413fa7e37410ac
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="app-search-enhancements"></a>Mejoras en la búsqueda de la aplicación

_Este artículo tratan las mejoras de Apple ha realizado en la búsqueda de la aplicación en iOS 10 y cómo implementarlas en Xamarin.iOS._

En iOS 10, Apple ha realizado varias mejoras en la búsqueda de la aplicación como la vinculación profunda de Crowdsourced, en la aplicación de búsqueda, continuación de búsqueda y visualización de resultados de la validación. En este artículo se tratará la implementación de estas características en una aplicación de Xamarin.iOS.

## <a name="about-app-search-enhancements"></a>Acerca de las mejoras de búsqueda de la aplicación

Noticias destacadas de núcleos en iOS 10 incluye varias mejoras en la búsqueda de la aplicación como:

- **Popularidad de vínculo profundo de Crowdsourced (con privacidad diferencial)** -proporciona una manera para promover el contenido de la aplicación con vínculo profundo en resultados de búsqueda.
- **Buscar en la aplicación** -usar el nuevo `CSSearchQuery` clase para proporcionar capacidad de búsqueda de Spotlight de aplicación similar al funcionan de las aplicaciones de correo electrónico, mensajes y notas.
- **Buscar continuación** : permite a un usuario iniciar una búsqueda de Spotlight o Safari, a continuación, abrir una aplicación y continuar la búsqueda.
- **Visualización de resultados de validación** -de Apple [herramienta de validación de API de búsqueda de aplicación](https://search.developer.apple.com/appsearch-validation-tool) ahora muestra una representación visual de la vinculación profunda y marcado de un sitio Web cuando preforming pruebas.
- **Imagen de aplicación de uso compartido de mensaje** -permite populares imágenes de aplicación proporcionadas para el uso compartido de mensajes (a través de una extensión de la aplicación de mensaje) para que aparezca en las búsquedas de servicios.

En las siguientes secciones se tratan en estos temas con más detalle.

## <a name="crowdsourced-deep-link-popularity"></a>Vínculo profundo de Crowdsourced popularidad

iOS 10 proporciona un mecanismo para contar la frecuencia que profundo: vínculos más populares en una aplicación se seguido por el usuario y usa esta información para mejorar la clasificación de una aplicación del contenido en los resultados de búsqueda, mientras todavía protege la identidad del usuario mediante el uso de  *Privacidad diferencial*.

Para aplicaciones que utilizan `NSUserActivity` objetos proporcionar las direcciones URL de vínculo profundo y tener la `EligibleForPublicIndexing` propiedad establecida en `true`, iOS 10 envía un subconjunto de *diferencial Hashes de privacidad* para servidores de Apple. Esta información, a continuación, se usa para promover popular contenido en la aplicación en los resultados de búsqueda.

Para obtener más información sobre cómo implementar la vinculación profunda en una aplicación Xamarin.iOS, visite nuestro [búsqueda con NSUserActivity](~/ios/platform/search/nsuseractivity.md) documentación.

## <a name="in-app-searching"></a>En la aplicación de búsqueda

Al implementar la nueva [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) (clase), una aplicación puede proporcionar de servicios búsqueda y tecnología de reglas de coincidencia para buscar contenido dentro de sí mismo, sin la necesidad de salir de la aplicación (similar a cómo el correo electrónico, mensajes y notas de la aplicación del usuario trabajo).

Normalmente, las aplicaciones que admiten `CSSearchQuery` no tendrá que mantener su propio índice de búsqueda independiente. 

## <a name="search-continuation"></a>Continuación de búsqueda

En iOS 9, Apple incorporó las API de búsqueda (como servicios de núcleo, `NSUserActivity` y marcado de web) para proporcionar gusto profunda de contenido dentro de una aplicación para permitir a los usuarios buscar contenido mediante interfaces de búsqueda de Spotlight tanto de Safari. Consulte nuestro [nuevas API de búsqueda](~/ios/platform/search/index.md) documentación para obtener más detalles.

En iOS Apple 10 se basa en esta característica al permitir que el usuario iniciar una búsqueda de Spotlight o Safari, a continuación, continuar la búsqueda cuando abre una aplicación. 

Para implementar esta característica, edite la aplicación `Info.plist` , agregue el `CoreSpotlightContinuation` clave de tipo **booleano** y establezca su valor en `YES`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](app-search-enhancements-images/search01.png "Editar CoreSpotlightContinuation en el archivo Info.plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](app-search-enhancements-images/searchw01.png "Editar CoreSpotlightContinuation en el archivo Info.plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Para responder al usuario continuar un resultado de búsqueda (`NSUserActivity`), editar la `AppDelegate.cs` archivo e invalide el `ContinueUserActivity` método. Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

Este código busca el tipo de acción de continuación de consulta (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`), a continuación, lee la consulta del usuario actual de la `NSUserActivity` diccionario de información de usuario de la clase (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`). Desde aquí, la aplicación debe tomar medidas para continuar la búsqueda del usuario.

Para obtener más información sobre cómo trabajar con búsquedas en una aplicación Xamarin.iOS, visite nuestro [búsqueda de Spotlight Core](~/ios/platform/search/corespotlight.md) documentación.

## <a name="visualization-of-validation-results"></a>Visualización de resultados de validación

Apple [herramienta de validación de API de búsqueda de aplicación](https://search.developer.apple.com/appsearch-validation-tool) ahora muestra una representación visual de la vinculación profunda y marcado de un sitio Web (incluido el marcado, como se define en [Schema.org](http://schema.org/)) cuando preforming pruebas.

Mediante la herramienta de validación, un programador podrá ver la información que el rastreador (crawler) Web de Applebot se ha indexado para el sitio como el título, descripción, dirección URL y cualquier otro elementos que admite.

Para obtener más información sobre cómo trabajar con marcado de Web, consulte nuestra [buscar con marcado Web](~/ios/platform/search/web-markup.md) documentación.

## <a name="message-app-image-sharing"></a>Imagen de la aplicación de uso compartido del mensaje

Si una extensión de la aplicación de mensaje proporciona imágenes para su uso compartido en los mensajes, la extensión puede configurarse para permitir al usuario realizar búsquedas de Spotlight para imágenes populares desde dentro de los mensajes, sin necesidad de salir de la aplicación.

Para habilitar esta característica, haga lo siguiente:

1. Crear una extensión de la aplicación de mensaje.
2. Agregue el `com.apple.developer.associated-domains` a los derechos de la aplicación e incluyen una lista de dominios web que hospedan las imágenes que está compartiendo la extensión de la aplicación de mensaje. Para cada dominio, especifique el `spotlight-image-search` service.
3. Agregar un `apple-app-site-association` archivo al sitio Web que hospeda las imágenes. Este archivo incluye un diccionario para el `spotlight-image-search` de servicio e incluye el identificador de la aplicación, que es el prefijo de identificador de equipo o aplicación seguido por el identificador de paquete. El archivo puede contener hasta 500 rutas de acceso y patrones que se indizado por servicios y se incluyen en las búsquedas de imagen populares. Para obtener más información, vea de Apple [crear y cargar el archivo de asociación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) documentación.
4. Permitir la Applebot rastrear los sitios Web. Vea de Apple [Applebot sobre](https://support.apple.com/HT204683) documentación.

Consulte nuestro [integración de aplicaciones de mensaje](~/ios/platform/message-app-integration/index.md) documentación para obtener más detalles.

## <a name="summary"></a>Resumen

En este artículo se trata las mejoras de Apple ha realizado en la búsqueda de la aplicación en iOS 10 y cómo implementarlas en Xamarin.iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
