---
title: CloudKit
description: "iCloud API permiten que las aplicaciones de iOS 8 almacenar datos en iCloud, con compatibilidad con la sincronización automática a través de una cuenta de usuario. Usar CloudKit proporciona a los usuarios una experiencia coherente y sin problemas entre dispositivos compatibles con iCloud. Este artículo tratan habilitar CloudKit en una aplicación de iOS 8 mediante la API de comodidad."
ms.topic: article
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/11/2016
ms.openlocfilehash: f55620720bb986142a56de7e8602be56280006d4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="cloudkit"></a>CloudKit

_iCloud API permiten que las aplicaciones de iOS 8 almacenar datos en iCloud, con compatibilidad con la sincronización automática a través de una cuenta de usuario. Usar CloudKit proporciona a los usuarios una experiencia coherente y sin problemas entre dispositivos compatibles con iCloud. Este artículo tratan habilitar CloudKit en una aplicación de iOS 8 mediante la API de comodidad._

El marco de trabajo CloudKit simplifica el desarrollo de aplicaciones que iCloud de acceso. Esto incluye la recuperación de datos de la aplicación y activos derechos así como la posibilidad de almacenar información de la aplicación de forma segura. Este kit ofrece a los usuarios un nivel de anonimato permitiendo el acceso a las aplicaciones con su identificadores de iCloud sin compartir información personal.

Los programadores pueden centrarse en sus aplicaciones del lado cliente y permiten iCloud elimina la necesidad de escribir una lógica de aplicación de servidor. CloudKit proporciona autenticación, en las bases de datos públicas y privadas y los datos estructurados y los servicios de almacenamiento de activos.

## <a name="requirements"></a>Requisitos

El siguiente es necesario para completar los pasos que aparecen en este artículo:

-  **Xcode y el SDK de iOS** – Apple Xcode y iOS 8 API deben instalarse y configurarse en el equipo del desarrollador.
-  **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo de usuario.
-  **iOS 8 dispositivo** : un dispositivo iOS ejecutando la última versión de iOS 8 para las pruebas.

## <a name="what-is-cloudkit"></a>¿Qué es CloudKit?

CloudKit es una manera para permitir el acceso de desarrollador en los servidores de iCloud. Proporciona la base para iCloud unidad y iCloud biblioteca de fotografías. CloudKit es compatible con dispositivos de Mac OS X y Apple iOS.

 [ ![](intro-to-cloudkit-images/image1.png "Si se admite CloudKit en Mac OS X y dispositivos de Apple iOS")](intro-to-cloudkit-images/image1.png)

CloudKit utiliza la infraestructura de la cuenta de iCloud. Si hay un usuario ha iniciado sesión en una cuenta en el dispositivo de iCloud, CloudKit usará su Id. para identificar al usuario. Si no hay ninguna cuenta está disponible, se proporcionará acceso limitado de solo lectura.

CloudKit admite tanto el concepto de bases de datos públicas y privadas. Bases de datos públicas proporcionan una "sopa" de todos los datos que el usuario tenga acceso a. Las bases de datos privados están diseñados para almacenar los datos privados enlazados a un usuario específico.

CloudKit es compatible con los datos estructurados y de forma masiva. Es capaz de controlar las transferencias de archivos grandes sin problemas. CloudKit se encarga de forma eficaz transferir archivos grandes a y desde lo servidores de iCloud en segundo plano, liberando al programador centrarse en otras tareas.

> [!NOTE]
> **Nota:** es importante tener en cuenta que es CloudKit una _tecnología de transporte_. No se ofrecen persistencia; solo permite que una aplicación enviar y recibir información de los servidores de forma eficaz.

Cuando se redactó este documento, Apple inicialmente proporciona CloudKit gratuitamente con un límite más alto en la capacidad de ancho de banda y almacenamiento. Para mayor proyectos o aplicaciones con una base de usuarios grande, Apple ha indicado que se proporcionen una escala de precios asequible.


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Habilitar CloudKit en una aplicación de Xamarin

