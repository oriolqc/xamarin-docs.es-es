---
title: Compra de productos no consumible en Xamarin.iOS
description: Este documento describe los productos no consumible en Xamarin.iOS, que son características adquiridas por un usuario que siguen estando disponibles de forma indefinida, independientemente del dispositivo.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: db193493ea11a7963fe5fc57f0c45978c26a7a74
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786956"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Compra de productos no consumible en Xamarin.iOS

No consumible productos son 'propiedad' por el cliente. La expectativa es que siempre tendrán acceso a ellos, incluso si su dispositivo se pierda o le roben o adquirir una nueva. Son útiles para libros, artículos de la revista, niveles de juegos, filtros de foto, 'características pro', etcetera. Una vez que un usuario ha comprado un producto no consumible, nunca tienen que pagar por él nuevamente. Si el código accidentalmente les permite probar, StoreKit mostrará un mensaje que ya se ha comprado.

## <a name="non-consumable-products-sample"></a>Ejemplo de productos no consumible

El [InAppPurchaseSample código](https://developer.xamarin.com/samples/monotouch/StoreKit/) contiene un proyecto denominado *NonConsumables*. El ejemplo de código muestra cómo implementar productos no consumible utilizando filtros de fotografías como ejemplo. Una vez que haya adquirido un filtro puede aplicarla a la foto y otra vez. Nunca debe volver a comprarlos.   
   
   
   
 El proceso de compra se muestra en la siguiente serie de capturas de pantalla: el **comprar** botón se convierte en el botón de activación de características:   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "El proceso de compra se muestra en la siguiente serie de capturas de pantalla")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 El proceso de compra es el mismo que un producto consumible; la diferencia clave está en cómo se realiza el seguimiento de la compra en el código de aplicación. En este ejemplo que el botón comprar solo está disponible si ya no se ha comprado el producto, en caso contrario, activa el botón la propia característica.   
   
   
   

El siguiente diagrama muestra las interacciones entre las clases y el servidor de la tienda de aplicaciones para realizar una compra no consumible producto:   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "Compra de las interacciones entre las clases y el servidor de la tienda de aplicaciones para realizar un producto no consumible")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 La principal diferencia del ejemplo consumibles es que, una vez completada la compra se actualiza la interfaz de usuario para evitar que vuelva a adquirir. En este ejemplo, la notificación de una transacción correcta actualiza la interfaz de usuario para que la **comprar** botón se convierte en un botón que activa la característica de sí mismo.

## <a name="re-purchasing-non-consumable-products"></a>Volver a comprar productos no consumible

El código normalmente debe ocultar o cambiar la finalidad de un botón de compra una vez que el producto se ha adquirido correctamente, para evitar que el usuario ha intentado adquirir el producto de nuevo. La aplicación de ejemplo para ello, cambie la **comprar** botón en el botón que hace que el filtro de fotografía de ejemplo funcione.   
   
   
   
 Hay situaciones donde una aplicación no puede saber si se ha comprado un producto no consumible:

-  Si una aplicación se elimina y vuelve a instalar en un dispositivo, se perderán todos los registros de compra (a menos que / hasta que el usuario realiza una restauración de copia de seguridad). 
-  Si el usuario tiene la aplicación se instala en dispositivos de dos (o más) y realiza una compra en uno de los dispositivos. Los demás dispositivos seguirán mostrando el producto disponible para su compra. 
-  Si un cliente intenta volver a comprar un producto no consumible en estas situaciones, la tienda de aplicaciones completará el producto nuevo sin cargo alguno. Para realizar una compra aparecerá inicialmente la interfaz de usuario (por ejemplo, se muestra una alerta de confirmación y el ID de Apple será necesario) sin embargo el usuario, a continuación, verá un mensaje que les informa de que se ha comprado el producto.  
   
   
   
 La ruta de acceso del código en este escenario es exactamente igual que una compra normal, las únicas diferencias son:

-  El usuario no se le cobre nuevo para el producto.
-  El `SKPaymentTransaction` objeto pasa a la aplicación tendrá un `OriginalTransaction` propiedad que hace referencia a la transacción que se genera cuando el producto se compró inicialmente. 
-  Las aplicaciones que venden productos no consumible también deben implementar del StoreKit **restaurar** característica para ayudar a los usuarios recuperar compras existentes. 
