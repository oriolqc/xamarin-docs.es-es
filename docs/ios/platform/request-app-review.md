---
title: "Solicitar revisión de aplicación"
description: "En este artículo se trata el método RequestReview que agrega al 10 de iOS y cómo implementarla en Xamarin.iOS de Apple."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 2e63f2c47bbcd6da0f0d5370ebfc231d19a10e7d
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="request-app-review"></a>Solicitar revisión de aplicación

_En este artículo se trata el método RequestReview que agrega al 10 de iOS y cómo implementarla en Xamarin.iOS de Apple._

Nuevo en iOS 10.3 la `RequestReview()` método permite que una aplicación iOS pedir al usuario que calificar o revisarlo. Cuando se llama a este método en una aplicación de envío que el usuario ha instalado desde la tienda de aplicaciones, iOS 10 controlará la clasificación de toda y proceso de revisión para el programador. Dado que este proceso se rige por una directiva de almacén de la aplicación, una alerta puede o no puede mostrarse.

![](request-app-review-images/review01.png "Una alerta de solicitud de revisión de ejemplo")

## <a name="requesting-a-rating-or-review"></a>Solicitar una clasificación o una revisión

Mientras el `RequestReview()` método estático de la `SKStoreReviewController` puede llamar a la clase en cualquier punto donde tiene sentido en la experiencia del usuario, el proceso de revisión rige y controlado por una directiva de almacén de aplicación. Como resultado, este método puede o no puede mostrar una alerta y nunca se debería llamar en respuesta a una acción del usuario, como pulsar un botón.

Por ejemplo, una aplicación puede solicitar una revisión de una vez había iniciado un número determinado de veces o un juego puede solicitar una revisión, el Reproductor al finalizar un nivel.

A las solicitudes de una revisión en cuanto finaliza de una aplicación de Xamarin.iOS iniciar, realice los cambios siguientes en el `AppDelegate.cs` archivo:

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }
        
        ...
        
    }
}
```

> [!NOTE]
> Al llamar a `RequestReview()` en un desarrollo debajo del aplicación siempre muestra la clasificación y revise el cuadro de diálogo para que se pueda probar. Esto no se aplica a las aplicaciones que se han distribuido a través de TestFlight, donde se pasará por alto la llamada al método.

Cuando el `RequestReview()` método se llama en una aplicación de envío que el usuario ha instalado desde la tienda de aplicaciones, iOS 10 controlará todo el proceso de revisión y la clasificación para el desarrollador. De nuevo, porque este proceso se rige por una directiva de almacén de la aplicación, una alerta puede o no puede mostrarse.

## <a name="linking-to-an-app-store-product-page"></a>Vincular a una página de producto de la tienda de aplicaciones 

Además de las nuevas `RequestReview` método, el desarrollador puede proporcionar un vínculo profundo de la página de producto de la aplicación en la tienda de aplicaciones desde dentro de una aplicación. Mediante la anexión de `action=write-review` hasta el final de la URL de la página de producto, una página se abrirán donde el usuario puede escribir una reseña de la aplicación automáticamente. 

## <a name="summary"></a>Resumen

En este artículo ha cubierto el método RequestReview que agrega al 10 de iOS y cómo implementarla en Xamarin.iOS de Apple.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
