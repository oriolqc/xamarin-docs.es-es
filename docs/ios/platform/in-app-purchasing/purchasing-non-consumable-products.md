---
title: Compra de productos no consumibles en Xamarin.iOS
description: Este documento describe los productos no consumibles en Xamarin.iOS, que son características que haya comprado un usuario que siguen estando disponibles de forma indefinida, independientemente del dispositivo.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 060403baf8ac28b9b160632a01471b9828735069
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61403223"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Compra de productos no consumibles en Xamarin.iOS

Productos no consumibles son 'propiedad' por el cliente. La expectativa es que siempre tendrán acceso a ellos, incluso si su dispositivo se pierda o le roben o comprar uno nuevo. Son útiles para los libros en pantalla, artículos de la revista, niveles de juego, filtros de foto, 'características de pro', etcetera. Una vez que un usuario ha comprado un producto no consumibles, nunca debe pagar por él nuevamente. Si el código accidentalmente les permite intentar, StoreKit mostrará un mensaje que ya se ha adquirido.

## <a name="non-consumable-products-sample"></a>Ejemplo de productos no consumibles

El [InAppPurchaseSample código](https://developer.xamarin.com/samples/monotouch/StoreKit/) contiene un proyecto denominado *NonConsumables*. El ejemplo de código muestra cómo implementar productos no consumibles con filtros de foto como ejemplo. Una vez que haya adquirido un filtro puede aplicarla a la foto y otra vez. Nunca debe volver a adquirirlo.   
   
   
   
 El proceso de compra se muestra en esta serie de capturas de pantalla: el **comprar** botón se convierte en el botón de activación de características:   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "El proceso de compra se muestra en esta serie de capturas de pantalla")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 El proceso de compra es el mismo que un producto consumible; la diferencia clave está en cómo se realiza el seguimiento de la compra en el código de aplicación. En este ejemplo en que el botón comprar solo está disponible si ya no se adquirió el producto, en caso contrario, activa el botón la propia característica.   
   
   
   

El siguiente diagrama muestra las interacciones entre las clases y el servidor de App Store para realizar una compra de productos no consumibles:   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "Compra de las interacciones entre las clases y el servidor de App Store para realizar un productos no consumibles")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 La diferencia clave en el ejemplo consumible es que una vez completada la compra se actualiza la interfaz de usuario para evitar volver a adquirir. En este ejemplo, la notificación de una transacción correcta actualiza la interfaz de usuario para que la **comprar** botón se convierte en un botón que activa la característica de sí mismo.

## <a name="re-purchasing-non-consumable-products"></a>Volver a comprar productos no consumibles

El código normalmente debe ocultar o reasignar un botón de compra una vez que el producto se correctamente adquirió, para evitar que el usuario intenta adquirir el producto nuevo. La aplicación de ejemplo para ello, cambie el **comprar** botón en el botón que hace que el filtro de la foto de ejemplo funcione.   
   
   
   
 Existen situaciones donde una aplicación no puede saber si ya se ha adquirido un producto no consumibles:

-  Si una aplicación se elimina y vuelve a instalar en un dispositivo, desaparecerán todos los registros de compra (a menos que / hasta que el usuario realiza una restauración de copia de seguridad). 
-  Si el usuario tiene la aplicación se instala en dispositivos de dos (o más) y realiza una compra en uno de los dispositivos. Los demás dispositivos continuará mostrar los productos disponibles para su compra. 
-  Si un cliente intenta volver a comprar un producto no consumibles en estas situaciones, la aplicación de Store, cumplirá con el producto nuevo sin cargo alguno. Para realizar una compra aparecerá inicialmente la interfaz de usuario (por ejemplo, se muestra una alerta de confirmación y será necesario el identificador de Apple) pero el usuario verá un mensaje que les advierte que ya se ha adquirido el producto.  
   
   
   
 La ruta de acceso del código en este escenario es exactamente igual que una compra regular, las únicas diferencias son:

-  El usuario no se le cobra nuevo para el producto.
-  El `SKPaymentTransaction` objeto pasa a la aplicación tendrá un `OriginalTransaction` propiedad que hace referencia a la transacción que se generó cuando inicialmente se adquirió el producto. 
-  Las aplicaciones que venden productos no consumibles también deben implementar del StoreKit **restaurar** característica para ayudar a los usuarios a recuperar las compras existentes. 
