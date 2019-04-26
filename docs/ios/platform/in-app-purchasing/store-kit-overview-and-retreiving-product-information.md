---
title: Información general de StoreKit y recuperar información del producto en Xamarin.iOS
description: Este documento proporciona información general de StoreKit. Describe las clases utilizadas con StoreKit pruebas StoreKit interacciones, visualización de productos para su venta, productos no válidos de control y mostrar los precios localizados.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 0dcda2e4fd1ca7773668a0a6fdf46e01f2f0841d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61367016"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Información general de StoreKit y recuperar información del producto en Xamarin.iOS

La interfaz de usuario para una compra en la aplicación se muestra en las capturas de pantalla siguiente.
Antes de cualquier transacción, la aplicación debe recuperar precio del producto y una descripción para la presentación. A continuación, cuando el usuario presiona **comprar**, la aplicación realiza una solicitud a StoreKit que administra el cuadro de diálogo de confirmación y el inicio de sesión del Id. de Apple. Suponiendo que la transacción, a continuación, se realiza correctamente, StoreKit notifica el código de aplicación, que debe almacenar el resultado de la transacción y proporcionar al usuario acceso a su compra.   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifica el código de aplicación, que debe almacenar el resultado de la transacción y proporcionar al usuario acceso a su compra")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Clases

Implementación de compras de la aplicación requiere las siguientes clases de framework StoreKit:   
   
 **SKProductsRequest** : una solicitud en StoreKit de productos aprobados a vender (App Store). Se puede configurar con un número de Id. del producto.

-   **SKProductsRequestDelegate** : declara los métodos para controlar las solicitudes de producto y las respuestas. 
-   **SKProductsResponse** – enviados al delegado desde StoreKit (App Store). Contiene el SKProducts que coinciden con el producto identificadores enviados con la solicitud. 
-   **SKProduct** – recuperados de un producto de StoreKit (que se ha configurado en iTunes Connect). Contiene información sobre el producto, como el Id. de producto, título, descripción y precio. 
-   **SKPayment** – creado con un identificador de producto y se agrega a la cola de pago para realizar una compra. 
-   **SKPaymentQueue** – puesta en cola las solicitudes de pago para enviarse a Apple. Las notificaciones se desencadenan como resultado de cada pago que se está procesando. 
-   **SKPaymentTransaction** : representa una transacción completada (una solicitud de compra que se ha procesado por el Store de la aplicación y envía de vuelta a su aplicación a través de StoreKit). La transacción puede ser comprado, restaurada o con errores. 
-   **SKPaymentTransactionObserver** – subclase personalizada que responde a eventos generados por la cola de pago StoreKit. 
-   **Las operaciones de StoreKit son asincrónicas** : después de que se inicia un SKProductRequest o un SKPayment se agrega a la cola, el control se devuelve al código. StoreKit llamará a métodos en la subclase SKProductsRequestDelegate o SKPaymentTransactionObserver cuando recibe datos de servidores de Apple. 


El siguiente diagrama muestra las relaciones entre las distintas clases de StoreKit (clases abstractas deben implementarse en la aplicación):   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "Las relaciones entre las diversas clases abstractas de clases de StoreKit deben implementarse en la aplicación")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 Estas clases se explican con más detalle más adelante en este documento.

## <a name="testing"></a>Pruebas

La mayoría de las operaciones de StoreKit requiere un dispositivo real para las pruebas. Recuperar información de productos (es decir. precio &amp; descripción) funcionarán en el simulador pero la compra y las operaciones de restauración, devolverán un error (por ejemplo, código FailedTransaction = 5002 se ha producido un error desconocido).

Nota: StoreKit no funciona en el simulador de iOS. Al ejecutar la aplicación en el simulador de iOS, StoreKit registra una advertencia si la aplicación intenta recuperar la cola de pago. Las pruebas de la tienda deben realizarse en dispositivos reales.   
   
   
   
 Importante: No se inicie la sesión con su cuenta de prueba en la aplicación configuración. Puede usar la aplicación de configuración de inicio de sesión de cualquier cuenta de Id. de Apple existente y, después, debe esperar hasta que se le pida *dentro de la secuencia de compra en la aplicación* para iniciar sesión con una prueba de ID de Apple.   
   
   
   

Si intenta iniciar sesión en el almacén de real con una cuenta de prueba, se convertirán automáticamente a un identificador de Apple real. Dicha cuenta ya no podrá utilizar para las pruebas.

Para probar código StoreKit debe cerrar la sesión de la cuenta de prueba de iTunes normal y el inicio de sesión con una cuenta de prueba especial (creada en iTunes Connect) que esté vinculada a la tienda de prueba. Para cerrar la sesión de la cuenta actual, visite **configuración > iTunes y App Store** como se muestra aquí:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "Para cerrar la sesión de la visita iTunes de configuración de cuenta actual y la App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
a continuación, inicie sesión con una cuenta de prueba *cuando lo solicite StoreKit dentro de la aplicación*:



