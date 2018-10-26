---
title: Las suscripciones y los informes en Xamarin.iOS
description: Este documento describe no renovar las suscripciones, las suscripciones gratuitas, suscripciones puede renovar automáticamente y mediante iTunes Connect para informar sobre estos elementos.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4e63894cb862db3b5b5a1e7a2bebd79160c311a9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121235"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Las suscripciones y los informes en Xamarin.iOS

## <a name="about-non-renewing-subscriptions"></a>Acerca de las suscripciones que no sean renovar

No renuevan las suscripciones están pensadas para productos que representan la venta de un servicio con una restricción de tiempo como (acceso de una semana a una aplicación de navegación) o acceso de tiempo limitado a un archivo de datos.   
   
Diferencias clave entre no renovar las suscripciones y otros tipos de producto:

-  La definición del producto en iTunes Connect no incluye el término. El código de aplicación debe ser capaz de inferir el período de validez desde el identificador de producto. 
-  Se pueden adquirir varias veces (por ejemplo, un producto consumible). Las aplicaciones deben administrar la renovación y expiración o término de suscripción y evitar que el usuario compra de suscripciones que se superponen. 
-  No se admiten las compras realizadas por la función StoreKit restaurar. Si la suscripción debe estar disponible a través de todos los dispositivos del usuario, la aplicación tendrá que diseñar e implementar esta característica, junto con un servidor remoto. Las aplicaciones también son responsables de realizar copias de seguridad del estado de la suscripción para los casos cuando un dispositivo se copia de seguridad, a continuación, puede restaurar desde-copia de seguridad. 
-  Información general de implementación
-  Normalmente se debe implementar no renuevan las suscripciones mediante el flujo de trabajo entregado por el servidor y administrados, como productos consumibles. 


## <a name="about-free-subscriptions"></a>Acerca de las suscripciones gratuitas

Las suscripciones gratuitas permiten a los desarrolladores colocar el contenido disponible en las aplicaciones de quiosco (no pueden utilizarse en aplicaciones que no sean Newsstand). Una vez que se inicia una suscripción gratuita estará disponible en todos los dispositivos del usuario. Las suscripciones gratuitas no expiren nunca; éstas terminen solo cuando se desinstala la aplicación.

### <a name="implementation-overview"></a>Información general de implementación

Suscripciones gratuitas tienen un comportamiento muy similar a las suscripciones puede renovar automáticamente. La aplicación debe tener un producto de suscripción gratuita disponible para "purchase" en iTunes Connect. Cuando se adquieren por el usuario, se debe validar la compra de suscripción gratuita como un producto de suscripción puede renovar automáticamente. Las transacciones de la suscripción gratuita se pueden restaurar.


## <a name="about-auto-renewable-subscriptions"></a>Acerca de las suscripciones automáticamente renovable

Puede renovar automáticamente suscripciones se utilizan principalmente en aplicaciones de quiosco. Representan un producto que concede al usuario acceso a contenido dinámico para un determinado período de tiempo, que está configurada en iTunes Connect (establecer períodos que van de 7 días a 1 año). Las suscripciones se renuevan automáticamente, cargando a los usuarios de Id. de Apple al final de cada período de suscripción, a menos que el usuario no participa en horizontal. Este tipo de producto funciona bien para las suscripciones de revista o noticias, donde el usuario obtiene acceso a cada problema publicado mientras su suscripción es válida.

### <a name="implementation-overview"></a>Información general de implementación

Puede renovar automáticamente suscripciones deben implementarse mediante el flujo de trabajo de productos Server-Delivered (hacen referencia a la *comprobación de recepción y productos Server-Delivered* sección).

#### <a name="shared-secret"></a>Secreto compartido

El secreto compartido de compra de en la aplicación debe usarse en la solicitud JSON al comprobar las suscripciones puede renovar automáticamente en el servidor. El secreto compartido es crear o acceder a través de iTunes Connect.

De iTunes Connect página principal seleccione **mis aplicaciones**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Selección de Mis aplicaciones")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
Seleccione una aplicación y haga clic en el **compras de la aplicación** pestaña:

[![](subscriptions-and-reporting-images/image6.png "Haga clic en la pestaña de compras en la aplicación")](subscriptions-and-reporting-images/image6.png#lightbox)

En la parte inferior de la página, seleccione **vista o generar un secreto compartido**:
   
 [![](subscriptions-and-reporting-images/image40.png "Seleccione la vista o generar un secreto compartido")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Generar un secreto compartido")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 Para usar el secreto compartido, incluirlo en la carga de JSON que se envía a los servidores de Apple al validar un recibo de compra en la aplicación para una suscripción puede renovar automáticamente, similar al siguiente:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Campo de estado de la respuesta será cero si la compra es válida, como con otros tipos de producto.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Descarga de elementos tras el período de suscripción inicial

Como parte de la entrega de productos de suscripción, el código debe comprobar con frecuencia la recepción conocida más reciente contra servidores de Apple. Si una suscripción se renueva automáticamente desde la última comprobación, la respuesta JSON contendrá campos adicionales que se notifique a la aplicación de la transacción que se ha producido (cuál debería extender la validez de las suscripciones). La respuesta JSON contendrá:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Si el estado es cero, a continuación, la suscripción sigue siendo válida y los demás campos contienen datos válidos. Si el estado es 21006 Expiró la suscripción. Consulte la [comprobando una confirmación de suscripción puede renovar automáticamente](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) documentación para otros códigos de error.

#### <a name="restoring-auto-renewable-subscriptions"></a>Restauración automática renovables suscripciones

Pondremos en contacto varias transacciones: la transacción de compra original además de una transacción independiente para cada período de tiempo que se renovó la suscripción. Deberá realizar un seguimiento de las fechas de inicio y los términos para comprender qué es el período de validez.   
   
   
   
 El objeto SKPaymentTransaction no incluye el período de suscripción, debe usar un identificador de producto diferente para cada término y escribir código que se puede extrapolar el período de suscripción desde la fecha de compra de la transacción.

#### <a name="testing-auto-renewal"></a>Probar la renovación automática

Para que sea más fácil probar suscripciones, sus duraciones se comprimen cuando se prueba en el espacio aislado. 1 semana en las suscripciones se renuevan cada 3 minutos, 1 año las suscripciones se renuevan cada hora. Las suscripciones renovarán automáticamente un máximo de 6 veces durante las pruebas en el espacio aislado.

## <a name="reporting"></a>Informes

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) proporciona:   
   
 **Ventas y las tendencias** : muestra los detalles de la aplicación de descargas, actualizaciones y compras de la aplicación.   
   
 **Los pagos e informes financieros** – detalla los ingresos obtenidos por sus aplicaciones, así como los pagos de lista que se han realizado y cuánto cobrarle.

Un informe de ventas y las tendencias de ejemplo se muestra a continuación:   

 [![](subscriptions-and-reporting-images/image42.png "Un ejemplo de informe de ventas y las tendencias")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 También hay un [ **ITC conectar Mobile**aplicación iOS (vínculo de iTunes)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
aquí se muestran capturas de pantalla de iPhone para algunas de las estadísticas disponibles:   
   
 [![](subscriptions-and-reporting-images/image43.png "capturas de pantalla de iPhone para algunas de las estadísticas disponibles")](subscriptions-and-reporting-images/image43.png#lightbox)