Antes de una aplicación de Xamarin puede utilizar el marco de trabajo CloudKit, debe estar correctamente la aplicación tal como se detalla en la [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) y [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guías

1.  Abra el proyecto en Visual Studio para Mac o en Visual Studio.
2.  En el **el Explorador de soluciones**, abra el **Info.plist** de archivos y asegúrese del **identificador de paquete** coincide con la que se definió en **Id. de aplicación**creado como parte del aprovisionamiento configurar:
 
    [ ![](intro-to-cloudkit-images/image26a.png "Especifique el identificador de paquete")](intro-to-cloudkit-images/image26a-orig.png "Info.plist file displaying Bundle Identifier")

3.  Desplácese hacia abajo hasta la parte inferior de la **Info.plist** de archivos y seleccione **habilitado modos en segundo plano**, **ubicación actualizaciones** y **notificaciones remoto**:

    [ ![](intro-to-cloudkit-images/image27a.png "Seleccione habilitado modos en segundo plano, las actualizaciones de la ubicación y notificaciones remotas")](intro-to-cloudkit-images/image27a-orig.png "Info.plist file displaying background modes")
4.  Haga clic en el proyecto de iOS en la solución y seleccione **opciones**.
5.  Seleccione **agrupación de firma de iOS**, seleccione la **Developer identidad** y **perfil de aprovisionamiento** creado anteriormente.
6.  Asegúrese del **Entitlements.plist** incluye **habilitar iCloud** , **almacenamiento de información de clave y valor** y **CloudKit** .
7.  Asegúrese del **ubicuidad contenedor** existe para la aplicación (como creada anteriormente). Ejemplo: `iCloud.com.your-company.CloudKitAtlas`
8.  Guarde los cambios en el archivo.


Con esta configuración en su lugar, la aplicación ahora está lista para tener acceso a las API de Framework CloudKit.

## <a name="cloudkit-api-overview"></a>Introducción a la API CloudKit

Antes de implementar CloudKit en una aplicación de iOS de Xamarin, en este artículo se va a cubre los aspectos fundamentales de CloudKit Framework, que incluirá los siguientes temas:

1.  **Contenedores** – aislada silos de comunicaciones de iCloud.
2.  **Las bases de datos** – públicas y privadas están disponibles para la aplicación.
3.  **Registros** : el mecanismo en el que se mueven datos estructurados hacia y desde CloudKit.
4.  **Zonas de registro** : son grupos de registros.
5.  **Registrar identificadores** : totalmente se normalizan y representar la ubicación específica del registro.
6.  **Referencia** : proporcionar elementos primarios y secundarios de las relaciones entre los registros relacionados en una base de datos.
7.  **Activos** : permitir que el archivo de datos no estructurados grandes para cargarse en iCloud o asociados a un registro determinado.


### <a name="containers"></a>Contenedores

Siempre se está ejecutando una aplicación determinada que se ejecuta en un dispositivo iOS en paralelo junto otras aplicaciones y servicios en dicho dispositivo. En el dispositivo de cliente, la aplicación se va a ser silos o en un espacio aislado de alguna manera. En algunos casos, se trata de un espacio aislado literal y en otros casos, la aplicación es simplemente ejecutar en él es propio espacio de memoria.

El concepto de tomar una aplicación cliente y ejecutarlo separada de otros clientes es muy eficaz y proporciona las siguientes ventajas:

1.  **Seguridad** : una aplicación no puede interferir con otras aplicaciones de cliente o el sistema operativo.
1.  **Estabilidad** : Si bloquea la aplicación cliente no se puede suscribir otras aplicaciones del sistema operativo.
1.  **Privacidad** : cada aplicación cliente tiene acceso limitado a la información personal almacenada en el dispositivo.


CloudKit se diseñó para proporcionar las mismas ventajas que se muestran los pasos anteriores y aplicarlas a trabajar con información basada en la nube:

 [ ![](intro-to-cloudkit-images/image31.png "Las aplicaciones de CloudKit comunicarse con contenedores")](intro-to-cloudkit-images/image31.png)

Al igual que la aplicación que está ejecutando en el dispositivo, de uno de varios es por lo que las comunicaciones de la aplicación con uno de varios iCloud. Cada uno de estos silos de comunicación diferentes se denominan contenedores.

Los contenedores se exponen en el marco de trabajo CloudKit a través de la `CKContainer` clase. De forma predeterminada, se comunica una aplicación con un contenedor y este contenedor aísla los datos para esa aplicación. Esto significa que varias aplicaciones pueden estar almacenando información en la misma cuenta de iCloud, pero nunca se le mezclada esta información.

La inclusión en contenedores de datos de iCloud también permite CloudKit encapsular la información de usuario. De esta manera, la aplicación tendrá acceso limitado a la cuenta de iCloud y la información de usuario se almacena en, todo ello mientras todavía se protege la privacidad y seguridad del usuario.

Los contenedores están totalmente administrados por el desarrollador de la aplicación a través del portal WWDR. El espacio de nombres del contenedor es global en todos los desarrolladores de Apple, por lo que el contenedor no sólo debe ser único para las aplicaciones de un desarrollador determinado, pero para todas las aplicaciones y los desarrolladores de Apple.

Apple sugiere utilizando la notación DNS inversa al crear el espacio de nombres para los contenedores de la aplicación. Ejemplo:

```csharp
iCloud.com.company-name.application-name
```

Mientras que los contenedores son, de forma predeterminada, enlazado uno a uno para cada aplicación, se puede compartir entre aplicaciones. Por lo que pueden coordinar varias aplicaciones en un único contenedor. Una sola aplicación también puede hablar con varios contenedores.

### <a name="databases"></a>Bases de datos

Una de las funciones principales de CloudKit es a modelo de datos de una aplicación y la replicación de ese modelo hasta los servidores de iCloud. Parte de la información está diseñado para el usuario que lo creó, otra información datos públicos que se pueden crear por un usuario para uso público (por ejemplo, una revisión de restaurantes), o podría ser información que el desarrollador ha publicado para la aplicación. En cualquier caso, la audiencia no es simplemente un solo usuario, pero es una comunidad de personas.

 [ ![](intro-to-cloudkit-images/image32.png "Diagrama de contenedor de CloudKit")](intro-to-cloudkit-images/image32.png)

Dentro de un contenedor, en primer lugar y más importante es la base de datos público. Aquí es donde toda la información pública vive y mingles conjuntamente. Además, existen varios privadas bases de datos individuales para cada usuario de la aplicación.

Cuando se ejecuta en un dispositivo iOS, la aplicación solo tendrá acceso a la información para el usuario ha iniciado sesión actualmente iCloud. Por lo tanto, la vista de la aplicación del contenedor será como se indica a continuación:

 [ ![](intro-to-cloudkit-images/image33.png "La vista de aplicaciones del contenedor")](intro-to-cloudkit-images/image33.png)

Solo pueden ver la base de datos público y la base de datos privada asociada con el usuario ha iniciado sesión actualmente iCloud.

Las bases de datos se exponen en el marco de trabajo CloudKit a través de la `CKDatabase` clase. Cada aplicación tiene acceso a dos bases de datos: la base de datos público y uno privado.

El contenedor es el punto de entrada inicial a CloudKit. El código siguiente puede utilizarse para tener acceso a la base de datos público y privado desde el contenedor predeterminado de la aplicación:

```csharp
using CloudKit;
...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

Estas son las diferencias entre los tipos de base de datos:

<table cellpadding="1" cellspacing="1" border="1" width="100%">
<thead>
<tr>
<td></td>
<td>Base de datos público</td>
<td>Base de datos privados</td>
</tr>
</thead>
<tbody>
<tr>
<td>Tipo de datos</td>
<td>Datos compartidos</td>
<td>Datos del usuario actual</td>
</tr>

<tr>
<td>Cuota</td>
<td>Tenga en cuenta en la cuota del desarrollador</td>
<td>Tenga en cuenta en la cuota del usuario</td>
</tr>

<tr>
<td>Permisos predeterminados</td>
<td>Mundo legible</td>
<td>Usuario legible</td>
</tr>

<tr>
<td>Editar permisos</td>
<td>iCloud panel Roles a través de un nivel de clase de registro</td>
<td>N/D</td>
</tr>
</tbody>
</table>

### <a name="records"></a>Registros

Contenedores contienen las bases de datos y, dentro de las bases de datos son registros. Los registros son el mecanismo en el que se mueven datos estructurados hacia y desde CloudKit:

 [ ![](intro-to-cloudkit-images/image34.png "Contenedores contienen las bases de datos y, dentro de las bases de datos son registros")](intro-to-cloudkit-images/image34.png)

Los registros se exponen en el marco de trabajo CloudKit a través de la `CKRecord` (clase), que contenga pares de clave y valor. Una instancia de un objeto en una aplicación es equivalente a un `CKRecord` en CloudKit. Además, cada `CKRecord` posee un tipo de registro, que es equivalente a la clase de un objeto.

Los registros tienen un esquema de just-in-time, por lo que los datos se describen a CloudKit antes de que se entregue para su procesamiento. Desde ese punto, CloudKit interpreta la información y controlar la logística necesaria para almacenar y recuperar el registro.

La `CKRecord` clase también admite una amplia gama de metadatos. Por ejemplo, un registro contiene información acerca de cuándo se creó y el usuario que lo creó. Un registro también contiene información sobre el usuario que modificó y se modificó por última vez.

Los registros contienen la noción de una etiqueta de cambio. Se trata de una versión anterior de una revisión de un registro determinado. La etiqueta de cambio se utiliza como una manera ligera de determinar si el cliente y el servidor tienen la misma versión de un registro determinado.

Como se indicó anteriormente, `CKRecords` ajustar pares de clave-valor y por lo tanto, los siguientes tipos de datos pueden almacenarse en un registro de:

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


Además de los tipos de valor único, un registro puede contener una matriz homogénea de cualquiera de los tipos enumerados anteriormente.

El código siguiente puede utilizarse para crear un nuevo registro y almacenarla en una base de datos:

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>Zonas de registro

No existen registros por sí solas o dentro de una base de datos: grupos de registros juntos existen dentro de una zona de registro. Zonas de registro puede considerarse como tablas en una base de datos relacionales tradicionales:

 [ ![](intro-to-cloudkit-images/image35.png "Grupos de registros juntos existen dentro de una zona de registro")](intro-to-cloudkit-images/image35.png)

Puede haber varios registros dentro de una zona de registro determinada y varias zonas de registro en una base de datos. Cada base de datos contiene una zona de registro de forma predeterminada:

 [ ![](intro-to-cloudkit-images/image36.png "Cada base de datos contiene una zona de registro predeterminada y la zona personalizada")](intro-to-cloudkit-images/image36.png)

Esto es donde se almacenan los registros de forma predeterminada. Además, pueden crearse las zonas de registro personalizado. Grabar representan las zonas se lleva a cabo la granularidad base en las confirmaciones atómica y seguimiento de cambios.

## <a name="record-identifiers"></a>Identificadores de registro

Identificadores de registro se representan como una tupla, que contiene tanto un cliente proporciona el nombre de registro y la zona en el que existe el registro. Identificadores de registro tienen las siguientes características:

-  Se crean en la aplicación cliente.
-  Estos ejemplos se normalizan totalmente y representan la ubicación específica del registro.
-  Al asignar el identificador único de un registro en una base de datos externa en el nombre de registro, puede utilizar para enlazar las bases de datos locales que no se almacenan en CloudKit.


Cuando los programadores crean nuevos registros, puede pasar un identificador de registro. Si no se especifica un identificador de registro, un UUID automáticamente ser creado y asignado al registro.

Cuando los programadores crean nuevos identificadores de registro, puede especificar la zona de registro al que pertenece cada registro. Si no se especifica ninguno, se utilizará la zona de registro predeterminado.

Identificadores de registro se exponen en el marco de trabajo CloudKit a través de la `CKRecordID` clase. El código siguiente puede utilizarse para crear un nuevo identificador de registro:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Referencias

Las referencias proporcionan las relaciones entre los registros relacionados en una base de datos:

 [ ![](intro-to-cloudkit-images/image37.png "Las referencias proporcionan las relaciones entre los registros relacionados en una base de datos")](intro-to-cloudkit-images/image37.png)

En el ejemplo anterior, los padres poseen a los elementos secundarios para que el elemento secundario es un registro secundario del registro primario. La relación va desde el registro secundario para el registro primario y se conoce como un *volver referencia*.

Las referencias se exponen en el marco de trabajo CloudKit a través de la `CKReference` clase. Son una manera de permitir que el servidor de iCloud comprender la relación entre los registros.

Las referencias proporcionan el mecanismo detrás de eliminaciones en cascada. Si se elimina un registro principal de la base de datos, los registros secundarios (tal y como se especifica en una relación) se eliminarán automáticamente de la base de datos.

> [!NOTE]
> **Tenga en cuenta**: pendiente punteros son una posibilidad cuando se usa CloudKit. Por ejemplo, en el momento de la aplicación tiene captura una lista de punteros de registros, seleccionado un registro y, a continuación, pregunte por el registro, el registro ya no exista en la base de datos. Una aplicación debe codificarse para controlar correctamente esta situación.

Si bien no es necesario, volver referencias son preferidas cuando se trabaja con el marco de trabajo CloudKit. Apple ha ajustado el sistema para convertir el tipo de referencia más eficaz.

Al crear una referencia, el desarrollador puede proporcionar un registro que ya está en la memoria o crear una referencia a un identificador de registro. Si utiliza un identificador de registro y la referencia especificada no existe en la base de datos, se crearía un puntero pendiente.

El siguiente es un ejemplo de creación de una referencia con un registro conocido:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Activos

Activos permite que un archivo de datos no estructurados grandes para cargarse en iCloud o asociados a un registro determinado:

 [ ![](intro-to-cloudkit-images/image38.png "Permiten activos para un archivo de datos no estructurados grandes para cargarse en iCloud o asociados a un registro determinado")](intro-to-cloudkit-images/image38.png)

En el cliente, un `CKRecord` se crea que describe el archivo que se va a cargar en el servidor de iCloud. Un `CKAsset` se crea para contener el archivo y se vincula al registro que lo describe.

Cuando el archivo se carga en el servidor, el registro se coloca en la base de datos y el archivo se copia en una base de datos de almacenamiento masivo especial. Se crea un vínculo entre el registro de puntero y el archivo cargado.

Activos se exponen en el marco de trabajo CloudKit a través de la `CKAsset` clase y se utilizan para almacenar datos grandes y no estructurados. Dado que el desarrollador no desea nunca tienen grandes, datos no estructurados en memoria, activos se implementan utilizando los archivos en disco.

Activos pertenecen a los registros, que permite a los activos que se van a recuperar de iCloud mediante el registro como un puntero. De esta manera, el servidor puede recopilar activos de elementos no utilizados cuando se elimina el registro que posee el recurso.

Dado que `CKAssets` pretenden controlan los archivos de datos grandes, Apple diseñado CloudKit para cargar y descargar los activos de forma eficaz.

El siguiente código se puede utilizar para crear un activo y asociarlo con el registro:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

Ahora que hemos explicado todos los objetos fundamentales en CloudKit. Contenedores están asociados a las aplicaciones y contienen bases de datos. Las bases de datos contienen registros que se agrupan en las zonas de registro y que señala a los identificadores de registro. Se definen las relaciones de elementos primarios y secundarios entre los registros de uso de referencias. Por último, los archivos de gran tamaño se pueden cargar y asociados a los registros mediante activos.

## <a name="cloudkit-convenience-api"></a>API de conveniencia CloudKit

Apple ofrece dos conjuntos diferentes de API para trabajar con CloudKit:

-  **API operativa** – ofrece todas las funciones de CloudKit. Para las aplicaciones más complejas, esta API proporciona un control exhaustivo CloudKit.
-  **API de conveniencia** : ofrece un subconjunto común y preconfigurado de características de CloudKit. Proporciona una solución de acceso fácil y cómodo para incluir la funcionalidad de CloudKit en una aplicación de iOS.


La API de comodidad suele ser la mejor opción para la mayoría de las aplicaciones de iOS y Apple sugiere a partir de él. El resto de esta sección tratará los siguientes temas de la API de comodidad:

-  Guardar un registro.
-  Capturar un registro.
-  Actualizar un registro.


### <a name="common-setup-code"></a>Código de configuración comunes

Antes de comenzar con la API de comodidad CloudKit, hay algún código de programa de instalación estándar necesario. Iniciar mediante la modificación de la aplicación `AppDelegate.cs` de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Computed Properties
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
        #endregion
    }
}
```

El código anterior muestra las bases de datos CloudKit públicas y privadas como accesos directos a fin de facilitar su trabajo con el resto de la aplicación.

A continuación, agregue el código siguiente para cualquier contenedor de vista que se va a utilizar CloudKit o vista:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Esto agrega un acceso directo para ir a la `AppDelegate` y tener acceso a los métodos abreviados de la base de datos públicos y privados creados anteriormente.

Con este código en su lugar, echemos un vistazo a la implementación de la API de conveniencia CloudKit en una aplicación de iOS 8 de Xamarin.

### <a name="saving-a-record"></a>Guardar un registro

Mediante el modelo presentado anteriormente al hablar de registros, el código siguiente se crea un nuevo registro y usar la API de conveniencia para guardarlo en la base de datos público:

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Tenga en cuenta sobre el código anterior tres cosas:

1.  Mediante una llamada a la `SaveRecord` método de la `PublicDatabase`, el programador no tiene que especificar cómo se envían los datos, qué zona se está escribiendo, etcetera. La API de comodidad se hace cargo de todos los detalles de sí mismo.
1.  La llamada es asincrónica y proporciona una rutina de devolución de llamada cuando se complete la llamada, ya sea correcta o incorrectamente. Si se produce un error en la llamada, se proporcionará un mensaje de error.
1.  CloudKit no proporciona almacenamiento local o persistencia; es sólo un medio de transferencia. Por lo que cuando se realiza una solicitud para guardar un registro, se envía inmediatamente a los servidores de iCloud.


> [!NOTE]
> **Tenga en cuenta**: debido a la naturaleza "débil" de las comunicaciones de red móvil, donde las conexiones constantemente se quitan o interrumpen, una de las primeras consideraciones que los desarrolladores deben realizar al trabajar con CloudKit es el control de errores.

### <a name="fetching-a-record"></a>Capturar un registro

Con un registro que creó y almacenó correctamente en el servidor de iCloud, utilice el código siguiente para recuperar el registro:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Al igual que en Guardar el registro, el código anterior es asincrónica y sencillo y requiere un tratamiento excelente de error.

### <a name="updating-a-record"></a>Actualizar un registro

Después de que se han capturado un registro de los servidores de iCloud, el siguiente código se puede utilizar para modificar el registro y guardar los cambios en la base de datos:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

El `FetchRecord` método de la `PublicDatabase` devuelve un `CKRecord` si la llamada se realizó correctamente. La aplicación, a continuación, modifica el registro y la llama `SaveRecord` nuevo volver a escribir los cambios en la base de datos.

En esta sección se muestra el ciclo típico que utilizará la aplicación cuando se trabaja con la API de conveniencia CloudKit. La aplicación se guardar registros en iCloud, recuperar los registros de iCloud, modificar los registros y guardar los cambios en iCloud.

## <a name="designing-for-scalability"></a>Diseño de la escalabilidad

Hasta ahora en este artículo ha buscado almacenar y recuperar el modelo de objetos completo de una aplicación desde los servidores de iCloud, cada vez que se va a actuar. Aunque este enfoque funciona bien con una pequeña cantidad de datos y una base de usuarios muy pequeño, lo no se escala bien cuando la cantidad de información o usuario base aumenta.

### <a name="big-data-tiny-device"></a>Big Data, dispositivo pequeño

El más popular una aplicación se convierte en, los datos en la base de datos más y menos factible resulta tienen una memoria caché de datos completas en el dispositivo. Pueden emplear las técnicas siguientes para resolver este problema:

-  **Mantener los datos de gran tamaño en la nube** – CloudKit se ha diseñado para controlar eficazmente los datos de gran tamaño.
-  **Cliente solo debe ver un segmento de datos** : desconecta la configuración mínima de datos necesarios para controlar cualquier tarea en un momento dado.
-  **Pueden cambiar las vistas de cliente** : dado que cada usuario tiene distintas preferencias, el segmento de datos que se muestran puede cambiar de un usuario a otro y el usuario individual de la vista de cualquier sector determinado puede ser diferente.
-  **Cliente utiliza las consultas para centrar el punto de vista** : las consultas permiten al usuario ver un pequeño subconjunto de un conjunto de datos más grande que existe dentro de la nube.


### <a name="queries"></a>Consultas

Como se mencionó anteriormente, las consultas permiten al desarrollador seleccionar un pequeño subconjunto del conjunto de datos más grande que existe en la nube. Las consultas se exponen en el marco de trabajo CloudKit a través de la `CKQuery` clase.

Una consulta combina tres cosas diferentes: un tipo de registro ( `RecordType`), un predicado ( `NSPredicate`) y, opcionalmente, un Descriptor de ordenación ( `NSSortDescriptors`). CloudKit admite la mayoría de `NSPredicate`.

#### <a name="supported-predicates"></a>Predicados admitidos

CloudKit admite los siguientes tipos de `NSPredicates` cuando se trabaja con consultas:


1. Coincidencia de registros donde el nombre es igual a un valor almacenado en una variable:
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. Permite la coincidencia de basarse en un valor de clave dinámico, por lo que no tiene la clave que se conoce en tiempo de compilación:
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. Coincidencia de registros donde el valor de registro es mayor que el valor proporcionado:
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Donde es la ubicación del registro dentro de la ubicación dada 100 metros de registros coincidentes:
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit admite una búsqueda con tokens. Esta llamada creará dos tokens, uno para `after` y otro para `session`. Devolverá un registro que contiene esas dos tokens:
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
 6. Admite CloudKit compuesta predicados combinados mediante el `AND` operador.
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>Crear consultas

El código siguiente puede utilizarse para crear un `CKQuery` en una aplicación de iOS 8 de Xamarin:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

En primer lugar, crea un predicado para seleccionar solo los registros que coinciden con un nombre determinado. A continuación, crea una consulta que seleccionará los registros del tipo de registro especificado que coinciden con el predicado.

#### <a name="performing-a-query"></a>Realizar una consulta

Una vez creada una consulta, utilice el código siguiente para realizar la consulta y procesar los registros devueltos:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

El código anterior, toma la consulta creada anteriormente y ejecuta en la base de datos públicos. Puesto que no se especifica ninguna zona de registro, se busca en todas las zonas. Si se produjo ningún error, una matriz de `CKRecords` se devolverán los parámetros de la consulta de búsqueda de coincidencias.

La manera de pensar acerca de las consultas es que son sondeos y son apropiados para segmentar grandes conjuntos de datos. Las consultas, sin embargo, no se adapta perfectamente a los conjuntos de datos grandes, principalmente estático por las razones siguientes:

-  Únicamente son repercute negativamente en la duración de la batería del dispositivo.
-  Únicamente son incorrectos para el tráfico de red.
-  Son repercute negativamente en la experiencia del usuario porque la información que ven está limitada por la frecuencia con la aplicación está sondeando la base de datos. Hoy en día, los usuarios esperan las notificaciones de inserción cuando se produzca algún cambio.


### <a name="subscriptions"></a>Suscripciones

Cuando se trabaja con conjuntos de datos grandes, principalmente estático, la consulta no debe realizarse en el dispositivo cliente, debe ejecutarse en el servidor en nombre del cliente. La consulta debe ejecutarse en segundo plano y se debe ejecutar después de guardar todos los registros, ya sea por el dispositivo actual u otro dispositivo tocar la misma base de datos.

Por último, se debe enviar una notificación de inserción para cada dispositivo conectado a la base de datos cuando se ejecuta la consulta de servidor.

Las suscripciones se exponen en el marco de trabajo CloudKit a través de la `CKSubscription` clase. Combinación de un tipo de registro ( `RecordType`), un predicado ( `NSPredicate`) y una notificación de inserción de Apple ( `Push`).

> [!NOTE]
> **Tenga en cuenta**: CloudKit inserciones ligeramente se han aumentado ya que contienen una carga que contiene información específica de CloudKit como la causa de la inserción de que se produzca.

#### <a name="how-subscriptions-work"></a>Cómo funcionan las suscripciones

Antes de implementar la suscripción en código C#, veamos una introducción rápida de cómo funcionan las suscripciones:

 [ ![](intro-to-cloudkit-images/image39.png "Información general sobre cómo funcionan las suscripciones")](intro-to-cloudkit-images/image39.png)

El gráfico anterior muestra el proceso de suscripción típica como sigue:

1.  El dispositivo de cliente crea una nueva suscripción que contiene el conjunto de condiciones que desencadenarán la suscripción y una notificación de inserción que se enviará cuando se produce el desencadenador.
2.  La suscripción se envía a la base de datos donde se agrega a la colección de las suscripciones existentes.
3.  Un segundo dispositivo crea un nuevo registro y guarda el registro a la base de datos.
4.  Busca en la base de datos a través de su lista de suscripciones para ver si el nuevo registro coincide con cualquiera de sus condiciones.
5.  Si se encuentra una coincidencia, se envía la notificación de inserción en el dispositivo que registró la suscripción con información sobre el registro del que hizo que se va a desencadenar.


Con esta información en su lugar, echemos un vistazo a crear las suscripciones en un iOS Xamarin aplicación 8.

#### <a name="creating-subscriptions"></a>Creación de suscripciones

El siguiente código se puede utilizar para crear una suscripción:

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

En primer lugar, crea un predicado que proporciona la condición que se va a desencadenar la suscripción. A continuación, crea la suscripción con un tipo de registro específico y establece la opción de cuando se prueba el desencadenador. Por último, define el tipo de notificación que se producirá cuando la suscripción está activa y adjunta la suscripción.

### <a name="saving-subscriptions"></a>Guarda la carpeta suscripciones

Con la suscripción creada, el código siguiente lo guardará en la base de datos:

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

Con la API de comodidad, la llamada es asincrónica, simple y proporciona control de errores fácil.

#### <a name="handling-push-notifications"></a>Controlar las notificaciones de inserción

Si el programador ha utilizado previamente las notificaciones de inserción de Apple (AP), el proceso de trabajar con notificaciones generadas por CloudKit debe estar familiarizado.

En el `AppDelegate.cs`, invalidar la `ReceivedRemoteNotification` clase como sigue:

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

El código anterior le pide CloudKit para analizar la información de usuario en una notificación de CloudKit. A continuación, se extrae información sobre la alerta. Por último, se prueba el tipo de notificación y la notificación se controla en consecuencia.

En esta sección se muestra cómo se responden las grandes cantidades de datos, el problema de dispositivo pequeño presentada anteriormente mediante el uso de las consultas y las suscripciones. La aplicación se deje sus datos de gran tamaño en la nube y usar estas tecnologías para proporcionar vistas en este conjunto de datos.

## <a name="cloudkit-user-accounts"></a>Cuentas de usuario de CloudKit

Como se indicó al principio de este artículo, CloudKit se basa en la infraestructura existente de iCloud. En la siguiente sección se tratará en detalle, cómo se exponen las cuentas para los desarrolladores que utilizan la API de CloudKit.

### <a name="authentication"></a>Autenticación

Cuando se trabaja con cuentas de usuario, la primera consideración es la autenticación. CloudKit admite la autenticación mediante el usuario ha iniciado sesión actualmente iCloud en el dispositivo. La autenticación tiene lugar en segundo plano y se controla por iOS. De este modo, los desarrolladores nunca tienen que preocuparse sobre los detalles de implementación de la autenticación. Solo se prueban para ver si un usuario ha iniciado sesión.

### <a name="user-account-information"></a>Información de la cuenta de usuario

CloudKit proporciona la siguiente información de usuario para el desarrollador:

-  **Identidad** : una manera de identificar de forma exclusiva el usuario.
-  **Metadatos** : la capacidad de guardar y recuperar información acerca de los usuarios.
-  **Privacidad** : toda la información se controla en un manor consciente de privacidad. Nada se expone a menos que el usuario ha aceptado.
-  **Detección** : ofrece a los usuarios la capacidad de detectar sus amigos que usan la misma aplicación.


A continuación, veremos estos temas detalladamente.

#### <a name="identity"></a>identidad

Como se mencionó anteriormente, CloudKit proporciona una manera para que la aplicación identificar de forma única un usuario determinado:

 [ ![](intro-to-cloudkit-images/image40.png "Identificación de forma única un usuario determinado")](intro-to-cloudkit-images/image40.png)

Hay una aplicación de cliente que se ejecuta en dispositivos de un usuario y todas las bases de datos de usuario privados específico dentro del contenedor CloudKit. La aplicación cliente se va a vincularse a uno de esos usuarios específicos. Esto se basa en el usuario que ha iniciado sesión en iCloud localmente en el dispositivo.

Dado que esto procede de iCloud, hay un variado realizar una copia de la información del almacén de usuario. Y porque iCloud realmente hospeda el contenedor, pueden poner en correlación los usuarios. En el gráfico anterior, el usuario cuya cuenta de iCloud `user@icloud.com` está vinculado al cliente actual.

En función de los contenedores, se crea un identificador de usuario único, genera de forma aleatoria y se asociada a la cuenta de usuario iCloud (dirección de correo electrónico). Id. de usuario se devuelve a la aplicación y puede usarse en cualquier forma que considere apropiada al desarrollador.

> [!NOTE]
> **Tenga en cuenta**: aplicaciones diferentes que se ejecutan en el mismo dispositivo para el mismo usuario iCloud tendrán distintos identificadores de usuario porque están conectados a diferentes contenedores CloudKit.

El siguiente código obtiene el identificador de usuario CloudKit para tiene iniciada sesión en iCloud del usuario en el dispositivo:

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

El código anterior está solicitando el contenedor CloudKit debe proporcionar el identificador del usuario que actualmente ha iniciado sesión. Puesto que esta información se transmite desde el servidor de iCloud, la llamada es asincrónica y control de errores es necesario.

#### <a name="metadata"></a>Metadatos

Cada usuario en CloudKit tiene metadatos específicos que los describe. Estos metadatos se representan como un registro CloudKit:

 [ ![](intro-to-cloudkit-images/image41.png "Cada usuario en CloudKit tiene metadatos específicos que los describe")](intro-to-cloudkit-images/image41.png)

Busca un usuario específico de un contenedor no existe dentro de la base de datos privado es un registro que define ese usuario. Hay varios registros de usuario dentro de la base de datos públicos, uno para cada usuario del contenedor. Uno de ellos tendrá un identificador de registro que coincida con el Id. del usuario actualmente conectado

Registros de usuario en la base de datos públicos son world legible. Se tratan, en su mayor parte, como un registro normal y tienen un tipo de `CKRecordTypeUserRecord`. Estos registros están reservados por el sistema y no están disponibles para las consultas.

Utilice el código siguiente para tener acceso a un registro de usuario:

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

El código anterior pide que devuelva el registro de usuario para el usuario que se tiene acceso por encima de Id. de la base de datos públicos. Puesto que esta información se transmite desde el servidor de iCloud, la llamada es asincrónica y control de errores es necesario.

#### <a name="privacy"></a>Privacidad

CloudKit era diseño, de forma predeterminada, para proteger la privacidad del usuario que ha iniciado sesión actualmente. De forma predeterminada, no se expone ninguna información de identificación personal sobre el usuario. Hay algunos casos donde la aplicación solicitará información limitada sobre el usuario.

En estos casos, la aplicación puede solicitar que el usuario revelar esta información. Un cuadro de diálogo se presentará al usuario que se les solicita a participar en la exposición de información de su cuenta.

#### <a name="discovery"></a>de esquema JSON

Suponiendo que el usuario que participa en para permitir que la aplicación acceso limitado a su información de cuenta de usuario, pueden ser reconocibles a otros usuarios de la aplicación:

 [ ![](intro-to-cloudkit-images/image42.png "Un usuario puede ser reconocible a otros usuarios de la aplicación")](intro-to-cloudkit-images/image42.png)

La aplicación cliente se comunica a un contenedor y el contenedor está hablando iCloud acceder a la información de usuario. El usuario puede proporcionar una dirección de correo electrónico y detección se puede usar para obtener información sobre el usuario. Opcionalmente, el identificador de usuario también se puede usar para detectar información sobre el usuario.

CloudKit también proporciona una manera para detectar información sobre todos los usuarios que pueden ser elementos friend de tiene iniciada sesión en iCloud usuario consultando la libreta de direcciones completa. El proceso de CloudKit de extracción en el libro de contacto del usuario y usar las direcciones de correo electrónico para ver si se puede encontrar otro usuario de la aplicación que coincidan con esas direcciones.

Esto permite que la aplicación aprovechar el libro de contacto del usuario sin proporcionar acceso a él o que pide al usuario que apruebe el acceso a los contactos. En ningún momento la información de contacto estará disponible para la aplicación, solo el proceso de CloudKit tiene acceso.

En resumen, hay tres tipos diferentes de entradas disponibles para la detección de usuario:

-  **Id. de registro de usuario** – detección puede realizarse con el identificador de usuario de que tiene iniciada en usuario CloudKit.
-  **Dirección de correo electrónico del usuario** : el usuario puede proporcionar una dirección de correo electrónico y se puede utilizar para la detección.
-  **Póngase en contacto con el libro** : libreta de direcciones del usuario puede usarse para detectar a los usuarios que tienen la misma dirección de correo electrónico como se muestra en su lista de contactos de la aplicación.


Detección de usuario devolverá la siguiente información:

-  **Id. de registro de usuario** -el identificador único de un usuario en la base de datos públicos.
-  **Primer y último nombre** : como se almacena en la base de datos públicos.


Esta información solo se devolverá para usuarios que tengan participa en la detección.

El código siguiente detectará la información sobre el usuario actualmente registrado en iCloud en el dispositivo:

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

Utilice el código siguiente para consultar todos los usuarios de la libreta de contacto:

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

En esta sección hemos tratado las cuatro áreas principales de acceso a una cuenta de usuario que puede proporcionar CloudKit a una aplicación. Al obtener metadatos y la identidad del usuario, a las directivas de privacidad que están integradas en CloudKit y por último, la capacidad para detectar otros usuarios de la aplicación.

## <a name="the-development-and-production-environments"></a>Los entornos de producción y desarrollo

CloudKit proporciona entornos de desarrollo y producción independientes para los tipos de registro y datos de una aplicación. El entorno de desarrollo es un entorno más flexible que solo está disponible para los miembros de un equipo de desarrollo. Cuando una aplicación agrega un nuevo campo a un registro y guarda el registro en el entorno de desarrollo, el servidor actualiza automáticamente la información de esquema.

El programador puede utilizar esta característica para realizar cambios en un esquema durante el desarrollo, lo que ahorra tiempo. Una advertencia es que después de agregar un campo a un registro, el tipo de datos asociado a ese campo no se puede cambiar mediante programación. Para cambiar el tipo de un campo, el desarrollador debe eliminar el campo de [CloudKit panel](https://icloud.developer.apple.com/dashboard/) y vuelva a agregarlo con el nuevo tipo.

Antes de implementar la aplicación, el desarrollador puede migrar su esquema y los datos en el entorno de producción mediante **CloudKit panel**. Cuando se ejecuta en el entorno de producción, el servidor impide que una aplicación cambiar el esquema mediante programación. El desarrollador puede seguir realizando modificaciones con **CloudKit panel** pero se intenta agregar campos a un registro en el resultado del entorno de producción de errores.

> [!NOTE]
> **Nota:** iOS simulador sólo funciona con la **entorno de desarrollo**. Cuando el desarrollador está listo para probar una aplicación en un **entorno de producción**, se requiere un dispositivo de E/s físicas.


## <a name="shipping-a-cloudkit-enabled-app"></a>Aplicación habilitada para una CloudKit de envío

Antes de enviar una aplicación que utiliza CloudKit, deberá configurarse para destino el **entorno de producción CloudKit** o la aplicación se rechazarán por Apple.

Haga lo siguiente:

1. En Visual Studio para Ma, compile la aplicación para **versión** > **dispositivo iOS**: 

    [![](intro-to-cloudkit-images/shipping01.png "Compile la aplicación para la versión")](intro-to-cloudkit-images/shipping01.png)

2. Desde el **generar** menú, seleccione **archivo**: 

    [![](intro-to-cloudkit-images/shipping02.png "Seleccione el archivo")](intro-to-cloudkit-images/shipping02.png)

3. El **archivo** creará y se muestra en Visual Studio para Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "El archivo se crea y se mostrará")](intro-to-cloudkit-images/shipping03.png)

