---
title: Las transacciones y comprobación
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: c8d86d0ce3119b3e104a65a170ab141484af44a7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="transactions-and-verification"></a>Las transacciones y comprobación

## <a name="restoring-past-transactions"></a>Restaurar más allá de las transacciones

Si la aplicación admite tipos de productos que se pueden restaurables, debe incluir algunos elementos de interfaz de usuario para permitir a los usuarios restaurar esas compras.
Esta funcionalidad permite que un cliente para agregar el producto a dispositivos adicionales o para restaurar el producto en el mismo dispositivo después de que se va a limpiarse íntegramente o quitar y volver a instalar la aplicación. Los siguientes tipos de producto son restaurables:

-  Productos no consumible
-  Suscripciones automáticamente renovable
-  Suscripciones gratuitas


El proceso de restauración debe actualizar los registros mantiene en el dispositivo para satisfacer sus productos. El cliente puede elegir restaurar en cualquier momento, en cualquiera de sus dispositivos. El proceso de restauración vuelve a enviar todas las transacciones anteriores para que el usuario; el código de aplicación, a continuación, debe determinar qué acción realizar con dicha información (por ejemplo, para comprobar si ya existe un registro de ese compra en el dispositivo y si no es así, al crear un registro de la compra y habilitar el producto para el usuario).

### <a name="implementing-restore"></a>Implementación de restauración

La interfaz de usuario **restaurar** botón llama al método siguiente, lo que desencadena RestoreCompletedTransactions en el `SKPaymentQueue`.

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit enviará la solicitud de restauración a servidores de Apple asincrónicamente.   
   
   
   
 Dado que el `CustomPaymentObserver` está registrado como un observador de transacción, lo recibirá mensajes cuando los servidores de Apple responden. La respuesta contendrá todas las transacciones que este usuario ha realizado en esta aplicación (a través de todos sus dispositivos). El código recorre cada transacción, detecta el estado restaurado y llama el `UpdatedTransactions` método procesarla tal y como se muestra a continuación:

```csharp
// called when the transaction status is updated
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
       case SKPaymentTransactionState.Purchased:
          theManager.CompleteTransaction(transaction);
           break;
       case SKPaymentTransactionState.Failed:
          theManager.FailedTransaction(transaction);
           break;
       case SKPaymentTransactionState.Restored:
           theManager.RestoreTransaction(transaction);
           break;
default:
           break;
       }
   }
}
```

Si no hay productos pueden restaurarse para el usuario, `UpdatedTransactions` no se llama.   
   
   
   
 El código de posibles más sencillo para restaurar una transacción determinada en el ejemplo realiza la misma acción que cuando una compra realiza, salvo que la `OriginalTransaction` propiedad se utiliza para acceder al Id. de producto:

```csharp
public void RestoreTransaction (SKPaymentTransaction transaction)
{
   // Restored Transactions always have an 'original transaction' attached
   var productId = transaction.OriginalTransaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId); // it's as though it was purchased again
   FinishTransaction(transaction, true);
}
```

Puede comprobar una implementación más sofisticada Sí `transaction.OriginalTransaction` propiedades, como el número original de fecha y la recepción. Esta información será útil para algunos tipos de producto (por ejemplo, suscripciones).

#### <a name="restore-completion"></a>Restauración de finalización

El `CustomPaymentObserver` tiene dos métodos adicionales que llamará StoreKit cuando se haya completado el proceso de restauración (correctamente o con un error), se muestra a continuación:

```csharp
public override void PaymentQueueRestoreCompletedTransactionsFinished (SKPaymentQueue queue)
{
   Console.WriteLine(" ** RESTORE Finished ");
}
public override void RestoreCompletedTransactionsFailedWithError (SKPaymentQueue queue, NSError error)
{
   Console.WriteLine(" ** RESTORE FailedWithError " + error.LocalizedDescription);
}
```

En el ejemplo de estos métodos no hacen nada, sin embargo, una aplicación real puede optar por implementar un mensaje al usuario o a otras funcionalidades.

## <a name="securing-purchases"></a>Protección de las compras

Los dos ejemplos de este documento use `NSUserDefaults` para realizar el seguimiento de las compras:   
   
 **Consumibles** : el saldo de' ' de compras de crédito es un sencillo `NSUserDefaults` valor entero que se incrementa con cada compra.   
   
 **No consumibles** : cada compra de filtro de fotos se almacena como un par de clave y valor en `NSUserDefaults`.

Usar `NSUserDefaults` mantiene el código de ejemplo simple, pero no ofrece una solución muy segura ya que es posible que los usuarios preocupados por técnicamente actualizar la configuración (omitir el mecanismo de pago).   
   
