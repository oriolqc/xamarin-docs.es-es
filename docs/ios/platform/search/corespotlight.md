---
title: Búsqueda de Spotlight Core
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 0074cfcdc4a80f66f64d12f8e5c0a5e81ab2b8a1
ms.sourcegitcommit: c9ebf456e1c6924956bedb13f4ea78ff09f7b1a0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
---
# <a name="search-with-core-spotlight"></a>Búsqueda de Spotlight Core

Noticias destacadas de núcleo es un nuevo marco de iOS 9 que presenta una API de base de datos similares para agregar, editar o eliminar los vínculos a contenido dentro de la aplicación. Elementos que se han agregado mediante servicios de núcleo estarán disponibles en la búsqueda de Spotlight en el dispositivo iOS.

Para obtener un ejemplo de los tipos de contenido que se pueden indizar mediante servicios de núcleo, examine los mensajes de Apple, correo electrónico, calendario y notas de aplicaciones. Todas las utilizan actualmente servicios principales para proporcionar los resultados de la búsqueda.

## <a name="creating-an-item"></a>Creación de un elemento

El siguiente es un ejemplo de creación de un elemento e indización mediante servicios principales:

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Esta información será similar al siguiente en un resultado de búsqueda siguiente:

[![](corespotlight-images/corespotlight01.png "Información general sobre el resultado de búsqueda de Spotlight de principales")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Si restaura un elemento

Cuando el usuario puntea en un elemento se agrega al resultado de la búsqueda a través de servicios principales de la aplicación, el `AppDelegate` método `ContinueUserActivity` se denomina (este método también se utiliza para `NSUserActivity`). Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

Tenga en cuenta que esta vez se busca la actividad que tenga un `ActivityType` de `CSSearchableItem.ActionType`.

## <a name="updating-an-item"></a>Actualizar un elemento

Puede haber ocasiones en un elemento de índice se crea con los servicios principales deba modificarse, como se requiere un cambio en el título o la imagen en miniatura. Para realizar este cambio, usamos el mismo método que se utilizó para crear inicialmente el índice.
Creamos un nuevo `CSSearchableItem` con el mismo identificador que se utilizó para crear el elemento y adjuntar un nuevo `CSSearchableItemAttributeSet` que contiene los atributos modificados:

[![](corespotlight-images/corespotlight02.png "Un elemento de información general sobre la actualización")](corespotlight-images/corespotlight02.png#lightbox)

Cuando este elemento se escribe en el índice de búsqueda, el elemento de trabajo se actualiza con la nueva información.

## <a name="deleting-an-item"></a>Eliminar un elemento

Servicios principales proporciona varias maneras de eliminar un elemento de índice cuando ya no sea necesario.

En primer lugar, puede eliminar un elemento por su identificador, por ejemplo:

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

A continuación, puede eliminar un grupo de elementos de índice por su nombre de dominio. Por ejemplo:

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Por último, puede eliminar todos los elementos de índice con el código siguiente:

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```
## <a name="additional-core-spotlight-features"></a>Características de servicios de núcleo adicionales

Noticias destacadas de núcleo tiene las siguientes características que ayudan a mantener el índice precisa y actualizada:

- **Compatibilidad de actualización de lote** : si la aplicación necesita para crear o modificar un grupo grande de índices al mismo tiempo, el lote completo puede enviarse a la `Index` método de la `CSSearchableIndex` clase en una llamada.
- **Responder a cambios en el índice** : con la `CSSearchableIndexDelegate` la aplicación puede responder a los cambios y las notificaciones desde el índice de búsqueda.
- **Aplicar la protección de datos** – usando las clases de protección de datos, puede implementar la seguridad en los elementos que agregar al índice permite realizar búsquedo con Spotlight Core.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de la aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