4. Inicie **Xcode**.
5. Desde el **ventana** menú, seleccione **organizador**: 

    [![](intro-to-cloudkit-images/shipping04.png "Seleccione organizador")](intro-to-cloudkit-images/shipping04.png)

6. Seleccione el archivo de la aplicación y haga clic en el **exportar...**  botón: 

    [![](intro-to-cloudkit-images/shipping05.png "Archivo de la aplicación")](intro-to-cloudkit-images/shipping05.png)
    
7. Seleccione un método para la exportación y haga clic en el **siguiente** botón: 

    [![](intro-to-cloudkit-images/shipping06.png "Seleccione un método para la exportación")](intro-to-cloudkit-images/shipping06.png)

8. Seleccione el **equipo de desarrollo** desde la lista desplegable y haga clic en el **elegir** botón: 

    [![](intro-to-cloudkit-images/shipping07.png "Seleccione el equipo de desarrollo en la lista desplegable")](intro-to-cloudkit-images/shipping07.png)

9. Seleccione **producción** desde la lista desplegable y haga clic en el **siguiente** botón: 

    [![](intro-to-cloudkit-images/shipping08.png "Seleccione producción en la lista desplegable")](intro-to-cloudkit-images/shipping08.png)

10. Revise la configuración y haga clic en el **exportar** botón: 

    [![](intro-to-cloudkit-images/shipping09.png "Revise la configuración")](intro-to-cloudkit-images/shipping09.png)