Para crear usuarios de prueba en iTunes Connect, haga clic en **usuarios y Roles** en la página principal.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "Para crear usuarios de prueba en iTunes Connect haga clic en usuarios y Roles en la página principal")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Seleccione **evaluadores de espacio aislado**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Seleccionar evaluadores de espacio aislado")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

Se muestra la lista de usuarios existentes. Puede agregar un nuevo usuario o eliminar un registro existente. El portal no (actualmente) le permite ver o editar existentes de prueba a los usuarios, por lo que se recomienda que mantenga un buen historial de cada usuario de prueba que se crea (especialmente la contraseña que asigne). Cuando se elimina un usuario de prueba de la dirección de correo electrónico no se puede reutilizar para otra cuenta de prueba.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "Se muestra la lista de usuarios existentes")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Nuevos usuarios de prueba tienen atributos similares a un identificador de Apple real (por ejemplo, nombre, contraseña, pregunta y respuesta secretas). Mantener un registro de todos los detalles especificados aquí. El **seleccione iTunes Store** campo determinará qué moneda y lenguaje compras de la aplicación usará cuando ha iniciado sesión como ese usuario.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "El campo seleccione iTunes Store determinará la moneda y el idioma para sus compras en la aplicación del usuario")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Al recuperar la información de producto

El primer paso en la venta de un producto de compra en la aplicación se muestra: recuperar el precio actual y la descripción de la Store de la aplicación para su presentación.   
   
   
   
 Independientemente de qué tipo de productos de una aplicación vende (consumibles, no puede consumir o un tipo de suscripción), el proceso de recuperación de información de producto para mostrar es el mismo. El código InAppPurchaseSample que acompaña este artículo contiene un proyecto denominado *consumibles* que muestra cómo recuperar la información de producción para su presentación. Muestra cómo:

-  Crear una implementación de `SKProductsRequestDelegate` e implemente el `ReceivedResponse` método abstracto. El ejemplo de código lo llama el `InAppPurchaseManager` clase. 
-  Póngase en contacto con StoreKit para ver si se permiten los pagos (mediante `SKPaymentQueue.CanMakePayments` ). 
-  Crear una instancia de un `SKProductsRequest` con el Id. de producto que se han definido en iTunes Connect. Esto se hace en el ejemplo `InAppPurchaseManager.RequestProductData` método. 
-  Llame al método Start en el `SKProductsRequest` . Esto desencadena una llamada asincrónica a los servidores de App Store. El delegado ( `InAppPurchaseManager` ) será back llama con los resultados. 
-  El delegado ( `InAppPurchaseManager` ) `ReceivedResponse` método actualiza la interfaz de usuario con los datos devueltos por la aplicación de Store (precios de productos & descripciones o mensajes acerca de los productos no válidos). 

