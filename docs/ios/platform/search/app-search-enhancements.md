---
title: Mejoras en la búsqueda de la aplicación de Xamarin.iOS
description: En este artículo se trata las mejoras de Apple ha realizado en búsqueda de la aplicación en iOS 10 y cómo implementarlos en Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: e61cb20f12f6373ef57beb759b933d3dd9b6e76e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408140"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Mejoras en la búsqueda de la aplicación de Xamarin.iOS

_En este artículo se trata las mejoras de Apple ha realizado en búsqueda de la aplicación en iOS 10 y cómo implementarlos en Xamarin.iOS._

En iOS 10, Apple ha realizado varias mejoras en la búsqueda de la aplicación como vínculos profundos de Micromecenazgo, búsqueda de la aplicación, continuación de la búsqueda y visualización de los resultados de validación. En este artículo se tratará la implementación de estas características en una aplicación de Xamarin.iOS.

## <a name="about-app-search-enhancements"></a>Acerca de las mejoras de búsqueda de la aplicación

Core Spotlight en iOS 10 proporciona varias mejoras para la búsqueda de la aplicación, como:

- **Popularidad de vínculo profundo de Micromecenazgo (con privacidad diferencial)** -proporciona una manera para promover el contenido de la aplicación con vínculo profundo en resultados de búsqueda.
- **Buscar en la aplicación** -usar el nuevo `CSSearchQuery` clase para proporcionar capacidad de búsqueda de Spotlight en aplicación similar a cómo funcionan las aplicaciones de correo electrónico, mensajes y notas.
- **Buscar continuación** : permite que un usuario iniciar una búsqueda de Spotlight o Safari, a continuación, abrir una aplicación y continuar esa búsqueda.
- **Visualización de resultados de validación** -Apple [herramienta de validación de API de búsqueda de aplicación](https://search.developer.apple.com/appsearch-validation-tool) ahora muestra una representación visual de marcado de un sitio Web y vínculos profundos cuando realice las pruebas.
- **Aplicación de uso compartido de imágenes del mensaje** -permite imágenes populares de aplicación proporcionadas para el uso compartido de mensajes (a través de una extensión de la aplicación de mensaje) para que aparezca en las búsquedas de Spotlight.

Las secciones siguientes tratarán estos temas con más detalle.

## <a name="crowdsourced-deep-link-popularity"></a>Popularidad de vínculo profundo de Micromecenazgo

iOS 10 proporciona un mecanismo para contar la frecuencia que profundo: vínculos más populares en una aplicación van seguidos por el usuario y usa esta información para mejorar la clasificación de una aplicación de contenido en los resultados de búsqueda, mientras sigue protegiendo la identidad del usuario mediante el uso de  *Privacidad diferencial*.

Para la aplicación que utilizan `NSUserActivity` objetos para ofrecer las direcciones URL de vínculo profundo y el `EligibleForPublicIndexing` propiedad establecida en `true`, iOS 10 envía un subconjunto de *diferencial Hashes de privacidad* a servidores de Apple. Esta información, a continuación, se usa para promover popular contenido en la aplicación en los resultados de búsqueda.

Para obtener más información sobre cómo implementar vínculos profundos en una aplicación de Xamarin.iOS, consulte nuestra [búsqueda con NSUserActivity](~/ios/platform/search/nsuseractivity.md) documentación.

## <a name="in-app-searching"></a>Buscar en la aplicación

Al implementar el nuevo [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) (clase), puede proporcionar una aplicación de búsqueda y la tecnología de reglas de coincidencia para buscar contenido dentro de sí mismo, sin tener que salir de la aplicación (similar a cómo el correo electrónico, mensajes y notas de la aplicación que el usuario de Spotlight trabajo).

Normalmente, las aplicaciones que admiten `CSSearchQuery` no tendrá que mantener su propio índice de búsqueda independiente. 

## <a name="search-continuation"></a>Continuación de la búsqueda

En iOS 9, Apple introdujo las API de búsqueda (como contenido destacado de Core, `NSUserActivity` y marcado web) para proporcionar el gusto de profundo del contenido dentro de una aplicación para permitir a los usuarios buscar ese contenido con el foco y Safari interfaces de búsqueda. Consulte nuestra [nuevas API de búsqueda](~/ios/platform/search/index.md) documentación para obtener más detalles.

En iOS 10 Apple se basa en esta característica, ya que permite al usuario iniciar una búsqueda de Spotlight o Safari, a continuación, continuar la búsqueda cuando abran una aplicación. 

Para implementar esta característica, editar la aplicación `Info.plist` , agregue el `CoreSpotlightContinuation` clave de tipo **booleano** y establezca su valor en `YES`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Edición CoreSpotlightContinuation en el archivo Info.plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Edición CoreSpotlightContinuation en el archivo Info.plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Para responder al usuario continuar un resultado de búsqueda (`NSUserActivity`), editar la `AppDelegate.cs` archivo e invalidar la `ContinueUserActivity` método. Por ejemplo:

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

Este código busca el tipo de acción de continuación de consulta (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`), a continuación, lee la consulta del usuario actual desde el `NSUserActivity` diccionario de información de usuario de la clase (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`). Desde aquí, la aplicación debe tomar medidas para continuar la búsqueda del usuario.

Para obtener más información sobre cómo trabajar con las búsquedas en una aplicación de Xamarin.iOS, consulte nuestra [búsqueda de Spotlight Core](~/ios/platform/search/corespotlight.md) documentación.

## <a name="visualization-of-validation-results"></a>Visualización de resultados de validación

Apple [herramienta de validación de API de búsqueda de aplicación](https://search.developer.apple.com/appsearch-validation-tool) ahora muestra una representación visual de marcado de un sitio Web y vínculos profundos (incluido el marcado, como se define en [Schema.org](http://schema.org/)) cuando realice las pruebas.

Mediante el uso de la herramienta de validación, un programador podrá ver la información que el rastreador (crawler) Web de Applebot ha indizado para el sitio como título, descripción, dirección URL y cualquier otro admite los elementos.

Para obtener más información sobre cómo trabajar con marcado Web, consulte nuestra [buscar con marcado Web](~/ios/platform/search/web-markup.md) documentación.

## <a name="message-app-image-sharing"></a>Imagen de la aplicación de uso compartido del mensaje

Si una extensión de aplicación de mensajes proporciona imágenes para compartir en los mensajes, la extensión puede configurarse para permitir al usuario realizar búsquedas de Spotlight para imágenes populares desde dentro de los mensajes, sin tener que salir de la aplicación.

Para habilitar esta característica, haga lo siguiente:

1. Crear una extensión de aplicación de mensaje.
2. Agregue el `com.apple.developer.associated-domains` a los derechos de la aplicación e incluyen una lista de dominios web que hospedan las imágenes que se comparte la extensión de aplicación de mensajes. Para cada dominio, especifique el `spotlight-image-search` service.
3. Agregar un `apple-app-site-association` archivo al sitio Web que hospeda las imágenes. Este archivo incluye un diccionario para el `spotlight-image-search` de servicio e incluye el identificador de la aplicación, que es el prefijo de identificador de aplicación o Id. de equipo seguido por el identificador de lote. El archivo puede contener hasta 500 patrones que se Spotlight indexar y se incluyen en las búsquedas de imágenes populares y las rutas de acceso. Para obtener más información, consulte Apple [crear y cargar el archivo de asociación](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) documentación.
4. Permitir la Applebot rastrear los sitios Web. Consulte Apple [Applebot sobre](https://support.apple.com/HT204683) documentación.

Consulte nuestra [integración de aplicaciones de mensaje](~/ios/platform/message-app-integration/index.md) documentación para obtener más detalles.

## <a name="summary"></a>Resumen

Este artículo ha tratado las mejoras de Apple ha realizado en búsqueda de la aplicación en iOS 10 y cómo implementarlos en Xamarin.iOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
