---
title: Conceptos básicos de la compra en la aplicación y la configuración de Xamarin.iOS
description: Este documento describe las compras en la aplicación de Xamarin.iOS, analizar información relevante acerca de iTunes Connect, la configuración y reglas.
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 267dac5b6aec263f1d8b69d81f34f732118c1802
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61407230"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Conceptos básicos de la compra en la aplicación y la configuración de Xamarin.iOS

Implementación de compras de la aplicación requiere que la aplicación para usar la API StoreKit en el dispositivo. StoreKit administra toda la comunicación con servidores de iTunes de Apple para obtener información de producto y realizar transacciones. El perfil de aprovisionamiento debe estar configurado para la compra de la aplicación y la información del producto debe especificarse en iTunes Connect.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit administra toda la comunicación con Apple, tal como se muestra en este gráfico")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

Mediante la aplicación de Store para proporcionar compras requiere la instalación y la configuración siguiente:

-  **iTunes Connect** : configuración de los productos a vender y configurar cuentas de usuario de espacio aislado para probar la compra. Debe haber ofrecido también la información de impuestos y banca a Apple por lo que pueden remitir fondos recopilados en su nombre.
-   **Portal de aprovisionamiento de iOS** : creación de un identificador de lote y habilitar el acceso de la App Store de la aplicación.
-  **Store Kit** : agregar código a la aplicación para mostrar productos, compra de productos y restaurar las transacciones.
-  **Código personalizado** : lo ayuda a realizar un seguimiento de las compras realizadas por los clientes y proporcionar los productos o servicios que han comprado. Es posible que también debe implementar un proceso de servidor para validar las confirmaciones si sus productos constan de descarga contenido desde un servidor (por ejemplo, libros y artículos de la revista).


Hay dos Store Kit "entornos de servidor":

-  **Producción** : las transacciones con dinero real. Solo es accesible a través de las aplicaciones que se han enviado y aprobado por Apple. Productos de compra en la aplicación también deben revisar y aprobar para que estén disponibles en el entorno de producción.
-  **Espacio aislado** : donde ocurre la prueba. Productos aquí están disponibles inmediatamente después de crear (el proceso de aprobación solo se aplica al entorno de producción). Las transacciones en el espacio aislado de exigir que los usuarios de prueba (ID de Apple no real) realizar transacciones.

## <a name="in-app-purchase-rules"></a>Reglas de compra en la aplicación

No se puede aceptar otras formas de pago para productos o servicios digitales dentro de la aplicación, ni mencionarlo o hacer referencia a los usuarios a ellos desde dentro de una aplicación. Esto significa que no puede aceptar tarjetas de crédito o PayPal cuando compra en la aplicación es el mecanismo más adecuado del pago. Hay un caso especial para la compra de productos digitales fuera de la aplicación, pero para usar en la aplicación, como comprar los libros en pantalla en un sitio Web que están asociados con un determinado "login" y el uso que "inicio de sesión" en la aplicación permite al usuario acceso de los libros comprados.
No se permiten aplicaciones que funcionan de esta manera mencionar o vincular a la característica de compra externo: los desarrolladores deben comunicar esta funcionalidad a los usuarios de otras maneras (quizás a través de marketing por correo electrónico o algún otro canal directo).

Sin embargo, puesto que no se puede utilizar en la aplicación adquiere bienes físicos, en que el caso de que se permite usar un mecanismo de pago alternativo (p ej. tarjeta de crédito, PayPal) desde dentro de la aplicación.

Apple debe aprobar todos los productos antes de que deja de venta: el nombre, descripción y una captura de pantalla de la 'product' es necesario para su revisión. Tiempos de revisión de producto son las mismas que para las revisiones de aplicaciones.

No se puede elegir cualquier precio del producto: solo se puede seleccionar un nivel de precios' ' que tiene un valor específico en cada país/moneda es compatible con Apple. No puede tener un nivel de precio diferente en diferentes mercados.

## <a name="configuration"></a>Configuración

