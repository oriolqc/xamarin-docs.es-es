---
title: PassKit
description: Cartera es una aplicación de iOS de sistema que almacena y muestra los códigos de barras y otra información para vincular las transacciones del cliente en su teléfono con el mundo real.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: f1c8ac92c5ff7eed5116587ed13755ddee74a877
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="passkit"></a>PassKit

_Cartera es una aplicación de iOS de sistema que almacena y muestra los códigos de barras y otra información para vincular las transacciones del cliente en su teléfono con el mundo real._

Cartera es una aplicación para iPhone y iPod toca con iOS 6. Almacena y muestra los códigos de barras y otra información para vincular las transacciones del cliente en su teléfono con el mundo real. Se genera comerciantes pasadas y se envían al cliente por correo electrónico, direcciones URL o desde una aplicación de iOS del vendedor. Cartera almacena y organiza todas las pasadas en un teléfono y muestra los avisos de paso en la pantalla de bloqueo según la fecha y hora o la ubicación del dispositivo.

Este documento presenta la cartera, mediante la API de Kit pasar con Xamarin.iOS y describe cómo implementar pasadas en el servidor.

 [![](passkit-images/image1.png "La cartera almacena y organiza todas las pasadas en un teléfono")](passkit-images/image1.png#lightbox)


## <a name="requirements"></a>Requisitos

Las características de almacén Kit descritas en este documento requieren iOS 6 y 4.5 de Xcode, junto con Xamarin.iOS 6.0.


## <a name="introduction"></a>Introducción

Pasar Kit resuelve el problema de clave es la distribución y administración de códigos de barras. Algunos ejemplos reales de cómo se utilizan actualmente los códigos de barras son:

-   **Compra de vales de película en línea** : los clientes normalmente se envían por correo electrónico un código de barras que representa sus billetes. Este código de barras se imprime y se dirige a la cine que deben analizarse para la entrada.
-   **Tarjetas de fidelización** : los clientes llevan un número de tarjetas específicas del almacén diferentes en su cartera o cartera, para la presentación y análisis cuando compran productos.
-   **Cupones** – cupones se distribuyen a través de correo electrónico, como las páginas web imprimible, a través de buzones de correo y como códigos de barras en periódicos y revistas. Los clientes volver a ponen en un almacén para el análisis, para recibir los productos, servicios o descuentos en respuesta.
-   **Bordo pasadas** : Similar a la compra de un vale de película.


Kit de fase ofrece una alternativa para cada uno de estos escenarios:

-   **Vales de película** : después de la compra, el cliente agrega un paso de vale de evento (por correo electrónico o un vínculo al sitio Web). Como la hora de los enfoques de película, la fase aparecerán automáticamente en la pantalla de bloqueo como recordatorio, y a la llegada en el cine la fase de fácilmente se recuperan y muestran en cartera, para examinar.
-   **Tarjetas de fidelización** : en lugar de (o además) proporciona una tarjeta física, almacenes pueden emitir (a través de correo electrónico o después de un inicio de sesión del sitio Web) un paso de tarjeta de almacenamiento. El almacén puede proporcionar características adicionales, como la actualización el saldo de la cuenta en el paso a través de notificaciones de inserción, y usar servicios de ubicación geográfica la fase podría aparece automáticamente en la pantalla de bloqueo cuando el cliente se aproxime a una ubicación de almacén.
-   **Cupones** – cupón pasa fácilmente puede generar con características únicas para ayudar a seguimiento y distribuido a través de vínculos de correo electrónico o un sitio Web. Cupones descargados automáticamente pueden aparecer en la pantalla de bloqueo cuando el usuario está cerca de una ubicación específica, o en una determinada fecha (por ejemplo, cuando se está aproximando la fecha de expiración). Dado que los vales se almacenan en el teléfono del usuario, que siempre son útiles y no se traspapelan. Cupones pueden instar a los clientes para descargar aplicaciones complementaria porque los vínculos de la tienda de aplicaciones se pueden incorporar en el paso, aumentar la interacción con el cliente.
-   **Bordo pasadas** – después de un proceso en línea en el repositorio, el cliente recibiría su fase de incorporación a través de correo electrónico o un vínculo. Una aplicación complementaria proporcionado por el proveedor de transporte podría incluir el proceso en el repositorio y también permiten al cliente realizar funciones adicionales como elegir su puesto o pedido de comida. El proveedor de transporte puede usar notificaciones de inserción para actualizar el paso si el transporte se retrase o se canceló. Como el tiempo de embarque enfoques la fase aparecerá en la pantalla de bloqueo como recordatorio y para proporcionar acceso rápido a la fase.


En esencia, pasar Kit proporciona una manera sencilla y cómoda para almacenar y mostrar los códigos de barras en el dispositivo de iPhone o iPod touch. Con el tiempo adicional y la integración de la pantalla de bloqueo de ubicación, las notificaciones de inserción y aplicación complementaria de integran ofrece un foundation para las ventas muy sofisticadas, control de vales y facturación de servicios.


## <a name="pass-kit-ecosystem"></a>Pasar el ecosistema de Kit

Kit de paso no es simplemente una API en CocoaTouch, en su lugar es parte de un mayor ecosistema de aplicaciones, datos y servicios que facilitan el uso compartido seguro y administración de códigos de barras y otros datos. Este diagrama de alto nivel muestra las distintas entidades que pueden estar implicadas en la creación y uso de pasos:

 [![](passkit-images/image2.png "Este diagrama de alto nivel muestra las entidades implicadas en la creación y uso de pasadas")](passkit-images/image2.png#lightbox)

Cada parte del ecosistema tiene un rol claramente definido:

-   **Cartera** : aplicación de iOS integradas de Apple (para iPhone y iPod touch) que almacena y muestra pasadas. Esto es el único lugar en el que se representan las pasadas para su uso en el mundo real (es decir el código de barras se muestra, junto con todos los datos localizados en la fase).
-   **Complementario aplicaciones** : aplicaciones de iOS 6 creadas por los proveedores de paso para ampliar la funcionalidad de las fases de emitir, como agregar valor a una tarjeta de almacén, cambiar el puesto en un paso de embarque o de las demás funciones específicos del negocio. Asistente para aplicaciones no son necesarias para un paso ser útil.
-   **El servidor** : un servidor seguro donde se pueden generar y firmadas para su distribución pasadas. La aplicación complementaria puede conectarse al servidor para generar nueva pasadas o solicitar actualizaciones pasadas existentes. Si lo desea, puede implementar la API del servicio web que cartera llamaría a actualizar pasadas.
-   **Servidores de APN** : el servidor tiene la capacidad para notificar la cartera de actualizaciones a una fase en un dispositivo determinado con APNS. Insertar una notificación en la cartera que, a continuación, se pondrá en contacto con el servidor para obtener detalles sobre el cambio. Asistente para aplicaciones no es necesario implementar APN para esta característica (pueden escuchar el `PKPassLibraryDidChangeNotification` ).
-   **Conducto aplicaciones** : las aplicaciones que no manipulan directamente pasadas (como hacen el Asistente para aplicaciones), pero que puede mejorar su utilidad reconocido por fases y lo que va a agregar a la cartera. Los clientes de correo electrónico, exploradores de redes sociales y otras aplicaciones de la agregación de datos pueden todos encontrar los datos adjuntos o los vínculos en fases.


El ecosistema completo parece complejo, por lo que debe tener en cuenta que algunos componentes son opcionales y mucho más fácil implementaciones pasar Kit son posibles.

## <a name="what-is-a-pass"></a>¿Qué es un paso?

Un paso es una colección de datos que representan un vale, cupón o una tarjeta. Puede ser intencionado de forma individual para un solo uso (y, por tanto, contienen detalles como una asignación de número y puestos de vuelo) o es posible que un token de usar varios que se pueden compartir por cualquier número de usuarios (por ejemplo, un cupón descuento). Una descripción detallada está disponible en de Apple [archivos pasar](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) documento.


### <a name="types"></a>Tipos

Actualmente cinco tipos compatibles, que se pueden distinguir en la aplicación de cartera por el diseño y el borde superior de la fase de:

-  **Evento vale** – recorte semicircular pequeño.
-   **Fase de incorporación** – posiciones en paralelo, específica del transporte icono pueden especificarse (p. ej. bus, tren, avión).
-   **Almacenar tarjeta** : redondeado superior, como una tarjeta de crédito o débito.
-  **Cupón** : perforada a lo largo de la parte superior.
-  **Genérico** : igual que la tarjeta de la tienda, parte superior redondeada.


Se muestran los tipos de fase de cinco en esta captura de pantalla (en orden: cupón, genérico, almacenar tarjeta, fase de embarque y vales de evento):

 [![](passkit-images/image3.png "Se muestran los tipos de fase de cinco en esta captura de pantalla")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Estructura de archivos

Un archivo de fase es realmente un archivo ZIP con un **.pkpass** , que contiene algunos JSON archivos específicos (obligatorios), una variedad de imagen de archivos de extensión (opcional), así como las cadenas localizadas (también opcional).

-   **PASS.JSON** : es necesario. Contiene toda la información de la fase.
-   **manifest.JSON** : es necesario. Contiene el hash de SHA1 para cada archivo en la fase excepto el archivo de signatura y este archivo (manifest.json).
-   **firma** : es necesario. Creado mediante la firma de la `manifest.json` archivo con el certificado generado en el Portal de aprovisionamiento de iOS.
-  **Logo.png** : opcional.
-  **Background.png** : opcional.
-  **Icon.png** : opcional.
-  **Archivos de cadenas localizables** : opcional.


A continuación se muestra la estructura de directorios de un archivo de paso (este es el contenido del archivo ZIP):

 [![](passkit-images/image4.png "Estructura de directorios de un archivo de paso se muestra aquí")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON es el formato porque pasa normalmente se crea en un servidor: significa que el código de generación es independiente de la plataforma en el servidor. Las tres partes claves de la información en cada fase son:

-   **teamIdentifier** : Esto vincula todos los pasos que se genera para la cuenta de la tienda de aplicaciones. Este valor es visible en el Portal de aprovisionamiento de iOS.
-   **passTypeIdentifier** – registrar en el Portal de aprovisionamiento a grupo juntos pasa (si se genera más de un tipo). Por ejemplo, una cafetería podría crear un tipo de pasar de tarjeta de almacén para permitir que sus clientes disfrutar de créditos de fidelidad, sino también un tipo de pasar de cupón independiente para crear y distribuir cupones de descuento. Ese mismo cafetería incluso puede contener eventos de música en directo y emitir eventos vale pasadas para aquellos.
-   **serialNumber** : una cadena única dentro de este `passTypeidentifier` . El valor es opaco para cartera, pero es importante para el seguimiento de pasos específicos cuando se comunica con el servidor.


Hay un gran número de otras claves JSON en cada fase, se muestra un ejemplo de lo que a continuación:

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

Se admiten los formatos solo 2D: PDF417, Aztec, QR. Apple notificaciones que son adecuados para la detección en una pantalla de teléfono con retroiluminación códigos de barras 1D.

Texto alternativo que se muestra a continuación el código de barras es opcional, algunos de los comercios deseen ser capaz de lectura/tipo manualmente.

Codificación ISO-8859-1 es la comprobación más comunes, la codificación que es utilizada por los sistemas de análisis que va a leer las pasadas.

### <a name="relevancy-lock-screen"></a>Relevancia (pantalla de bloqueo)

Hay dos tipos de datos que pueden causar un paso que se mostrará en la pantalla de bloqueo:

 **Ubicación**

Hasta 10 ubicaciones puede especificarse en un paso, por ejemplo, los almacenes que un cliente visita con frecuencia, o la ubicación de un cine o un aeropuerto. Un cliente puede especificar estas ubicaciones a través de una aplicación complementaria o el proveedor podría determinar ellos desde los datos (si se recopilan con el permiso del cliente).

Cuando el paso se muestra en la pantalla de bloqueo, se calcula una barrera para que cuando el usuario abandona el área está oculta para el paso de la pantalla de bloqueo. El radio está enlazado para pasar los estilos para evitar abusos.

 **Fecha y hora**

Solo una fecha y hora puede especificarse en un paso. La fecha y hora es útil para activar los avisos de pantalla de bloqueo de embarque pasadas y vales de eventos.

Pueden actualizarse mediante inserción o a través de la API de PassKit, por lo que se pudo actualizar la fecha y hora en el caso de un vale de diversos usos (por ejemplo, un vale de temporada a un teatro o complejo deportivo).

### <a name="localization"></a>Localización

Traducir un paso en varios idiomas es similar a adaptar una aplicación de iOS: creación de lenguaje directorios específicos con el `.lproj` extensión y colocar los elementos localizados dentro. Las traducciones de texto deben escribirse en un `pass.strings` de archivos, mientras imágenes localizadas deben tener el mismo nombre que la imagen que se reemplazan en la raíz de la fase.

## <a name="security"></a>Seguridad

Pasadas se firman con un certificado privado que se genera en el Portal de aprovisionamiento de iOS. Los pasos para iniciar la fase de son:

1.  Calcular un hash SHA1 para cada archivo en el directorio de paso (no incluya la `manifest.json` o `signature` archivo, ninguna de las cuales debe existir en esta fase de todos modos).
1.  Escribir `manifest.json` como una lista de clave/valor JSON de cada nombre de archivo con el hash.
1.  Usar el certificado para firmar el `manifest.json` archivo y escribe el resultado en un archivo denominado `signature` .
1.  COMPRIMIR el todo y asigne al archivo resultante una `.pkpass` la extensión de archivo.


Dado que se requiere la clave privada para firmar la fase, este proceso solo debe realizarse en un servidor seguro que usted controla. No distribuya las claves para intentar generar pasadas en una aplicación.

 
## <a name="configuration-and-setup"></a>Instalación y configuración

Esta sección contiene instrucciones para ayudar a configurar los detalles de aprovisionamiento y crear su primer paso.

### <a name="provisioning-passkit"></a>Aprovisionamiento PassKit

Para un paso escribir la tienda de aplicaciones, debe vincularse a una cuenta de desarrollador. Esto requiere dos pasos:

1.  La fase se debe registrar con un identificador único, denominado el identificador de tipo pasar.
1.  Debe generarse un certificado válido para firmar la fase con firma digital del desarrollador.

Para crear el siguiente no pasar el identificador de tipo.


<a name="create-passid"/>

#### <a name="create-a-pass-type-id"></a>Crear un identificador de tipo de paso

El primer paso es configurar un identificador de tipo pasar para diferentes _tipo_ de pase a ser compatibles. El Id. de pasar (o identificador de tipo pasar) crea un identificador único para el paso. Se usará este identificador para vincular el paso con la cuenta de desarrollador con un certificado.

1. En el [sección certificados, identificadores y los perfiles del Portal de aprovisionamiento de iOS](https://developer.apple.com/account/overview.action), vaya a **identificadores** y seleccione **pasar identificadores de tipo** . A continuación, seleccione la **+** botón para crear un nuevo tipo de paso: [ ![ ] (passkit-images/passid.png "crear un nuevo tipo de paso")](passkit-images/passid.png#lightbox)

2.   Proporcionar un **descripción** (nombre) y **identificador** (cadena única) para la fase. Tenga en cuenta que todos los identificadores de tipo pasar debe comenzar con la cadena `pass.` en este ejemplo se utiliza `pass.com.xamarin.coupon.banana` : [ ![ ] (passkit-images/register.png "proporcionar una descripción y un identificador")](passkit-images/register.png#lightbox)


3.   Confirmar el Id. de pasar presionando el **registrar** botón.


<a name="generate" />

#### <a name="generate-a-certificate"></a>Generar un certificado

Para crear un nuevo certificado para este identificador de tipo pasar, haga lo siguiente:

1.  Seleccione el identificador de pasar recién creado en la lista y haga clic en **editar** : [ ![ ] (passkit-images/pass-done.png "seleccione el nuevo identificador de pasar de la lista")](passkit-images/pass-done.png#lightbox)

    A continuación, seleccione **Create Certificate...** :

    [![](passkit-images/cert-dist.png "Seleccione Crear certificado")](passkit-images/cert-dist.png#lightbox)


2.  Siga los pasos para crear una firma de solicitud certificado (CSR).
  
3. Presione el **continuar** situado en el portal para desarrolladores y cargar la CSR para generar el certificado.

4. Descargue el certificado y haga doble clic en él para instalarlo en su cadena de claves.


Ahora que hemos creado un certificado para este identificador de tipo pasar, la siguiente sección describe cómo crear un paso manualmente.

Para obtener más información sobre el aprovisionamiento de cartera, consulte el [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) guía.

 <a name="Create_a_Pass_Manually" />

### <a name="create-a-pass-manually"></a>Crear un paso manualmente

Ahora que hemos creado el tipo pasar manualmente se pueden crear un paso de prueba en el simulador o en un dispositivo. Los pasos para crear un acceso son:

-  Cree un directorio para que contenga los archivos de paso.
-  Cree un archivo pass.json que contiene todos los datos necesarios.
-  Incluir imágenes en la carpeta (si es necesario).
-  Calcular el hash SHA1 para todos los archivos de la carpeta y escribir en manifest.json.
-  Manifest.json de inicio de sesión con el archivo de certificado descargado. p12.
-  COMPRIMA el contenido del directorio y cambiar el nombre con extensión .pkpass.


Hay algunos archivos de origen en el código de ejemplo de este artículo que puede usarse para generar un paso. Usar los archivos en el `CouponBanana.raw` directorio del directorio CreateAPassManually. Los archivos siguientes están presentes:

 [![](passkit-images/image18.png "Estos archivos están presentes")](passkit-images/image18.png#lightbox)

Abra pass.json y edite el JSON. Al menos debe actualizar el `passTypeIdentifier` y `teamIdentifer` para que coincida con la cuenta de desarrollador de Apple.

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

A continuación, debe calcular los valores hash de cada archivo y crear el `manifest.json` archivo. Tendrá un aspecto similar al siguiente cuando haya terminado:

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

 <a name="Signing_On_a_Mac" />


#### <a name="signing-on-a-mac"></a>Iniciar sesión en un equipo Mac

Descargue el **materiales de soporte técnico de inicialización de cartera** desde el [descarga Apple](https://developer.apple.com/downloads/index.action?name=Passbook) sitio. Use la `signpass` herramienta para convertir la carpeta en un paso (Esto también calculará el SHA1 aplica un algoritmo hash y código postal de la salida en un archivo .pkpass).

 <a name="Signing_On_a_PC" />


#### <a name="signing-on-a-pc"></a>Iniciar sesión en un equipo

En el ejemplo de código para este artículo no existe, es un proyecto denominado `signpassnet` que se ejecuta en .NET en Windows. Intenta imitar herramienta de Apple, sin embargo, incluye mucho menos código de validación.

 <a name="Testing" />


#### <a name="testing"></a>Pruebas

Si tuviera que examinar los resultados de estas herramientas (establecer el nombre de archivo a .zip y, a continuación, ábralo), verá los archivos siguientes (tenga en cuenta la adición de la `manifest.json` y `signature` archivos):

 [![](passkit-images/image19.png "Examinar los resultados de estas herramientas")](passkit-images/image19.png#lightbox)

Una vez iniciado, ZIP y cambiar el nombre del archivo (p. ej. para `BananaCoupon.pkpass`) puede arrastrar en el simulador para probar o enviar por correo electrónico a sí mismo para recuperar en un dispositivo real. Debería ver una pantalla a **agregar** el paso, similar al siguiente:

 [![](passkit-images/image20.png "Agregar la pantalla de paso")](passkit-images/image20.png#lightbox)

Normalmente se debería automatizar ese proceso en un servidor, sin embargo manual creación paso podría ser una opción para pequeñas empresas que sólo va a crear cupones que no requieren la compatibilidad de un servidor back-end.

 <a name="Wallet" />

## <a name="wallet"></a>Cartera

Cartera es la parte central del ecosistema pasar Kit. Esta captura de pantalla muestra la cartera vacía y el aspecto de la lista de paso y pasa individual:

 [![](passkit-images/image21.png "Esta captura de pantalla muestra la cartera vacía y el aspecto de la lista de paso y pasa individual")](passkit-images/image21.png#lightbox)

Características de cartera incluyen:

-  Es el único lugar pasadas se representan con su código de barras para el análisis.
-  Usuario puede cambiar la configuración de actualizaciones. Si está habilitada, las notificaciones de inserción pueden desencadenar actualizaciones a los datos en el paso.
-  Usuario puede habilitar o deshabilitar la integración de la pantalla de bloqueo. Si está habilitada, esto permite el paso de aparecen automáticamente en su pantalla de bloqueo, que se basa en datos relevantes de hora y la ubicación incrustados en la fase de.
-  La parte posterior de la fase admite Deslizar para actualizar, si se proporciona una URL de servidor web en el JSON pasar.
-  Complementaria aplicaciones puede abrir (o descargar) si se proporciona el Id. de la aplicación en el JSON pasar.
-  Fases pueden eliminarse (con una animación de purga hermosa).


 <a name="Getting_Passes_into_Wallet" />

## <a name="adding-passes-into-wallet"></a>Agregar pasadas en cartera

Fases pueden agregarse a cartera de las maneras siguientes:

* **Conducto aplicaciones** : estos manipular no pasa directamente, simplemente cargar archivos de paso y presentan al usuario con la opción de agregarlos a cartera. 

* **Complementario aplicaciones** : estos se escriben proveedores para distribuir pasadas y ofrecen una funcionalidad adicional para examinar o editarlos. Aplicaciones de Xamarin.iOS tienen acceso completo a la API de Kit pasar para crear y manipular pasadas. A continuación, se puede agregar pasa al uso de la cartera del `PKAddPassesViewController`. Este proceso se describe con más detalle en la **aplicaciones complementarias** sección de este documento.

### <a name="conduit-applications"></a>Aplicaciones de conducto

Aplicaciones de conducto son aplicaciones intermedias que es posible que reciba pasadas en nombre del usuario y deben programarse para que reconozca su tipo de contenido y ofrecen una funcionalidad para agregar a la cartera. Ejemplos de aplicaciones de la canalización:

-   **Correo electrónico** – reconoce los datos adjuntos como un paso.
-   **Safari** – reconoce el tipo de contenido pasar cuando se hace clic en un vínculo URL pasar.
-   **Otras aplicaciones personalizadas** : cualquier aplicación que recibe los datos adjuntos o abrir vínculos (los clientes de redes sociales, lectores de correo electrónico, etcetera).


Esta captura de pantalla muestra cómo **correo** en iOS 6 reconoce un archivo adjunto de paso y (cuando tocadas) ofrece la posibilidad de **agregar** a cartera.

 [![](passkit-images/image22.png "Esta captura de pantalla muestra la forma en que el correo electrónico de iOS 6 reconoce un archivo adjunto de paso")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "Esta captura de pantalla muestra cómo ofrece el correo electrónico para agregar un anexo pase a cartera")](passkit-images/image23.png#lightbox)

Si está compilando una aplicación que podría ser un conducto para pasadas, pueden ser reconocidos por:

-  **Extensión de archivo** -.pkpass
-  **Tipo MIME** -application/vnd.apple.pkpass
-  **UTI** – com.apple.pkpass


La operación básica de una aplicación de conducto es recuperar el archivo de paso y llamar a pasar Kit `PKAddPassesViewController` para dar al usuario la opción para agregar el paso a su cartera. La implementación de este controlador de vista se trata en la sección siguiente en **aplicaciones complementarias**.

Las aplicaciones de la canalización no es necesario se aprovisione para un identificador de tipo específico de pasar de la misma manera que las aplicaciones complementarias.

## <a name="companion-applications"></a>Asistente para aplicaciones

Una aplicación complementaria proporciona funcionalidad adicional para trabajar con fases, incluida la creación de un paso, actualizar la información asociada a un paso y en caso contrario, pasa asociado a la aplicación.

Aplicaciones complementarias no deben intentar duplicar las características de cartera. No pretenden mostrar fases para el análisis.

Este restantes de esta sección describen cómo compilar una aplicación complementaria básica que interactúa con el Kit de pasar.

### <a name="provisioning"></a>Aprovisionamiento

Dado que cartera es una tecnología de almacenamiento, la aplicación debe aprovisionar por separado y no puede usar el perfil de aprovisionamiento de equipo o Wildcard App ID. Hacer referencia a la [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) guía para crear un único identificador de la aplicación y perfil de aprovisionamiento para la aplicación de cartera.

### <a name="entitlements"></a>Derechos

El **Entitlements.plist** archivo debe incluirse en todas las reciente proyecto Xamarin.iOS. Para agregar un nuevo archivo Entitlements.plist, siga los pasos descritos en la [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guía.

Para establecer derechos haga lo siguiente:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Haga doble clic en el **Entitlements.plist** archivo en el panel de la solución para abrir el editor de Entitlements.plist:

![](passkit-images/image31.png "Editor de Entitlements.plst")

En la sección de la cartera, seleccione la **habilitar cartera** opción

![](passkit-images/image32.png "Habilitar derechos de cartera")


La opción predeterminada es de la aplicación permitir que todos los tipos de pasar. Sin embargo, es posible restringir la aplicación y que solo un subconjunto de tipos de fase de equipo. Para habilitar esta instrucción select la **permitir subconjunto del equipo de pasar tipos** y especifique el identificador de tipo de paso del subconjunto que desea permitir.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Haga doble clic en el **Entitlements.plist** archivo para abrir el archivo de origen XML.

Para agregar los derechos de cartera, establezca el **propiedad** a `Passbook Identifiers` en la lista desplegable, que establecerá automáticamente el **tipo** `Array`. A continuación, establezca la cadena **valor** a `$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "Habilitar derechos de cartera")

De esta manera, la aplicación admitirá todos los tipos de pases. Para restringir la aplicación y que solo un subconjunto de tipos de fase de equipo, establezca el valor de cadena en:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Donde `pass.$(CFBundleIdentifier)` es el identificador pasar en el que se ha creado [anteriormente](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Depuración

Si tiene problemas al implementar la aplicación, compruebe que está usando el valor correcto **perfil de aprovisionamiento de** y que la `Entitlements.plist` está seleccionado como el **derechos personalizada** archivo en el **iPhone agrupación firma** opciones.

Si experimenta este error durante la implementación:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

la `pass-type-identifiers` matriz derechos es incorrecta (o no coincide con el **perfil de aprovisionamiento de**). Compruebe que los identificadores de tipo pasar y su identificador de equipo son correctas.

 <a name="Classes" />

## <a name="classes"></a>Clases

Las siguientes clases de pasar Kit están disponibles para las aplicaciones tener acceso a las fases:

-  **PKPass** : una instancia de un paso.
-  **PKPassLibrary** : proporciona la API para tener acceso a las fases en el dispositivo.
-  **PKAddPassesViewController** – utilizado para mostrar un paso para el usuario si desea guardar en su cartera.
-  **PKAddPassesViewControllerDelegate** – Xamarin.iOS developers


## <a name="example"></a>Ejemplo

Hace referencia al proyecto PassLibrary en el código de ejemplo de este artículo. Muestra las funciones comunes siguientes que sean necesarias en una aplicación complementaria de cartera:

### <a name="check-that-wallet-is-available"></a>Compruebe que la cartera está disponible

Cartera no está disponible en el iPad, por lo que las aplicaciones deben comprobar antes de intentar obtener acceso a características de pasar Kit.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Creación de una instancia de la biblioteca de paso

La biblioteca de pasar Kit no es un singleton, las aplicaciones deben crear y almacenar e instancia para tener acceso a la API de Kit pasar.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Obtener una lista de los pasos de

Las aplicaciones pueden solicitar una lista de fases de la biblioteca. Esta lista se filtra automáticamente Kit pasar, por lo que solo puede ver pasos que se han creado con el identificador de equipo y que se muestran en los derechos.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Tenga en cuenta que el simulador no filtra la lista de los pasos que se devuelve, de por lo que este método siempre debe probarse en dispositivos reales. Esta lista se puede mostrar en un UITableView, la apariencia de la aplicación de ejemplo similar al siguiente después de que se han agregado dos cupones:

 [![](passkit-images/image29.png "La apariencia de la aplicación de ejemplo similar a éste después de que se han agregado dos cupones")](passkit-images/image29.png#lightbox)


### <a name="displaying-passes"></a>Mostrar pasadas

Un conjunto limitado de información está disponible para la representación de fases dentro de las aplicaciones complementarias.

Elija de este conjunto de propiedades estándar para mostrar listas de fases, tal como hace el código de ejemplo.

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

Esta cadena se muestra como una alerta en el ejemplo:

 [![](passkit-images/image30.png "La alerta seleccionada del cupón en el ejemplo")](passkit-images/image30.png#lightbox)

También puede usar el `LocalizedValueForFieldKey()` método para recuperar datos de campos en las fases que se han diseñado (puesto que sepa qué campos deben estar presente). El código de ejemplo no muestra esto.

### <a name="loading-a-pass-from-a-file"></a>Cargar un paso de un archivo

Dado que un paso solo se puede agregar a cartera con el permiso del usuario, un controlador de vista debe estar presente para que les permiten decidir. Este código se usa en la **agregar** botón en el ejemplo, para cargar un paso pregenerado que está incrustado en la aplicación (que debe reemplazar con uno que se haya suscrito):

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

 [![](passkit-images/image20.png "El paso que se presentan las opciones de agregar y cancelar")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Reemplazar una fase existente

Reemplazar una fase existente no requiere el permiso del usuario, sin embargo, se producirá un error si no existe la fase.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Edición de un paso

PKPass no es mutable, por lo que no se puede actualizar los objetos de paso en el código. Para modificar los datos en una fase de una aplicación debe tener acceso a un servidor web que se pueden mantener un registro de pasadas y generar un nuevo archivo de paso con valores actualizados que puede descargar la aplicación.

Fase de creación del archivo debe realizarse en un servidor porque pasadas deben firmarse con un certificado que se debe mantener privada y segura.

Una vez que se ha generado un archivo actualizado de paso, utilice el `Replace` método para sobrescribir los datos antiguos en el dispositivo.

### <a name="display-a-pass-for-scanning"></a>Mostrar un paso para el análisis

Como se indicó anteriormente, solo cartera puede mostrar un paso para el análisis. Se pueden mostrar un paso con el `OpenUrl` método tal como se muestra:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Recibir notificaciones de cambios

Las aplicaciones pueden realizar escuchas de cambios realizados en la biblioteca pasar mediante el `PKPassLibraryDidChangeNotification`. Cambios pudieron deberse a las notificaciones de activación de las actualizaciones en segundo plano, por lo que es recomendable escuchar para ellos en la aplicación.

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

Una explicación detallada de la creación de una aplicación de servidor para admitir pasar Kit queda fuera del ámbito de este artículo introductorio.

El código de .NET proporcionado en el *signpassnet* ejemplo podría utilizarse como base para un método de servidor que puede generar pasadas.

Vista [WWDC vídeo: Introducción a Libreta, parte 2](https://developer.apple.com/videos/wwdc/2012/?include=309#309) desde 27:00 minutos para obtener más información.

### <a name="other-resources"></a>Otros recursos

Vea [dotnet libreta](https://github.com/tomasmcguinness/dotnet-passbook) abra código fuente C# del servidor.

## <a name="push-notifications"></a>Notificaciones push

Obtener una explicación detallada del uso de notificaciones de inserción para actualizar pasa queda fuera del ámbito de este artículo de introducción.

Se necesitarían para implementar la API de REST tipo definido por Apple para responder a las solicitudes web de cartera cuando se requieren actualizaciones. El código de .NET proporcionado en el *signpassnet* ejemplo podría utilizarse como base para generar nueva pasadas como resultado de dichas solicitudes.

Vista [WWDC vídeo: Introducción a Libreta, parte 2](https://developer.apple.com/videos/wwdc/2012/?include=309#309) desde 27:00 minutos para obtener más información.

## <a name="summary"></a>Resumen

En este artículo introdujo pasar Kit, que se describe algunos de los motivos por los es útil y se describe las distintas partes que deben implementarse para una solución completa de pasar Kit. Describen los pasos necesarios para configurar la cuenta de desarrollador de Apple para crear pasos, el proceso de hacer un paso manualmente y también cómo obtener acceso a las API de Kit pasar desde una aplicación de Xamarin.iOS.

## <a name="related-links"></a>Vínculos relacionados

- [CreateAPassManually (ejemplo)](https://developer.xamarin.com/samples/PassKit/)
- [Ejemplo de PassKit](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [Introducción a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Guía de programación de libreta](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Conceptual/PassKit_PG/Chapters/Introduction.html)
- [Libreta para desarrolladores](https://developer.apple.com/passbook/)
- [Acerca de los archivos de paso](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [Pasar referencia de marco de trabajo Kit](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Pasar referencia de marco de trabajo Kit](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Referencia de servicio Web de libreta](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