La interacción general tiene este aspecto ( **StoreKit** está integrada en iOS y el **App Store** representa servidores de Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Recuperar el gráfico de información del producto")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Mostrar ejemplo de información de producto

El [InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *consumibles* código de ejemplo muestra cómo se puede recuperar información del producto. La pantalla principal del ejemplo muestra información de dos productos que se recupera de la aplicación de Store:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "La pantalla principal muestra productos información recuperados de la aplicación de Store")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 El código de ejemplo para recuperar y mostrar información de producto se explica más detalladamente a continuación.


#### <a name="viewcontroller-methods"></a>ViewController métodos

La `ConsumableViewController` clase va a administrar la presentación de los precios de los dos productos cuyo Id. de producto están codificados en la clase.

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

En la clase nivel debería haber también un NSObject declara que se utilizará al programa de instalación una `NSNotificationCenter` observador:

```csharp
NSObject priceObserver;
```

En el método ViewWillAppear el observador se crea y se asigna mediante el centro de notificaciones de forma predeterminada:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 Al final de la `ViewWillAppear` método, llame a la `RequestProductData` método para iniciar la solicitud StoreKit. Una vez que se ha realizado esta solicitud, StoreKit asincrónicamente pondremos en contacto con los servidores de Apple para obtener la información y distribuir la aplicación. Esto se logra mediante el `SKProductsRequestDelegate` subclase ( `InAppPurchaseManager`) que se explica en la sección siguiente.

```csharp
iap.RequestProductData(products);
```

El código para mostrar el precio y la descripción simplemente recupera la información de la SKProduct y lo asigna a los controles de UIKit (tenga en cuenta que se muestre el `LocalizedTitle` y `LocalizedDescription` – StoreKit resuelve automáticamente el texto correcto y precios basados en configuración de cuenta del usuario). El siguiente código pertenece a la notificación que hemos creado anteriormente:

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

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Métodos SKProductRequestDelegate (InAppPurchaseManager)

El `RequestProductData` se llama al método cuando la aplicación que desea recuperar los precios de productos y otra información. Analiza la colección de identificadores de producto en el tipo de datos correcto a continuación, se crea un `SKProductsRequest` con esa información. Llame al método Start hace que una solicitud de red para servidores de Apple. La solicitud se ejecutará de forma asincrónica y llamar a la `ReceivedResponse` método del delegado al que se ha completado correctamente.

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

iOS enrutará automáticamente la solicitud a la versión de "espacio aislado" o "producción" de la Store de la aplicación dependiendo de qué perfil de aprovisionamiento que se ejecuta la aplicación, por lo que al desarrollar o probar la aplicación, la solicitud tendrá acceso a todos los productos configurada en iTunes Connect (incluso aquellos que aún no se han enviado o aprobado por Apple). Cuando la aplicación está en producción, las solicitudes de StoreKit solo devolverá información para **aprobado** productos.   
   
   
   
 El `ReceivedResponse` invalidado método se llama después de que los servidores de Apple han respondido con datos. Dado que esto se denomina en segundo plano, el código debe analizar los datos válidos y usar una notificación para enviar la información del producto a cualquier ViewControllers que estén "escuchando' para dicha notificación. El código para recopilar información de producto válida y enviar una notificación se muestra a continuación:

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

Aunque no se muestra en el diagrama, el `RequestFailed` método también se debe invalidar para que pueda proporcionar algunos comentarios al usuario en caso de los servidores de App Store no son accesibles (o se produce algún otro error). Simplemente escribe el código de ejemplo en la consola, pero es posible que desee consultar a una aplicación real `error.Code` propiedad e implementar un comportamiento personalizado (por ejemplo, una alerta al usuario).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Esta captura de pantalla muestra la aplicación de ejemplo inmediatamente después de cargar (cuando la información de producto no está disponible):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "La aplicación de ejemplo inmediatamente después de cargar cuando la información de producto no está disponible")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Productos no válidos

Un `SKProductsRequest` también pueden devolver una lista de identificadores de producto no válida. Normalmente, se devuelven productos no válidos debido a uno de los siguientes:   
   
   
   
 **Id. de producto se ha escrito incorrectamente** – se aceptan sólo los identificadores de producto válida.   
   
 **No se ha aprobado producto** – durante las pruebas, se deben devolver todos los productos que se borran para su venta por un `SKProductsRequest`; sin embargo, en producción se devuelven solo los productos de aprobado.   
   
 **Id. de aplicación no es explícito** – Wildcard App IDs (con un asterisco) no se permiten en la aplicación de compras.   
   
 **Incorrecta de perfil de aprovisionamiento** : si realiza cambios en la configuración de la aplicación en el portal de aprovisionamiento (por ejemplo, para habilitar la adquisición de la aplicación), no olvide volver a generar y usar el perfil de aprovisionamiento correcto al compilar la aplicación.   
   
 **contrato de aplicaciones de pago de iOS no está en su lugar** – StoreKit características no funcionarán en absoluto a menos que haya un contrato válido para la cuenta de desarrollador de Apple.   
   
 **El archivo binario está en el estado rechazado** : si hay un archivo binario enviado anteriormente en el estado rechazado (ya sea por el equipo de App Store, o por el desarrollador), a continuación, StoreKit características no funcionarán.

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

Los niveles de precios especifican un precio específico para cada producto a lo largo de todas las tiendas de aplicaciones internacionales. Para asegurarse de que se muestran correctamente los precios de cada divisa, utilice el siguiente método de extensión (definido en `SKProductExtension.cs`) en lugar de la propiedad de precio de cada `SKProduct`:

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

El código que establece el título del botón usa el método de extensión como esta:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

Uso de dos cuentas de prueba de iTunes diferentes (uno para el almacén de American) y otro para el almacén de japonés da como resultado las capturas de pantalla siguiente:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Mostrando resultados específicos de idioma de cuentas de prueba de dos iTunes diferentes")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 Tenga en cuenta que el almacén afecta al idioma que se usa para la divisa de información y el precio del producto, mientras que las etiquetas y otros contenidos localizados afecta a la configuración de idioma del dispositivo.   
   
   
   
 Recuerde que para usar un almacén diferente probar cuenta debe **cerrar sesión** en el **configuración > iTunes y App Store** y vuelva a iniciar la aplicación para iniciar sesión con una cuenta diferente. Para cambiar de idioma del dispositivo, vaya a **configuración > General > internacional > idioma**.

