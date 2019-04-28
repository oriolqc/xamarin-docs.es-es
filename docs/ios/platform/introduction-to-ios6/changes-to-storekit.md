---
title: Cambios en StoreKit en iOS 6
description: 'iOS 6 presenta dos cambios en la API de Store Kit: la capacidad para mostrar de iTunes (y App Store/iBookstore) opción donde va a hospedar los archivos que se puede descargar Apple de compra de productos desde dentro de su aplicación y una aplicación de nuevo. Este documento explica cómo implementar estas características con Xamarin.iOS.'
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 5d1bb5ab636cd7527a560332a9890e9907fac454
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61295895"
---
# <a name="changes-to-storekit-in-ios-6"></a>Cambios en StoreKit en iOS 6

_iOS 6 introdujo dos cambios a la API de Store Kit: la capacidad para mostrar de iTunes (y App Store/iBookstore) opción donde va a hospedar los archivos que se puede descargar Apple de compra de productos desde dentro de su aplicación y una aplicación de nuevo. Este documento explica cómo implementar estas características con Xamarin.iOS._

Los cambios principales a Store Kit en iOS6 son estas dos nuevas características:

- **Visualización de contenido de la aplicación & Purchasing** : los usuarios pueden comprar y descargar aplicaciones, música, libros y otros iTunes de contenido sin salir de la aplicación. También puede vincular a sus propias aplicaciones promover comprar o simplemente fomentar revisiones y valoraciones.
- **En la aplicación de compra hospedan contenido** : Apple almacenará y entregar el contenido asociado con los productos de compra en la aplicación, que elimina la necesidad de un servidor independiente hospedar los archivos, automáticamente se admite la descarga en segundo plano y se le permite escribir menos código.

Hacer referencia a la [compra en la aplicación](~/ios/platform/in-app-purchasing/index.md) guías para una cobertura detallada de las APIs StoreKit.

## <a name="requirements"></a>Requisitos

Las características de Store Kit descritas en este documento requieren iOS 6 y Xcode 4.5, junto con Xamarin.iOS 6.0.

## <a name="in-app-content-display--purchasing"></a>Visualización del contenido de la aplicación y compras

La nueva característica de la compra de la aplicación de iOS permite a los usuarios ver la información de producto y adquirir o descargar el producto desde dentro de la aplicación.
Anteriormente, las aplicaciones tendría que desencadenar iTunes, Store de la aplicación o el iBookstore, lo que daría lugar al usuario salir de la aplicación original. Esta nueva característica devuelve automáticamente al usuario a la aplicación cuando hayan terminado.