Antes de escribir ningún código de compra de la aplicación debe realizar algún trabajo de instalación en iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) y el Portal de aprovisionamiento de iOS ( [developer.apple.com/iOS](https://developer.apple.com/iOS)).

Estos tres pasos deben completas antes de escribir ningún código:

-  **Cuenta de desarrollador de Apple** : envíe la información bancaria y fiscal a Apple.
-  **Portal de aprovisionamiento de iOS** : asegúrese de que la aplicación tiene un identificador de aplicación válido (no un carácter comodín con un asterisco * en él) y se habilitó en compras desde la aplicación.
-  **iTunes Connect Application Management** : agregar productos a la aplicación.


### <a name="apple-developer-account"></a>Cuenta de desarrollador de Apple

Creación y distribución de aplicaciones gratuitas requieren muy poca configuración en [iTunes Connect](https://itunesconnect.apple.com); sin embargo, para vender pago aplicaciones o la adquisición de la aplicación requiere que proporcione Apple con la información bancaria y fiscal. Haga clic en **contratos, impuestos y banca** desde el menú principal se muestra aquí:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Haga clic en los contratos, impuestos y banca en el menú principal")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

La cuenta de desarrollador debe tener un **aplicaciones de iOS de pago** contrato vigente, como se muestra en esta captura de pantalla:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "La cuenta de desarrollador debe tener un aplicaciones de pago de contrato en vigor de iOS")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

No podrá probar cualquier funcionalidad StoreKit hasta que haya un **aplicaciones de iOS de pago** contrato: StoreKit llamadas en el código producirá un error hasta que Apple haya procesado el **contratos, impuestos y banca** información.

### <a name="ios-provisioning-portal"></a>Portal de aprovisionamiento de iOS

Nuevas aplicaciones se configuran en el **identificadores de aplicación** sección de la **Portal de aprovisionamiento de iOS**. Para crear un nuevo identificador de aplicación, vaya a la [centro de usuarios registrados del Portal de aprovisionamiento de iOS](https://developer.apple.com/membercenter/index.action), vaya a **certificados, identificadores y perfiles** sección del Portal y haga clic en **identificadores** en *aplicaciones iOS*. A continuación, haga clic en "+" en la parte superior derecha para generar un nuevo identificador de aplicación.


El formulario para crear nuevas **identificadores de aplicación**

 este aspecto:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "El formulario de creación de nuevos identificadores de aplicación")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Escriba un valor apropiado para el *descripción*, para que pueda identificar fácilmente este identificador de aplicación en una lista. Para el *prefijo de Id. de aplicación*, seleccione el identificador de equipo.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Formato de paquete de sufijo de Id. de aplicación o identificador

Puede utilizar cualquier cadena que desee como su **identificador de paquete** (siempre y cuando sea único en su cuenta); sin embargo, Apple recomienda que siga el formato de DNS inversa en lugar de utiliza cualquier cadena arbitraria. La aplicación de ejemplo que acompaña este artículo usa com.xamarin.storekit.testing para el identificador de paquete, sin embargo, sería válido igualmente para usar un identificador como my_store_example (aunque Apple no recomendable).

> [!IMPORTANT]
> Apple también permite el comodín de asterisco para agregarse al final de un **identificador de paquete** para que un identificador de aplicación solo puede usarse para varias aplicaciones, sin embargo _identificadores de aplicación comodín no se puede usar paraenAppPurchase_. Un ejemplo de identificador de paquete comodín podría ser com.xamarin.*

#### <a name="enabling-app-services"></a>Habilitar servicios de aplicación

Tenga en cuenta que **compra en la aplicación** se habilitarán automáticamente en la lista de servicios:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "Compra en la aplicación se habilitarán automáticamente en la lista de servicios")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Perfiles de aprovisionamiento

Crear perfiles de aprovisionamiento de producción y de desarrollo como normalmente haría, seleccionar el identificador de aplicación que ha configurado para la compra de la aplicación. Hacer referencia a la [iOS Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) y [publicar en la aplicación de Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) guías para obtener más información.

## <a name="itunes-connect"></a>iTunes Connect

Haga clic en **mis aplicaciones** en iTunes Connect para crear o editar una entrada de la aplicación de iOS. La página de información general de la aplicación se muestra aquí:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "La página de información general de la aplicación")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Haga clic en **compras de la aplicación** para crear o editar sus productos para su venta. Esta captura de pantalla muestra la aplicación de ejemplo con varios productos que ya ha agregado:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "La aplicación de ejemplo con varios productos ya agregado")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

