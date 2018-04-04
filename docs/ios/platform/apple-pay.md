---
title: Apple Pay
description: Esta guía explora cómo configurar el entorno de Xamarin.iOS para su uso con Apple Pay para pagar por mercancías físicas, como comida, entretenimiento y pertenencias a grupos a través de la aplicación. Incluye información sobre los identificadores necesarios, los certificados y los derechos.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: fc7c247e5edcdc25d53c34c922801a5497b8c367
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="apple-pay"></a>Apple Pay

_Esta guía explora cómo configurar el entorno de Xamarin.iOS para su uso con Apple Pay para pagar por mercancías físicas, como comida, entretenimiento y pertenencias a grupos a través de la aplicación. Incluye información sobre los identificadores necesarios, los certificados y los derechos._


Apple Pay se introdujo junto con iOS 8, permitiendo a los usuarios pagar por mercancías físicas, como la comida, entretenimiento y pertenencias a grupos a través de sus dispositivos iOS. Está disponible en iPhone 6 e iPhone 6 Plus y también se pueden emparejar con la de Apple Watch para las compras en el almacén. Cuando se utiliza en un iPhone, utiliza Touch ID como un medio para confirmar y autorizar las transacciones para de un usuario tarjeta de crédito o débito.


## <a name="requirements"></a>Requisitos

Apple Pay solo está disponible en iOS 8 y versiones posteriores y, por tanto, requiere un mínimo de Xcode 6.

Los siguientes elementos también son necesarios para integrar Apple Pay en su aplicación:

 - Plataforma del procesador de pago
 - Identificador de comerciante
 - Un certificado de Apple Pay
 - Apple Pay derechos

Este documento veremos estos elementos con más detalle.

## <a name="differences-between-apple-pay-and-iap"></a>Diferencias entre el pago de Apple y así como

La diferencia principal entre Apple Pay y *en la aplicación de compra* (así como), pertenece a los productos que venden. *Físico* se venden mercancías a través de Apple Pay; comida, alojamiento y ocio físico (por ejemplo, vales de cine) son ejemplos de esto. En cambio, así como vende *virtuales* mercancías, como meses adicionales de suscripciones: reflexión de un servicio de transmisión por secuencias o vida adicional en un juego o contenido adicional y premium.

Los marcos de trabajo usan también son una diferencia clave; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) es usa para Apple Pay, mientras [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) proporciona la API del marco para así como.

Con Apple Pay, Apple [estados](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) que "[] no cobra usuarios, comerciantes o a los desarrolladores usar pago de Apple para pagos". En comparación, así como tiene un 30% de la carga para cada transacción. Además, con Apple Pay, la transacción no pasa a través de Apple en absoluto, en su lugar, se lleva a cabo de una plataforma de pago.


## <a name="using-a-payment-processor-platform"></a>Con una plataforma de procesador de pago

Una de las partes fundamentales de Apple Pay es el procesamiento de pagos. Aunque es posible hacerlo usted mismo, requiere un conocimiento significativo de criptografía
- tal como se detalla en de Apple [Guía de procesamiento de pago](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html).
Plataformas de procesamiento de pago, por otro lado, controlen estas operaciones para usted, lo que le permite concentrarse en la creación de la aplicación.

Incluyen dos opciones:

