---
title: PassKit en Xamarin.iOS
description: La aplicación Wallet permite a los usuarios almacenar pasadas digitales en sus dispositivos de iOS. El marco de PassKit permite a los programadores interactuar con pasadas mediante programación.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2018
ms.openlocfilehash: d1c640bef41e875b3bb427d657c9c239e4c3e16d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121404"
---
# <a name="passkit-in-xamarinios"></a>PassKit en Xamarin.iOS

La aplicación de iOS Wallet permite a los usuarios almacenar pasadas digitales en sus dispositivos.
Estas fases se generan por los comercios y se envían al cliente por correo electrónico, direcciones URL, o a través de la aplicación de iOS de comerciante. Estos pases pueden representar varias cosas, de vales de película a tarjetas de fidelización de embarque. El marco de PassKit permite a los programadores interactuar con pasadas mediante programación.

Este documento presenta Wallet y mediante la API de PassKit con Xamarin.iOS.

 [![](passkit-images/image1.png "Wallet almacena y organiza todas las fases en un teléfono")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>Requisitos

Las características de PassKit descritas en este documento requieren iOS 6 y Xcode 4.5, junto con Xamarin.iOS 6.0.

## <a name="introduction"></a>Introducción

El problema clave que resuelva PassKit es la distribución y administración de códigos de barras. Algunos ejemplos reales de cómo se utilizan actualmente los códigos de barras son:

-   **Compra de vales de película en línea** : normalmente, los clientes reciben un correo electrónico un código de barras que representa los billetes. Este código de barras se imprime y tarda en el cine analizarse para la entrada.
-   **Tarjetas de fidelización** : los clientes llevan un número de tarjetas específicos del almacén diferentes en su cartera o el bolso, para mostrar y de análisis cuando compran productos.
-   **Cupones** – cupones se distribuyen a través de correo electrónico, como páginas web imprimible, a través de los buzones de correo y como códigos de barras en periódicos y revistas. Los clientes llevarlos a un almacén para el análisis, para recibir como devolución de mercancía, servicios o descuentos.
-   **Fases de embarque** – Similar a la compra de un vale de película.

PassKit ofrece una alternativa para cada uno de estos escenarios:

-   **Vales de película** : después de la compra, el cliente agrega un paso de la incidencia de eventos (a través de correo electrónico o un vínculo de sitio Web). Como la hora de los enfoques de película, la fase de aparecerán automáticamente en la pantalla de bloqueo como recordatorio, y a la llegada en el cine el pase fácilmente se recupera y muestra en la cartera para el análisis.
-   **Tarjetas de fidelización** : en lugar de (o además) proporcionar una tarjeta física, almacenes pueden emitir (por correo electrónico o después de un inicio de sesión del sitio Web) un paso de la tarjeta de Store. El almacén puede proporcionar características adicionales, como actualizar el saldo de la cuenta en el paso a través de notificaciones de inserción, y utilizando los servicios de ubicación geográfica la fase podría aparecer automáticamente en la pantalla de bloqueo cuando el cliente está cerca de una ubicación de almacén.
-   **Cupones** – pasadas de cupón fácilmente se pueden generar con características únicas para ayudar a con el seguimiento y distribuidas a través de vínculos de correo electrónico o un sitio Web. Cupones descargados pueden aparecer automáticamente en la pantalla de bloqueo cuando el usuario está cerca de una ubicación específica, o en una fecha determinada (por ejemplo, cuando se está aproximando la fecha de expiración). Dado que los cupones se almacenan en el teléfono del usuario, que siempre son útiles y no se traspapelan. Cupones podrían anime a los clientes para descargar aplicaciones complementarias porque los vínculos de App Store se pueden incorporar en el pase, aumentar la interacción con el cliente.
-   **Fases de embarque** – después de un proceso en línea en el repositorio, el cliente recibiría su embarque a través de correo electrónico o un vínculo. Una aplicación complementaria proporcionada por el proveedor de transporte podría incluir el proceso de comprobación y también permiten al cliente realizar funciones adicionales como la elección de su asiento o comida. El proveedor de transporte puede usar notificaciones de inserción para actualizar el paso si el transporte se retrasa o cancelado. Como los métodos de tiempo de embarque el pase aparecerá en la pantalla de bloqueo como recordatorio y para proporcionar acceso rápido a la fase.

En esencia, PassKit proporciona una manera sencilla y cómoda para almacenar y mostrar los códigos de barras en el dispositivo iOS. Con el tiempo adicional y la integración de la pantalla de bloqueo de ubicación, las notificaciones de inserción y la aplicación complementaria integran ofrece una base para las ventas muy sofisticadas, vales y servicios de facturación.

## <a name="passkit-ecosystem"></a>Ecosistema de PassKit

PassKit no es simplemente una API en CocoaTouch, sino más bien parte de un ecosistema mayor de aplicaciones, datos y servicios que facilitan el uso compartido y administración de códigos de barras y otros datos. Este diagrama de alto nivel muestra las distintas entidades que pueden estar implicadas en la creación y uso de fases:

 [![](passkit-images/image2.png "Este diagrama de alto nivel muestra las entidades implicadas en la creación y uso de las pasadas")](passkit-images/image2.png#lightbox)

Cada parte del ecosistema tiene un rol claramente definido:

-   **Wallet** : aplicación de iOS integrada de Apple que almacena y muestra pasadas. Esto es el único lugar en que se representan pasadas para su uso en el mundo real (es decir el código de barras se muestra, junto con todos los datos localizados en la fase).
-   **Companion en aplicaciones** : pasan de las aplicaciones de iOS 6 creadas por proveedores para extender la funcionalidad de las pasadas emitir, como agregar valor a una tarjeta de almacén, cambiar la plaza en un paso de embarque u otra función específicos del negocio. Aplicaciones complementarias no son necesarias para un pase para ser útil.
-   **El servidor** : un servidor seguro donde pasa puede ser generado y firmado para la distribución. Su aplicación complementaria puede conectarse al servidor para generar nuevos pasadas o solicitar actualizaciones pasadas existentes. También puede implementar la web pasa de la API de servicio que se llamaría Wallet para actualizar.
-   **Servidores de Apple Push Notification Service** : el servidor tiene la capacidad para notificar a Wallet de actualizaciones a una fase en un dispositivo determinado con APNS. Una notificación de inserción a Wallet que, a continuación, pondremos en contacto con el servidor para obtener detalles del cambio. No es necesario implementar APNS para esta característica aplicaciones complementarias (pueden escuchar la `PKPassLibraryDidChangeNotification` ).
-   **Conduit Apps** : las aplicaciones que no se manipulan directamente pasa (como ocurre con aplicaciones complementarias), pero lo que puede mejorar su utilidad al reconocer pasadas y permitir que se agreguen a Wallet. Los clientes de correo electrónico, exploradores de redes sociales y otras aplicaciones de la agregación de datos pueden todos encontrar vínculos a pasa o datos adjuntos.

Todo el ecosistema parece complejo, por lo que conviene tener en cuenta que algunos componentes son opcionales y mucho más fácil de implementaciones de PassKit son posibles.

## <a name="what-is-a-pass"></a>¿Qué es un paso?

Una fase es una colección de datos que representan un vale, vale o una tarjeta. Se puede pensar para un solo uso por un individuo (y, por tanto, contener detalles como una asignación de número y los puestos de vuelo) o es posible que un token de usar varios que se puede compartir por cualquier número de usuarios (por ejemplo, un cupón de descuento). Una descripción detallada está disponible en Apple [archivos pasar](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) documento.

### <a name="types"></a>Tipos

Actualmente cinco tipos admitidos, que se pueden distinguir en la aplicación Wallet por el diseño y el borde superior del pase:

-  **Evento vale** – recorte semicircular orientada hacia el pequeño.
-   **Fase de incorporación** – muescas de icono en paralelo, específica del transporte se pueden especificar (p ej. bus, tren, avión).
-   **Store tarjeta** : redondeado superior, como una tarjeta de crédito o débito.
-  **Cupón** : perforado a lo largo de la parte superior.
-  **Genérico** : igual que la tarjeta de Store, la parte superior redondeada.


Los tipos de cinco pase se muestran en esta captura de pantalla (en orden: cupón, genérico, almacenar tarjeta, embarque y vales de eventos):

 [![](passkit-images/image3.png "Los tipos de cinco pase se muestran en esta captura de pantalla")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Estructura de archivos

Un archivo de pass es realmente un archivo ZIP con una **.pkpass** , que contiene algunos específicos archivos JSON (obligatorios), una variedad de imagen de los archivos con extensión (opcional), así como las cadenas localizadas (también opcional).

-   **PASS.JSON** : requerido. Contiene toda la información de la fase.
-   **manifest.JSON** : requerido. Contiene el hash de SHA1 para cada archivo en la fase, exceptuando el archivo de firma y este archivo (manifest.json).
-   **firma** : requerido. Crea al firmar el `manifest.json` archivo con el certificado generado en el Portal de aprovisionamiento de iOS.
-  **Logo.png** : opcional.
-  **Background.png** : opcional.
-  **Icon.png** : opcional.
-  **Archivos de cadenas localizables** : opcional.

A continuación se muestra la estructura de directorios de un archivo de pass (este es el contenido del archivo ZIP):

 [![](passkit-images/image4.png "Estructura de directorios de un archivo de paso se muestra aquí")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>PASS.JSON

JSON es el formato porque pasa normalmente se crea en un servidor, significa que el código de generación es independiente de la plataforma en el servidor. Son tres piezas clave de la información en cada paso:

-   **teamIdentifier** : Esto vincula todas las fases que se genera para su cuenta de App Store. Este valor es visible en el Portal de aprovisionamiento de iOS.
-   **passTypeIdentifier** – Regístrese en el Portal de aprovisionamiento al grupo pasa entre sí (si se produce más de un tipo). Por ejemplo, una cafetería podría crear un tipo de pase de tarjeta de almacén para permitir que sus clientes ganan créditos de fidelización, pero también un cupón independiente pasar el tipo para crear y distribuir los cupones de descuento. Ese misma cafetería incluso podría contener eventos de música en directo y emitir eventos vale pasadas para aquellos.
-   **serialNumber** : una cadena única dentro de este `passTypeidentifier` . El valor es opaco a Wallet, pero es importante para el seguimiento de pasos específicos cuando se comunica con el servidor.

Hay un gran número de otras claves JSON en cada pasada, se muestra un ejemplo de los cuales a continuación:

``` 
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>Códigos de barras

Se admiten formatos sólo 2D: PDF417, Aztec, QR. Las notificaciones de Apple que los códigos de barras 1D son adecuados para la detección en una pantalla de teléfono con retroiluminación.

Texto alternativo que se muestra a continuación el código de barras es opcional: algunos comerciantes desean ser capaz de lectura/tipo manualmente.

Codificación ISO-8859-1 es la comprobación más comunes, la codificación utilizada por los sistemas de análisis que va a leer sus pases.

### <a name="relevancy-lock-screen"></a>Relevancia (pantalla de bloqueo)

Hay dos tipos de datos que pueden causar un pase para mostrarse en la pantalla de bloqueo:

 **Ubicación**

Se pueden especificar hasta 10 ubicaciones en un paso, por ejemplo, los almacenes que un cliente visita con frecuencia, o la ubicación de un cine o un aeropuerto. Un cliente puede especificar estas ubicaciones a través de una aplicación complementaria o el proveedor podría determinar ellos desde los datos (si se recopilan con el permiso del cliente).

Cuando el paso se muestra en la pantalla de bloqueo, se calcula una barrera para que cuando el usuario abandona el área de la fase está oculto en la pantalla de bloqueo. Está asociado el radio para pasar de estilo para evitar abusos.

 **Fecha y hora**

En un paso, se puede especificar solo una fecha y hora. La fecha y hora es útil para la activación de los avisos de pantalla de bloqueo de embarque y vales de eventos.

Se puede actualizar mediante inserción o a través de API de PassKit, por lo que se pudo actualizar la fecha y hora en el caso de una incidencia de uso múltiple (por ejemplo, un vale de temporada en un teatro o complejo deportivo).

### <a name="localization"></a>Localización

Traducir un pase a varios idiomas es similar a la localización de una aplicación de iOS: creación de lenguaje directorios específicos con el `.lproj` extensión y colocar los elementos dentro de localizado. Traducciones de texto deben escribirse en un `pass.strings` de archivo, mientras que imágenes localizadas deben tener el mismo nombre que la imagen que se sustituyen en la raíz de Pass.

## <a name="security"></a>Seguridad

Pasadas se firman con un certificado privado que genera en el Portal de aprovisionamiento de iOS. Los pasos para iniciar la fase de son:

1.  Calcular un hash SHA1 para cada archivo en el directorio de pass (no incluya el `manifest.json` o `signature` archivo, ninguno de los cuales debe existir en esta fase de todos modos).
1.  Escribir `manifest.json` como una lista de pares clave-valor JSON de cada nombre de archivo con su hash.
1.  Usar el certificado para firmar el `manifest.json` de archivos y escribir el resultado en un archivo denominado `signature` .
1.  COMPRIMA el todo y asigne al archivo resultante una `.pkpass` la extensión de archivo.


Dado que la clave privada se necesita para firmar el pase, este proceso solo debe realizarse en un servidor seguro que usted controla. No distribuya las claves para probar y generar pasadas en una aplicación.

 
## <a name="configuration-and-setup"></a>Instalación y configuración

Esta sección contiene instrucciones para ayudar a los detalles de aprovisionamiento de la configuración y crear su primer paso.

### <a name="provisioning-passkit"></a>Aprovisionamiento de PassKit

Para un paso escribir la aplicación de Store, se debe vincular a una cuenta de desarrollador. Esto requiere dos pasos:

1.  La fase debe estar registrada con un identificador único, llamado el identificador del tipo de pase.
1.  Para iniciar la fase con firma digital del desarrollador, se debe generar un certificado válido.

Para crear la siguiente no pase el identificador de tipo.

#### <a name="create-a-pass-type-id"></a>Crear un identificador de tipo de paso

El primer paso es configurar un identificador de tipo de pase para cada uno de ellos diferentes _tipo_ de pase a ser compatibles. El identificador de pase (o identificador de tipo pasar) crea un identificador único para la fase. Este identificador se usará para vincular la fase con su cuenta de desarrollador con un certificado.

1. En el [certificados, identificadores y perfiles de sección del Portal de aprovisionamiento de iOS](https://developer.apple.com/account/overview.action), vaya a **identificadores** y seleccione **pasar los identificadores de tipo** . A continuación, seleccione el **+** botón para crear un nuevo tipo de fase: [ ![](passkit-images/passid.png "crear un nuevo tipo de paso")](passkit-images/passid.png#lightbox)

2.   Proporcione un **descripción** (nombre) y **identificador** (cadena única) para la fase. Tenga en cuenta que todos los identificadores de tipo pasar debe comenzar con la cadena `pass.` en este ejemplo usamos `pass.com.xamarin.coupon.banana` : [ ![](passkit-images/register.png "proporcionar una descripción y un identificador")](passkit-images/register.png#lightbox)


3.   Confirme el identificador de pase presionando el **registrar** botón.

#### <a name="generate-a-certificate"></a>Generar un certificado

Para crear un nuevo certificado para este identificador de tipo pase, realice lo siguiente:

1.  Seleccione el identificador de pase recién creado en la lista y haga clic en **editar** : [ ![](passkit-images/pass-done.png "seleccione el nuevo identificador de pasar de la lista")](passkit-images/pass-done.png#lightbox)

    A continuación, seleccione **Create Certificate...** :

    [![](passkit-images/cert-dist.png "Seleccione Crear certificado")](passkit-images/cert-dist.png#lightbox)


2.  Siga los pasos para crear una firma de solicitud certificado (CSR).
  
3. Presione el **continuar** situado en el portal para desarrolladores y cargar el archivo CSR para generar el certificado.

4. Descargue el certificado y haga doble clic en él para instalarlo en la cadena de claves.


Ahora que hemos creado un certificado para este identificador de tipo pase, la siguiente sección describe cómo crear un paso manualmente.

Para obtener más información sobre el aprovisionamiento de Wallet, consulte el [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) guía.

### <a name="create-a-pass-manually"></a>Crear un paso manualmente

Ahora que hemos creado el tipo de pasar manualmente podemos crear un paso de prueba en el simulador o en un dispositivo. Los pasos para crear un paso son:

-  Cree un directorio que contenga los archivos de pass.
-  Cree un archivo pass.json que contiene todos los datos necesarios.
-  Incluir imágenes en la carpeta (si es necesario).
-  Calcular el hash SHA1 para todos los archivos de la carpeta y escribir a manifest.json.
-  Manifest.json de inicio de sesión con el archivo de certificado descargado. p12.
-  COMPRIMA el contenido del directorio y cambiar el nombre con extensión .pkpass.


Hay algunos archivos de origen en el [código de ejemplo](https://developer.xamarin.com/samples/monotouch/PassKit/) para este artículo que se puede usar para generar un paso. Use los archivos en el `CouponBanana.raw` directorio del directorio CreateAPassManually. Los siguientes archivos están presentes:

 [![](passkit-images/image18.png "Estos archivos están presentes")](passkit-images/image18.png#lightbox)

Abra pass.json y edite el archivo JSON. Debe actualizar al menos el `passTypeIdentifier` y `teamIdentifer` para que coincida con su cuenta de desarrollador de Apple.

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

A continuación, debe calcular los valores hash para cada archivo y crear el `manifest.json` archivo. Lo tendrá un aspecto similar al siguiente cuando haya terminado:

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

A continuación se debe generar una firma para este archivo mediante el certificado (archivo. p12) que se generó para este identificador de tipo de paso.

#### <a name="signing-on-a-mac"></a>Iniciar sesión en un equipo Mac

Descargue el **materiales de soporte de semillas de Wallet** desde el [descargas Apple](https://developer.apple.com/downloads/index.action?name=Passbook) sitio. Use el `signpass` herramienta para convertir la carpeta en un paso (Esto también calculará SHA1 aplica un algoritmo hash y comprimir la salida en un archivo .pkpass).

#### <a name="testing"></a>Pruebas

Si tuviera que examinar el resultado de estas herramientas (al establecer el nombre de archivo a .zip y, a continuación, abrirlo), verá los archivos siguientes (tenga en cuenta la adición de la `manifest.json` y `signature` archivos):

 [![](passkit-images/image19.png "Al examinar la salida de estas herramientas")](passkit-images/image19.png#lightbox)

Cuando haya iniciado sesión, ZIP y el nombre del archivo (p ej. para `BananaCoupon.pkpass`) puede arrastrarlo al simulador para probar o enviarlo por correo electrónico a sí mismo para recuperar en un dispositivo real. Debería ver una pantalla a **agregar** pass, similar al siguiente:

 [![](passkit-images/image20.png "Agregar la pantalla de pass")](passkit-images/image20.png#lightbox)

Normalmente se desea automatizar ese proceso en un servidor, creación de pase manual pero podría ser una opción para pequeñas empresas que sólo va a crear cupones que no requieren la compatibilidad de un servidor back-end.

## <a name="wallet"></a>Cartera

Wallet es la pieza central del ecosistema de PassKit. Esta captura de pantalla muestra Wallet vacío y el aspecto de la lista de pass y pasadas individuales:

 [![](passkit-images/image21.png "Esta captura de pantalla muestra Wallet vacío y el aspecto de la lista de pass y pasadas individuales")](passkit-images/image21.png#lightbox)

Las características de Wallet incluyen:

-  Es el único lugar pasadas se representan con su código de barras para el análisis.
-  Usuario puede cambiar la configuración de actualizaciones. Si está habilitada, las notificaciones de inserción pueden desencadenar actualizaciones a los datos en la fase.
-  Usuario puede habilitar o deshabilitar la integración de la pantalla de bloqueo. Si está habilitada, esto permite el paso aparecen automáticamente en su pantalla de bloqueo, según los datos de hora y la ubicación pertinentes incrustados en el paso.
-  La parte posterior del pase admite Deslizar para actualizar, si se proporciona una URL de servidor web en la fase de JSON.
-  Las aplicaciones complementarias se pueden abrir (o descargar) si se proporciona el identificador de la aplicación en la fase de JSON.
-  Pasadas pueden eliminarse (con una animación de purga bonita).

## <a name="adding-passes-into-wallet"></a>Agregar fases de Wallet

Pueden agregarse pasadas a Wallet de las maneras siguientes:

* **Conduit Apps** : estos no manipulan directamente pasadas, simplemente cargar archivos de pass y muéstrele al usuario la opción de agregarlo a Wallet. 

* **Companion en aplicaciones** : estos se escriben proveedores para distribuir pases y ofrecer una funcionalidad adicional para examinar o modificarlos. Las aplicaciones de Xamarin.iOS tienen acceso completo a la API de PassKit para crear y manipular pasadas. A continuación, se pueden agregar fases al uso de Wallet el `PKAddPassesViewController`. Este proceso se describe con más detalle en la **aplicaciones complementarias** sección de este documento.

### <a name="conduit-applications"></a>Aplicaciones de la canalización

Aplicaciones conducto son aplicaciones intermedias que pueden aparecer pasadas en el nombre de un usuario y deben programarse para que reconozca su tipo de contenido y proporcionan funcionalidad para agregar a Wallet. Ejemplos de aplicaciones de la canalización:

-   **Correo electrónico** – reconoce los datos adjuntos como un paso.
-   **Safari** – reconoce la fase de Content-Type al que se hace clic en un vínculo de dirección URL pasada.
-   **Otras aplicaciones personalizadas** : cualquier aplicación que recibe los datos adjuntos o abrir vínculos (los clientes de medios sociales, los lectores de correo electrónico, etcetera).


Esta captura de pantalla se muestra cómo **correo** en iOS 6 reconoce un adjunto de pass y (cuando se toca) ofrece la posibilidad de **agregar** a Wallet.

 [![](passkit-images/image22.png "Esta captura de pantalla muestra cómo Mail de iOS 6 reconoce un adjunto de pass")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "Esta captura de pantalla muestra cómo ofrece correo para agregar datos adjuntos de paso a Wallet")](passkit-images/image23.png#lightbox)

Si va a compilar una aplicación que podría ser un conducto para pasadas, puede reconocer por:

-  **Extensión de archivo** -.pkpass
-  **Tipo MIME** -application/vnd.apple.pkpass
-  **UTI** – com.apple.pkpass


La operación básica de una aplicación conducto es recuperar el archivo de pass y llamar a de PassKit `PKAddPassesViewController` para proporcionar al usuario la opción para agregar el pase a su cartera. Se trata la implementación de este controlador de vista en la siguiente sección en **aplicaciones complementarias**.

No es necesario aprovisionar para un paso específico de Id. de tipo de la misma manera que lo hacen con aplicaciones complementarias conducto aplicaciones.

## <a name="companion-applications"></a>Aplicaciones complementarias

Una aplicación complementaria proporciona funcionalidad adicional para trabajar con pasadas, incluyendo la creación de un paso, actualizar la información asociada con un paso y administrar en caso contrario pasadas asociadas a la aplicación.

Las aplicaciones complementarias no deben intentar duplicar las características de Wallet. No pretenden mostrar pasadas para el análisis.

En el resto de esta sección se describe cómo crear una aplicación complementaria básico que interactúa con PassKit.

### <a name="provisioning"></a>Aprovisionamiento

Porque Wallet es una tecnología de almacén, la aplicación debe aprovisionar por separado y no puede usar el perfil de aprovisionamiento de equipo o Wildcard App ID. Hacer referencia a la [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) guía para crear un perfil de aprovisionamiento y un Id. de aplicación únicos para la aplicación Wallet.

### <a name="entitlements"></a>Derechos

El **Entitlements.plist** archivo se debe incluir en proyecto de Xamarin.iOS de todas las reciente. Para agregar un nuevo archivo Entitlements.plist, siga los pasos descritos en la [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guía.

Para establecer los derechos, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Haga doble clic en el **Entitlements.plist** archivo en el panel de solución para abrir el editor de Entitlements.plist:

![](passkit-images/image31.png "Editor de Entitlements.plst")

En la sección de Wallet, seleccione el **habilitar Wallet** opción

![](passkit-images/image32.png "Habilitar los derechos de wallet")


La opción predeterminada es para que la aplicación permitir que todos los tipos de pasar. Sin embargo, es posible restringir la aplicación y permitir sólo un subconjunto de tipos de pase de equipo. Para habilitar este, seleccione el **permitir subconjunto del equipo de pasar tipos** y escriba el identificador del tipo pass del subconjunto que desea permitir.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Haga doble clic en el **Entitlements.plist** archivo para abrir el archivo de origen XML.

Para agregar el derecho de Wallet, establezca el **propiedad** a `Passbook Identifiers` en la lista desplegable, que establecerá automáticamente el **tipo** `Array`. A continuación, establezca la cadena **valor** a `$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "Habilitar los derechos de wallet")

De esta manera, la aplicación admitirá todos los tipos de pases. Para restringir la aplicación y permitir sólo un subconjunto de tipos de pase de equipo, establezca el valor de cadena en:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Donde `pass.$(CFBundleIdentifier)` es el identificador del paso que se ha creado [anteriormente](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Depuración

Si tiene problemas al implementar la aplicación, compruebe que está usando el valor correcto **perfil de aprovisionamiento** y que la `Entitlements.plist` está seleccionado como el **derechos personalizados** archivo en el **firma de lote de iPhone** opciones.

Si experimenta este error al implementar:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

el `pass-type-identifiers` matriz derechos es incorrecta (o no coincide con el **perfil de aprovisionamiento**). Compruebe los identificadores de tipo pasar y el identificador del equipo son correctas.

## <a name="classes"></a>Clases

Las clases de PassKit siguientes están disponibles para las aplicaciones accedan a pasadas:

-  **PKPass** : es una instancia de un paso.
-  **PKPassLibrary** : proporciona la API para tener acceso a las fases en el dispositivo.
-  **PKAddPassesViewController** – utilizado para mostrar un paso para el usuario guardar en su billetera.
-  **PKAddPassesViewControllerDelegate** : los desarrolladores de Xamarin.iOS

## <a name="example"></a>Ejemplo

Hacer referencia al proyecto PassLibrary en el [código de ejemplo](https://developer.xamarin.com/samples/monotouch/PassKit/) para este artículo. Muestra las siguientes funciones comunes que serían necesarios en una aplicación complementaria de Wallet:

### <a name="check-that-wallet-is-available"></a>Compruebe que Wallet está disponible

Wallet no está disponible en el iPad, por lo que las aplicaciones deben comprobar antes de intentar tener acceso a características de PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Creación de una instancia de la biblioteca de Pass

La biblioteca de PassKit no es un singleton, las aplicaciones deben crear y almacenar y de instancia para obtener acceso a la API de PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Obtener una lista de fases

Las aplicaciones pueden solicitar una lista de fases de la biblioteca. Esta lista se filtra automáticamente por PassKit, para que solo puedan ver fases que se han creado con el identificador del equipo y que se muestran en los derechos.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Tenga en cuenta que el simulador no filtrar la lista de fases devuelto, por lo que este método siempre debe probarse en dispositivos reales. Esta lista se puede mostrar en un UITableView. El [aplicación de ejemplo](https://developer.xamarin.com/samples/monotouch/PassKit/) tiene este aspecto después de han agregado los dos cupones:

 [![](passkit-images/image29.png "El aspecto de la aplicación de ejemplo así después de que se han agregado dos cupones")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>Mostrar pasadas

Un conjunto limitado de información está disponible para la representación de fases de las aplicaciones complementarias.

Elija entre este conjunto de propiedades estándares para mostrar listas de pasadas, como hace el código de ejemplo.

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

Esta cadena se muestra como una alerta en el [ejemplo](https://developer.xamarin.com/samples/monotouch/PassKit/):

 [![](passkit-images/image30.png "La alerta seleccionada cupón en el ejemplo")](passkit-images/image30.png#lightbox)

También puede usar el `LocalizedValueForFieldKey()` método para recuperar datos de los campos en las fases han diseñado (puesto que sabrá qué campos deben estar presente). El código de ejemplo no muestra esto.

### <a name="loading-a-pass-from-a-file"></a>Cargar un pase de un archivo

Porque solo se puede agregar una fase a Wallet con el permiso del usuario, un controlador de vista debe estar presente para permitirles a decidir. Este código se usa en el **agregar** botón en el ejemplo, para cargar un pase pregenerado que está incrustado en la aplicación (que debe reemplazar con uno que han iniciado sesión):

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

Se presentará la fase de **agregar** y **cancelar** opciones:

 [![](passkit-images/image20.png "La fase que presentan las opciones de agregar y cancelar")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Reemplazar un paso existente

Reemplazar un paso existente no requiere el permiso del usuario, sin embargo, se producirá un error si el paso aún no existe.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Edición de un paso

PKPass no es mutable, por lo que no se puede actualizar los objetos de paso en el código. Para modificar los datos en un paso de una aplicación debe tener acceso a un servidor web que puede mantener un registro de pasadas y generar un nuevo archivo pass con valores actualizados que puede descargar la aplicación.

Creación del archivo paso debe realizarse en un servidor porque pasadas deben firmarse con un certificado que se debe mantener privada y segura.

Una vez que se ha generado un archivo actualizado de pass, utilice el `Replace` método para sobrescribir los datos antiguos en el dispositivo.

### <a name="display-a-pass-for-scanning"></a>Mostrar un paso de análisis

Como se indicó anteriormente, solo Wallet puede mostrar un paso para el análisis. Un paso puede mostrarse usando el `OpenUrl` método tal como se muestra:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Recibir notificaciones de cambios

Las aplicaciones pueden escuchar los cambios realizados en la biblioteca de pasar mediante la `PKPassLibraryDidChangeNotification`. La causa de los cambios podrían ser notificaciones desencadenar actualizaciones en segundo plano, por lo que es recomendable escuchar para ellos en la aplicación.

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

Es importante pasar una instancia de la biblioteca al registrarse para la notificación porque PKPassLibrary no es un singleton.

## <a name="server-processing"></a>Procesamiento del servidor

Para obtener una explicación detallada de la creación de una aplicación de servidor para admitir PassKit queda fuera del ámbito de este artículo introductorio.

Consulte [dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook) abierto C# código del lado servidor.

## <a name="push-notifications"></a>Notificaciones push

Para obtener una explicación detallada del uso de notificaciones de inserción para actualizar pases queda fuera del ámbito de este artículo introductorio.

Se necesitaría para implementar la API de REST como definido por Apple para responder a solicitudes web de Wallet, cuando se requieren actualizaciones.

Consulte Apple [actualizar un paso](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) guía para obtener más información.

## <a name="summary"></a>Resumen

En este artículo introdujo PassKit, que se describe algunas de las razones por las es útil y se describe las distintas partes que se deben implementar para una solución completa de PassKit. Describen los pasos necesarios para configurar la cuenta de desarrollador de Apple para crear pasadas, el proceso para crear un paso manualmente y también cómo acceder a las APIs de PassKit desde una aplicación de Xamarin.iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Wallet para desarrolladores](https://developer.apple.com/wallet/)
- [Ejemplo de PassKit](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [Guía del desarrollador de Wallet](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [Marcos: Apple Pay y Wallet (vídeos sesión WWDC)](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [Referencia de marco de PassKit](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Referencia de servicio Web de passbook](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [Acerca de los archivos de Pass](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook), una biblioteca de código abierto para generar paquetes de la cartera de iOS
- [Introducción a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