El proceso para agregar nuevos productos consta de dos pasos:

1.   Elija el tipo de producto: [![](in-app-purchase-basics-and-configuration-images/image8.png "Elija el tipo de producto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   Escriba los atributos del producto, incluidos el identificador de producto, plan de tarifa y descripciones localizadas: [![](in-app-purchase-basics-and-configuration-images/image9.png "Escribir los atributos de productos")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

Los campos requeridos para cada producto de compra en la aplicación se describen a continuación:


### <a name="reference-name"></a>Nombre de referencia

El nombre de referencia no se mostrará a los usuarios; es para uso interno y solo aparece en iTunes Connect.

### <a name="product-id-format"></a>Formato de Id. de producto

Un identificador de producto solo puede contener alfanuméricos (A-z, a-z, 0-9), caracteres de subrayado (_) y punto (.). Aunque puede utilizar cualquier cadena para los identificadores, Apple recomienda el formato de DNS inversa. Por ejemplo, la aplicación de ejemplo usa este identificador de paquete:

 `com.xamarin.storekit.testing`

Por lo tanto, la convención para identificar los productos de compra en la aplicación sería como sigue:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

No se aplica esta convención de nomenclatura, simplemente una recomendación que le ayudarán a administrar sus productos. Además, a pesar de seguir la misma convención de DNS inversa, los identificadores de producto son *no relacionado con* para el identificador de paquete y son no debe para comenzar con la misma cadena. Todavía sería válido utilizar identificadores como photo_product_greyscale (aunque Apple no recomendable).

Id. de producto no se mostrará a los usuarios, pero se usa para hacer referencia a producto en el código de aplicación.

### <a name="product-type"></a>Tipo de producto

Hay cinco tipos de producto de compra en la aplicación que puede ofrecer:

1.  **Consumibles** : cosas que son 'usa seguridad de', como moneda en el juego que pueda dedicar el Reproductor. Si el usuario hace una copia de seguridad/restauración o si no actualizó su dispositivo, una transacción consumible no obtener restaurada (que efectivamente se daría a las mismas ventajas de nuevo el Reproductor). Código de la aplicación debe ser seguro proporcionar el elemento consumible tan pronto como finalice la transacción.
1.  **No puede consumir** – productos que el usuario 'posee' una vez adquiridos, como un problema de la revista digital o un nivel de juego.
1.  **Puede renovar automáticamente suscripciones** , tan sólo como suscripción al magazine reales, al final del período de suscripción Apple automáticamente cobra el cliente nuevo y amplía el período de suscripción indefinidamente o hasta que el cliente de forma explícita la cancela. Este es el método de pago preferido para las aplicaciones de quiosco (de hecho, las aplicaciones deben admitir este método de pago que se aprueben para la distribución de Newsstand).
1.  **Suscripción gratuita** : solo se pueden ofrecer en aplicaciones habilitadas para Newsstand y permite al cliente para tener acceso al contenido de suscripción en todos sus dispositivos. Las suscripciones gratuitas no expiren nunca.
1.  **Suscripción de renovación de no** : se debe usar para vender el acceso de tiempo limitado para contenido estático, como el acceso a un archivo de fotos de un mes.


 *Actualmente, este documento cubre solo la primera tipos de producto de dos (consumibles y no puede consumir).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Niveles de precios

Store de la aplicación no le permiten elegir un precio arbitrario para sus productos, Apple proporciona niveles de precio fijo que puede elegir. Los precios se han corregido en cada divisa y Apple reserva el derecho para ajustar los precios relativos (por ejemplo, después de un cambio constante en la tasa de cambio de divisa extranjera relativa entre una moneda concreta y el dólar estadounidense).

Apple proporciona una matriz de precios para ayudarle a seleccionar el nivel correcto para la moneda de precio que desee. Aquí se muestra un extracto de la matriz de precio (agosto de 2012):

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Un extracto de la matriz de precios de agosto de 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

En el momento de redactar (junio de 2013) hay 87 niveles de USD 0,99 a 999,99 USD. La matriz de precios muestra el precio que los clientes pagarán y también la cantidad que se recibirán de Apple: Esto es menos su cargo del 30% y también los impuestos locales son necesarios para recopilar (Observe que en el ejemplo que los vendedores de Estados Unidos y Canadá recibir 70c para un 99 p c producto, mientras que Australia vendedores reciben solo 63 c debido a ' bienes &amp; servicios impuestos ' percibidos sobre el precio de venta).

Precios de su producto pueden actualizarse en cualquier momento, incluso los cambios de precio programado que surten efecto en una fecha futura. Esta captura de pantalla muestra cómo se agrega un cambio de precio con una fecha futura: el precio se temporalmente cambia de nivel 1 al nivel 3 para el mes de septiembre solo:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Un cambio de precio con una fecha futura, donde el precio se temporalmente cambia de nivel 1 al nivel 3 para el mes de septiembre solo")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Productos gratuitos no compatibles

Aunque Apple ha proporcionado una opción de suscripción gratuita especial para las aplicaciones de quiosco, no es posible establecer un precio cero (gratis) para cualquier otro tipo de compra en la aplicación. Aunque puede editar (ie. inferior) los precios de promociones de ventas, no podrá realizar compras de la aplicación 'free' a través de iTunes Connect.

### <a name="localization"></a>Localización

Puede escribir texto de nombre y una descripción diferente para cualquier número de idiomas admitidos en iTunes Connect. Cada lenguaje puede ser agregar/editar en mediante un ventana emergente:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Cada lenguaje puede ser agregar/editar en a través de un elemento emergente")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 Cuando se muestra información de producto en la aplicación, está disponible para que pueda mostrar a través de StoreKit el texto localizado. También se debe localizar la presentación de moneda para mostrar el símbolo correcto y el formato decimal: este formato se trata más adelante en el documento.

### <a name="app-store-review"></a>Revisión del App Store

Igual que las aplicaciones: cada producto es revisado por Apple antes de que se puede estar en venta. Productos pueden ser rechazados por contenido inadecuado en el nombre o la descripción o Apple puede decidir que ha elegido el tipo de un producto incorrecto (p ej. ha creado un libro o un problema de revista pero usa el tipo de producto consumibles). Reseñas de productos pueden tardar hasta una revisión de aplicación.

La primera vez que se envía una aplicación con compras habilitado (si es una nueva aplicación o la funcionalidad se ha agregado a uno existente) también debe elegir algunos productos para enviar con él. El portal de iTunes Connect le pedirá que lo haga, como se muestra en esta captura de pantalla:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "El portal de iTunes Connect le pedirá que envíe también algunos productos")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 La aplicación y las compras de la aplicación se revisará juntos, para que todos ellos se aprueban a la vez (por lo que la aplicación no pasa en el almacén sin ningún producto aprobada!).

Una vez aprobada su primera versión con capacidad de compra en la aplicación, puede agregar más productos y enviarlos para su revisión en cualquier momento. También puede elegir enviar una nueva versión junto con productos de compra en la aplicación específica, mediante el **detalles de la versión** página tal como sugiere el símbolo del sistema.

Hacer referencia a la [directrices de revisión del App Store](https://developer.apple.com/appstore/guidelines.html) para obtener más información.

 [Parte 2: Introducción al Kit de Store e información de productos de recuperación de la configuración](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
