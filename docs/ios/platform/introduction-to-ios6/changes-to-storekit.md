---
title: Cambios en StoreKit en iOS 6
description: 'iOS 6 introduce dos cambios en la API del Kit de almacén: la capacidad para mostrar iTunes (y la tienda de aplicaciones/iBookstore) opción donde Apple va a hospedar los archivos descargables de compra de productos desde dentro de la aplicación y una aplicación de nuevo. Este documento explica cómo implementar esas características con Xamarin.iOS.'
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: ff717d1e4ea7da947d5534f1ce790b58d84fdfd4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787697"
---
# <a name="changes-to-storekit-in-ios-6"></a>Cambios en StoreKit en iOS 6

_iOS 6 introduce dos cambios en la API del Kit de almacén: la capacidad para mostrar iTunes (y la tienda de aplicaciones/iBookstore) opción donde Apple va a hospedar los archivos descargables de compra de productos desde dentro de la aplicación y una aplicación de nuevo. Este documento explica cómo implementar esas características con Xamarin.iOS._

Los cambios principales al almacén Kit en iOS6 son estas dos nuevas características:

-   **Visualización de contenido de la aplicación y compras** : los usuarios pueden comprar y descargar aplicaciones, música, libros y otros iTunes contenido sin salir de la aplicación. También puede vincular a sus propias aplicaciones promover compras o simplemente fomentar las revisiones y las clasificaciones. 
-   **En la aplicación de compra hospedan contenido** – Apple almacenará y entregar el contenido asociado con sus productos en la aplicación de compra, lo que elimina la necesidad de un servidor independiente hospedar los archivos, automáticamente admite descarga en segundo plano y le permite escribir menos código. 


Es recomendable que se lea este documento junto con la existente Xamarin.iOS [en la aplicación de compra](~/ios/platform/in-app-purchasing/index.md) documentación.

## <a name="requirements"></a>Requisitos

Las características de almacén Kit descritas en este documento requieren iOS 6 y 4.5 de Xcode, junto con Xamarin.iOS 6.0.


## <a name="in-app-content-display--purchasing"></a>Visualización del contenido de la aplicación y compras

