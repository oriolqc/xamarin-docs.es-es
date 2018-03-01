---
title: Compra de productos consumibles
ms.topic: article
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7366a4ce5cbb6a3026a7445a03f03b45d89d9210
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="purchasing-consumable-products"></a>Compra de productos consumibles

Productos consumibles son las más fáciles de implementar, porque no hay ningún requisito 'restore'. Son útiles para productos como moneda en juego o una parte de uso único de funcionalidad. Los usuarios volver a pueden comprar productos consumibles over y over nuevo.

## <a name="built-in-product-delivery"></a>Entrega de productos integrados

El código de ejemplo que acompañan a este documento muestra productos integrados: los identificadores de producto están codificados en la aplicación porque están estrechamente relacionadas con el código que desbloquea la característica tras el pago. El proceso de compra se puede visualizar similar al siguiente:   
   
[ ![La visualización de proceso de compra](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png)     
   
 El flujo de trabajo básico es:   
   
   
   
 1. La aplicación agrega un `SKPayment` a la cola. Si es necesario el usuario se le pedirá su Id. de Apple y se le pedirá que confirme el pago.   
   
 2. StoreKit envía la solicitud al servidor para su procesamiento.   
   
 3. Una vez completada la transacción, el servidor responde con una confirmación de transacción.   
   
 4. El `SKPaymentTransactionObserver` subclase recibe la notificación y lo procesa.   
   
 5. La aplicación habilita el producto (mediante la actualización de `NSUserDefaults` o algún otro mecanismo) y, a continuación, llama de StoreKit `FinishTransaction`.

Hay otro tipo de flujo de trabajo: *Server-Delivered productos* , es decir se describe más adelante en el documento (vea la sección *comprobación de recepción y los productos de Server-Delivered*).

## <a name="consumable-products-example"></a>Ejemplo de productos consumible

El [InAppPurchaseSample código](https://developer.xamarin.com/samples/monotouch/StoreKit/) contiene un proyecto denominado *consumibles* que implementa un basic 'en el juego currency' (denominado "juguetear créditos"). El ejemplo muestra cómo implementar dos productos de compra en la aplicación para permitir al usuario comprar como muchos "créditos de mono" que deseen: en una aplicación real existe también alguna manera de gastos a ellos.   
   
   
   
 La aplicación se muestra en estas capturas de pantalla: cada compra agrega más "créditos de mono" para equilibrar la del usuario:   
   
   
   
 [ ![Cada compra agrega más créditos de mono para el equilibrio de los usuarios](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png)   
   
   
   
 Las interacciones entre las clases personalizadas, StoreKit y la tienda de aplicaciones tiene este aspecto:   
   
   
   
 [ ![Las interacciones entre las clases personalizadas, StoreKit y la tienda de aplicaciones](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png)

&nbsp;

### <a name="viewcontroller-methods"></a>Métodos de ViewController

Además de las propiedades y métodos necesarios para recuperar información del producto, el controlador de vista necesita observadores de notificación adicionales realizar escuchas para las notificaciones relacionadas con la compra. Se trata simplemente `NSObjects` que se registra y se quitan en `ViewWillAppear` y `ViewWillDisappear` respectivamente.

```csharp
NSObject succeededObserver, failedObserver;
```

El constructor también creará la `SKProductsRequestDelegate` subclase ( `InAppPurchaseManager`) que a su vez crea y registra el `SKPaymentTransactionObserver` ( `CustomPaymentObserver`).   
   
   
   
 La primera parte de una transacción de compra en la aplicación de procesamiento es controlar presionar el botón cuando el usuario desea comprar algo, como se muestra en el siguiente código de la aplicación de ejemplo:

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 La segunda parte de la interfaz de usuario está controlando la notificación de que la transacción se realizó correctamente, en este caso mediante la actualización del saldo mostrado:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

La parte final de la interfaz de usuario muestra un mensaje si se cancela una transacción por algún motivo. En el código de ejemplo simplemente se escribe un mensaje en la ventana de salida:

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

Además de estos métodos en el controlador de vista, una transacción de compra de productos consumibles también requiere código en el `SKProductsRequestDelegate` y `SKPaymentTransactionObserver`.

### <a name="inapppurchasemanager-methods"></a>Métodos de InAppPurchaseManager

El código de ejemplo implementa un número de compra métodos relacionados en la clase InAppPurchaseManager, incluido el `PurchaseProduct` método que crea un `SKPayment` de instancia y la agrega a la cola para su procesamiento:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Agregando el pago a la cola es una operación asincrónica. La aplicación recupera el control mientras StoreKit procesa la transacción y lo envía a los servidores de Apple. En este punto es que iOS comprobará el usuario inicia sesión en la tienda de aplicaciones y pedir a ella para un Id. de Apple y la contraseña si es necesario.   
   
   
   
 Suponiendo que el usuario correctamente se autentica con la tienda de aplicaciones y está de acuerdo con la transacción, la `SKPaymentTransactionObserver` recibirá respuesta del StoreKit y llame al método siguiente para completar la transacción y finalizarla.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

El último paso consiste en asegurarse de que lo notifique a StoreKit que cumplirá correctamente la transacción, mediante una llamada a `FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

Una vez entregado el producto, `SKPaymentQueue.DefaultQueue.FinishTransaction` debe llamarse para suprimir la transacción de la cola de pago.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Métodos de SKPaymentTransactionObserver (CustomPaymentObserver)

Llamadas de StoreKit el `UpdatedTransactions` método cuando se recibe una respuesta desde servidores de Apple y pasa una matriz de `SKPaymentTransaction` objetos inspeccionar el código. El método recorre cada transacción y realiza una función diferente en función del estado de transacción (como se muestra aquí):

```csharp
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
           default:
               break;
       }
   }
}
```

El `CompleteTransaction` método se ha analizado anteriormente en esta sección: guarda los detalles de la compra para `NSUserDefaults`, finaliza la transacción con StoreKit y por último, notifica a la interfaz de usuario para la actualización.

### <a name="purchasing-multiple-products"></a>Comprar varios productos

Si tiene sentido en la aplicación para comprar varios productos, use la `SKMutablePayment` clase y establezca el campo de cantidad:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

El código de control de la transacción completada también debe consultar la propiedad cantidad para completar correctamente la compra:

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

Cuando un usuario adquiere varias cantidades, la alerta de confirmación StoreKit reflejará la cantidad, el precio por unidad y el precio total que le cobrarán, tal y como se muestra en la captura de pantalla siguiente:

[ ![Confirmar una compra](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png)

## <a name="handling-network-outages"></a>Control de interrupciones de red

En la aplicación de compras requieren una conexión de red de trabajo para StoreKit para comunicarse con servidores de Apple. Si una conexión de red no está disponible, a continuación, en la aplicación de compra dejará de estar disponible.

### <a name="product-requests"></a>Solicitudes de producto

Si la red no está disponible al hacer que un `SKProductRequest`, `RequestFailed` método de la `SKProductsRequestDelegate` subclase ( `InAppPurchaseManager`) se llamará, tal y como se muestra a continuación:

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

El ViewController, a continuación, realiza escuchas para la notificación y muestra un mensaje en los botones de compra:

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Dado que una conexión de red puede ser transitoria en dispositivos móviles, aplicaciones pueden desea supervisar el estado de la red mediante el marco de trabajo SystemConfiguration y vuelva a intentar cuando hay disponible una conexión de red. Consulte de Apple o la lo usa.

### <a name="purchase-transactions"></a>Transacciones de compra

La cola de pago StoreKit almacenará y compra reenviar las solicitudes si es posible, por lo que cuando la red durante el proceso de compra, el efecto de una interrupción de red variará en función de.   
   
   
   
 Si se produce un error durante una transacción, el `SKPaymentTransactionObserver` subclase ( `CustomPaymentObserver`) tendrá la `UpdatedTransactions` método llamado y `SKPaymentTransaction` clase estará en el estado de error.

```csharp
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
           default:
               break;
       }
   }
}
```

El `FailedTransaction` método detecta si el error fue debido a la cancelación de usuario, como se muestra aquí:

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

Incluso si se produce un error en una transacción, la `FinishTransaction` método debe llamarse para suprimir la transacción de la cola de pago:

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

El código de ejemplo, a continuación, envía una notificación para que la ViewController puede mostrar un mensaje. Las aplicaciones no deben mostrar más mensaje si el usuario canceló la transacción. Otros códigos de error que pueden producirse incluyen:

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Las restricciones del control

El **configuración > General > restricciones** característica de iOS permite a los usuarios bloquear ciertas características de su dispositivo.   
   
   
   
 Puede consultar si se permite al usuario realizar compras desde la aplicación a través de la `SKPaymentQueue.CanMakePayments` método. Si se devuelve false, a continuación, el usuario no tiene acceso en la aplicación de compra. StoreKit mostrará automáticamente un mensaje de error al usuario si se intenta realizar una compra. Comprobando este valor de la aplicación en su lugar puede ocultar los botones de la compra o realizar alguna otra acción para ayudar al usuario.   
   
   
   
 En el `InAppPurchaseManager.cs` archivo la `CanMakePayments` método ajusta la función StoreKit similar al siguiente:

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Para probar este método, use la **restricciones** característica de iOS para deshabilitar **compras dentro de la aplicación**:   
   
   
   
 [ ![Use la característica de restricciones de iOS para deshabilitar las compras de la aplicación](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png)   
   
   
   
 Este código de ejemplo de `ConsumableViewController` reacciona a `CanMakePayments` devolver false mostrando **AppStore deshabilitado** texto en los botones deshabilitados.

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

La aplicación es similar a cuando el **compras dentro de la aplicación** característica está restringida, se deshabilitan los botones de la compra.   
   
   
   
 [ ![La aplicación sería si las compras en la aplicación característica está restringida por la compra botones están deshabilitados](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png)   
   
   
   

Información del producto puede ser solicitada cuando `CanMakePayments` es false, por lo que la aplicación todavía puede recuperar y mostrar los precios. Esto significa que si se quita el `CanMakePayments` comprobación desde el código de los botones de compra sería estar activas, sin embargo, cuando se intenta realizar una compra, el usuario verá un mensaje que **compras dentro de la aplicación no se permite** (generados por StoreKit Cuando la cola de pago se accede a):   
   
   
   
 [ ![No se permiten las compras de la aplicación](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png)   
   
   
   
 Las aplicaciones reales pueden adoptar un enfoque diferente para controlar la restricción, como ocultar los botones por completo y es posible proporcionar un mensaje más detallado de la alerta que StoreKit muestra automáticamente.

