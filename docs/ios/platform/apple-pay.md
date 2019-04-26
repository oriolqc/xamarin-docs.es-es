---
title: Apple Pay en Xamarin.iOS
description: Esta guía analiza cómo configurar el entorno de Xamarin.iOS para su uso con Apple Pay pueden pagar bienes físicos, como los alimentos, entretenimiento y las pertenencias a través de la aplicación. Incluye información sobre los identificadores necesarios, certificados y los derechos.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: b971029ff3b2b1e8f5e63233d1d754c44b0e3309
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346980"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay en Xamarin.iOS

_Esta guía analiza cómo configurar el entorno de Xamarin.iOS para su uso con Apple Pay pueden pagar bienes físicos, como los alimentos, entretenimiento y las pertenencias a través de la aplicación. Incluye información sobre los identificadores necesarios, certificados y los derechos._

Apple Pay se introdujo junto con iOS 8, permitiendo a los usuarios pueden pagar bienes físicos como alimentos, entretenimiento y las pertenencias a través de sus dispositivos iOS. Está disponible en iPhone 6 y iPhone 6 Plus y también se pueden emparejar con el Apple Watch para compras en la tienda. Cuando se utiliza en un iPhone, usa Touch ID como una manera de confirmar y autorizar a las transacciones para de un usuario tarjeta de crédito o débito.

## <a name="requirements"></a>Requisitos

Apple Pay solo está disponible en iOS 8 y versiones posteriores y por lo tanto, requiere un mínimo de Xcode 6.

Los siguientes elementos también son necesarios para integrar Apple Pay en su aplicación:

 - Plataforma de procesador de pago
 - Identificador de comerciante
 - Un certificado de Apple Pay
 - Derecho de Apple Pay

Este documento veremos estos elementos con más detalle.

## <a name="differences-between-apple-pay-and-iap"></a>Diferencias entre Apple Pay y IAP

La diferencia principal entre Apple Pay y *compras* (IAP), se refiere a los productos que venden. *Física* bienes se venden a través de Apple Pay; alimentos, alojamiento y ocio físico (por ejemplo, los vales de cine) son ejemplos de esto. En cambio, vende IAP *virtual* bienes, como meses adicionales de suscripciones: de reflexión de un servicio de transmisión o adicional reside en un juego o contenido adicional y premium.

Los marcos de trabajo usa también son una diferencia clave; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) es usa para Apple Pay, mientras [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) proporciona el marco API para IAP.