Nota: Las aplicaciones reales deben adoptar un mecanismo seguro para almacenar adquirido contenido que no está sujeto a la alteración de usuario. Esto puede implicar el cifrado y otras técnicas incluida la autenticación de servidor remoto.   
   
 El mecanismo también debe estar diseñado para aprovechar las características integradas de copia de seguridad y recuperación de iOS, iTunes y iCloud. Esto garantizará que cuando un usuario restaura una copia de seguridad de sus compras anteriores estarán disponibles inmediatamente.   
   
   
 Consulte seguros codificación Guía de Apple para obtener más instrucciones específicos de iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Comprobación de recepción y productos de servidor distribuida

Los ejemplos de este documento hasta ahora han consta únicamente de la aplicación que se comunique directamente con los servidores de la tienda de aplicaciones para realizar transacciones de compra, que desbloquear características o capacidades ya codificadas en la aplicación.   
   
   
   
 Apple proporciona un nivel adicional de seguridad de compra al permitir que los recibos de compra se deben comprobar independientemente por otro servidor, lo que puede resultar útil para validar una solicitud antes de entregar contenido digital como parte de una compra (por ejemplo, un libro digital o Magazine).   
   
   
   
 **Productos integrados** : al igual que los ejemplos de este documento, el producto que se compra existe como funcionalidad incluida con la aplicación. Una compra en la aplicación permite al usuario tener acceso a la funcionalidad.
Id. de producto está codificados.   
   
 **Productos de servidor distribuida** : el producto consta de contenido descargable que se almacena en un servidor remoto hasta que una transacción correcta hace que la descarga de contenido.
Ejemplos pueden incluir libros o revistos problemas. Id. de producto normalmente se obtienen de un servidor externo (que también se hospeda el contenido del producto). Las aplicaciones deben implementar una manera segura de grabar cuando se complete una transacción, por lo que si se produce un error de descarga de contenido puede ser volverá a intentar sin confundir al usuario.

### <a name="server-delivered-products"></a>Productos de servidor distribuida

La contenido, de algunos productos, como libros y revistas (o incluso un nivel de juego), deben descargarse desde un servidor remoto durante el proceso de compra. Esto significa que es necesario un servidor adicional para almacenar y entregar el contenido del producto tras su adquisición.

#### <a name="getting-prices-for-server-delivered-products"></a>Obtener los precios de productos de servidor distribuida

