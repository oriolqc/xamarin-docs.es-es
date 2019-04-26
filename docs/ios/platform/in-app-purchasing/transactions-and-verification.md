---
title: Las transacciones y comprobación de Xamarin.iOS
description: Este documento describe cómo permitir para la restauración de compras anteriores en una aplicación de Xamarin.iOS. También describe maneras de proteger las compras y los productos entregados por el servidor.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 83f5fd233c004271169a4d00d0a65e70aa925b95
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369113"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Las transacciones y comprobación de Xamarin.iOS

## <a name="restoring-past-transactions"></a>Restauración de las transacciones

Si la aplicación admite los tipos de productos que se pueden restaurables, debe incluir algunos elementos de interfaz de usuario para permitir a los usuarios restaurar esas compras.
Esta funcionalidad permite que un cliente para agregar el producto a dispositivos adicionales o para restaurar el producto en el mismo dispositivo después de que se va a limpiar del todo o quitar y volver a instalar la aplicación. Los siguientes tipos de producto son que se pueden restaurar:

-  Productos no consumibles
-  Puede renovar automáticamente suscripciones
-  Suscripciones gratuitas

El proceso de restauración debe actualizar los registros mantiene en el dispositivo para cumplir sus productos. El cliente puede elegir restaurar en cualquier momento, en cualquiera de sus dispositivos. El proceso de restauración vuelve a enviar todas las transacciones anteriores para que el usuario; el código de aplicación, a continuación, debe determinar qué acción realizar con esa información (por ejemplo, comprobar si ya existe un registro de los que compra en el dispositivo y si no, crear un registro de la compra y habilitar el producto para el usuario).

### <a name="implementing-restore"></a>Implementación de restauración

La interfaz de usuario **restaurar** botón llama al método siguiente, que desencadena RestoreCompletedTransactions en el `SKPaymentQueue`.

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit enviará la solicitud de restauración a servidores de Apple asincrónicamente.   
   
Dado que el `CustomPaymentObserver` está registrado como un observador de transacción, lo recibirá mensajes cuando los servidores de Apple responden. La respuesta contendrá todas las transacciones que este usuario ha realizado en esta aplicación (a través de todos sus dispositivos). El código recorre cada transacción, detecta el estado restaurado y llama el `UpdatedTransactions` método procesarlo como se muestra a continuación:

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

Si hay productos para el usuario, que se pueden restaurar `UpdatedTransactions` no se llama.   
   
El código posibles más sencillo para restaurar una transacción determinada en el ejemplo realiza las mismas acciones que cuándo entra una compra en su lugar, excepto que el `OriginalTransaction` propiedad se utiliza para obtener acceso al identificador de producto:

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

Puede comprobar una implementación más sofisticada Sí `transaction.OriginalTransaction` propiedades, como el número de fecha y la recepción original. Esta información será útil para algunos tipos de producto (por ejemplo, suscripciones).

#### <a name="restore-completion"></a>Restauración de finalización

El `CustomPaymentObserver` tiene dos métodos adicionales que llamará StoreKit cuando haya finalizado el proceso de restauración (correctamente o con un error), se muestra a continuación:

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

Los dos ejemplos de este documento usan `NSUserDefaults` para realizar el seguimiento de las compras:   
   
 **Consumibles** : el saldo de' ' de compras de crédito es una sencilla `NSUserDefaults` valor entero que se incrementa con cada compra.   
   
 **No consumibles** : cada compra de filtro de la foto se almacena como un par clave-valor en `NSUserDefaults`.

Uso de `NSUserDefaults` mantiene el código de ejemplo simple, pero no ofrece una solución muy segura ya que es posible que los usuarios preocupados por técnicamente actualizar la configuración (omitiendo el mecanismo de pago).   
   