Con Apple Pay, Apple [estados](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) [que lo "no] cobra a los usuarios, los comerciantes o a los desarrolladores usar Apple Pay para pagos". En comparación, IAP tiene un cargo de un 30% para cada transacción. Además, con Apple Pay, la transacción no pasa a través de Apple en absoluto, en su lugar, pasa a través de una plataforma de pago.

## <a name="using-a-payment-processor-platform"></a>Uso de una plataforma de procesador de pago

Uno de los componentes fundamentales de Apple Pay es el procesamiento de pagos. Aunque es posible hacerlo usted mismo, requiere un conocimiento significativo de criptografía
- tal como se detalla en Apple [Guía del procesamiento de pagos](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html).
Plataformas de procesamiento de pagos, por otro lado, controlen estas operaciones para usted, lo que le permite concentrarse en la creación de la aplicación.

Incluyen dos opciones:

- **Bandas** -Regístrese en [Stripe.com](https://stripe.com/) para acceder a su API.

- **JudoPay** -consulte sus [código de ejemplo de Xamarin en github](https://github.com/Judopay/Xamarin-Sample-App)y regístrese en [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>Aprovisionamiento de Apple Pay

Configuración de una aplicación para usar Apple Pay requiere una configuración en el Portal para desarrolladores de Apple y dentro de la aplicación. Hay una serie de pasos que se deben seguir para aprovisionar correctamente su aplicación para Apple pay:

1. Crear un identificador de comerciante:
    - Siga los pasos [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Cree un identificador de aplicación con la capacidad de aplicar a pagar y agregue el identificador de comerciante a él:
    - Siga los pasos [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Generar un certificado para el identificador de comerciante:
    - Siga los pasos [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Generar un perfil de aprovisionamiento con el identificador de aplicación recién creado:
    - Siga los pasos [aquí](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Agregar el derecho de Apple Pay:
    - Seleccione los derechos de pago de Apple, tal como se detalla [aquí](~/ios/deploy-test/provisioning/entitlements.md), o agregar manualmente el par clave/valor en el archivo de [aquí](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>Trabajar con Apple Pay

Apple ha realizado varias mejoras en Apple Pay en iOS 10 que permiten al usuario realizar pagos seguros desde sitios Web y mediante la interacción con Siri y mapas.

Con iOS 10, varias API nuevas se han agregado que funcionan con iOS y watchOS para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

### <a name="apple-pay-website-integration"></a>Integración del sitio Web de Apple Pay

Nuevo en iOS 10, el desarrollador puede incorporar Apple Pay directamente en sus sitios Web con **ApplePay JS**. Los usuarios examinan el sitio Web con Safari en iOS o macOS para hacer los pagos con Apple Pay mediante la validación de la transacción en su iPhone o Apple Watch. Para obtener más información, consulte Apple [ApplePay JP Framework referencia](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Mejoras del marco de PassKit

En iOS 10, el marco de PassKit se ha ampliado para admitir Apple Pay fuera de `UIKit` y para permitir que los emisores de tarjeta presentar sus propias tarjetas desde dentro de sus aplicaciones.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Compatibilidad con Apple Pay fuera de UIKit

Mediante el uso de [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), una aplicación puede admitir la misma funcionalidad proporcionada por [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sin usar UIKit. Aunque esta nueva API es necesaria para admitir Apple Pay en el Apple Watch (e intenciones específicas también), es opcional en otras situaciones (por ejemplo, las aplicaciones existentes). Sin embargo, Apple sugiere lo mueve a la nueva API tan pronto como sea posible para proporcionar una amplia compatibilidad de Apple Pay a lo largo de todas las aplicaciones del desarrollador con un único código base. Para obtener más información acerca de las intenciones y la integración de Siri, consulte nuestra [Introducción a SiriKit](~/ios/platform/sirikit/index.md) documentación.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Presentación de las tarjetas de emisor desde dentro de las aplicaciones

Con iOS 10, se agregaron nuevas características para el marco de PassKit que permiten emisores de tarjeta presentar sus tarjetas desde dentro de sus propias aplicaciones. El programador puede agregar un `PKPaymentButtonTypeInStore` UIButton para la interfaz de usuario de la aplicación que se mostrará un botón de Apple Pay para una tarjeta.

El `PresentPaymentPass` método de la [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) clase también puede usarse para mostrar mediante programación la tarjeta.

### <a name="new-payment-network-support"></a>Nueva compatibilidad de red de pago

Nuevo en iOS 10, una aplicación puede admiten automáticamente una nueva red de pago cuando esté disponible sin el desarrollador tenga que modificar, volver a compilar la aplicación y volver a enviarlo a Store de la aplicación.

El nuevo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) método de la `PKPaymentNetwork` clase permite que una aplicación detectar las redes disponibles en el dispositivo del usuario en tiempo de ejecución. Además, el [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propiedad se ha ampliado para tomar el nombre del proveedor de pago como argumento. Uso de estos métodos, una aplicación puede admitir automáticamente cualquier red que admite el proveedor de pago.

Para obtener más información, consulte nuestra [Apple pagar configuración](~/ios/platform/apple-pay.md) y Apple [Apple pagar guía](https://developer.apple.com/apple-pay/).

### <a name="new-testing-environment"></a>Nuevo entorno de prueba

Con iOS 10, Apple introdujo un nuevo entorno de prueba que permite al desarrollador aprovisionar las tarjetas de pago de prueba directamente en un dispositivo iOS. Este nuevo entorno de pruebas, a continuación, devuelve datos de prueba cifrada de pago a la aplicación.

Para habilitar el nuevo entorno de prueba, realice lo siguiente:

1. Cree una nueva cuenta de iCloud pruebas en iTunes Connect.
2. Inicie sesión en el dispositivo iOS con la nueva cuenta de prueba.
3. Establece la región deseada para probar la aplicación en.
4. Use una de las tarjetas de pago de prueba desde el [Apple pagar guía](https://developer.apple.com/apple-pay/) realizar pagos.

> [!IMPORTANT]
> Al cambiar las cuentas de iCloud, el dispositivo le cambiará automáticamente al nuevo entorno de prueba. Sin embargo, todavía Apple **requiere** tarjetas de la aplicación va a probar con real en un entorno de producción antes de enviarla a iTunes App Store.

## <a name="summary"></a>Resumen

En este artículo, hemos explorado los diferentes elementos necesarios para usar Apple Pay dentro de la aplicación. Hemos visto cómo crear un identificador de comerciante y cómo se utiliza dentro de la **Entitlements.plist**, que debe modificarse manualmente.

## <a name="related-links"></a>Vínculos relacionados

- [Compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md)
- [Introducción a PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