La nueva característica de compra en la aplicación en iOS permite a los usuarios ver la información de producto y adquirir o descargar el producto desde dentro de la aplicación.
Anteriormente, las aplicaciones tendría que desencadenar iTunes, la tienda de aplicaciones o el iBookstore que el usuario deja la aplicación original, se crearán. Esta nueva característica devuelve automáticamente al usuario a la aplicación cuando se realizan.

 [![](changes-to-storekit-images/image1.png "Devolver automáticamente a una aplicación después de la compra")](changes-to-storekit-images/image1.png#lightbox)

Hay una serie de escenarios donde esto podría ser útil, incluidos (pero sin limitarse a):

-   **Anima a los usuarios para calificar la aplicación** : puede abrir la página de la tienda de aplicaciones para que el usuario puede valorar y revisar la aplicación sin salir de él. 
-   **Promoción de entre aplicaciones** : permitir al usuario ver otras aplicaciones que se publican con la capacidad de compra/descargar inmediatamente. 
-   **Ayuda a los usuarios buscar y descargar contenido** : ayudar a los usuarios comprar contenido que la aplicación busca, administra o agrega (p. ej. una aplicación de música podría proporcionar una lista de reproducción de canciones y permitir que cada canción a adquirirse desde dentro de la aplicación). 


Una vez el `SKStoreProductViewController` se ha mostrado el usuario puede interactuar con la información de producto como si estuvieran en iTunes, la tienda de aplicaciones o el iBookstore. Esto incluye:

-  Capturas de pantalla de visualización (para aplicaciones),
-  Canciones de muestreo o de vídeo (para música, programas de TV y películas)
-  Revisiones de lectura (y escritura)
-  Comprar y descargar, algo que tiene lugar completamente dentro del controlador de vista y el Kit de almacén. No es necesario proporcionar ningún código adicional para que funcione la aplicación. 


Tenga en cuenta que algunas opciones dentro de la `SKStoreProductViewController` seguirá el forzar al usuario para salir de la aplicación y abra la aplicación de tienda pertinente, por ejemplo, al hacer clic en **productos relacionados** o una aplicación **compatibilidad** vínculo.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

La API para mostrar un producto dentro de cualquier aplicación es muy simple: solo requiere que crear y mostrar un `SKStoreProductViewController`. Siga estos pasos para crear y mostrar un producto:

1.  Crear un `StoreProductParameters` objeto para pasar parámetros para el controlador de vista, incluidos el `productId` en el constructor. 
1.  Crear una instancia de la `SKProductViewController` . Asigne a un campo de nivel de clase. 
1.  Asignar un controlador para el controlador de vista `Finished` evento, que debe desechar el controlador de vista. Se llama a este evento cuando el usuario presiona cancela; o en caso contrario, finaliza una transacción en el controlador de vista. 
1.  Llame a la `LoadProduct` método pasando la `StoreProductParameters` y un controlador de finalización. El controlador de finalización debe comprobar que la solicitud del producto se generó correctamente y si es así, no hay el `SKProductViewController` forma modal. Debe agregarse el tratamiento de errores adecuado en caso de que el producto no se puede recuperar. 

### <a name="example"></a>Ejemplo

El *ProductView* del proyecto en el *StoreKit* código de ejemplo de este artículo implementa un `Buy` método que acepta cualquier producto de los Id. de Apple y muestra el `SKStoreProductViewController`. El código siguiente muestra la información de producto para cualquier Id. de Apple determinado:

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

La aplicación siguiente aspecto cuando se ejecuta: descarga ni la compra se produce su totalidad en la `SKStoreProductViewController`:

 [![](changes-to-storekit-images/image2.png "La aplicación siguiente aspecto cuando se ejecuta")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Compatibilidad con sistemas operativos anteriores

La aplicación de ejemplo incluye código que muestra cómo abrir el almacén de aplicación, iTunes o la iBookstore en versiones anteriores de iOS. Use la `OpenUrl` método para abrir un correctamente diseñado **itunes.com** dirección URL.

Puede implementar una comprobación de versión para determinar qué código se ejecute, similar al siguiente:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax 
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>Errores

El siguiente error se producirá si el identificador de Apple que utiliza no es válido, lo que puede resultar confuso porque implica un problema de red o la autenticación de algún tipo.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Leer documentación Objective-c.

Los desarrolladores información adicional sobre el Kit de almacén en el Portal para desarrolladores de Apple verán un protocolo de [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) : hemos explicado con respecto a esta nueva característica. El protocolo de delegado tiene un solo método: productViewControllerDidFinish: que se haya expuesto como el `Finished` evento en el `SKStoreProductViewController` en Xamarin.iOS.


## <a name="determining-apple-ids"></a>Determinar el Id. de Apple

El identificador de Apple requeridos por la `SKStoreProductViewController` es un *número* (no a se debe confundir con el Id. de lote como "com.xamarin.mwc2012"). Hay varias maneras diferentes, que puede encontrar el identificador de Apple para los productos que desea mostrar, figuran:

### <a name="itunesconnect"></a>iTunesConnect

Para las aplicaciones que se publican, es fácil distinguir la **ID de Apple** en iTunes Connect:

 [![](changes-to-storekit-images/image3.png "Buscar el ID de Apple en iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />


### <a name="search-api"></a>API de búsqueda

Apple proporciona una API de búsqueda dinámica para consultar todos los productos de la tienda de aplicaciones, iTunes y la iBookstore. Obtener información sobre cómo obtener acceso a la API de búsqueda se puede encontrar en [afiliados recursos de Apple](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html), aunque la API se expone a cualquier persona (filiales simplemente no está registrados). Se puede analizar el JSON resultante para detectar la `trackId` que es el identificador de Apple para utilizarlo con `SKStoreProductViewController`.

Los resultados también incluyen otros metadatos incluida la información de presentación y las direcciones URL de material gráfico que pueden usarse para representar el producto de la aplicación.

A continuación se muestran algunos ejemplos:

-   **aplicación de iBooks*- [http://itunes.apple.com/search?term=ibooks&amp; entidad = software&amp;país = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us) 
-   **Punto y el Ibooks canguro*- [http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; entidad = ebook&amp;país = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us) 


### <a name="enterprise-partner-feed"></a>Fuente de socios comerciales de empresa

Apple proporciona aprobados asociados con un volcado de memoria completa de los datos de todos sus productos en forma de archivos sin formato se pueden descargar preparada para la base de datos. Si puede obtener acceso a la [fuente de socio comercial de Enterprise](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html) , a continuación, el identificador de Apple para cualquier producto puede encontrarse en ese conjunto de datos.

Tenga en cuenta que muchos usuarios de la fuente de socios empresariales son miembros de la [Affiliate Program](http://www.apple.com/itunes/affiliates) que permite la Comisión ser ganan en ventas de productos. `SKStoreProductViewController` no se admiten identificadores de afiliados (en el momento de escritura; Esto puede agregarse por Apple en el futuro).

### <a name="direct-product-links"></a>Producto vínculos directos

El identificador de Apple para un producto se puede inferir a partir su vínculo de dirección URL de vista previa de iTunes.
En cualquier iTunes vínculo del producto (para aplicaciones, música o libros) busque la parte de la dirección URL que empieza con `id` y use el número que sigue.

Por ejemplo, es el vínculo directo al iBooks

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

y el identificador de Apple es **364709193**. De forma similar para la aplicación MWC2012, es el vínculo directo

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

y el identificador de Apple es **496963922**.

## <a name="in-app-purchase-hosted-content"></a>En la aplicación de compra contenido hospedado

Si las compras que realice en la aplicación se componen de contenido descargable (por ejemplo, libros u otro medio, carátulas de nivel de juego y configuración u otros archivos grandes), a continuación, estos archivos se usan para insertarse en el servidor web y aplicaciones tenían que incorporar código para descargarlos de forma segura después de compra. En iOS 6 Apple ha introducido una opción que va a hospedar los archivos en sus servidores, eliminando la necesidad de un servidor independiente. La característica solo está disponible para los productos no consumible (no puede reconocer o suscripciones). Ventajas de utilizar el servicio de hospedaje de Apple incluyen:

-  Ahorrar costos de ancho de banda y de hospedaje.
-  Probablemente es más escalable que cualquier host del servidor que usa actualmente. 
-  Menos código para escribir, puesto que no es necesario ningún procesamiento en el servidor de compilación. 
-  Descarga en segundo plano se implementa automáticamente.


Nota: pruebas hospedan contenido de compra en la aplicación de iOS que Simulator no es compatible, por lo que debe probar con un dispositivo real.

### <a name="hosted-content-basics"></a>Conceptos básicos de contenido hospedados

Antes de iOS 6, hay dos formas de proporcionar un producto (se describe con más detalle en [en la aplicación de compra de Xamarin](~/ios/platform/in-app-purchasing/index.md) documentación):

-   **Productos integrados** – características que son "desbloquear" mediante la adquisición, pero que están integrados en la aplicación (ya sea como código o recursos incrustados). Filtros de foto desbloqueados o en el juego de alimentación ups son ejemplos de productos integrados. 
-   **Productos de servidor distribuida** : después de la compra, la aplicación debe descargar contenido desde un servidor que ejecuta. Este contenido se descargan durante la compra, almacenado en el dispositivo y, a continuación, se representa como parte de la entrega del producto. Algunos ejemplos son los libros, artículos de la revista o niveles de juegos que constan de archivos de configuración y de imágenes de fondo. 


En iOS 6 Apple ofrece una variación de productos de servidor distribuida: va a hospedar los archivos de contenido en sus servidores. Esto hace mucho más fácil de compilar productos entregados por el servidor ya que no es necesarias para usar un servidor independiente y Kit de almacén proporciona funcionalidad de descarga en segundo plano que previamente tenía que escribir usted mismo. Para aprovechar las ventajas de hospedaje de Apple, habilitar el hospedaje de contenido para nuevos productos en la aplicación de compra y modifica tu código de almacén Kit para aprovechar las ventajas del mismo. Archivos de contenido del producto, a continuación, están compilados con Xcode y cargados en servidores de Apple para su revisión y versión.

 [![](changes-to-storekit-images/image4.png "El proceso de generación y entrega")](changes-to-storekit-images/image4.png#lightbox)

Usar la tienda de aplicaciones para proporcionar la compra de la aplicación *con contenido hospedado* requiere la instalación y la configuración siguientes:

-   **iTunes Connect** – se *debe* proporcionar la información de banca e impuestos a Apple por lo que pueden remitir fondos recopilados en su nombre. A continuación, puede configurar los productos para vender y configurar cuentas de usuario de espacio aislado para probar la compra.  *También debe configurar contenido hospedado**para esos productos no consumible que desee a un host con Apple* *.* 
-   **Portal de aprovisionamiento de iOS** : crear un identificador de paquete y habilitar el acceso de la tienda de aplicaciones para la aplicación, tal y como lo haría para cualquier aplicación que admita la compra de la aplicación. 
-   **Almacenar Kit** : agregar código a la aplicación para mostrar productos, compra de productos y restaurar las transacciones.  *IOS 6 Kit Store también administrará la descarga de su producto, en segundo plano, con las actualizaciones de progreso.* 
-   **Código personalizado** : para realizar un seguimiento de las compras realizadas por los clientes y proporcionar los productos o servicios que han adquirido. Utilizar las nuevas clases de Kit Store de iOS 6 como `SKDownload` para recuperar el contenido hospedado por Apple. 


Las siguientes secciones explican cómo implementar contenido hospedado, de creación y carga el paquete a la administración de la compra y descargar el proceso, con el código de ejemplo de este artículo.


### <a name="sample-code"></a>Código de ejemplo

El proyecto de ejemplo *HostedNonConsumables* (en StoreKitiOS6.zip) se muestra cómo compilar una aplicación que usa hospeda el contenido. La aplicación ofrece dos capítulos de libro de"" para la venta, el contenido para el que se hospeda en servidores de Apple. El contenido se compone de un archivo de texto y una imagen, aunque mucho más complejo contenido podría usarse en una aplicación real.

La aplicación es similar a esto antes, durante y después de una compra:

 [![](changes-to-storekit-images/image5.png "La aplicación es similar a esto antes, durante y después de una compra")](changes-to-storekit-images/image5.png#lightbox)

El archivo de texto e imagen se descarga y se copian en el directorio de documentos de la aplicación. Consulte la [trabajar con la documentación del sistema de archivos](~/ios/app-fundamentals/file-system.md) para obtener más información sobre los diferentes directorios disponibles para el almacenamiento de la aplicación.

## <a name="itunes-connect"></a>iTunes Connect

Al crear nuevos productos que va a usar Apple contenido del hospedaje Asegúrese de seleccionar la **no consumible** tipo de producto. Otros tipos de producto no admite el hospedaje de contenido. Además, no debe habilitar el hospedaje de contenido para *existente* productos que venden; solo activar el hospedaje de contenido para nuevos productos.

 [![](changes-to-storekit-images/image6.png "Seleccione el tipo de producto no consumible")](changes-to-storekit-images/image6.png#lightbox)

Escriba un **Id. de producto**. Esto se requerirá más adelante cuando se crea el contenido de este producto.

 [![](changes-to-storekit-images/image7.png "Escriba un identificador de producto")](changes-to-storekit-images/image7.png#lightbox)

Hospedaje de contenido se establece en la sección de detalles. Antes de la compra en la aplicación de puesta en marcha, simplemente desactive la casilla "Host contenido con Apple" Si desea cancelar (incluso si se ha cargado el contenido de prueba). Sin embargo hospedar contenido no se puede quitar después de la compra de la aplicación se ha salido en vivo.

 [![](changes-to-storekit-images/image8.png "Hospedar contenido de Apple")](changes-to-storekit-images/image8.png#lightbox)

Una vez que ha activado hospedar contenido, deberá especificar el producto **esperando carga** estado y mostrar este mensaje:

 [![](changes-to-storekit-images/image9.png "El producto se escriba en espera de estado de la carga y mostrar este mensaje")](changes-to-storekit-images/image9.png#lightbox)

El contenido debe crearse ahora con Xcode y cargados mediante la herramienta para archivar. Instrucciones para crear paquetes de contenido se expresa en la siguiente sección **crear. Archivos PKG**.

## <a name="creating-pkg-files"></a>Crear. Archivos de paquete

Los archivos de contenido que se carga en Apple deben cumplir las siguientes restricciones:

-  No puede superar los 2GB de tamaño.
-  No puede contener código ejecutable (o vínculos simbólicos que señalan fuera del contenido). 
-  Debe tener el formato correcto (incluido un archivo .plist) y tienen una extensión de archivo .pkg. Esto se hace automáticamente si sigue estas instrucciones mediante Xcode. 


Puede agregar muchos archivos diferentes y los tipos de archivos, siempre y cuando cumplen estas restricciones. El contenido es ZIP antes de entregarlo a la aplicación y se han descomprimido por almacén Kit antes de que el código tiene acceso a él.

Después de cargar un paquete de contenido, puede reemplazarse con el contenido más reciente. Nuevo contenido se debe cargar y enviado para su revisión y aprobación mediante el proceso normal. Debe incrementar el `ContentVersion` campo en los paquetes de contenido actualizados.

### <a name="xcode-in-app-purchase-content-projects"></a>Proyectos de Xcode en la aplicación de compra contenido

Actualmente, la creación de paquetes de contenido para productos de compra en la aplicación requiere Xcode. Hay n OBJECTIVE-C codificación necesarios; Xcode tiene un nuevo tipo de proyecto para estos paquetes que contenga únicamente los archivos y un plist.

Nuestra aplicación de ejemplo tiene capítulos del libro para la venta: cada paquete de contenido de capítulo contendrá:

-  un archivo de texto, y
-  una imagen para representar el capítulo.


Empiece seleccionando **archivo > Nuevo proyecto** en el menú y elija **en la aplicación de compra contenido**:

 [![](changes-to-storekit-images/image10.png "Elegir el contenido de la compra de la aplicación")](changes-to-storekit-images/image10.png#lightbox)

Escriba el **Product Name** y **identificador de la empresa** tal que la **identificador de paquete** coincide con la **Id. de producto** que escribió en iTunes Conectarse para este producto.

 [![](changes-to-storekit-images/image11.png "Escriba el nombre y el identificador")](changes-to-storekit-images/image11.png#lightbox)

Ahora tendrá un espacio en blanco **en la aplicación de compra contenido** proyecto. Puede hacer clic y **agregar archivos...** o arrástrelos en la **proyecto navegador**. Asegúrese de que el **ContentVersion** es correcta (debe iniciar en 1.0, pero si más adelante decide actualizar su contenido, no olvide incrementarlo).

Esta captura de pantalla muestra Xcode con los archivos de contenido que se incluye en el proyecto y las entradas de plist visibles en la ventana principal:

 [![](changes-to-storekit-images/image12.png "Esta captura de pantalla muestra Xcode con los archivos de contenido que se incluye en el proyecto y las entradas de plist visibles en la ventana principal")](changes-to-storekit-images/image12.png#lightbox)

Cuando haya agregado todos los archivos de contenido puede guardar este proyecto y editarlo de nuevo más tarde o comenzar el proceso de carga.

## <a name="uploading-pkg-files"></a>Cargando. Archivos de paquete

Es la manera más fácil para cargar paquetes de contenido con el **Xcode archivo herramienta**. Elija **producto > archivo** desde el menú para comenzar:

 ![](changes-to-storekit-images/image13.png "Elija Archiven")

El paquete de contenido, a continuación, aparecerán en el archivo tal como se muestra a continuación. Observe que el tipo de archivo y el icono mostrar se trata de un **archivo de contenido en la aplicación de compra**. Haga clic en **validar...** Para comprobar el contenido del paquete para los errores sin realmente preforming la carga.

 [![](changes-to-storekit-images/image14.png "Validar el paquete")](changes-to-storekit-images/image14.png#lightbox)

Inicie sesión con sus credenciales de conexión de iTunes:

 [![](changes-to-storekit-images/image15.png "Inicio de sesión con sus credenciales de conexión de iTunes")](changes-to-storekit-images/image15.png#lightbox)

Elija la aplicación correcta y en la aplicación de compra para asociar este contenido con:

 [![](changes-to-storekit-images/image16.png "Elija la aplicación correcta y en la aplicación de compra para asociar este contenido con")](changes-to-storekit-images/image16.png#lightbox)

Debería ver un mensaje similar al siguiente:

 ![](changes-to-storekit-images/image17.png "Un ejemplo ningún mensaje de problemas")

Ahora, vaya a través de un proceso similar, pero al hacer clic en **distribuir...** en realidad se cargará el contenido.

 [![](changes-to-storekit-images/image18.png "Distribuir la aplicación")](changes-to-storekit-images/image18.png#lightbox)

Seleccione la primera opción para cargar el contenido:

 ![](changes-to-storekit-images/image19.png "Cargar el contenido")

Nuevo inicio de sesión:

 [![](changes-to-storekit-images/image15.png "Inicio de sesión en")](changes-to-storekit-images/image15.png#lightbox)

Elija la aplicación correcta y el registro de compra en la aplicación para cargar el contenido en:

 [![](changes-to-storekit-images/image20.png "Elija el registro de aplicación y en la aplicación de compra")](changes-to-storekit-images/image20.png#lightbox)

Espere mientras se cargan los archivos:

 [![](changes-to-storekit-images/image21.png "El cuadro de diálogo de carga de contenido")](changes-to-storekit-images/image21.png#lightbox)

Una vez finalizada la carga, aparecerá un mensaje indicar que el contenido se ha enviado a la tienda de aplicaciones.

 [![](changes-to-storekit-images/image22.png "Un mensaje de carga correcta de ejemplo")](changes-to-storekit-images/image22.png#lightbox)

Una vez que se ha realizado, cuando vuelva a la página del producto en iTunes Connect se muestran los detalles del paquete y estar en **listo para enviar** estado. Cuando el producto se encuentra en este estado, puede empezar a probar en el entorno de espacio aislado. NO es necesario para el producto para la realización de pruebas en el espacio aislado 'enviar'.

 [![](changes-to-storekit-images/image23.png "iTunes Connect se muestran los detalles del paquete y estar listo para el estado del envío")](changes-to-storekit-images/image23.png#lightbox)

Puede tardar algún tiempo (p. ej. unos minutos) entre cargar el archivo y el estado de conexión que se está actualizando de iTunes. Puede enviar el producto para su revisión por separado o enviarla junto con un binario de la aplicación. Solo después de Apple ha aprobado oficialmente el contenido estará disponible en la tienda de aplicaciones de producción para su compra en la aplicación.

### <a name="pkg-file-format"></a>Formato de archivo de paquete

Mediante Xcode y la herramienta de archivo para crear y cargar un paquete de contenido hospedado significa que nunca se mostrará el contenido del propio paquete. Los archivos y directorios en los paquetes creados para la aplicación de ejemplo este aspecto, con el `plist` archivo en la raíz y los archivos del producto en un `Contents` subdirectorio:

 [![](changes-to-storekit-images/image24.png "El archivo plist en la raíz y los archivos del producto en un subdirectorio de contenido")](changes-to-storekit-images/image24.png#lightbox)

Tenga en cuenta la estructura de directorios del paquete (especialmente la ubicación de los archivos de la `Contents` subdirectorio) ya que necesitará entender esta información para extraer los archivos del paquete en el dispositivo.

### <a name="updating-package-content"></a>Actualizar el contenido del paquete

El procedimiento para actualizar el contenido después de que se ha aprobado:

-  Edite el proyecto en la aplicación compra contenido en Xcode.
-  Aumentar el número de versión.
-  Volver a cargar en iTunes Connect. Compradores posteriores obtendrán automáticamente la versión más reciente, pero los usuarios que ya tienen la versión anterior no recibirá ninguna notificación. 
-  La aplicación es responsable de notificar a los usuarios y fomentar que recupere una versión más reciente del contenido. La aplicación también debe crear una función que descarga la nueva versión. Esto debe hacerse con la característica de restauración del Kit de almacén. 
-  Para determinar si existe una versión más reciente puede crear una característica en su aplicación para capturar SKProducts (p. ej. el mismo proceso que se usa para recuperar los precios de productos) y comparar la propiedad ContentVersion. 

## <a name="purchasing-overview"></a>Información general sobre compras

Antes de leer esta sección, revisar el existente [documentación de compra en la aplicación](~/ios/platform/in-app-purchasing/index.md).

La secuencia de eventos que tiene lugar cuando un producto con el contenido hospedado se compra y descarga se muestra en este diagrama:

 [![](changes-to-storekit-images/image25.png "La secuencia de eventos que tiene lugar cuando un producto con el contenido hospedado se compra y descargar")](changes-to-storekit-images/image25.png#lightbox)

1.  Pueden crearse nuevos productos en iTunes Connect con Hosted Content habilitado. El contenido real se construye por separado en Xcode (como arrastrar simplemente como archivos en una carpeta) y, a continuación, archivado y cargan en iTunes (no se requiere ningún código). Cada producto, a continuación, se envía para su aprobación, tras el cual estará disponible para su compra. En el código de ejemplo estos identificadores de producto están codificados, pero hospedar contenido de Apple es más flexible si almacena la lista de productos disponibles en un servidor remoto para que se puede actualizar al enviar nuevos productos y contenido en iTunes Connect. 
1.  Cuando un usuario adquiere un producto, una transacción se coloca en la cola de pago para su procesamiento. 
1.  Kit de almacén reenvía la solicitud de compra a servidores de iTunes para su procesamiento. 
1.  Finalice la transacción en los servidores de iTunes (p. ej. se cobra al cliente) y se devuelve una confirmación a la aplicación, con información de producto asociada incluyendo si es descargable (y si es así, el tamaño de archivo y otros metadatos). 
1.  El código debe comprobar si el producto es downloadble y si es así, realizar una solicitud de descarga de contenido que también se coloca en la cola de pago. Kit de almacén envía esta solicitud a los servidores de iTunes. 
1.  Servidor devuelve el archivo de contenido al Kit de almacén, que proporciona una devolución de llamada para devolver el progreso de la descarga y el tiempo restante estimaciones en el código. 
1.  Una vez completado, obtenga una notificación y pasa una ubicación de archivo en la carpeta de caché. 
1.  El código debe copiar los archivos de y comprobar, si se guarda cualquier estado que debe recordar que se ha comprado el producto. Aproveche esta oportunidad para establecer la marca de copia de seguridad correctamente en los nuevos archivos (Sugerencia: si proceden de un servidor y nunca se ha editado por el usuario, se debe probablemente omitir una copia de seguridad, ya que el usuario puede siempre recuperarlos desde servidores de Apple en el futuro). 
1.  Llame a FinishTransaction. Esto es importante ya que quita la transacción de la cola de pago. También es importante que no se llama FinishTransaction hasta después de que haya copiado el contenido fuera del directorio de caché. Una vez que se llama a FinishTransaction, están probable que se purgan rápidamente los archivos de almacenamiento en caché. 


## <a name="implementing-hosted-content-purchase"></a>Implementación de compra contenido hospedado

La siguiente información se debe leer junto con la sección completa [documentación de compras en la aplicación](~/ios/platform/in-app-purchasing/index.md). La información de este documento se centra en las diferencias entre el contenido hospedado y la implementación anterior.


### <a name="classes"></a>Clases

Las clases siguientes se han agregado o modificado al contenido de soporte técnico que se hospedan en iOS 6:

-   **SKDownload** – nueva clase que represente una descarga en curso. La API permite más de una por producto, se implementó inicialmente sin embargo, solo uno. 
-   **SKProduct** : nuevas propiedades: `Downloadable` , `ContentVersion` , `ContentLengths` matriz. 
-   **SKPaymentTransaction** – nueva propiedad agregada: `Downloads` , que contiene una colección de `SKDownload` objetos si este producto tiene hosted contenido disponible para su descarga. 
-   **SKPaymentQueue** : nuevo método de agregado: `StartDownloads` . Llamar a este método con `SKDownload` objetos que se va a recuperar su contenido hospedado. Descargar puede producirse en segundo plano. 
-   **SKPaymentTransactionObserver** : nuevo método: `UpdateDownloads` . Kit de almacén llama a este método con la información de progreso de las operaciones actuales de descarga. 


Detalles de la nueva `SKDownload` clase:

-   **Curso** : un valor entre 0-1, que puede usar para mostrar un indicador de porcentaje completado al usuario. No utilice progreso == 1 para detectar si la descarga se completa, comprobar estado == finalizado. 
-   **TimeRemaining** : estimación de la descarga tiempo restante, en segundos. -1 significa que aún está calculando estimación. 
-   **Estado** : activo, en espera, finalizado, error, en pausa, cancelado. 
-   **ContentURL** – archivo ubicación donde el contenido se coloca en el disco, en la `Cache` directory. SOLO se rellena cuando haya finalizado la descarga. 
-   **Error** : compruebe esta propiedad si el estado es error. 


En este diagrama (el código específico para las compras de contenido hospedadas se muestra en verde), se muestran las interacciones entre las clases en el código de ejemplo:

 [![](changes-to-storekit-images/image26.png "Las compras de contenido hospedadas se muestra en verde en este diagrama")](changes-to-storekit-images/image26.png#lightbox)

El código de ejemplo donde se usaron estas clases se muestra en el resto de esta sección:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Cambiar existente `UpdatedTransactions` invalidación para comprobar que se puede descargar contenido y llame al método `StartDownloads` si es necesario:

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

Nuevo método invalidado `UpdatedDownloads` se muestra a continuación. Kit de almacén llama a este método después de `StartDownloads` se desencadena en `UpdatedTransactions`. Se llama a este método *varias veces* a intervalos indeterminados para proporcionar a progreso de la descarga y, a continuación, vuelva a cuándo ha finalizado la descarga. Observe el método acepta una matriz de `SKDownload` objetos, por lo que cada llamada al método puede proporcionar con el estado de varias descargas en la cola. Como se muestra en la siguiente implementación que son los Estados de descarga comprueba cada hora y la acción más apropiada realizada.

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Cancelled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate)

Esta clase contiene un nuevo método `SaveDownload` que se llama después de cada descarga se completa correctamente.

El contenido hospedado se ha descargado correctamente y se han descomprimido en las `Cache` directory. La estructura de la. Archivo PKG requiere que todos los archivos se guarden en un `Contents` subdirectorio, por lo que el código siguiente extrae los archivos desde el `Contents` subdirectorio.

El código recorre en iteración todos los archivos en el paquete de contenido y los copia en el `Documents` directorio, en una subcarpeta con el nombre de la `ProductIdentifier`. Por último, llama a `CompleteTransaction`, que llama `FinishTransaction` para quitar la transacción de la cola de pago.

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles 
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

Cuando `FinishTransaction` se denomina descargado ya no se garantiza que los archivos estén en la `Cache` directory. Se deben copiar todos los archivos antes de llamar a `FinishTransaction`.


## <a name="other-considerations"></a>Otras consideraciones

El código de ejemplo anterior muestra una implementación bastante simple de compra contenido hospedado. Hay algunos puntos adicionales que debe tener en cuenta:

### <a name="detecting-updated-content"></a>Detectar contenido actualizado

Aunque es posible actualizar los paquetes de contenido hospedados, el Kit de almacén no proporciona ningún mecanismo para enviar estas actualizaciones a los usuarios que ya han descargado y comprado el producto. Para implementar esta funcionalidad, el código puede comprobar la nueva `SKProduct.ContentVersion` propiedad (si la `SKProduct` es `Downloadable`) con regularidad y detectar si se incrementa el valor. O bien puede crear un sistema de notificación de inserción.

### <a name="installing-updated-content-versions"></a>Instalar las versiones actualizadas de contenido

El código de ejemplo anterior omite la copia de archivos si el archivo ya existe. Esto no es una buena idea si desea admitir las versiones más recientes del contenido que se va a descargar.

Puede ser una alternativa a copiar el contenido en una carpeta con el nombre de la versión y realizar un seguimiento de lo que es la versión actual (p. ej. en `NSUserDefaults` o, donde almacenar los registros de compra completos).

### <a name="restoring-transactions"></a>Restauración de las transacciones

Cuando `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` es llama, Kit Store devuelve todas las transacciones anteriores para el usuario. Si ha adquirido un gran número de elementos, o si cada compra tiene paquetes de contenido muy grandes, a continuación, la restauración podría provocar una gran cantidad de tráfico de red como todo obtiene puesto en la cola para su descarga a la vez.

Considere la posibilidad de realizar el seguimiento de si se ha comprado un producto por separado de la descarga real del paquete de contenido asociado.

### <a name="pausing-restarting-and-canceling-downloads"></a>Pausar, reiniciar y cancelar la descarga

Aunque el código de ejemplo no muestra esta característica, es posible pausar y reanudar las descargas de contenido hospedadas. El `SKPaymentQueue.DefaultQueue` tiene métodos para `PauseDownloads`, `ResumeDownloads` y `CancelDownloads`.

Si el código llama `FinishTransaction` en la cola de pago antes de la descarga `Finished` , a continuación, que descarga se cancela automáticamente.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Si establece la marca de omitir la copia de seguridad en el contenido descargado

Instrucciones de copia de seguridad de iCloud de Apple sugiere que el contenido no es de usuario que se restaura fácilmente desde un servidor debe *no* se copia de seguridad (porque innecesariamente utilizaría el almacenamiento de iCloud). Hacer referencia a la [trabajar con el sistema de archivos](~/ios/app-fundamentals/file-system.md) documentación para obtener más información acerca de cómo establecer el atributo de copia de seguridad.

## <a name="summary"></a>Resumen

Este artículo presenta dos nuevas características de almacén Kit en iOS6: compra iTunes y otro tipo de contenido dentro de la aplicación y el servidor de Apple para hospedar sus propios compras en la aplicación que utiliza. Esta introducción debe leerse junto con las existentes [documentación de compra en la aplicación](~/ios/platform/in-app-purchasing/index.md) completa cobertura de implementación de la funcionalidad del Kit de almacén.

## <a name="related-links"></a>Vínculos relacionados

- [StoreKit (ejemplo)](https://developer.xamarin.com/samples/StoreKit/)
- [Compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md)
- [Referencia de StoreKit Framework](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Referencia de clase SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [Referencia de la API de búsqueda de iTunes](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC vídeo: Vende productos con el Kit de almacén](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