[![](changes-to-storekit-images/image1.png "Devolver automáticamente a una aplicación después de la compra")](changes-to-storekit-images/image1.png#lightbox)

Ejemplos de cómo se podría usar esto:

- **Anima a los usuarios que valoren la aplicación** : puede abrir la página de App Store para que el usuario puede clasificar y revisar la aplicación sin salir de él.
- **Promoción de entre aplicaciones** : permitir al usuario ver otras aplicaciones que se publican con la posibilidad de comprar y descargar inmediatamente.
- **Ayuda a los usuarios buscar y descargar contenido** : ayudar a los usuarios a comprar el contenido que se encuentra la aplicación, administra o agrega (p ej. una aplicación de música podría proporcionar una lista de reproducción de canciones y permitir que cada canción a adquirirse desde dentro de la aplicación).

Una vez el `SKStoreProductViewController` se ha mostrado el usuario puede interactuar con la información del producto como si estuvieran en iTunes Store de la aplicación o el iBookstore. El usuario puede:

- Capturas de pantalla de vista (para aplicaciones),
- Canciones de ejemplo o de vídeo (para música, TV y películas)
- Revisiones de lectura (y escritura)
- Compra y descarga, lo que se produce completamente en el controlador de vista y el Kit de Store.

Algunas opciones dentro de la `SKStoreProductViewController` seguirán el fuerza el usuario para dejar la aplicación y abra la aplicación de la tienda pertinente, por ejemplo, al hacer clic en **productos relacionados** o una aplicación **soporte** vínculo.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

La API para mostrar un producto dentro de cualquier aplicación es sencilla: solo requiere que crear y mostrar un `SKStoreProductViewController`. Siga estos pasos para crear y mostrar un producto:

1. Crear un `StoreProductParameters` objeto para pasar parámetros al controlador de vista, incluidos el `productId` en el constructor.
1. Crear una instancia de la `SKProductViewController`. Asigna a un campo de nivel de clase.
1. Asignar un controlador para el controlador de vista `Finished` evento, que debe desechar el controlador de vista. Este evento se llama cuando el usuario presiona cancela; o si no finaliza una transacción en el controlador de vista.
1. Llame a la `LoadProduct` método pasando la `StoreProductParameters` y un controlador de finalización. Debe comprobar que la solicitud de producto fue correctamente al controlador de finalización y si es así, no hay el `SKProductViewController` modalmente. Debe agregarse el tratamiento de errores adecuado en caso de que el producto no se puede recuperar.

### <a name="example"></a>Ejemplo

El *ProductView* del proyecto en el *StoreKit* implementa código de ejemplo de este artículo un `Buy` Id. de Apple del método que acepta cualquier producto y se muestra el `SKStoreProductViewController`. El código siguiente muestra la información de producto para cualquier identificador de Apple dado:

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

La aplicación se parecerá a la captura de pantalla siguiente cuando se ejecuta, descarga o compra tiene lugar íntegramente en el `SKStoreProductViewController`:

[![](changes-to-storekit-images/image2.png "La aplicación este aspecto cuando se ejecuta")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Compatibilidad con sistemas operativos anteriores

La aplicación de ejemplo incluye código que se muestra cómo abrir el Store de la aplicación, iTunes o la iBookstore en versiones anteriores de iOS. Use la `OpenUrl` método para abrir un correctamente diseñado **itunes.com** dirección URL.

Puede implementar una comprobación de versión para determinar qué código se ejecute, como se muestra aquí:

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

Se producirá el siguiente error si el identificador de Apple que utiliza no es válido, lo que puede resultar confuso, ya que implica un problema de red o la autenticación de algún tipo.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Leer documentación de Objective-c.

Los desarrolladores leer sobre Store Kit en el Portal para desarrolladores de Apple verán un protocolo – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) : hemos explicado en relación con esta nueva característica. El protocolo de delegado sólo tiene un método – productViewControllerDidFinish: que se haya expuesto como el `Finished` eventos en el `SKStoreProductViewController` en Xamarin.iOS.

## <a name="determining-apple-ids"></a>Determinar los identificadores de Apple

El identificador de Apple requeridos por la `SKStoreProductViewController` es un *número* (no a confundir con el Id. de lote, como "com.xamarin.mwc2012"). Hay varias maneras diferentes, que puede averiguar el identificador de Apple para productos que desea mostrar, enumerados a continuación:

### <a name="itunesconnect"></a>iTunesConnect

Para las aplicaciones que publique, es fácil encontrar la **ID de Apple** en iTunes Connect:

[![](changes-to-storekit-images/image3.png "Buscar el identificador de Apple en iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>API de búsqueda

Apple proporciona una API de búsqueda dinámica para consultar todos los productos en el iBookstore, iTunes y Store de la aplicación. Puede encontrar información sobre cómo acceder a la API de búsqueda en [afiliados recursos de Apple](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html), aunque la API se expone a cualquier persona (no recién registradas filiales). Se puede analizar el JSON resultante para detectar el `trackId` que es el identificador de Apple para usar con `SKStoreProductViewController`.

Los resultados incluirán también otros metadatos incluida la información de presentación y direcciones URL de material gráfico que se pueden usar para representar el producto en la aplicación.

A continuación se muestran algunos ejemplos:

- **aplicación de iBooks** – [ http://itunes.apple.com/search?term=ibooks&amp; entidad = software&amp;country = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **Punto y el iBook canguro** – [ http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; entidad = ebook&amp;country = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>Fuente de socio de Enterprise

Apple ofrece aprobados asociados con un volcado de datos completos de todos sus productos en forma de archivos planos de descargables listos para la base de datos. Si reúne los requisitos para el acceso a la [Enterprise asociado fuente](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html), a continuación, encontrará el identificador de Apple para cualquier producto en ese conjunto de datos.

Muchos usuarios de la fuente de socios empresariales son miembros de la [programa de afiliados](http://www.apple.com/itunes/affiliates) que permite a las comisiones a pulso en ventas de producto. `SKStoreProductViewController` no admite identificadores afiliados (en el momento de escribir este artículo).

### <a name="direct-product-links"></a>Producto vínculos directos

El identificador de Apple para un producto se puede inferir de su vínculo de dirección URL de vista previa de iTunes.
En cualquier iTunes vínculos de productos (las aplicaciones, música o los libros en pantalla) busque el elemento de la dirección URL que empieza con `id` y usar el número que sigue.

Por ejemplo, es el vínculo directo a iBooks

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

y es el identificador de Apple **364709193**. De forma similar para la aplicación MWC2012, es el vínculo directo.

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

y es el identificador de Apple **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Contenido hospedado de compra en la aplicación

Si las compras de la aplicación constan de contenido descargable (por ejemplo, los libros en pantalla u otro medio, gráficos del nivel juegos y configuración u otros archivos grandes), a continuación, estos archivos se usan para hospedarse en el servidor web y aplicaciones tenían que incorporar código para descargarlos de forma segura después de compra. A partir de iOS 6, Apple va a hospedar los archivos en sus servidores, eliminando la necesidad de un servidor independiente. La característica solo está disponible para los productos no consumibles (no consumibles o suscripciones). Ventajas de usar el servicio de hospedaje de Apple incluyen:

- Ahorre costos de hospedaje y el ancho de banda.
- Probablemente es más escalable que cualquier host de servidor que usa actualmente. 
- Menos código para escribir, ya que no es necesario ningún procesamiento en el servidor de compilación. 
- Descargando en segundo plano se implementa automáticamente.

Nota: las pruebas hospedan contenido de la compra en la aplicación de iOS que no se admite el simulador, por lo que debe probar con un dispositivo real.

### <a name="hosted-content-basics"></a>Conceptos básicos de contenido hospedados

Antes de iOS 6, había dos formas de proporcionar un producto (se describe con más detalle en [compra en la aplicación de Xamarin](~/ios/platform/in-app-purchasing/index.md) documentación):

- **Productos integrados** : características que son "desbloqueado" comprando, pero que están integradas en la aplicación (ya sea como código o los recursos incrustados). Ejemplos de productos integrados incluyen filtros de foto desbloqueados o potenciadores del juego.
- **Productos de servidor entregan** : después de la compra, la aplicación debe descargar contenido desde un servidor que trabaja. Este contenido se descargan durante la compra, almacenado en el dispositivo y, a continuación, se representa como parte de la entrega del producto. Por ejemplo, libros, artículos de la revista o niveles de juego que constan de archivos de configuración y material gráfico de fondo.

En iOS, Apple 6 ofrece una variación de productos ofrecidos por el servidor: va a hospedar los archivos de contenido en sus servidores. Esto simplifica en gran medida crear productos entregados por el servidor porque no es necesario para hacer funcionar un servidor independiente y Store Kit proporciona funcionalidad de descarga en segundo plano que previamente tenía que escribir usted mismo. Para aprovechar las ventajas del hospedaje de Apple, habilitar el hospedaje de contenido de nuevos productos de compra en la aplicación y modifique el código de Store Kit para aprovecharlo. Archivos de contenido del producto, a continuación, se compiladas con Xcode y se cargan a los servidores de Apple para su revisión y publicación.

[![](changes-to-storekit-images/image4.png "El proceso de compilación y entrega")](changes-to-storekit-images/image4.png#lightbox)

Mediante la aplicación de Store para proporcionar compras *con contenido hospedado* requiere la instalación y la configuración siguiente:

- **iTunes Connect** – le *debe* proporcionar la información de impuestos y banca a Apple por lo que pueden remitir fondos recopilados en su nombre. A continuación, puede configurar productos para vender y configurar cuentas de usuario de espacio aislado para probar la compra.  _También debe configurar contenido hospedado para esos productos no consumibles que desea hospedar con Apple_.
- **Portal de aprovisionamiento de iOS** : creación de un identificador de lote y habilitar el acceso de la App Store de la aplicación, como lo haría para cualquier aplicación que admita la compra de la aplicación.
- **Store Kit** : agregar código a la aplicación para mostrar productos, compra de productos y restaurar las transacciones.  _En iOS 6 Store Kit también va a administrar la descarga del contenido del producto, en segundo plano, con las actualizaciones de progreso._
- **Código personalizado** : lo ayuda a realizar un seguimiento de las compras realizadas por los clientes y proporcionar los productos o servicios que han comprado. Usar las nuevas clases de Kit Store de iOS 6, como `SKDownload` para recuperar el contenido hospedado por Apple.

Las siguientes secciones explican cómo implementar el contenido hospedado, desde la creación y carga del paquete de administración de la compra y descargar el proceso mediante el código de ejemplo para este artículo.

### <a name="sample-code"></a>Código de ejemplo

El proyecto de ejemplo *HostedNonConsumables* (en StoreKitiOS6.zip) usa el contenido hospedado. La aplicación ofrece dos "capítulos de" para la venta, el contenido para el que se hospeda en servidores de Apple. El contenido se compone de un archivo de texto y una imagen, aunque contenido mucho más complejo podría usarse en una aplicación real.

La aplicación se parecerá a esto antes, durante y después de una compra:

 [![](changes-to-storekit-images/image5.png "La aplicación este aspecto antes, durante y después de una compra")](changes-to-storekit-images/image5.png#lightbox)

La imagen y el archivo de texto se descargan y se copian en el directorio de documentos de la aplicación. Para obtener más información sobre los diferentes directorios disponibles para el almacenamiento de la aplicación, consulte el [documentación del sistema de archivos](~/ios/app-fundamentals/file-system.md).

## <a name="itunes-connect"></a>iTunes Connect

Cuando la creación de nuevos productos que va a usar Apple contenida del hospedaje, no olvide seleccionar la **no consumible** tipo de producto. Otros tipos de producto no admite el hospedaje de contenido. Además, no debe habilitar el hospedaje de contenido para *existente* productos que vende; solo activar el hospedaje de contenido de nuevos productos.

 [![](changes-to-storekit-images/image6.png "Seleccione el tipo de productos no consumibles")](changes-to-storekit-images/image6.png#lightbox)

Escriba un **Id. de producto**. Este Id. será necesario más adelante al crear el contenido de este producto.

 [![](changes-to-storekit-images/image7.png "Escriba un identificador de producto")](changes-to-storekit-images/image7.png#lightbox)

Hospedaje de contenido se establece en la sección de detalles. Antes de la compra en la aplicación puesta en marcha, desactive la **hospedar contenido con Apple** casilla de verificación si desea cancelar (incluso si ha cargado el contenido de prueba). Sin embargo hospedaje de contenido no se puede quitar después de la compra en la aplicación ha quedado en vivo.

 [![](changes-to-storekit-images/image8.png "Hospedar contenido de Apple")](changes-to-storekit-images/image8.png#lightbox)

Una vez que se ha activado en el contenido que hospeda, especificará el producto **esperando la carga** estado y mostrar este mensaje:

 [![](changes-to-storekit-images/image9.png "El producto se escriba en espera de estado de la carga y mostrar este mensaje")](changes-to-storekit-images/image9.png#lightbox)

El paquete de contenido debe crearse con Xcode y cargados mediante la herramienta para archivar. Se proporcionan instrucciones para crear paquetes de contenido en la siguiente sección **creación. Archivos de paquete**.

## <a name="creating-pkg-files"></a>Creando. Archivos de paquete

Los archivos de contenido que se cargan en Apple deben cumplir las restricciones siguientes:

- No puede superar los 2 GB de tamaño.
- No puede contener código ejecutable (o vínculos simbólicos que apuntan fuera del contenido).
- Debe tener el formato correcto (incluido un **.plist** archivo) y tiene un **.pkg** la extensión de archivo. Esto se realizará automáticamente si sigue estas instrucciones mediante Xcode.

Puede agregar muchos archivos diferentes y tipos de archivos, siempre cumplan estas restricciones. El contenido se comprimen antes de entregarlo a la aplicación y descomprime Store Kit antes de que el código tiene acceso a él.

Después de cargar un paquete de contenido, puede reemplazarse con contenido más reciente. Nuevo contenido debe estar cargado y enviado para revisión y aprobación mediante el proceso normal. Incremento del `ContentVersion` campo en los paquetes de contenido actualizados para indicar que es más reciente.

### <a name="xcode-in-app-purchase-content-projects"></a>Proyectos de Xcode en la aplicación de compra contenido

Actualmente, la creación de paquetes de contenido para productos de compra en la aplicación requiere Xcode. No hay ningún OBJECTIVE-C se requiere código; Xcode tiene un nuevo tipo de proyecto para estos paquetes que solo contiene los archivos y un archivo plist.

Nuestra aplicación de ejemplo tiene capítulos del libro para la venta, cada paquete de contenido de capítulo contendrá:

-  un archivo de texto y
-  una imagen para representar el capítulo.


Empiece seleccionando **archivo > Nuevo proyecto** en el menú y elija **contenido de compra en la aplicación**:

 [![](changes-to-storekit-images/image10.png "Elegir el contenido de la compra en la aplicación")](changes-to-storekit-images/image10.png#lightbox)

Escriba el **Product Name** y **identificador de la empresa** tal que la **identificador de paquete** coincide con el **Id. de producto** que escribió en iTunes Conéctese para este producto.

[![](changes-to-storekit-images/image11.png "Escriba el nombre e identificador")](changes-to-storekit-images/image11.png#lightbox)

Ahora tendrá un espacio en blanco **en la aplicación de compra contenido** proyecto. Puede hacer clic en y **agregar archivos...** o arrástrelos en el **Project Navigator**. Asegúrese de que el **ContentVersion** es correcto (debe empiezan en 1.0, pero si posteriormente decide actualizar su contenido, no olvide incrementarlo).

Esta captura de pantalla muestra Xcode con los archivos de contenido incluidos en el proyecto y las entradas de plist visibles en la ventana principal:

[![](changes-to-storekit-images/image12.png "Esta captura de pantalla muestra con Xcode con los archivos de contenido incluidos en el proyecto y las entradas de plist visibles en la ventana principal")](changes-to-storekit-images/image12.png#lightbox)

Una vez haya agregado todos los archivos de contenido se puede guardar este proyecto y editarla de nuevo más tarde o comenzar el proceso de carga.

## <a name="uploading-pkg-files"></a>Cargando. Archivos de paquete

Es la manera más fácil cargar paquetes de contenido con el **Xcode Archive herramienta**. Elija **producto > archivo** desde el menú para comenzar:

![](changes-to-storekit-images/image13.png "Elija Archiven")

El paquete de contenido, a continuación, aparecerá en el archivo como se muestra a continuación. El tipo de archivo y el icono mostrar esta línea es un **archivo de contenido para compras en la aplicación**. Haga clic en **validar...** Para comprobar el paquete de contenido para los errores sin tener que realizar la carga.

[![](changes-to-storekit-images/image14.png "Validar el paquete")](changes-to-storekit-images/image14.png#lightbox)

Inicie sesión con su credenciales de conexión de iTunes:

[![](changes-to-storekit-images/image15.png "Inicio de sesión con su credenciales de conexión de iTunes")](changes-to-storekit-images/image15.png#lightbox)

Elija la aplicación correcta y la compra en la aplicación para asociar este contenido con:

[![](changes-to-storekit-images/image16.png "Elija la aplicación correcta y la compra en la aplicación para asociar este contenido con")](changes-to-storekit-images/image16.png#lightbox)

Debería ver un mensaje similar a esta captura de pantalla:

![Un ejemplo no contiene ningún mensaje problemas](changes-to-storekit-images/image17.png "un ejemplo ningún mensaje de problemas")

Ahora vaya a través de un proceso similar, pero al hacer clic en **Distribute...** en realidad se cargará el contenido.

[![Distribuir la aplicación](changes-to-storekit-images/image18.png "distribuir la aplicación")](changes-to-storekit-images/image18.png#lightbox)

Seleccione la primera opción para cargar el contenido:

![Cargar el contenido](changes-to-storekit-images/image19.png "cargar el contenido")

Vuelva a iniciarla:

[![](changes-to-storekit-images/image15.png "Inicio de sesión en")](changes-to-storekit-images/image15.png#lightbox)

Elija la aplicación correcta y el registro de la compra en la aplicación para cargar el contenido en:

[![](changes-to-storekit-images/image20.png "Elija el registro de aplicación y en la aplicación de compra")](changes-to-storekit-images/image20.png#lightbox)

Espere mientras se cargan los archivos:

[![](changes-to-storekit-images/image21.png "El cuadro de diálogo de carga de contenido")](changes-to-storekit-images/image21.png#lightbox)

Una vez completada la carga, aparecerá un mensaje para informarle de que el contenido se ha enviado a la aplicación de Store.

[![](changes-to-storekit-images/image22.png "Un mensaje de ejemplo carga correcta")](changes-to-storekit-images/image22.png#lightbox)

Una vez que se ha realizado, cuando vuelva a la página del producto en iTunes Connect mostrará los detalles del paquete y estar en **listo para enviar** estado. Cuando el producto está en este estado, puede empezar a probar en el entorno de recinto de seguridad. NO es necesario para el producto para las pruebas en el espacio aislado 'enviar'.

[![](changes-to-storekit-images/image23.png "iTunes Connect mostrará los detalles del paquete y estar listo para el estado del envío")](changes-to-storekit-images/image23.png#lightbox)

Puede tardar algún tiempo (p ej. unos minutos) entre cargar el archivo y el estado de iTunes Connect que se está actualizando. Puede enviar el producto para su revisión por separado o enviarla junto con un binario de la aplicación. Solo después de que Apple ha aprobado oficialmente el contenido estará disponible en la App Store de producción para su compra en la aplicación.

### <a name="pkg-file-format"></a>Formato de archivo de paquete

Utilizando la herramienta para archivar y Xcode para crear y cargar un paquete de contenido hospedado, es decir, nunca verá el contenido del propio paquete. Los archivos y directorios en los paquetes creados para el aspecto de la aplicación de ejemplo, como la captura de pantalla siguiente, con el **plist** archivo en la raíz y los archivos del producto en un **contenido** subdirectorio:

[![](changes-to-storekit-images/image24.png "El archivo plist en la raíz y los archivos del producto en un subdirectorio de contenido")](changes-to-storekit-images/image24.png#lightbox)

Tenga en cuenta la estructura de directorios del paquete (especialmente la ubicación de los archivos en el `Contents` subdirectorio) porque necesitará conocer esta información para extraer los archivos del paquete en el dispositivo.

### <a name="updating-package-content"></a>Actualizar el contenido del paquete

El procedimiento para actualizar el contenido una vez que se haya aprobado:

- Editar el proyecto de contenido de compra en la aplicación en Xcode.
- Subir el número de versión.
- Vuelva a cargar en iTunes Connect. Los compradores posteriores obtendrán automáticamente la versión más reciente, pero los usuarios que ya tienen la versión anterior no recibirán ninguna notificación.
- La aplicación es responsable de notificar a los usuarios y animándoles a recuperar una versión más reciente del contenido. La aplicación también debe crear una función que se descarga la nueva versión, con la característica de restauración del Kit de Store.
- Para determinar si existe una versión más reciente, puede crear una característica en su aplicación para capturar SKProducts (p ej. el mismo proceso que se usa para recuperar los precios de productos) y comparar la propiedad ContentVersion.

## <a name="purchasing-overview"></a>Información general sobre compras

Antes de leer esta sección, revisar el existente [documentación de compra en la aplicación](~/ios/platform/in-app-purchasing/index.md).

Se adquiere la secuencia de eventos que se produce cuando un producto con el contenido hospedado y descarga se muestra en este diagrama:

[![](changes-to-storekit-images/image25.png "La secuencia de eventos que se produce cuando un producto con el contenido hospedado se compre y descargar")](changes-to-storekit-images/image25.png#lightbox)

1. Pueden crearse nuevos productos en iTunes Connect con Hosted Content habilitado. El contenido real es construido por separado en Xcode (como arrastrar simplemente como archivos en una carpeta) y, a continuación, se archivan y cargar en iTunes (se requiere ningún código). Cada producto, a continuación, se envía para su aprobación, tras el cual estará disponible para su compra. En el código de ejemplo, estos identificadores de producto están codificados, pero hospedar contenido de Apple es más flexible si almacena la lista de productos disponibles en un servidor remoto para que se puede actualizar al enviar nuevos productos y contenido en iTunes Connect.
1. Cuando un usuario adquiere un producto, una transacción se coloca en la cola de pago para su procesamiento.
1. Store Kit reenvía la solicitud de compra a los servidores de iTunes para su procesamiento.
1. Finalice la transacción en los servidores de iTunes (p ej. se cobra al cliente) y se devuelve una confirmación a la aplicación, con información de producto adjunta incluido si está disponible para descarga (y si es así, el tamaño del archivo y otros metadatos).
1. El código debe comprobar si el producto es descargable y si es así, realice una solicitud de descarga de contenido que también se coloca en la cola de pago. Store Kit envía esta solicitud a los servidores de iTunes.
1. Servidor devuelve el archivo de contenido a Store Kit, que proporciona una devolución de llamada para devolver el progreso de descarga y el tiempo restante de las estimaciones en el código.
1. Una vez que haya finalizado, obtener una notificación y pasa una ubicación de archivo en la carpeta de caché.
1. El código debe copiar los archivos y comprobar, si se guarda cualquier estado que tenga que recordar que se adquirió el producto. Aproveche esta oportunidad para establecer la marca de copia de seguridad correctamente en los archivos nuevos (Sugerencia: si proceden de un servidor y no se editan nunca por el usuario, se debe probablemente omitir una copia de seguridad, puesto que el usuario puede siempre recuperarlos desde servidores de Apple en el futuro).
1. Llame a FinishTransaction. Este paso es importante, ya que elimina la transacción de la cola de pago. También es importante no llamar a FinishTransaction hasta después de que ha copiado el contenido fuera del directorio de caché. Una vez que se llama a FinishTransaction, están probable que rápidamente se purgan los archivos de almacenamiento en caché.

## <a name="implementing-hosted-content-purchase"></a>Implementación de compra contenido hospedado

La siguiente información debe leerse junto con la completa [documentación de compras de la aplicación](~/ios/platform/in-app-purchasing/index.md). La información de este documento se centra en las diferencias entre el contenido hospedado y la implementación anterior.

### <a name="classes"></a>Clases

Las siguientes clases se han agregado o modificado al contenido de soporte técnico hospedado en iOS 6:

- **SKDownload** – nueva clase que representa una descarga en curso. La API permite más de una por productos, aunque inicialmente solo uno se ha implementado.
- **SKProduct** : las nuevas propiedades incluidas: `Downloadable`, `ContentVersion`, `ContentLengths` matriz.
- **SKPaymentTransaction** – nueva propiedad agregada: `Downloads`, que contiene una colección de `SKDownload` objetos si este producto ha hospedado contenido disponible para su descarga.
- **SKPaymentQueue** : nuevo método de agregado: `StartDownloads`. Llame a este método con `SKDownload` objetos que se va a recuperar su contenido hospedado. Puede producirse la descarga en segundo plano.
- **SKPaymentTransactionObserver** : nuevo método: `UpdateDownloads`. Store Kit llama a este método con la información de progreso de las operaciones de descarga actuales.

Detalles del nuevo `SKDownload` clase:

- **Curso** : un valor entre 0-1 que puede usar para mostrar un indicador de porcentaje completado para el usuario. No use progreso == 1 para detectar si la descarga se completa, comprobar estado == finalizado.
- **TimeRemaining** : estimación de la descarga de tiempo restante, en segundos. -1 significa que aún está calculando la estimación.
- **Estado** : activo, en espera, finalizado, no se pudo, pausar, Cancelar.
- **ContentURL** : archivo de la ubicación donde el contenido se coloca en el disco, en el `Cache` directory. SOLO se rellena una vez finalizada la descarga.
- **Error** : compruebe esta propiedad si el estado es error.

En este diagrama (el código específico para las compras de contenido hospedadas se muestra en verde), se muestran las interacciones entre las clases en el código de ejemplo:

[![](changes-to-storekit-images/image26.png "Las compras de contenido hospedadas se muestra en verde en el diagrama")](changes-to-storekit-images/image26.png#lightbox)

El código de ejemplo donde se han usado estas clases se muestra en el resto de esta sección:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Cambiar las existentes `UpdatedTransactions` invalidación para buscar contenido descargable y llamada `StartDownloads` si es necesario:

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

Nuevo método invalidado `UpdatedDownloads` se muestra a continuación. Store Kit llama a este método después de `StartDownloads` se desencadena en `UpdatedTransactions`. Este método se llama *varias veces* a intervalos indeterminados para proporcionar con progreso de la descarga y, a continuación, vuelva a cuando se haya completado la descarga. Tenga en cuenta el método acepta una matriz de `SKDownload` objetos, por lo que cada llamada al método puede proporcionar con el estado de varias descargas en la cola. Como se muestra en la siguiente implementación que son los Estados de descarga comprueba cada vez y la acción apropiada.

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
            Console.WriteLine ("Canceled"); // TODO: UI?
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

El contenido hospedado se ha descargado correctamente y descomprimido en la `Cache` directory. La estructura de la. Archivo PKG requiere todos los archivos se guarden en un `Contents` subdirectorio, por lo que el código siguiente extrae los archivos desde el `Contents` subdirectorio.

El código recorre en iteración todos los archivos en el paquete de contenido y los copia en el `Documents` directorio, en una subcarpeta denominada para el `ProductIdentifier`. Por último llama a `CompleteTransaction`, que llama a `FinishTransaction` para quitar la transacción de la cola de pago.

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

Cuando `FinishTransaction` se llama, los datos descargados ya no se garantiza que los archivos estén en el `Cache` directory. Se deben copiar todos los archivos antes de llamar a `FinishTransaction`.


## <a name="other-considerations"></a>Otras consideraciones

El código de ejemplo anterior muestra una implementación bastante sencilla de adquirir contenido hospedado. Hay algunos puntos adicionales que debe tener en cuenta:

### <a name="detecting-updated-content"></a>Detectar contenido actualizado

Aunque es posible actualizar los paquetes de contenido hospedados, Store Kit no proporciona ningún mecanismo para enviar estas actualizaciones a los usuarios que ya han descargado y adquirió el producto. Para implementar esta funcionalidad, el código puede comprobar el nuevo `SKProduct.ContentVersion` propiedad (si el `SKProduct` es `Downloadable`) con regularidad y detectar si el valor se incrementa. También puede crear un sistema de notificaciones de inserción.

### <a name="installing-updated-content-versions"></a>Instalar las versiones actualizadas de contenido

El código de ejemplo anterior omite la copia de archivos si el archivo ya existe. Esto no es una buena idea si desea admitir las versiones más recientes del contenido que se va a descargar.

Podría ser una alternativa a copiar el contenido en una carpeta denominada para la versión y realizar un seguimiento de lo que es la versión actual (p ej. en `NSUserDefaults` o donde almacenar los registros de compra completa).

### <a name="restoring-transactions"></a>Restauración de las transacciones

Cuando `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` es llamado, Store Kit devuelve todas las transacciones anteriores para el usuario. Si se ha comprado un gran número de elementos, o si cada compra tiene paquetes de contenido de gran tamaño, la restauración podría provocar una gran cantidad de tráfico de red como todo lo que pone en cola para su descarga a la vez.

Considere la posibilidad de realizar el seguimiento de si un producto se adquirió por separado desde la descarga real del paquete de contenido asociado.

### <a name="pausing-restarting-and-canceling-downloads"></a>Pausar, reiniciar y cancelar las descargas

Aunque el código de ejemplo no muestra esta característica, es posible pausar y reiniciar las descargas de contenido hospedadas. El `SKPaymentQueue.DefaultQueue` tiene métodos para `PauseDownloads`, `ResumeDownloads` y `CancelDownloads`.

Si el código llama a `FinishTransaction` en la cola de pago antes de la descarga que se va a `Finished` , a continuación, que descarga se cancela automáticamente.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Al establecer la marca SKIP-copia de seguridad en el contenido descargado

Las directrices de copia de seguridad de iCloud de Apple recomienda que el contenido que no es de usuario que fácilmente se restaura desde un servidor debe *no* se copia de seguridad (porque es innecesariamente utilizaría el almacenamiento de iCloud). Para obtener más información sobre cómo establecer el atributo de copia de seguridad, consulte el [sistema de archivos](~/ios/app-fundamentals/file-system.md) documentación.

## <a name="summary"></a>Resumen

En este artículo presenta dos nuevas características de Store Kit en iOS6: adquisición de iTunes y otro contenido desde dentro de la aplicación y el uso de servidor de Apple para hospedar sus propio compras de la aplicación. Esta introducción debe leerse junto con las existentes [documentación de compra en la aplicación](~/ios/platform/in-app-purchasing/index.md) para una cobertura completa de implementar la funcionalidad de Store Kit.

## <a name="related-links"></a>Vínculos relacionados

- [StoreKit (ejemplo)](https://developer.xamarin.com/samples/StoreKit/)
- [Compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md)
- [Referencia de Framework StoreKit](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Referencia de clase SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [Referencia de API de búsqueda de iTunes](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [Sesión WWDC vídeo: Venta de productos con el Kit de Store](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
