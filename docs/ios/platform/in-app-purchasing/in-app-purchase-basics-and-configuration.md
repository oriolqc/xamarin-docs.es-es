---
title: "Conceptos básicos de compra en la aplicación y configuración"
ms.topic: article
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: b622da2d6d9777fc200a091ccf003369b1c2e8d1
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2018
---
# <a name="in-app-purchase-basics-and-configuration"></a>Conceptos básicos de compra en la aplicación y configuración

Implementación de compras en la aplicación requiere que la aplicación para usar la API de StoreKit en el dispositivo. StoreKit administra toda la comunicación con los servidores de iTunes de Apple para obtener información del producto y realizar transacciones. El perfil de aprovisionamiento debe configurarse para la compra de la aplicación y la información del producto debe especificarse en iTunes Connect.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit administra toda la comunicación con de Apple, tal y como se muestra en este gráfico")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

El uso de la tienda de aplicaciones para proporcionar la compra de la aplicación requiere la instalación y la configuración siguientes:

-  **iTunes Connect** : configuración de los productos que venden y configuración de cuentas de usuario de espacio aislado para probar la compra. Debe, también ha proporcionado la información bancaria e impuestos a Apple por lo que pueden remitir fondos recopilados en su nombre.
-   **Portal de aprovisionamiento de iOS** : crear un identificador de paquete y habilitar el acceso de la tienda de aplicaciones para la aplicación.
-  **Almacenar Kit** : agregar código a la aplicación para mostrar productos, compra de productos y restaurar las transacciones.
-  **Código personalizado** : para realizar un seguimiento de las compras realizadas por los clientes y proporcionar los productos o servicios que han adquirido. También puede necesita implementar un proceso de servidor para validar confirmaciones si sus productos constan de descarga del contenido desde un servidor (por ejemplo, libros y artículos de la revista).


Hay dos Kit Store "entornos de servidor":

-  **Producción** : las transacciones con dinero real. Solo es accesible a través de las aplicaciones que se han enviado y aprobado por Apple. En la aplicación de compra productos también deben revisar y aprobar para que estén disponibles en el entorno de producción.
-  **Espacio aislado** : cuando las pruebas se produce. Productos aquí están disponibles inmediatamente después de crear (el proceso de aprobación solo se aplica al entorno de producción). Las transacciones en el espacio aislado exigir que los usuarios de prueba (ID de Apple no real) realizar transacciones.

## <a name="in-app-purchase-rules"></a>Reglas de compra en la aplicación

No puede aceptar otras formas de pago para los productos o servicios digitales dentro de la aplicación, ni mencionar ellos o hacer referencia a los usuarios a ellos desde dentro de una aplicación. Esto significa que no puede aceptar tarjetas de crédito o PayPal cuando compra en la aplicación es el mecanismo más adecuado de pago. Hay un caso especial para las compras de productos digitales fuera de la aplicación, pero para utilizar en la aplicación, como comprar libros en un sitio Web que están asociados a un determinado "login" y el uso que "inicio de sesión" en la aplicación permite al usuario acceder los libros adquiridos.
Las aplicaciones que funcionan de esta forma no se permiten para mencionar o vincular a la función de compra externa, los desarrolladores deben comunicar esta capacidad a los usuarios de otras maneras (quizás a través de marketing por correo electrónico o algún otro canal directo).

Sin embargo, ya que no se puede utilizar en compras desde la aplicación de mercancías físicas, en que el caso de que se le permite utilizar un mecanismo de pago alternativo (p. ej. tarjeta de crédito, PayPal) desde dentro de la aplicación.

Apple debe aprobar todos los productos antes de que entre en la venta: el nombre, descripción y una captura de pantalla de 'product' es necesario para su revisión. Horas de revisión de producto son los mismos que las revisiones de aplicación.

No puede elegir cualquier precio del producto: solo se puede seleccionar un nivel de precios' ' que tiene un valor específico en cada país/moneda admitido por Apple. No puede tener un nivel de precios diferente en distintos mercados.

## <a name="configuration"></a>Configuración

