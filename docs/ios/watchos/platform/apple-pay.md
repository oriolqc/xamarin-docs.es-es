---
title: Apple Pay en watchOS en Xamarin
description: En este artículo se trata las mejoras de Apple ha realizado en Apple Pay en watchOS 3 y cómo implementarlos en Xamarin.iOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 354e03ee1e07ba99fcdeb05617bc65ed89f0e8c2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364189"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay en watchOS en Xamarin

Apple ha realizado varias mejoras en Apple Pay en watchOS 3 que agrega compatibilidad para los pagos en la aplicación. Esto permite al usuario proporcionar pago y póngase en contacto con información para pagar bienes físicos y servicios directamente desde el Apple Watch de forma segura.


## <a name="about-apple-pay-enhancements"></a>Acerca de las mejoras de Apple Pay

Como indicado anteriormente, Apple ha realizado varias mejoras en Apple Pay en watchOS 3 que permiten pago seguro y póngase en contacto con información para pagar bienes físicos y servicios directamente desde el Apple Watch. Estas mejoras son proporcionadas por las modificaciones en el marco de PassKit.

Con iOS 10 y watchOS 3, varias API nuevas se han agregado que funcionan con iOS y watchOS para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

## <a name="passkit-framework-enhancements"></a>Mejoras del marco de PassKit

En iOS 10, el marco de PassKit se ha ampliado para admitir Apple Pay fuera de `UIKit` y para permitir que los emisores de tarjeta presentar sus tarjetas desde dentro de sus aplicaciones. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Compatibilidad con Apple Pay fuera de UIKit

Mediante el uso de [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), una aplicación puede admitir la misma funcionalidad proporcionada por [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sin usar UIKit. Aunque esta nueva API es necesaria para admitir Apple Pay en el Apple Watch (e intenciones específicas también), es opcional en otras situaciones (por ejemplo, las aplicaciones existentes). Sin embargo, Apple sugiere lo mueve a la nueva API tan pronto como sea posible para proporcionar una amplia compatibilidad de Apple Pay a lo largo de todas las aplicaciones del desarrollador con un único código base. Para obtener más información acerca de las intenciones y la integración de Siri, consulte nuestra [Introducción a SiriKit](~/ios/platform/sirikit/index.md) documentación.

### <a name="presenting-issuer-cards-from-within-apps"></a>Presentación de las tarjetas de emisor desde dentro de las aplicaciones

Con iOS 10 y watchOS 3, se agregaron nuevas características para el marco de PassKit que permiten emisores de tarjeta presentar sus tarjetas de pago desde dentro de sus propias aplicaciones. El programador puede agregar un `PKPaymentButtonTypeInStore` UIButton para la interfaz de usuario de la aplicación que se mostrará un botón de Apple Pay para una tarjeta.

El `PresentPaymentPass` método de la [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) clase también puede usarse para mostrar mediante programación la tarjeta.

## <a name="new-payment-network-support"></a>Nueva compatibilidad de red de pago

Nuevo en iOS 10 y watchOS 3, una aplicación puede admiten automáticamente una nueva red de pago cuando esté disponible sin el desarrollador tenga que modificar, volver a compilar la aplicación y volver a enviarlo a Store de la aplicación.

El nuevo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) método de la `PKPaymentNetwork` clase permite que una aplicación detectar las redes disponibles en el dispositivo del usuario en tiempo de ejecución. Además, el [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propiedad se ha ampliado para tomar el nombre del proveedor de pago como argumento. Uso de estos métodos, una aplicación puede admitir automáticamente cualquier red que admite el proveedor de pago.

Para obtener más información, consulte nuestra [Apple pagar configuración](~/ios/platform/apple-pay.md) y Apple [Apple pagar guía](https://developer.apple.com/apple-pay/).

## <a name="new-testing-environment"></a>Nuevo entorno de prueba

Con iOS 10 y watchOS 3, Apple introdujo un nuevo entorno de prueba que permite al desarrollador aprovisionar las tarjetas de pago de prueba directamente en un dispositivo iOS. Este nuevo entorno de pruebas, a continuación, devuelve datos de prueba cifrada de pago a la aplicación.

Para habilitar el nuevo entorno de prueba, realice lo siguiente:

1. Cree una nueva cuenta de iCloud pruebas en iTunes Connect.
2. Inicie sesión en el dispositivo iOS con la nueva cuenta de prueba.
3. Establece la región deseada para probar la aplicación en.
4. Use una de las tarjetas de pago de prueba desde el [Apple pagar guía](https://developer.apple.com/apple-pay/) realizar pagos.

> [!NOTE]
> Al cambiar las cuentas de iCloud, el dispositivo le cambiará automáticamente al nuevo entorno de prueba. Sin embargo, todavía Apple **requiere** tarjetas de la aplicación va a probar con real en un entorno de producción antes de enviarla a iTunes App Store.

## <a name="summary"></a>Resumen

Este artículo ha tratado las mejoras de Apple ha realizado en Apple Pay en watchOS 3 y cómo implementarlos en Xamarin.iOS.
