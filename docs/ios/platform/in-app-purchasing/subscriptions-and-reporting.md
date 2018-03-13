---
title: Las suscripciones e informes
ms.topic: article
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 237a986d6db2fb6984e99c6265fbbc212b35a351
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="subscriptions-and-reporting"></a>Las suscripciones e informes

## <a name="about-non-renewing-subscriptions"></a>Acerca de las suscripciones no renovar

Suscripciones de renovación no están diseñadas para los productos que representan la venta de un servicio con una restricción del tiempo como (acceso de una semana a una aplicación de navegación) o acceso de tiempo limitado a un archivo de datos.   
   
   
   
 Diferencias principales entre las suscripciones no renovar y otros tipos de producto:

-  La definición de producto en iTunes Connect no incluye el término. El código de aplicación debe ser capaz de inferir el período de validez de los Id. 
-  Puede adquirir varias veces (como un producto compatible). Se requiere que las aplicaciones para administrar la renovación y el término/expiración de suscripción y evitar que el usuario comprar suscripciones que se superponen. 
-  No se admiten las compras realizadas por la función StoreKit restaurar. Si la suscripción debe estar disponible a través de todos los dispositivos de un usuario, la aplicación debe diseñar e implementar esta característica junto con un servidor remoto. Las aplicaciones también son responsables de copia de seguridad del estado de la suscripción para casos vez de un dispositivo de copia de seguridad, a continuación, restaurar-de-copia de seguridad. 
-  Información general de implementación
-  Suscripciones de renovación no normalmente debe implementarse mediante el flujo de trabajo entregados por el servidor y administran como productos consumibles. 


## <a name="about-free-subscriptions"></a>Acerca de las suscripciones gratuitas

Las suscripciones gratuitas permiten a los desarrolladores colocar contenido disponible en aplicaciones de quiosco (que no se puede usar en aplicaciones no Newsstand). Una vez que se inicia una suscripción gratuita estará disponible en todos los dispositivos del usuario. Las suscripciones gratuitas no expiren nunca; solo finaliza cuando se desinstala la aplicación.

### <a name="implementation-overview"></a>Información general de implementación

Las suscripciones gratuitas tienen un comportamiento muy como suscripciones automáticamente renovable. La aplicación debe tener un producto de suscripción gratuita ' comprarse ' en iTunes Connect. Cuando se adquirió por el usuario, se debe validar la compra de suscripción gratuita como un producto de suscripción automáticamente renovable. Se pueden restaurar las transacciones de suscripción gratuita.


## <a name="about-auto-renewable-subscriptions"></a>Acerca de las suscripciones automáticamente renovable

Suscripciones automáticamente renovable se utilizan principalmente en aplicaciones de quiosco. Representan un producto que concede al usuario acceso al contenido dinámico para un determinado período de tiempo, que se configura en iTunes Connect (establecer períodos que van de 7 días a 1 año). Las suscripciones se renuevan automáticamente, se está cargando a los usuarios ID de Apple al final de cada período de suscripción, a menos que el usuario opts horizontal. Este tipo de producto funciona bien para las suscripciones de revista o noticias, donde el usuario obtiene acceso a cada problema publican mientras su suscripción es válida.

### <a name="implementation-overview"></a>Información general de implementación

Suscripciones automáticamente renovable debe implementarse mediante el flujo de trabajo de productos de Server-Delivered (consulte la *comprobación de recepción y los productos de Server-Delivered* sección).

#### <a name="shared-secret"></a>Secreto compartido

El secreto compartido de la aplicación de compra debe usarse en la solicitud JSON al comprobar automáticamente renovable suscripciones en el servidor. El secreto compartido es creado/tiene acceso a través de iTunes Connect.

En iTunes Connect página principal seleccione **mis aplicaciones**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Seleccione Mis aplicaciones")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
Seleccione una aplicación y haga clic en el **compras dentro de la aplicación** ficha:

[![](subscriptions-and-reporting-images/image6.png "Haga clic en la pestaña Compras en la aplicación")](subscriptions-and-reporting-images/image6.png#lightbox)

En la parte inferior de la página, seleccione **vista o generar un secreto compartido**:
   
 [![](subscriptions-and-reporting-images/image40.png "Seleccione la vista o generar un secreto compartido")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Generar un secreto compartido")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 Para usar el secreto compartido, debe incluirlo en la carga de JSON que se envía a los servidores de Apple cuando se valida una confirmación de compra en la aplicación para una suscripción automáticamente renovable, similar al siguiente:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Campo de estado de la respuesta será cero si la compra es válida, como con otros tipos de producto.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Descarga de elementos después de la vigencia de la suscripción inicial

Como parte de la entrega de productos de suscripción, el código debe comprobar con frecuencia la recepción conocida más reciente contra servidores de Apple. Si una suscripción se auto-renovado desde la última comprobación, la respuesta JSON contendrá campos adicionales que notificar a la aplicación de la transacción que se ha producido (que debe ampliar la validez de las suscripciones). La respuesta JSON contendrá:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Si el estado es cero, a continuación, la suscripción sigue siendo válida y los otros campos contienen datos válidos. Si el estado es 21006 la suscripción ha expirado. Consulte la [comprobar una confirmación de suscripción automáticamente renovable](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) documentación para otros códigos de error.

#### <a name="restoring-auto-renewable-subscriptions"></a>Restaurar las suscripciones automáticamente renovable

Podrá regresar varias transacciones: la transacción de compra original además de una transacción distinta para cada período de tiempo que se renueva la suscripción. Debe realizar un seguimiento de las fechas de inicio y los términos para entender lo que es el período de validez.   
   
   
   
 El objeto SKPaymentTransaction no incluye el término de suscripción: se debe utilizar un identificador de producto diferente para cada término y escribir código que puede extrapolar el período de suscripción de la fecha de compra de la transacción.

#### <a name="testing-auto-renewal"></a>Probar la renovación automática

Para facilitar la prueba de las suscripciones, sus duraciones se comprimen cuando se prueba en el espacio aislado. 1 semana las suscripciones se renuevan cada 3 minutos, 1 año las suscripciones renuevan cada hora. Las suscripciones renovarán automáticamente un máximo de 6 horas durante las pruebas en el espacio aislado.

## <a name="reporting"></a>Informes

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) proporciona:   
   
 **Ventas y las tendencias** – descarga muestra los detalles de aplicación, actualizaciones y las compras en la aplicación.   
   
 **Pagos e informes financieros** : detalles de los ingresos obtenidos por sus aplicaciones, así como los pagos de la lista que se realizaron a usted y a la cantidad se deban enviar.

Un informe de ventas y las tendencias de ejemplo se muestra a continuación:   

 [![](subscriptions-and-reporting-images/image42.png "Un informe de ventas y las tendencias de ejemplo")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 También hay un [ **ITC conectar Mobile**aplicación iOS (vínculo de iTunes)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
capturas de pantalla de iPhone para algunas de las estadísticas disponibles se muestran aquí:   
   
 [![](subscriptions-and-reporting-images/image43.png "capturas de pantalla de iPhone para algunas de las estadísticas disponibles")](subscriptions-and-reporting-images/image43.png#lightbox)
