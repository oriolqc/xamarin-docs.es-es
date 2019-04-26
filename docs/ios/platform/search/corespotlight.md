---
title: Búsqueda de Spotlight de núcleo en Xamarin.iOS
description: Este documento describe cómo usar Core Spotlight en una aplicación de Xamarin.iOS para proporcionar vínculos a contenido de la aplicación. Describe cómo crear, restaurar, actualizar y eliminar elementos que se pueden buscar.
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: fb9ddcc39bd33199dc370897250cd0d74597612f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248490"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>Búsqueda de Spotlight de núcleo en Xamarin.iOS

Contenido destacado de Core es un nuevo marco de iOS 9 que presenta una API de tipo base de datos para agregar, editar o eliminar vínculos a contenido dentro de la aplicación. Los elementos que se han agregado mediante Spotlight Core estará disponibles en la búsqueda de Spotlight en el dispositivo iOS.

Para obtener un ejemplo de los tipos de contenido que se pueden indizar mediante Spotlight de Core, examine los mensajes de Apple, correo electrónico, calendario y las notas de la aplicaciones. Todas las utilizan actualmente Spotlight Core para proporcionar los resultados de búsqueda.

## <a name="creating-an-item"></a>Creación de un elemento

Este es un ejemplo de creación de un elemento y cree un índice mediante Spotlight Core:

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

Esta información tendría un aspecto similar al siguiente en un resultado de búsqueda:

[![](corespotlight-images/corespotlight01.png "Principales de información general sobre el resultado de búsqueda de Spotlight")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Restaurar un elemento

Cuando el usuario puntea un elemento agregado al resultado de la búsqueda a través de contenido destacado de núcleo para la aplicación, el `AppDelegate` método `ContinueUserActivity` se denomina (este método también se usa para `NSUserActivity`). Por ejemplo:

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

Tenga en cuenta que esta vez estamos comprobación para la actividad que tenga un `ActivityType` de `CSSearchableItem.ActionType`.

## <a name="updating-an-item"></a>Actualizar un elemento

Puede haber ocasiones en un elemento de índice se ha creado con Core Spotlight deba modificarse, como se requiere un cambio en el título o la imagen en miniatura. Para realizar este cambio, usamos el mismo método que utilizó para crear inicialmente el índice.
Creamos un nuevo `CSSearchableItem` con el mismo identificador que utilizó para crear el elemento y conecte un nuevo `CSSearchableItemAttributeSet` que contiene los atributos modificados:

[![](corespotlight-images/corespotlight02.png "Un elemento de información general sobre la actualización")](corespotlight-images/corespotlight02.png#lightbox)

Cuando este elemento se escribe en el índice de búsqueda, el elemento existente se actualiza con la nueva información.

## <a name="deleting-an-item"></a>Eliminación de un elemento

Contenido destacado de Core proporciona varias maneras de eliminar un elemento de índice cuando ya no sea necesario.

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
## <a name="additional-core-spotlight-features"></a>Características adicionales principales del contenido destacado

Contenido destacado de Core tiene las siguientes características que ayudan a mantener el índice precisa y actualizada:

- **Compatibilidad con la actualización de batch** : si la aplicación necesita crear o modificar un grupo grande de índices al mismo tiempo, todo el lote se puede enviar a la `Index` método de la `CSSearchableIndex` clase en una llamada.
- **Responder a los cambios de índice** : con la `CSSearchableIndexDelegate` la aplicación puede responder a los cambios y las notificaciones desde el índice de búsqueda.
- **Aplicar la protección de datos** – utilizando las clases de protección de datos, puede implementar la seguridad en los elementos que agregar al índice que se puede buscar mediante Spotlight Core.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de la aplicación](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