11. Elija una ubicación para generar la aplicación resultante `.ipa` archivo.

El proceso es similar para el envío de la aplicación directamente en iTunes Connect, simplemente haga clic en el **enviar...**  botón en lugar de la exportación... después de seleccionar un archivo en la ventana de la galería.

## <a name="when-to-use-cloudkit"></a>Cuándo utilizar CloudKit

Como hemos visto en este artículo, CloudKit proporciona una manera sencilla de una aplicación almacenar y recuperar la información de los servidores de iCloud. Dicho esto, CloudKit no obsoletas o dejar de utilizar cualquiera de las herramientas existentes o marcos de trabajo.

### <a name="use-cases"></a>Casos de uso

Los siguientes casos de uso deben ayudar al programador decidir cuándo se debe usar un marco de iCloud específico o la tecnología:

-  **Almacén de valores clave de iCloud** : de forma asincrónica una cantidad pequeña de datos para mantener actualizados y es muy útil para trabajar con las preferencias de aplicación. Sin embargo, está restringido para una cantidad muy pequeña de información.
-  **Unidad de iCloud** : creado a partir de existente iCloud API de documentos y proporciona una API sencilla para sincronizar los datos no estructurados del sistema de archivos. Proporciona una caché sin conexión completa en Mac OS X y es muy útil para aplicaciones centradas en documento.
-  **Datos principales de iCloud** – permite que los datos se repliquen entre todos los dispositivos del usuario. Los datos son excelentes para mantener privada datos estructurados sincronizados y de usuario único.
-  **CloudKit** : proporciona ambos estructura de datos públicos y de forma masiva y es capaz de controlar el conjunto de datos grande y archivos no estructurados grandes. Su ligada al usuario de la cuenta de iCloud y proporciona cliente dirige la transferencia de datos.


Mantener estos casos de uso en mente, el desarrollador debe elegir la tecnología de iCloud correcto para proporcionar tanto la funcionalidad actual de aplicación necesaria y proporcionar buena escalabilidad para el crecimiento futuro.

## <a name="summary"></a>Resumen

En este artículo se trata una breve introducción a la API CloudKit. Ha mostrado cómo crear y configurar una aplicación de iOS de Xamarin para utilizar CloudKit. Ha cubierto las características de la API de conveniencia CloudKit. Tiene que muestran cómo diseñar un CloudKit habilita aplicaciones para escalabilidad, empleando las consultas y las suscripciones. Y, finalmente, muestra la información de cuenta de usuario que se expone a una aplicación mediante CloudKit.

## <a name="related-links"></a>Vínculos relacionados

- [CloudKitAtlas (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Crear un perfil de aprovisionamiento](~/ios/get-started/installation/device-provisioning/index.md)
