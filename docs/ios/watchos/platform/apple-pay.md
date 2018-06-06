---
title: Apple Pay en watchOS en Xamarin
description: Este artículo tratan las mejoras de Apple ha realizado en Apple Pay en watchOS 3 y cómo implementarlas en Xamarin.iOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 75d660ad0699b6fac3b1ae43046f322f380872b3
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791080"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay en watchOS en Xamarin

Apple ha realizado varias mejoras en Apple Pay en watchOS 3 que agrega compatibilidad para los pagos de la aplicación. Esto permite al usuario proporcionar pago y póngase en contacto con información para pagar por bienes físicos y servicios directamente desde el Apple Watch de forma segura.


## <a name="about-apple-pay-enhancements"></a>Acerca de las mejoras de pago de Apple

Como indicado anteriormente, Apple ha realizado varias mejoras en Apple Pay en watchOS 3 que permiten de pago seguro y póngase en contacto con información para pagar por bienes físicos y servicios directamente desde el Apple Watch. Estas mejoras se proporcionan con las modificaciones realizadas en el marco de trabajo PassKit.

Con iOS 10 y watchOS 3, varias nuevas API se han agregado que funcionan con iOS y watchOS para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

## <a name="passkit-framework-enhancements"></a>Mejoras de PassKit Framework

En iOS 10, el marco de trabajo PassKit se ha ampliado para admitir Apple Pay fuera de `UIKit` y deben permitir los emisores de tarjeta presentar sus tarjetas desde dentro de sus aplicaciones. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Compatibilidad con Apple pago fuera UIKit

Mediante el uso de [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), una aplicación puede admitir la misma funcionalidad proporcionada por [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sin usar UIKit. Cuando esta nueva API es necesaria para admitir Apple Pay en el de Apple Watch (y así propósitos específicos), es opcional en otras situaciones (por ejemplo, las aplicaciones existentes). Sin embargo, Apple sugiere mover a la nueva API tan pronto como sea posible para proporcionar una amplia compatibilidad con Apple Pay a lo largo de las aplicaciones del desarrollador de con un único código base. Para obtener más información acerca del color y la integración de Siri, visite nuestro [Introducción a SiriKit](~/ios/platform/sirikit/index.md) documentación.

### <a name="presenting-issuer-cards-from-within-apps"></a>Presentación de tarjetas de emisor desde dentro de las aplicaciones

Con iOS 10 y watchOS 3, se han agregado características nuevas para el marco de trabajo de PassKit que permiten emisores de tarjeta presentar sus tarjetas de pago desde dentro de sus propias aplicaciones. El programador puede agregar un `PKPaymentButtonTypeInStore` UIButton a la interfaz de usuario de la aplicación que se mostrará un botón de Apple Pay para una tarjeta.

El `PresentPaymentPass` método de la [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) clase también puede usarse para mostrar mediante programación la tarjeta.

## <a name="new-payment-network-support"></a>Nueva compatibilidad para red de pago

Nuevo iOS 10 y watchOS 3, una aplicación puede admiten automáticamente una nueva red de pago cuando esté disponible sin el desarrollador tenga que modificar, volver a compilar la aplicación y reenviarlo a la tienda de aplicaciones.

El nuevo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) método de la `PKPaymentNetwork` clase permite que una aplicación detectar las redes disponibles en el dispositivo del usuario en tiempo de ejecución. Además, el [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propiedad se ha ampliado para aprovechar el nombre del proveedor de pago como argumento. Uso de estos métodos, una aplicación puede admitir automáticamente cualquier red que admite el proveedor de pago.

Para obtener más información, vea nuestra [configuración de Apple pagar](~/ios/platform/apple-pay.md) y de Apple [Guía de Apple pagar](https://developer.apple.com/apple-pay/).

## <a name="new-testing-environment"></a>Nuevo entorno de prueba

Con iOS 10 y watchOS 3, Apple incorporó un nuevo entorno de prueba que permite al desarrollador aprovisionar las tarjetas de pago de prueba directamente en un dispositivo iOS. Este nuevo entorno de prueba, a continuación, devuelve los datos de pago de prueba cifrada a la aplicación.

Para habilitar el nuevo entorno de prueba, haga lo siguiente:

1. Crear una nueva cuenta de iCloud prueba en iTunes Connect.
2. Inicie sesión en el dispositivo iOS con la nueva cuenta de prueba.
3. Establece la región deseada para probar la aplicación en.
4. Use una de las tarjetas de pago de prueba desde el [Guía de Apple pagar](https://developer.apple.com/apple-pay/) para efectuar los pagos.

> [!NOTE]
> Al cambiar las cuentas de iCloud, el dispositivo cambiará automáticamente al nuevo entorno de prueba. Sin embargo, todavía Apple **requiere** tarjetas de la aplicación se va a probar con real en un entorno de producción antes de enviarla a iTunes App Store.

## <a name="summary"></a>Resumen

En este artículo se trata las mejoras de Apple ha realizado en Apple Pay en watchOS 3 y cómo implementarlas en Xamarin.iOS.
