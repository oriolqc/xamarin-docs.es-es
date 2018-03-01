---
title: "Almacenar Introducción al Kit y recuperar la información de producto"
ms.topic: article
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 2a636a5ee2b027a2b2889c375f1fef5be67c379b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="store-kit-overview-and-retrieving-product-information"></a>Almacenar Introducción al Kit y recuperar la información de producto

En las capturas de pantalla siguiente se muestra la interfaz de usuario para una compra en la aplicación.
Antes de cualquier transacción tiene lugar, la aplicación debe recuperar precio del producto y una descripción para su presentación. A continuación, cuando el usuario presiona **comprar**, la aplicación realiza una solicitud a StoreKit que administra el cuadro de diálogo de confirmación y el inicio de sesión de Id. de Apple. Suponiendo que la transacción, a continuación, se realiza correctamente, StoreKit notifica el código de aplicación, que debe almacenar el resultado de la transacción y proporcionar al usuario acceso a su compra.   

   
 [ ![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifica el código de aplicación, que debe almacenar el resultado de la transacción y proporcionar al usuario acceso a su compra")](store-kit-overview-and-retreiving-product-information-images/image14.png)

## <a name="classes"></a>Clases

La implementación de las compras de la aplicación requiere las siguientes clases desde el marco de trabajo de StoreKit:   
   
 **SKProductsRequest** – solicita StoreKit productos aprobados a vender (tienda de aplicaciones). Puede configurarse con un número de identificadores de producto.

-   **SKProductsRequestDelegate** : declara los métodos para controlar las solicitudes de producto y las respuestas. 
-   **SKProductsResponse** – envía al delegado de StoreKit (tienda de aplicaciones). Contiene el SKProducts que coinciden con el producto identificadores se envían con la solicitud. 
-   **SKProduct** – recuperados de un producto de StoreKit (que se haya configurado en iTunes Connect). Contiene información sobre el producto, como Id. de producto, título, descripción y el precio. 
-   **SKPayment** – creado con un identificador de producto y se agrega a la cola de pago para realizar una compra. 
-   **SKPaymentQueue** – en cola las solicitudes de pago para enviarse a Apple. Las notificaciones se desencadenan como resultado de cada pago está procesando. 
-   **SKPaymentTransaction** : representa una transacción completada (una solicitud de compra que se ha procesado por la tienda de aplicaciones y envía a la aplicación a través de StoreKit). La transacción puede ser comprado, restaurados o error. 
-   **SKPaymentTransactionObserver** – subclase personalizada que responde a los eventos generados por la cola de pago StoreKit. 
-   **Las operaciones de StoreKit son asíncronas** : después de que se inicia un SKProductRequest o un SKPayment se agrega a la cola, el control se devuelve al código. StoreKit llamará a métodos en su subclase SKProductsRequestDelegate o SKPaymentTransactionObserver cuando recibe datos de servidores de Apple. 


El siguiente diagrama muestra las relaciones entre las diferentes clases de StoreKit (las clases abstractas deben implementarse en la aplicación):   
   
   
   
 [ ![](store-kit-overview-and-retreiving-product-information-images/image15.png "Las relaciones entre las diversas clases abstractas de clases de StoreKit deben implementarse en la aplicación")](store-kit-overview-and-retreiving-product-information-images/image15.png)   
   
   
   
 Estas clases se explican con más detalle más adelante en este documento.

## <a name="testing"></a>Pruebas

La mayoría de las operaciones de StoreKit requiere un dispositivo real para las pruebas. Recuperar información de productos (en Internet Explorer. precio &amp; descripción) funcionará en el simulador pero la compra y las operaciones de restauración, devolverán un error (como el código de FailedTransaction = 5002 se ha producido un error desconocido).

Nota: StoreKit no funciona en el simulador de iOS. Cuando se ejecuta la aplicación en el simulador de iOS, StoreKit registra una advertencia si la aplicación intenta recuperar la cola de pago. Probar el almacén debe hacerse en dispositivos reales.   
   
   
   
 Importante: No iniciar sesión con su cuenta de prueba en la aplicación de configuración. Puede usar la aplicación de configuración de inicio de sesión fuera de cualquier cuenta de Id. de Apple existente, a continuación, debe esperar hasta que se le pida *dentro de la secuencia en la aplicación de compra* para iniciar sesión mediante una prueba de ID de Apple.   
   
   
   

Si intenta iniciar sesión en el almacén real con una cuenta de prueba, se se convertirá automáticamente a un identificador de Apple real. Esa cuenta ya no se podrá utilizar para realizar pruebas.

Para probar código StoreKit debe cerrar la sesión de la cuenta de prueba de iTunes regular y el inicio de sesión con una cuenta de prueba especial (creada en iTunes Connect) que está vinculada a la tienda de prueba. Para cerrar la sesión de la cuenta actual, visite **configuración > iTunes y la tienda de aplicaciones** como se muestra aquí:

 [ ![](store-kit-overview-and-retreiving-product-information-images/image16.png "Para cerrar la sesión de la cuenta actual visite iTunes de configuración y la tienda de aplicaciones")](store-kit-overview-and-retreiving-product-information-images/image16.png)
 
a continuación, inicie sesión con una cuenta de prueba *cuando se solicita mediante StoreKit dentro de la aplicación*:



Para crear usuarios de prueba en iTunes Connect, haga clic en **usuarios y Roles** en la página principal.

 [ ![](store-kit-overview-and-retreiving-product-information-images/image17.png "Para crear usuarios de prueba en iTunes Connect haga clic en usuarios y Roles en la página principal")](store-kit-overview-and-retreiving-product-information-images/image17.png)

Seleccione **evaluadores de espacio aislado**

 [ ![](store-kit-overview-and-retreiving-product-information-images/image18.png "Selección de evaluadores de espacio aislado")](store-kit-overview-and-retreiving-product-information-images/image18.png)

Se muestra la lista de usuarios existentes. Puede agregar un nuevo usuario o eliminar un registro existente. El portal no (actualmente) le permiten ver o editar existentes de prueba a los usuarios, por lo que se recomienda que mantenga un buen historial de cada usuario de prueba que se crea (especialmente la contraseña que asigne). Una vez eliminado un usuario de prueba la dirección de correo electrónico no se puede volver a usar para otra cuenta de prueba.  
   
 [ ![](store-kit-overview-and-retreiving-product-information-images/image19.png "Se muestra la lista de usuarios existentes")](store-kit-overview-and-retreiving-product-information-images/image19.png)   
   
 Nuevos usuarios de prueba tienen atributos similares a un identificador de Apple real (por ejemplo, nombre, la contraseña, la pregunta secreta y la respuesta). Mantener un registro de todos los detalles especificados aquí. El **Seleccionar almacén de iTunes** campo determinará qué moneda y lenguaje compras desde la aplicación usará cuando ha iniciado sesión como ese usuario.

 [ ![](store-kit-overview-and-retreiving-product-information-images/image20.png "El campo de tienda de iTunes seleccione determinará la moneda y el idioma de sus compras en la aplicación del usuario")](store-kit-overview-and-retreiving-product-information-images/image20.png)

## <a name="retrieving-product-information"></a>Al recuperar la información de producto

Se muestra el primer paso en un producto de compra en la aplicación de las ventas: recuperar el precio actual y la descripción de la tienda de aplicaciones para su presentación.   
   
   
   
 Independientemente de qué tipo de productos en una aplicación vende (consumibles, no consumible o un tipo de suscripción), el proceso de recuperación de información de producto para su presentación es el mismo. El código de InAppPurchaseSample que acompaña a este artículo contiene un proyecto denominado *consumibles* que muestra cómo recuperar información de producción para su presentación. Muestra cómo:

-  Crear una implementación de `SKProductsRequestDelegate` e implementar el `ReceivedResponse` método abstracto. El código de ejemplo llama a esto la `InAppPurchaseManager` clase. 
-  Póngase en contacto con StoreKit para ver si se permiten los pagos (mediante `SKPaymentQueue.CanMakePayments` ). 
-  Crear una instancia de un `SKProductsRequest` con los identificadores de producto que se han definido en iTunes Connect. Esto se hace en el ejemplo `InAppPurchaseManager.RequestProductData` método. 
-  Llame al método Start en el `SKProductsRequest` . Esto desencadena una llamada asincrónica a los servidores de App Store. El delegado ( `InAppPurchaseManager` ) será back llama con los resultados. 
-  El delegado ( `InAppPurchaseManager` ) `ReceivedResponse` método actualiza la interfaz de usuario con los datos devueltos desde la tienda de aplicaciones (precios de productos & descripciones o mensajes acerca de los productos no válidos). 

La interacción global tiene el siguiente aspecto ( **StoreKit** está integrada en iOS y el **App Store** representa servidores de Apple):

 [ ![](store-kit-overview-and-retreiving-product-information-images/image21.png "Gráfico de recuperar información del producto")](store-kit-overview-and-retreiving-product-information-images/image21.png)

### <a name="displaying-product-information-example"></a>Mostrar el ejemplo de información de producto

El [InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *consumibles* código de ejemplo muestra cómo se puede recuperar información del producto. La pantalla principal del ejemplo muestra información de dos productos que se recupera de la tienda de aplicaciones:   
   
   
   
 [ ![](store-kit-overview-and-retreiving-product-information-images/image23.png "La pantalla principal muestra información acerca de productos recuperado de la tienda de aplicaciones")](store-kit-overview-and-retreiving-product-information-images/image23.png)   
   
   
   
 El código de ejemplo para recuperar y mostrar información del producto se explica con más detalle a continuación.


#### <a name="viewcontroller-methods"></a>Métodos de ViewController

La `ConsumableViewController` clase va a administrar la presentación de los precios de dos productos cuyos identificadores de producto están codificados en la clase.

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

En la clase nivel debería haber también un NSObject declara que se utilizará para el programa de instalación un `NSNotificationCenter` observador:

```csharp
NSObject priceObserver;
```

En el método ViewWillAppear el observador crear y asignar mediante el centro de notificaciones de forma predeterminada:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 Al final de la `ViewWillAppear` método, llame a la `RequestProductData` método para iniciar la solicitud de StoreKit. Una vez que se ha realizado esta solicitud, StoreKit asincrónicamente pondrá en contacto con servidores de Apple para obtener la información y distribuir la aplicación. Esto se logra mediante la `SKProductsRequestDelegate` subclase ( `InAppPurchaseManager`) que se explica en la sección siguiente.

```csharp
iap.RequestProductData(products);
```

El código para mostrar el precio y la descripción simplemente recupera la información de la SKProduct y lo asigna a los controles de UIKit (tenga en cuenta que se muestra la `LocalizedTitle` y `LocalizedDescription` – StoreKit resuelve automáticamente el texto correcto y los precios basados en configuración de cuenta del usuario). El siguiente código pertenece a la notificación que hemos creado anteriormente:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

Por último, el `ViewWillDisappear` método debe garantizar que se quita el observador:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Métodos de SKProductRequestDelegate (InAppPurchaseManager)

El `RequestProductData` método se llama cuando la aplicación desea recuperar los precios de productos y otra información. Analiza la colección de identificadores de producto en el tipo de datos correcto a continuación, se crea un `SKProductsRequest` con esa información. Llame al método Start hace que una solicitud de red que se realizan en los servidores de Apple. La solicitud se ejecuta de forma asincrónica y llamar a la `ReceivedResponse` método del delegado al que se ha completado correctamente.

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS enrutará automáticamente la solicitud a la versión de la tienda de aplicaciones dependiendo de qué perfil de aprovisionamiento se está ejecutando la aplicación con: 'espacio aislado' o 'producción' modo cuando está desarrollando o probar su aplicación para la solicitud tendrá acceso a todos los productos configurado en iTunes Connect (incluso los que aún no se han enviado o aprobado por Apple). Cuando la aplicación está en producción, las solicitudes de StoreKit solo devolverá información para **aprobado** productos.   
   
   
   
 El `ReceivedResponse` método invalidado se llama después de servidores de Apple han respondido con datos. Dado que esto se denomina en segundo plano, el código debe analizar los datos válidos y usar una notificación para enviar la información del producto a cualquier ViewControllers que 'escuchan' esa notificación. El código para recopilar información de producto válida y enviar una notificación se muestra a continuación:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

Aunque no se muestra en el diagrama, la `RequestFailed` método también se debe invalidar para que pueda proporcionar algunos comentarios al usuario en caso de que los servidores de la tienda de aplicaciones no son accesibles (o se produce algún otro error). Simplemente escribe el código de ejemplo en la consola, pero puede elegir una aplicación real consultar a `error.Code` propiedad e implementar el comportamiento personalizado (por ejemplo, una alerta al usuario).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Esta captura de pantalla muestra la aplicación de ejemplo inmediatamente después de cargar (cuando no hay información de producto está disponible):

 [ ![](store-kit-overview-and-retreiving-product-information-images/image24.png "La aplicación de ejemplo inmediatamente después de cargar cuando no hay información de producto está disponible")](store-kit-overview-and-retreiving-product-information-images/image24.png)

## <a name="invalid-products"></a>Productos no válidos

Un `SKProductsRequest` también pueden devolver una lista de identificadores de producto no válido. Normalmente se devuelvan productos no válidos debido a uno de los siguientes:   
   
   
   
 **Se ha cometido un error al Id. de producto** : se aceptan sólo los identificadores de producto válida.   
   
 **No se ha aprobado producto** – durante las pruebas, se deben devolver todos los productos que están desactivados para la venta por un `SKProductsRequest`; sin embargo, en producción se devuelven solo los productos aprobado.   
   
 **Identificador de la aplicación no es explícito** – Wildcard App IDs (con un asterisco) no permiten la compra de la aplicación.   
   
 **Perfil de aprovisionamiento incorrecto** : si realiza cambios en la configuración de la aplicación en el portal de aprovisionamiento (como la habilitación de las compras de la aplicación), no olvide volver a generar y utilizar el perfil de aprovisionamiento correcto al compilar la aplicación.   
   
 **contrato de las aplicaciones de pago de iOS no está en su lugar** – StoreKit características no funcionarán en absoluto a menos que haya un contrato válido para la cuenta de desarrollador de Apple.   
   
 **El archivo binario está en el estado rechazado** : si hay un archivo binario enviado anteriormente en el estado rechazado (ya sea por el equipo de la tienda de aplicaciones o el desarrollador), a continuación, StoreKit características no funcionarán.

El `ReceivedResponse` método en el código de ejemplo genera los productos no válidos en la consola:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Mostrar los precios localizados

Niveles de precios especifican un precio específico para cada producto a lo largo de todas las tiendas de aplicaciones internacionales. Para asegurarse de que los precios se muestran correctamente para cada divisa, use el siguiente método de extensión (definido en `SKProductExtension.cs`) en lugar de la propiedad de precio de cada `SKProduct`:

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

El código que establece el título del botón usa el método de extensión similar al siguiente:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

Uso de dos cuentas de prueba de iTunes diferentes (uno para el almacén de American) y uno para el almacén de japonés ocasiona que las capturas de pantalla siguiente:   
   
   
   
 [ ![](store-kit-overview-and-retreiving-product-information-images/image25.png "Mostrando resultados específicos de idioma de cuentas de prueba de dos iTunes diferentes")](store-kit-overview-and-retreiving-product-information-images/image25.png)   
   
   
   
 Tenga en cuenta que el almacén afecta al idioma que se usa para la moneda de información y el precio del producto, mientras que las etiquetas y otro contenido localizado afecta a la configuración de idioma del dispositivo.   
   
   
   
 Recuerde que se debe usar un almacén de diferentes pruebas cuenta debe **cerrar sesión** en el **configuración > iTunes y App Store** y volver a iniciar la aplicación para iniciar sesión con una cuenta diferente. Para cambiar idioma del dispositivo, vaya a **configuración > General > internacional > lenguaje**.