Antes de escribir ningún código de compra de la aplicación debe hacer algún trabajo de instalación en iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) y el Portal de aprovisionamiento de iOS ( [developer.apple.com/iOS](http://developer.apple.com/iOS)).

Estos tres pasos deben completarse antes de escribir ningún código:

-  **Cuenta de desarrollador de Apple** : enviar la información bancaria e impuestos a Apple.
-  **Portal de aprovisionamiento de iOS** : asegúrese de que la aplicación tiene un identificador de aplicación válido (no un carácter comodín con un asterisco * en ella) y se han de compra de aplicación habilitado.
-  **iTunes Connect Application Management** : agregar productos a la aplicación.


### <a name="apple-developer-account"></a>Cuenta de desarrollador de Apple

Crear y distribuir aplicaciones gratuitas requieren muy poca configuración en [iTunes Connect](https://itunesconnect.apple.com); sin embargo, para vender pago aplicaciones o las compras de la aplicación requiere que proporcione Apple con información bancaria y de impuestos. Haga clic en **acuerdos, impuestos y banca** en el menú principal se muestra aquí:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Haga clic en acuerdos, impuestos y banca en el menú principal")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

La cuenta de desarrollador debe tener un **aplicaciones de iOS de pago** en vigor, tal y como se muestra en esta captura de pantalla del contrato:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "La cuenta de desarrollador debe tener un contrato de las aplicaciones de pago en vigor de iOS")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

No podrá probar cualquier funcionalidad de StoreKit hasta que haya un **aplicaciones de iOS de pago** contrato: se producirá un error en la StoreKit llamadas en el código hasta que Apple haya procesado el **contratos, los impuestos y banca** información.

### <a name="ios-provisioning-portal"></a>Portal de aprovisionamiento de iOS

Nuevas aplicaciones se configuran en el **Id. de aplicaciones** sección de la **Portal de aprovisionamiento de iOS**. Para crear un nuevo identificador de aplicación, vaya a la [centro de usuarios registrados del Portal de aprovisionamiento de iOS](https://developer.apple.com/membercenter/index.action), vaya a **certificados, los identificadores y los perfiles de** sección del Portal y haga clic en **identificadores** en *aplicaciones iOS*. A continuación, haga clic en el signo "+" en la parte superior derecha para generar un nuevo identificador de aplicación.


El formulario para crear nuevas **Id. de aplicaciones**

 este aspecto:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "El formulario para crear nuevos identificadores de aplicación")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Introduzca un valor adecuado para la *descripción*, por lo que puede identificar fácilmente este identificador de la aplicación en una lista. Para el *prefijo de Id. de aplicación*, seleccione el identificador de equipo.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Formato de paquete de sufijo de Id. de identificador/aplicación

Se puede utilizar cualquier cadena para su **identificador de paquete** (siempre y cuando sea único en su cuenta), si bien Apple recomienda seguir el formato de DNS inversa en lugar de utilizar cualquier cadena arbitraria. La aplicación de ejemplo que se incluye en este artículo utiliza com.xamarin.storekit.testing para el identificador de paquete, sin embargo, sería igualmente válido para usar un identificador como my_store_example (aunque Apple no recomienda).

> [!IMPORTANT]
> Apple también permite comodines asterisco va a agregar al final de un **identificador de paquete** para que un identificador único de la aplicación puede usarse para varias aplicaciones, sin embargo _no se puede usar el Id. de aplicación de comodines paraenAppPurchase_. Un ejemplo de identificador de paquete de comodines puede com.xamarin.*

#### <a name="enabling-app-services"></a>Habilitar servicios de aplicaciones

Tenga en cuenta que **en la aplicación de compra** se habilitarán automáticamente en la lista de servicios:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "En la aplicación de compra se habilitarán automáticamente en la lista de servicios")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Perfiles de aprovisionamiento

Crear perfiles de aprovisionamiento de producción y desarrollo como normalmente haría, al seleccionar el identificador de aplicación que ha configurado para la compra de la aplicación. Hacer referencia a la [aprovisionamiento de dispositivos de iOS](~/ios/get-started/installation/device-provisioning/index.md) y [publicar en la tienda de aplicaciones](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) guías para obtener más información.

## <a name="itunes-connect"></a>iTunes Connect

Haga clic en **mis aplicaciones** en iTunes Connect para crear o editar una entrada de la aplicación de iOS. La página de información general de la aplicación se muestra aquí:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "La página de información general de la aplicación")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Haga clic en **compras dentro de la aplicación** para crear o editar sus productos para la venta. Esta captura de pantalla muestra la aplicación de ejemplo con varios productos que ha agregado:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "La aplicación de ejemplo con varios productos que ya ha agregado")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