Dado que los productos de forma remota se envía, también es posible agregar más productos con el tiempo (sin actualizar el código de aplicación), como agregar más libros o nuevos problemas de una revista. Para que la aplicación puede detectar estos productos de noticias y mostrarlos al usuario, el servidor adicional debe almacenar y entregar esta información.   
   
   
   
 [![](transactions-and-verification-images/image38.png "Obtener los precios de productos de servidor distribuida")](transactions-and-verification-images/image38.png#lightbox)   
   
   
   
 1. Información del producto debe almacenarse en varios lugares: en el servidor y en iTunes Connect. Además, cada producto tendrá archivos de contenido asociados con él. Estos archivos se entregará después de una compra correcta.   
   
 2. Cuando el usuario desea comprar un producto, la aplicación debe determinar qué productos están disponibles. Esta información puede almacenarse en memoria caché, pero se debe entregar desde un servidor remoto donde se almacena la lista maestra de productos.   
   
 3. El servidor devuelve una lista de identificadores de producto para la aplicación analizar.   
   
 4. A continuación, la aplicación determina a cuál de estos identificadores de producto para enviar a StoreKit para recuperar las descripciones y precios.   
   
 5. StoreKit envía la lista de identificadores de producto para servidores de Apple.   
   
 6. Los servidores de iTunes responden con información de producto válida (descripción y el precio actual).   
   
 7. La aplicación `SKProductsRequestDelegate` se pasa la información del producto para su presentación al usuario.

#### <a name="purchasing-server-delivered-products"></a>Compra de productos de servidor distribuida

Dado que el servidor remoto requiere alguna forma de validar que una solicitud de contenido es válida (es decir. se ha pagado a), se pasa la información de recepción para la autenticación. El servidor remoto reenvía esos datos a iTunes para la comprobación y, si se realiza correctamente, incluye el contenido del producto en la respuesta a la aplicación.   
   
 [![](transactions-and-verification-images/image39.png "Compra de productos de servidor distribuida")](transactions-and-verification-images/image39.png#lightbox)   
   
 1. La aplicación agrega un `SKPayment` a la cola. Si es necesario el usuario se le pedirá su Id. de Apple y se le pedirá que confirme el pago.   
   
 2. StoreKit envía la solicitud al servidor para su procesamiento.   
   
 3. Una vez completada la transacción, el servidor responde con una confirmación de transacción.   
   
 4. El `SKPaymentTransactionObserver` subclase recibe la notificación y lo procesa. Dado que el producto debe descargarse de un servidor, la aplicación inicia una solicitud de red al servidor remoto.   
   
 5. La solicitud de descarga está acompañada por los datos de recepción para que pueda comprobar el servidor remoto que está autorizado para acceder al contenido. Cliente de red de la aplicación espera una respuesta a esta solicitud.   
   
 6. Cuando el servidor recibe una solicitud de contenido, analiza los datos de confirmación y envía una solicitud directamente a los servidores de iTunes para comprobar la recepción es para una transacción válida. El servidor debe utilizar alguna lógica para determinar si se debe enviar la solicitud a la dirección URL de producción o de espacio aislado. Apple sugiere siempre con la dirección URL de producción y cambia a espacio aislado si estado: 21007 (recepción de espacio aislado envía al servidor de producción): hacer referencia a [2259 de nota técnica preguntas más frecuentes sobre Nº 16](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html).   
   
 7. iTunes comprobará la recepción y un estado de retorno cero si es válido.   
   
 8. El servidor espera respuesta de iTunes. Si recibe una respuesta válida, el código debe buscar el archivo de contenido de producto asociado a incluir en la respuesta a la aplicación.   
   
 9. La aplicación recibe y analiza la respuesta, guardar el contenido del producto en el sistema de archivos del dispositivo.   
   
 10. La aplicación permite que el producto y, a continuación, llama de StoreKit `FinishTransaction`. La aplicación, a continuación, opcionalmente, puede mostrar el contenido adquirido (por ejemplo, mostrar la primera página de un libro adquirido o un problema revista).

Una implementación alternativa para los archivos de contenido de un producto muy grande puede implicar almacenar simplemente la confirmación de transacción en el paso 9 de # para que se pueda completar rápidamente la transacción y proporcionar una interfaz de usuario para el usuario descargar el contenido real del producto en algún momento posterior. La solicitud de descarga posteriores volver a puede enviar la confirmación almacenada para tener acceso al archivo de contenido de producto requerido.

### <a name="writing-server-side-receipt-verification-code"></a>Escribir código de comprobación de recepción del servidor

Validar una confirmación en el código de servidor puede realizarse con una simple HTTP POST solicitud/respuesta que incluye pasos #5 al 8 de # en el diagrama de flujo de trabajo.   
   
   
   
 Extraer el `SKPaymentTansaction.TransactionReceipt` propiedad en la aplicación. Se trata de los datos que deben enviarse a iTunes para la comprobación (paso 5 de #).

Codificación Base64 los datos de confirmación de transacción (ya sea en el paso 5 de # o #6).

Cree una carga JSON simple similar al siguiente:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST JSON a [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt) para la producción o [ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt) para las pruebas.   
   
 La respuesta JSON contendrá las claves siguientes:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

Un estado de cero indica una recepción válida. El servidor puede continuar para satisfacer el contenido del producto comprado. La clave de recepción contiene un diccionario JSON con las mismas propiedades que la `SKPaymentTransaction` objeto que se ha recibido en la aplicación, por lo que el código de servidor puede consultar este diccionario para recuperar información como el product_id y la cantidad de la compra.

Vea de Apple [comprobar confirmaciones de almacén](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/StoreKitGuide/VerifyingStoreReceipts/VerifyingStoreReceipts.html#//apple_ref/doc/uid/TP40008267-CH104-SW1) documentación para obtener información adicional.

#### <a name="using-aspnet"></a>Mediante ASP.NET

Para los desarrolladores de C#, hay un proyecto de código abierto útil denominado [APNS-Sharp](https://github.com/Redth/APNS-Sharp) que incluye código de comprobación de recepción que funciona en ASP.NET. En concreto, el `Receipt.cs` y `ReceiptVerification.cs` archivos en el [ `Jdsoft.Apple.AppStore` ](https://github.com/Redth/APNS-Sharp/tree/master/JdSoft.Apple.AppStore) directorio puede agregarse a un sitio Web de .NET para implementar fácilmente pasos #6 a &#8; del diagrama de flujo de trabajo Server-Delivered productos.   
   
   
   
 La comunicación con los servidores de iTunes utiliza JSON, lo que resulta sencillo procesar en C#.   
   
   
   
 Hacer referencia a la compra de la aplicación de Apple [recepción validación](https://developer.apple.com/library/ios/#releasenotes/StoreKit/IAP_ReceiptValidation/_index.html) documentación para obtener información detallada sobre cómo comprobar que una recepción es válida.

### <a name="3rd-party-receipt-verification"></a>3ª comprobación de entidad de recepción

Hay compañías que ofrecen plataformas para la comprobación de recepción (y otras cosas) que puede usar en lugar de generar su propio servidor. Xamarin no respalda a estos servicios; basta con que están mencionan aquí como referencia.

#### <a name="urban-airship"></a>Airship urbana

Airship urbana proporciona una serie de diferentes servicios back-end para aplicaciones de iOS, incluidas las notificaciones de comprobación y la inserción de recepción.   
   
 [http://urbanairship.com/products/in-app-purchase/](http://urbanairship.com/products/in-app-purchase/)



