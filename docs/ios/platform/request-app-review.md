---
title: Solicitar revisión de aplicación de Xamarin.iOS
description: En este artículo se describe el método RequestReview que Apple que se agregó a iOS 10 y se explica cómo se implementa en Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: f72aaa781b0712e206cf02725cfc434594287f41
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109788"
---
# <a name="request-app-review-in-xamarinios"></a>Solicitar revisión de aplicación de Xamarin.iOS

_Este artículo describe el método RequestReview que agregó a iOS 10 y cómo implementarlo en Xamarin.iOS de Apple._

Nuevo a iOS 10.3, el `RequestReview()` método permite que una aplicación de iOS pedir al usuario que calificar o lo revise. Cuando se llama a este método en una aplicación de trasvase de registros que el usuario ha instalado desde la aplicación de Store, iOS 10 controlará la clasificación de toda y revisar el proceso para el desarrollador. Dado que este proceso se rige por la directiva de App Store, una alerta puede o no pueden mostrarse.

![](request-app-review-images/review01.png "Una alerta de solicitud de revisión de ejemplo")

## <a name="requesting-a-rating-or-review"></a>Solicitar una clasificación o una revisión

Mientras el `RequestReview()` método estático de la `SKStoreReviewController` puede llamar a la clase y en cualquier momento donde tenga sentido en la experiencia del usuario, el proceso de revisión se rige controlado por la directiva de App Store. Como resultado, este método puede o no puede mostrar una alerta y nunca debe llamarse en respuesta a una acción del usuario, como pulsar un botón.

Por ejemplo, una aplicación puede solicitar una revisión después de haber sido inicia un número determinado de veces o un juego podría solicitar una revisión después de que el jugador acaba un nivel.

A las solicitudes de una revisión tan pronto como una aplicación de Xamarin.iOS acaben de iniciarse, realice los cambios siguientes en el `AppDelegate.cs` archivo:

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
> Una llamada a `RequestReview()` en un exceso de desarrollo de aplicación siempre mostrará la clasificación y revise el cuadro de diálogo por lo que se puede probar. Esto no es aplicable a las aplicaciones que se han distribuido a través de TestFlight, donde se pasará por alto la llamada al método.

Cuando el `RequestReview()` se llama al método en una aplicación de trasvase de registros que el usuario ha instalado desde la aplicación de Store, iOS 10 controlará todo el proceso de clasificación y revisión para el desarrollador. De nuevo, dado que este proceso se rige por la directiva de App Store, una alerta puede o no pueden mostrarse.

## <a name="linking-to-an-app-store-product-page"></a>Vincular a una página de producto de App Store 

Además de las nuevas `RequestReview` método, el programador todavía puede proporcionar un vínculo profundo a la página de producto de la aplicación en el Store de la aplicación desde dentro de una aplicación. Anexando `action=write-review` hasta el final de la dirección URL de página de producto, una página se abrirá donde el usuario puede escribir una reseña de la aplicación automáticamente. 

## <a name="summary"></a>Resumen

En este artículo ha cubierto el método RequestReview que agregó a iOS 10 y cómo implementarlo en Xamarin.iOS de Apple.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