El proceso para agregar nuevos productos consta de dos pasos:

1.   Elija el tipo de producto: [ ![ ] (in-app-purchase-basics-and-configuration-images/image8.png "elegir el tipo de producto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   Escriba los atributos del producto, incluidos el identificador de producto, plan de tarifa y descripciones traducidas: [ ![ ] (in-app-purchase-basics-and-configuration-images/image9.png "escribir los atributos de productos")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

A continuación se describen los campos requeridos para cada producto en la aplicación de compra


### <a name="reference-name"></a>Nombre de referencia

El nombre de referencia no se mostrará a los usuarios; es para uso interno y solo aparece en iTunes Connect.

### <a name="product-id-format"></a>Formato de Id. de producto

Un identificador de producto solo puede contener alfanuméricos (A-z, a-z, 0-9), caracteres de subrayado (_) y caracteres de punto (.). Aunque puede utilizar cualquier cadena para los identificadores, Apple recomienda el formato de DNS inversa. Por ejemplo, la aplicación de ejemplo utiliza este identificador de paquete:

 `com.xamarin.storekit.testing`

Por lo tanto, la convención para identificar los productos en la aplicación de compra sería como sigue:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

No se aplica esta convención de nomenclatura, simplemente una recomendación para ayudarle a administrar sus productos. Además, a pesar de que sigue la misma convención de DNS inversa, los identificadores de producto son *no relacionado con* para el identificador de paquete y que están no debe para comenzar con la misma cadena. Todavía sería válido usar identificadores como photo_product_greyscale (aunque Apple no recomienda).

Id. de producto no se mostrará a los usuarios, pero se utiliza para hacer referencia a producto en el código de aplicación.

### <a name="product-type"></a>Tipo de producto

Hay cinco tipos de producto de compra en la aplicación que puede ofrecer:

1.  **Puede usar** : tareas 'usa una', como moneda en el juego que pueda dedicar el Reproductor. Si el usuario realiza una copia de seguridad/restauración o en caso contrario, se actualiza su dispositivo, una transacción consumible no obtener restaurada (lo que efectivamente se daría a la misma ventaja de nuevo el Reproductor). Código de la aplicación debe ser seguro proporcionar el elemento puede usar en cuanto se completa la transacción.
1.  **No consumible** – productos pertenecientes al usuario '' una vez adquiridos, por ejemplo, un problema de revista digital o un nivel de juego.
1.  **Suscripciones automáticamente renovable** , simplemente como una suscripción revista del mundo real, al final del período de suscripción Apple automáticamente cargos por el cliente nuevo y amplía la vigencia de la suscripción, indefinidamente o hasta que el cliente de forma explícita cancela. Este es el método de pago preferido para las aplicaciones de quiosco (de hecho, las aplicaciones deben admitir este método de pago que se apruebe para la distribución de quiosco).
1.  **Suscripción gratuita a** : solo se pueden ofrecer en aplicaciones habilitadas para quiosco, y permite que el cliente para tener acceso al contenido de suscripción en todos sus dispositivos. Las suscripciones gratuitas no expiren nunca.
1.  **Suscripción de renovación no** : se debe usar para vender tiempo limitado el acceso a contenido estático, como el acceso de un mes en un archivo de almacenamiento de fotos.


 *Este documento cubre actualmente solo el primer tipos de producto de dos (puede reconocer y no consumible).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Niveles de precios

La tienda de aplicaciones no le permiten elegir un precio arbitrario para sus productos: Apple proporciona niveles de precio fijo que puede elegir. Los precios son fijas en cada divisa y Apple reserva el derecho para ajustar los precios relativos (por ejemplo, después de un cambio continuo en el tipo de divisa extranjera relativa entre una moneda concreta y el dólar estadounidense).

Apple proporciona una matriz de precio para ayudarle a seleccionar el nivel adecuado para la moneda/precio que desee. A continuación se muestra un extracto de la matriz de precio (agosto de 2012):

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Un extracto de la matriz de precios de agosto de 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

En el momento de la escritura (junio de 2013) hay 87 niveles de USD 0,99 a 999,99 USD. La matriz de precios muestra el precio que va a pagar los clientes y también la cantidad que se recibirá el mensaje de Apple: Esto es menos el 30% de la carga y también los impuestos locales son necesarios para recopilar (Observe que en el ejemplo que los vendedores de Estados Unidos y Canadá recibir 70c para un 99 p de c nformación, mientras que los vendedores australianos reciben sólo 63 c debido a ' mercancías &amp; servicios impuestos ' percibidas sobre el precio de venta).

Precios de los productos se pueden actualizar en cualquier momento, incluso los cambios de precio programado que surten efecto en una fecha futura. Esta captura de pantalla muestra cómo se agrega un cambio de precio con una fecha futura: el precio se temporalmente cambia de nivel 1 al nivel 3 del mes de septiembre solo:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Un cambio de precio con una fecha futura donde el precio se temporalmente cambia de nivel 1 al nivel 3 del mes de septiembre solo")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Productos gratuitos no compatibles

Aunque Apple ha proporcionado una opción de suscripción gratuita especial para las aplicaciones de quiosco, no es posible establecer un precio cero (gratis) para cualquier otro tipo de compra en la aplicación. Aunque puede editar (ie. inferior) los precios para promociones de ventas, no se puede realizar compras desde la aplicación 'free' a través de iTunes Connect.

### <a name="localization"></a>Localización

En iTunes Connect puede escribir texto diferentes de nombre y una descripción para cualquier número de idiomas admitidos. Cada idioma puede ser agregar/editar en mediante un ventana emergente:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Cada idioma puede ser agregar/editar en a través de un menú emergente")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 Cuando se muestra información de producto de la aplicación, el texto localizado está disponible para mostrar a través de StoreKit. También se debe localizar la visualización de moneda para mostrar el símbolo correcto y el formato decimal: este formato se trata más adelante en el documento.

### <a name="app-store-review"></a>Revisión de la tienda de aplicaciones

Igual que las aplicaciones: cada producto se revisa Apple antes de que se puede estar en la venta. Productos pueden ser rechazados por contenido inadecuado en el nombre o la descripción o Apple puede decidir que ha elegido el tipo de producto incorrecto (p. ej. ha creado un libro o un problema revista pero usa el tipo de producto compatible). Las revisiones del producto pueden tardar tanto como una revisión de aplicación.

La primera vez que se envía una aplicación con la compra de la aplicación habilitada (si se trata de una aplicación nueva o se ha agregado la funcionalidad a otro existente) también debe elegir algunos productos para enviar con él. El portal de iTunes Connect le solicitará que para hacer esto, tal y como se muestra en esta captura de pantalla:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "El portal de iTunes Connect le pedirá que envíe también algunos productos")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 La aplicación y las compras en la aplicación se revisarán juntos, por lo que todas ellas obtener la aprobación a la vez (por lo que la aplicación no funciona en el almacén sin los productos aprobados!).

Una vez aprobada la primera versión con capacidad de compra de la aplicación, puede agregar más productos y enviarlos para su revisión en cualquier momento. También puede elegir enviar una nueva versión junto con productos de compra específico de la aplicación, utilizando el **detalles de la versión** páginas como sugiere el símbolo del sistema.

Hacer referencia a la [directrices de revisión de App Store](https://developer.apple.com/appstore/guidelines.html) para obtener más información.

 [Parte 2: Introducción al Kit de almacén y recuperar información](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