Nota: Aplicaciones del mundo real deben adoptar un mecanismo seguro para almacenar adquirido contenido que no está sujeto a alteración de usuarios. Esto puede implicar el cifrado y otras técnicas, incluida la autenticación de servidor remoto.   
   
 El mecanismo también debe estar diseñado para aprovechar las características de copia de seguridad y recuperación integradas de iOS, iTunes y iCloud. Así se asegurará de que después de que un usuario restaura una copia de seguridad de sus compras anteriores estará disponibles inmediatamente.   
   
Consulte Secure codificación guía Apple para obtener más instrucciones específicas de iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Comprobación de recepción y los productos entregados por el servidor

Los ejemplos de este documento hasta el momento han consistido en únicamente de la aplicación que se comunican directamente con los servidores de App Store para llevar a cabo las transacciones de compra, que desbloqueen las características o capacidades automatizadas de IU ya en la aplicación.   
   
Apple proporciona un nivel adicional de seguridad de compra al permitir que los recibos de compra debe ser comprobada por separado por otro servidor, que puede ser útil para validar una solicitud antes de entregar contenidos digitales como parte de una compra (por ejemplo, un libro digital o Magazine).   
   
 **Productos integrados** : al igual que los ejemplos de este documento, el producto que se compra existe como funcionalidad se incluye con la aplicación. Una compra en la aplicación permite al usuario acceso a la funcionalidad.
Id. de producto está codificados.   
   
 **Productos de servidor entregan** : el producto consta de contenido descargable que se almacena en un servidor remoto hasta que una transacción correcta hace que la descarga de contenido.
Algunos ejemplos podrían incluir los libros en pantalla o revistas problemas. Id. de producto normalmente se obtienen de un servidor externo (donde también se hospeda el contenido del producto). Las aplicaciones deben implementar una manera sólida de grabar cuando una transacción se ha completado, por lo que si se produce un error de descarga de contenido puede ser volverá a intentar sin confundir al usuario.

### <a name="server-delivered-products"></a>Entregado por el servidor de productos

Algunos productos contenido del, tales como libros y revistas (o incluso un nivel de juego) debe descargarse desde un servidor remoto durante el proceso de compra. Esto significa que se requiere un servidor adicional para almacenar y entregar el contenido del producto tras su adquisición.

#### <a name="getting-prices-for-server-delivered-products"></a>Obtener los precios de productos ofrecidos por el servidor

Dado que los productos de forma remota se entregan, también es posible agregar más productos con el tiempo (sin actualizar el código de aplicación), como la adición de más de los libros en pantalla o nuevos problemas de una revista. Para que la aplicación puede detectar estos productos de noticias y mostrarlos al usuario, el servidor adicional debe almacenar y entregar esta información.   
   