- **Bandas** -Regístrese en [Stripe.com](https://stripe.com/) para acceder a su API.

- **JudoPay** -desproteger sus [código de ejemplo de Xamarin en github](https://github.com/Judopay/Xamarin-Sample-App)y se registra en [JudoPay.com](https://www.judopay.com/).


## <a name="provisioning-for-apple-pay"></a>Aprovisionamiento de pago de Apple

Configurar una aplicación para que use Apple Pay requiere el programa de instalación en el Portal para desarrolladores de Apple y dentro de la aplicación. Hay una serie de pasos que deben seguirse para aprovisionar correctamente la aplicación para el pago de Apple:

1. Crear un Id. de comerciante:
    - Siga los pasos [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Crear un identificador de aplicación con la capacidad de aplicar pagar y agregarle al comerciante:
    - Siga los pasos [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Generar un certificado para el Id. de comerciante:
    - Siga los pasos [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Generar un perfil de aprovisionamiento con el identificador de aplicación recién creado:
    - Siga los pasos [aquí](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Agregue derechos de Apple Pay:
    - Seleccione los derechos de pago de Apple, tal como se detalla [aquí](~/ios/deploy-test/provisioning/entitlements.md), o agregar manualmente el par clave/valor del archivo de [aquí](~/ios/deploy-test/provisioning/entitlements.md)


## <a name="working-with-apple-pay"></a>Trabajar con la opción de pago de Apple

Apple ha realizado varias mejoras en Apple Pay en iOS 10 que permiten al usuario realizar pagos seguros de sitios Web y a través de la interacción con Siri y mapas.

Con iOS 10, varias nuevas API se han agregado que funcionan con iOS y watchOS para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.


### <a name="apple-pay-website-integration"></a>Integración de sitio Web de pago de Apple

Nuevo en iOS 10, el desarrollador puede incorporar Apple Pay directamente en sus sitios Web usando **ApplePay JS**. Los usuarios que exploren el sitio Web con Safari de iOS o Mac OS pueden realizar pagos con Apple Pay mediante la validación de la transacción en su iPhone o Apple Watch. Para obtener más información, vea de Apple [ApplePay JP Framework Reference](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Mejoras de PassKit Framework

En iOS 10, el marco de trabajo PassKit se ha ampliado para admitir Apple Pay fuera de `UIKit` y deben permitir los emisores de tarjeta presentar sus propias tarjetas desde dentro de sus aplicaciones.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Compatibilidad con Apple pago fuera UIKit

Mediante el uso de [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), una aplicación puede admitir la misma funcionalidad proporcionada por [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sin usar UIKit. Cuando esta nueva API es necesaria para admitir Apple Pay en el de Apple Watch (y así propósitos específicos), es opcional en otras situaciones (por ejemplo, las aplicaciones existentes). Sin embargo, Apple sugiere mover a la nueva API tan pronto como sea posible para proporcionar una amplia compatibilidad con Apple Pay a lo largo de las aplicaciones del desarrollador de con un único código base. Para obtener más información acerca del color y la integración de Siri, visite nuestro [Introducción a SiriKit](~/ios/platform/sirikit/index.md) documentación.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Presentación de tarjetas de emisor desde dentro de las aplicaciones

Con iOS 10, se han agregado características nuevas para el marco de trabajo de PassKit que permiten emisores de tarjeta presentar sus tarjetas desde dentro de sus propias aplicaciones. El programador puede agregar un `PKPaymentButtonTypeInStore` UIButton a la interfaz de usuario de la aplicación que se mostrará un botón de Apple Pay para una tarjeta.

El `PresentPaymentPass` método de la [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) clase también puede usarse para mostrar mediante programación la tarjeta.

### <a name="new-payment-network-support"></a>Nueva compatibilidad para red de pago

Nuevo en 10 de iOS, una aplicación puede admiten automáticamente una nueva red de pago cuando esté disponible sin el desarrollador tenga que modificar, volver a compilar la aplicación y reenviarlo a la tienda de aplicaciones.

El nuevo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) método de la `PKPaymentNetwork` clase permite que una aplicación detectar las redes disponibles en el dispositivo del usuario en tiempo de ejecución. Además, el [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propiedad se ha ampliado para aprovechar el nombre del proveedor de pago como argumento. Uso de estos métodos, una aplicación puede admitir automáticamente cualquier red que admite el proveedor de pago.

Para obtener más información, vea nuestra [configuración de Apple pagar](~/ios/platform/apple-pay.md) y de Apple [Guía de Apple pagar](https://developer.apple.com/apple-pay/).

### <a name="new-testing-environment"></a>Nuevo entorno de prueba

Con iOS 10, Apple incorporó un nuevo entorno de prueba que permite al desarrollador aprovisionar las tarjetas de pago de prueba directamente en un dispositivo iOS. Este nuevo entorno de prueba, a continuación, devuelve los datos de pago de prueba cifrada a la aplicación.

Para habilitar el nuevo entorno de prueba, haga lo siguiente:

1. Crear una nueva cuenta de iCloud prueba en iTunes Connect.
2. Inicie sesión en el dispositivo iOS con la nueva cuenta de prueba.
3. Establece la región deseada para probar la aplicación en.
4. Use una de las tarjetas de pago de prueba desde el [Guía de Apple pagar](https://developer.apple.com/apple-pay/) para efectuar los pagos.

> [!IMPORTANT]
> Al cambiar las cuentas de iCloud, el dispositivo cambiará automáticamente al nuevo entorno de prueba. Sin embargo, todavía Apple **requiere** tarjetas de la aplicación se va a probar con real en un entorno de producción antes de enviarla a iTunes App Store.

## <a name="summary"></a>Resumen

En este artículo, analizamos los diferentes elementos necesarios para usar Apple Pay dentro de la aplicación. Analizamos cómo crear un Id. de comerciante y cómo se utiliza dentro de la **Entitlements.plist**, que es necesario modificar manualmente.


## <a name="related-links"></a>Vínculos relacionados

- [Compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md)
- [Introducción a PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