[![](transactions-and-verification-images/image38.png "Obtener los precios de productos ofrecidos por el servidor")](transactions-and-verification-images/image38.png#lightbox)   
   
1. Información del producto debe almacenarse en varios lugares: en el servidor y en iTunes Connect. Además, cada producto tendrá los archivos de contenido asociados con él. Estos archivos se entregará después de una compra correcta.   
   
2. Cuando el usuario desea comprar un producto, la aplicación debe determinar qué productos están disponibles. Esta información puede almacenarse en caché, pero se debe entregar desde un servidor remoto donde se almacena la lista maestra de productos.   
   
3. El servidor devuelve una lista de identificadores de producto para la aplicación analizar.   
   
4. A continuación, la aplicación determina cuál de estos identificadores de producto para enviar a StoreKit para recuperar las descripciones y precios.   
   
5. StoreKit envía la lista de identificadores de producto para servidores de Apple.   
   
6. Los servidores de iTunes responden con información de producto válida (descripción y el precio actual).   
   
7. La aplicación `SKProductsRequestDelegate` se pasa la información del producto para su presentación al usuario.

#### <a name="purchasing-server-delivered-products"></a>Compra de productos ofrecidos por el servidor

Dado que el servidor remoto requiere alguna forma de validar que una solicitud de contenido es válida (es decir. se ha pagado a), se pasa la información de recepción para la autenticación. El servidor remoto reenvía esos datos a iTunes para la comprobación y, si se realiza correctamente, incluye el contenido del producto en la respuesta a la aplicación.   
   
 [![](transactions-and-verification-images/image39.png "Compra de productos ofrecidos por el servidor")](transactions-and-verification-images/image39.png#lightbox)   
   
1. La aplicación agrega un `SKPayment` a la cola. Si es necesario el usuario se le solicitará su identificador de Apple y se le pedirá que confirme el pago.   
   
2. StoreKit envía la solicitud al servidor para su procesamiento.   
   
3. Cuando se completa la transacción, el servidor responde con una confirmación de transacción.   
   
4. El `SKPaymentTransactionObserver` subclase recibe la notificación y lo procesa. Dado que el producto se debe descargar desde un servidor, la aplicación inicia una solicitud de red al servidor remoto.   
   
5. La solicitud de descarga viene acompañada por los datos de recepción para que pueda comprobar el servidor remoto que está autorizado para acceder al contenido. Cliente de red de la aplicación espera una respuesta a esta solicitud.   
   
6. Cuando el servidor recibe una solicitud de contenido, analiza los datos de recepción y envía una solicitud directamente a los servidores de iTunes para comprobar la recepción es para una transacción válida. El servidor debe usar alguna lógica para determinar si se debe enviar la solicitud a la dirección URL de un espacio aislado o producción. Apple sugiere lo siempre utilizando la dirección URL de producción y al espacio aislado si la recepción estado 21007 (recepción de espacio aislado enviado al servidor de producción). Hacer referencia a Apple [Guía de programación de validación de recepción](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) para obtener más detalles.
   
7. iTunes comprobará la recepción y devolver un estado de cero si es válido.   
   
8. El servidor espera respuesta de iTunes. Si recibe una respuesta válida, el código debe buscar el archivo de contenido del producto asociado a incluir en la respuesta a la aplicación.   
  
9. La aplicación recibe y analiza la respuesta, guardar el contenido del producto en el sistema de archivos del dispositivo.   
   
10. La aplicación permite que el producto y, a continuación, llama a del StoreKit `FinishTransaction`. La aplicación, a continuación, puede mostrar opcionalmente el contenido adquirido (por ejemplo, mostrar la primera página de un libro adquirida o problema magazine).

Una implementación alternativa para los archivos de contenido de un producto muy grande podría implicar almacenar simplemente la recepción de transacción en el paso 9 # para que la transacción se puede completar rápidamente y proporcionar una interfaz de usuario para el usuario descargar el contenido real del producto en algún momento posterior. La solicitud de descarga subsiguiente vuelva a puede enviar la confirmación almacenada para tener acceso al archivo de contenido de producto necesario.

### <a name="writing-server-side-receipt-verification-code"></a>Escribir código de verificación de recepción del servidor

Validar una confirmación en el código del lado servidor puede hacerse con una simple HTTP POST solicitud/respuesta que abarca los pasos #5 al 8 de # en el diagrama de flujo de trabajo.   
   
Extraer el `SKPaymentTansaction.TransactionReceipt` propiedad en la aplicación. Se trata de los datos que deben enviarse a iTunes para la comprobación (paso 5 de #).

Codificar en Base64 los datos de recepción de transacción (ya sea en el paso 5 de # o #6).

Crear una carga JSON simple similar al siguiente:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST JSON a [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt) para entornos de producción o [ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt) para las pruebas.   
   
 La respuesta JSON contendrá las siguientes claves:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

El estado de cero indica una confirmación de válidas. El servidor puede continuar para cumplir el contenido del producto comprado. La clave de recepción contiene un diccionario JSON con las mismas propiedades que el `SKPaymentTransaction` objeto que ha sido recibido por la aplicación, por lo que el código de servidor puede consultar este diccionario para recuperar información como el product_id y la cantidad de la compra.

Consulte Apple [Guía de programación de validación de recepción](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) documentación para obtener más información.
