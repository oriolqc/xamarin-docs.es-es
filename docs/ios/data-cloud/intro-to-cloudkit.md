---
title: CloudKit en Xamarin.iOS
description: Este documento describe cómo trabajar con CloudKit en Xamarin.iOS. Proporciona información general de CloudKit y se explica cómo habilitarla, la API de conveniencia CloudKit, escalabilidad, las cuentas de usuario y entornos de desarrollo y producción.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/11/2016
ms.openlocfilehash: 8ef12c8b0822f3d0486f584878f572a266b0d44e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831861"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit en Xamarin.iOS

El marco de trabajo de CloudKit simplifica el desarrollo de aplicaciones que iCloud acceso. Esto incluye la recuperación de datos de la aplicación y derechos de activos así como ser capaz de almacenar información de la aplicación de forma segura. Este kit ofrece a los usuarios un nivel de anonimato permitiendo el acceso a las aplicaciones con su identificadores de iCloud sin compartir información personal.

Los desarrolladores pueden centrarse en sus aplicaciones de cliente y permiten iCloud elimina la necesidad de escribir la lógica de aplicación de servidor. CloudKit proporciona autenticación, las bases de datos públicos y privados y los datos estructurados y servicios de almacenamiento de recursos.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

## <a name="requirements"></a>Requisitos

Para completar los pasos que aparecen en este artículo, se requiere lo siguiente:

-  **Xcode y el SDK de iOS** : Xcode de Apple y iOS 8 API deben estar instalado y configurado en el equipo del desarrollador.
-  **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo del usuario.
-  **iOS 8 dispositivo** : un dispositivo iOS ejecutando la última versión de iOS 8 para las pruebas.

## <a name="what-is-cloudkit"></a>¿Qué es CloudKit?

CloudKit es una forma para permitir el acceso de desarrollador en los servidores de iCloud. Proporciona la base para la unidad de iCloud y fototeca de iCloud. CloudKit es compatible con dispositivos de Mac OS X y Apple iOS.

 [![](intro-to-cloudkit-images/image1.png "Cómo CloudKit se admite en dispositivos de Apple iOS y Mac OS X")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit usa la infraestructura de la cuenta de iCloud. Si hay un usuario ha iniciado sesión en una cuenta en el dispositivo de iCloud, CloudKit usará su Id. para identificar al usuario. Si no hay ninguna cuenta está disponible, se proporcionará acceso limitado de solo lectura.

CloudKit admite tanto el concepto de las bases de datos públicos y privados. Bases de datos públicas proporcionan una Sopa de"" de todos los datos que el usuario tiene acceso a. Las bases de datos privados están diseñados para almacenar los datos privados que se enlaza a un usuario específico.

CloudKit admite datos estructurados y de forma masiva. Es capaz de controlar las transferencias de archivos grandes sin problemas. CloudKit se encarga de forma eficaz transferir archivos grandes a y desde lo servidores de iCloud en segundo plano, liberando al programador centrarse en otras tareas.

> [!NOTE]
> Es importante tener en cuenta que CloudKit un _transporte tecnología_. No proporciona algún tipo de persistencia; solo permite que una aplicación enviar y recibir información de los servidores de forma eficaz.

Cuando se redactó este documento, Apple inicialmente ofrece CloudKit gratis con un límite alto de la capacidad de ancho de banda y almacenamiento. Para obtener más grandes proyectos o aplicaciones con una base de usuarios grande, Apple ha sugirió que se proporcionará una escala de precios asequibles.


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Habilitación de CloudKit en una aplicación de Xamarin

Antes de que una aplicación de Xamarin puede usar el marco de trabajo de CloudKit, debe aprovisionarse correctamente la aplicación tal como se detalla en el [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) y [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guías

1.  Abra el proyecto en Visual Studio para Mac o Visual Studio.
2.  En el **el Explorador de soluciones**, abra el **Info.plist** de archivos y asegúrese del **identificador de paquete** coincide con el que se definió en **Id. de aplicación**creado como parte del aprovisionamiento de la configuración de:
 
    [![](intro-to-cloudkit-images/image26a.png "Escriba el identificador de paquete")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  Desplácese hacia abajo hasta la parte inferior de la **Info.plist** de archivo y seleccione **habilitar modos en segundo plano**, **actualizaciones de ubicación** y **notificaciones remotas**:

    [![](intro-to-cloudkit-images/image27a.png "Seleccione habilita modos en segundo plano, las actualizaciones de ubicación y las notificaciones remotas")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  Haga clic en el proyecto de iOS en la solución y seleccione **opciones**.
5.  Seleccione **firma de lote de iOS**, seleccione el **Developer Identity** y **perfil de aprovisionamiento** creado anteriormente.
6.  Asegúrese del **Entitlements.plist** incluye **habilitar iCloud** , **almacenamiento de pares clave-valor** y **CloudKit** .
7.  Asegúrese del **ubicuidad contenedor** existe para la aplicación (tal y como se creó anteriormente). Ejemplo: `iCloud.com.your-company.CloudKitAtlas`
8.  Guarde los cambios en el archivo.


Con esta configuración en su lugar, la aplicación ahora está lista para tener acceso a las API de marco de trabajo de CloudKit.

## <a name="cloudkit-api-overview"></a>Introducción a la API CloudKit

Antes de implementar CloudKit en una aplicación Xamarin iOS, este artículo se va a abordará los conceptos fundamentales de CloudKit Framework, que incluirá los siguientes temas:

1.  **Contenedores** – aislado silos de comunicaciones de iCloud.
2.  **Las bases de datos** : públicas y privadas están disponibles para la aplicación.
3.  **Registros** : el mecanismo en el que se mueven datos estructurados hacia y desde CloudKit.
4.  **Las zonas de registro** – son grupos de registros.
5.  **Registrar identificadores** : totalmente se normalizan y representan la ubicación del Registro específica.
6.  **Referencia** : proporcionar elementos primarios y secundarios de las relaciones entre los registros relacionados en una base de datos.
7.  **Activos** : permitir que el archivo de datos grandes y no estructurados se cargó en iCloud y asociado con un registro determinado.


### <a name="containers"></a>Contenedores

Siempre se ejecuta una aplicación determinada que se ejecutan en un dispositivo iOS junto lado otras aplicaciones y servicios en ese dispositivo. En el dispositivo cliente, la aplicación se va a ser silos o espacio aislado de alguna manera. En algunos casos, se trata de un espacio aislado de literal y en otros casos, la aplicación es simplemente se ejecutan en él es propio espacio de memoria.

El concepto de tomar una aplicación cliente y su ejecución separada de otros clientes es muy eficaz y proporciona las siguientes ventajas:

1.  **Seguridad** : una aplicación no interfiere con otras aplicaciones cliente o el sistema operativo.
1.  **Estabilidad** : si la aplicación cliente bloquea, no se puede adquirir las demás aplicaciones del sistema operativo.
1.  **Privacidad** : cada aplicación cliente tiene acceso limitado a la información personal almacenada en el dispositivo.


CloudKit se diseñó para proporcionar las mismas ventajas que los enumerados anteriormente y aplicarlas a trabajar con la información en la nube:

 [![](intro-to-cloudkit-images/image31.png "Las aplicaciones de CloudKit comunicarse con contenedores")](intro-to-cloudkit-images/image31.png#lightbox)

Al igual que la aplicación que uno de varios que se ejecutan en el dispositivo, así que es las comunicaciones de la aplicación con uno de varios iCloud. Cada uno de estos silos de comunicación diferentes se denominan contenedores.

Los contenedores se exponen en el marco de CloudKit a través de la `CKContainer` clase. De forma predeterminada, se comunica una aplicación con un contenedor y este contenedor aísla los datos para esa aplicación. Esto significa que varias aplicaciones pueden almacenar información en la misma cuenta de iCloud, pero nunca se pueden entremezclar esta información.

La inclusión en contenedores de iCloud datos también permite CloudKit encapsular la información de usuario. De este modo, la aplicación tendrá acceso limitado a la cuenta de iCloud y la información de usuario almacenados en, todo ello mientras sigue protegiendo la privacidad y seguridad del usuario.

Los contenedores están totalmente administrados por el desarrollador de la aplicación a través del portal WWDR. El espacio de nombres del contenedor es global entre todos los desarrolladores de Apple, por lo que el contenedor no sólo debe ser único para aplicaciones de un desarrollador determinado, pero para todas las aplicaciones y los desarrolladores de Apple.

Apple sugiere el uso de la notación DNS inversa al crear el espacio de nombres para los contenedores de aplicación. Ejemplo:

```csharp
iCloud.com.company-name.application-name
```

Mientras que los contenedores son, de forma predeterminada, enlazado uno a uno para una aplicación determinada, pueden compartir entre aplicaciones. Por lo que pueden coordinar varias aplicaciones en un solo contenedor. Una sola aplicación también puede comunicarse con varios contenedores.

### <a name="databases"></a>Bases de datos

Una de las principales funciones de CloudKit es tomar el modelo de datos de la aplicación y replicación ese modelo hasta los servidores de iCloud. Alguna información está pensada para el usuario que lo creó, otra información es datos públicos que se pueden crear por el usuario para uso público (por ejemplo, un restaurante) o podría ser información que el desarrollador ha publicado para la aplicación. En cualquier caso, la audiencia no es simplemente un solo usuario, pero es una comunidad de usuarios.

 [![](intro-to-cloudkit-images/image32.png "Diagrama del contenedor de CloudKit")](intro-to-cloudkit-images/image32.png#lightbox)

Dentro de un contenedor, primero y más importante es la base de datos pública. Aquí es donde toda la información pública vive y mingles conjuntamente. Además, existen varios privadas bases de datos individuales para cada usuario de la aplicación.

Cuando se ejecuta en un dispositivo iOS, la aplicación solo tendrá acceso a la información para el usuario ha iniciado la sesión de iCloud. Por lo que la vista de la aplicación del contenedor será como sigue:

 [![](intro-to-cloudkit-images/image33.png "La vista de aplicaciones del contenedor")](intro-to-cloudkit-images/image33.png#lightbox)

Solo pueden ver las bases de datos pública y privada asociada con el usuario ha iniciado la sesión de iCloud.

Las bases de datos se exponen en el marco de CloudKit a través de la `CKDatabase` clase. Cada aplicación tiene acceso a dos bases de datos: la base de datos público y uno privado.

El contenedor es el punto de entrada inicial en directo de CloudKit. El código siguiente puede utilizarse para tener acceso a la base de datos público y privado del contenedor predeterminado de la aplicación:

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

||Base de datos público|Base de datos privado|
|---|--- |--- |
|**Tipo de datos**|Datos compartidos|Datos del usuario actual|
|**Quota**|Tiene en cuenta en la cuota del desarrollador|Tiene en cuenta en la cuota del usuario|
|**Permisos predeterminados**|Puede leer del mundo|Usuario legible|
|**Permisos de edición**|iCloud panel Roles a través de un nivel de clase de registro|N/D|

### <a name="records"></a>Registros

Los contenedores que contienen las bases de datos y, dentro de las bases de datos son los registros. Los registros son el mecanismo en el que se mueven datos estructurados hacia y desde CloudKit:

 [![](intro-to-cloudkit-images/image34.png "Los contenedores que contienen bases de datos y, dentro de las bases de datos son registros")](intro-to-cloudkit-images/image34.png#lightbox)

Los registros se exponen en el marco de CloudKit a través de la `CKRecord` (clase), que contiene pares clave-valor. Es equivalente a una instancia de un objeto en una aplicación un `CKRecord` en directo de CloudKit. Además, cada `CKRecord` posee un tipo de registro, que es equivalente a la clase de un objeto.

Los registros tienen un esquema de just-in-time, por lo que los datos se describen a CloudKit antes de que se va a entregar para su procesamiento. Desde ese punto, CloudKit interpretará la información y controlar la logística necesaria para almacenar y recuperar el registro.

La `CKRecord` clase también admite una amplia gama de metadatos. Por ejemplo, un registro contiene información acerca de cuándo se ha creado y el usuario que lo creó. Un registro también contiene información sobre el usuario que modificó y cuándo se modificó por última vez.

Los registros contienen la noción de una etiqueta de cambio. Se trata de una versión anterior de una revisión de un registro determinado. La etiqueta de cambio se usa como una manera ligera de determinar si el cliente y el servidor tienen la misma versión de un registro determinado.

Como se indicó anteriormente, `CKRecords` ajustar pares clave-valor y por lo tanto, los siguientes tipos de datos pueden almacenarse en un registro:

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


Además de los tipos de valor único, un registro puede contener una matriz homogénea de cualquiera de los tipos enumerados anteriormente.

El código siguiente puede usarse para crear un nuevo registro y almacenarlo en una base de datos:

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>Las zonas de registro

No hay registros por sí mismos dentro de una base de datos: grupos de registros entre sí existen dentro de una zona de registro. Las zonas de registro puede considerarse como tablas en una base de datos relacional tradicional:

 [![](intro-to-cloudkit-images/image35.png "Grupos de registros entre sí existen dentro de una zona de registro")](intro-to-cloudkit-images/image35.png#lightbox)

Puede haber varios registros dentro de una zona determinada del registro y varias zonas de registro dentro de una base de datos. Cada base de datos contiene una zona de registro predeterminado:

 [![](intro-to-cloudkit-images/image36.png "Cada base de datos contiene una zona de registro predeterminada y la zona personalizada")](intro-to-cloudkit-images/image36.png#lightbox)

Esto es donde se almacenan los registros de forma predeterminada. Además, se pueden crear zonas de registro personalizado. Registre representan las zonas se realiza la granularidad base en qué confirmaciones atómicas y seguimiento de cambios.

## <a name="record-identifiers"></a>Identificadores de registro

Identificadores de registro se representan como una tupla, que contiene tanto un cliente proporciona el nombre de registro y la zona en el que existe el registro. Identificadores de registro tienen las siguientes características:

-  Se crean mediante la aplicación cliente.
-  Que se normalizan totalmente y representan la ubicación del Registro específica.
-  Al asignar el identificador único de un registro en una base de datos externa para el nombre del registro, se puede usar para unir las bases de datos locales que no se almacenan en directo de CloudKit.


Cuando los desarrolladores crear nuevos registros, puede pasar un identificador de registro. Si no se especifica un identificador de registro, un UUID automáticamente se creará y asignado al registro.

Cuando los programadores crean nuevos identificadores de registro, puede especificar la zona de registro al que pertenece cada registro. Si se especifica ninguno, se utilizará la zona de registro predeterminado.

Identificadores de registro se exponen en el marco de CloudKit a través de la `CKRecordID` clase. El código siguiente puede usarse para crear un nuevo identificador de registro:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Referencias

Las referencias proporcionan las relaciones entre los registros relacionados en una base de datos:

 [![](intro-to-cloudkit-images/image37.png "Las referencias proporcionan las relaciones entre los registros relacionados en una base de datos")](intro-to-cloudkit-images/image37.png#lightbox)

En el ejemplo anterior, los padres poseen a los elementos secundarios para que el elemento secundario es un registro secundario del registro primario. La relación va desde el registro secundario del registro primario y se conoce como un *volver referencia*.

Las referencias se exponen en el marco de CloudKit a través de la `CKReference` clase. Son una manera de permitir que el servidor de iCloud a comprender la relación entre los registros.

Las referencias proporcionan el mecanismo subyacente eliminaciones en cascada. Si se elimina un registro primario de la base de datos, se eliminarán automáticamente los registros secundarios (como se especifica en una relación) de la base de datos.

> [!NOTE]
> Algunos indicios son una posibilidad cuando uso directo de CloudKit. Por ejemplo, en el momento de la aplicación tiene capturan una lista de punteros de registros, un registro seleccionado y, a continuación, más frecuentes del registro, el registro que ya no exista en la base de datos. Una aplicación debe codificarse para controlar esta situación correctamente.

Aunque no es necesario, volver a las referencias se prefieren cuando se trabaja con el marco de trabajo de CloudKit. Apple ha ajustado el sistema para hacerlo, el tipo de referencia más eficaz.

Al crear una referencia, el desarrollador puede proporcionan un registro que ya está en la memoria o crear una referencia a un identificador de registro. Si utiliza un identificador de registro y la referencia especificada no existe en la base de datos, se crearía un puntero pendiente.

El siguiente es un ejemplo de creación de una referencia con respecto a un registro conocido:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Activos

Permitir que los activos para un archivo de datos grandes y no estructurados se cargó en iCloud y asociado con un registro determinado:

 [![](intro-to-cloudkit-images/image38.png "Permitir que los recursos para un archivo de datos grandes y no estructurados se cargó en iCloud y asociado con un registro determinado")](intro-to-cloudkit-images/image38.png#lightbox)

En el cliente, un `CKRecord` se crea que describe el archivo que se va a cargar en el servidor de iCloud. Un `CKAsset` se crea para contener el archivo y se vincula al registro que lo describe.

Cuando se cargue el archivo en el servidor, el registro se coloca en la base de datos y el archivo se copia en una base de datos de almacenamiento masivo especial. Se crea un vínculo entre el registro de puntero y el archivo cargado.

Los activos se exponen en el marco de CloudKit a través de la `CKAsset` clase y se usan para almacenar datos grandes y no estructurados. Dado que el desarrollador no desea nunca tienen grandes de datos no estructurados en la memoria, activos se implementan mediante los archivos en disco.

Activos pertenecen a los registros, que permite a los recursos que deben recuperarse de iCloud con el registro como un puntero. De esta manera, el servidor puede activos de recopilación de elementos no utilizados cuando se elimina el registro que posee el recurso.

Dado que `CKAssets` pretenden controlar los archivos de datos de gran tamaño, Apple diseñado CloudKit para cargar y descargar los activos de manera eficaz.

El código siguiente puede usarse para crear un recurso y asociarlo con el registro:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

Ahora hemos tratado todos los objetos fundamentales dentro de CloudKit. Los contenedores están asociados a las aplicaciones y contienen las bases de datos. Las bases de datos contienen registros que se agrupan en las zonas de registro y apunta a los identificadores de registro. Se definen las relaciones de elementos primarios y secundarios entre los registros de uso de referencias. Por último, los archivos de gran tamaño se pueden cargar y asociados a los registros de uso de recursos.

## <a name="cloudkit-convenience-api"></a>API de conveniencia CloudKit

Apple ofrece dos conjuntos diferentes de API para trabajar con CloudKit:

-  **API Operational** : ofrece todas las funciones de CloudKit. Para aplicaciones más complejas, esta API proporciona un mayor control sobre CloudKit.
-  **API de conveniencia** : ofrece un subconjunto común y preconfigurado de CloudKit características. Proporciona una solución conveniente y fácil acceso para incluir la funcionalidad de CloudKit en una aplicación de iOS.


La API de conveniencia suele ser la mejor opción para la mayoría de las aplicaciones de iOS y Apple sugiere a partir de él. El resto de esta sección tratan los temas siguientes de la API de comodidad:

-  Guardar un registro.
-  Capturando un registro.
-  Actualizar un registro.


### <a name="common-setup-code"></a>Código de programa de instalación común

Antes de comenzar con la API de CloudKit comodidad, hay código de configuración estándar necesario. Iniciar mediante la modificación de la aplicación `AppDelegate.cs` de archivo y dele un aspecto similar al siguiente:

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

El código anterior muestra las bases de datos de CloudKit públicas y privadas como accesos directos a los que sean más fáciles trabajar con el resto de la aplicación.

A continuación, agregue el siguiente código para cualquier contenedor de vista que se va a utilizar CloudKit o vista:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Esto agrega un acceso directo para llegar a la `AppDelegate` y tener acceso a los métodos abreviados de la base de datos públicos y privados creados anteriormente.

Con este código en su lugar, echemos un vistazo a la implementación de la API de conveniencia CloudKit en una aplicación de Xamarin iOS 8.

### <a name="saving-a-record"></a>Guardar un registro

Con el patrón presentado anteriormente cuando se habla de registros, el código siguiente creará un nuevo registro y usar la API de conveniencia para guardarlo en la base de datos pública:

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

Tres cosas a tener en cuenta sobre el código anterior:

1.  Mediante una llamada a la `SaveRecord` método de la `PublicDatabase`, el desarrollador no tiene que especificar cómo se envían los datos, ¿en qué zona se está escribiendo, etcetera. La API de conveniencia se ocupa de todos los detalles de sí mismo.
1.  La llamada es asincrónica y proporciona una rutina de devolución de llamada cuando se complete la llamada, ya sea correcta o incorrectamente. Si se produce un error en la llamada, se proporcionará un mensaje de error.
1.  CloudKit no proporciona almacenamiento local o persistencia; es solo un medio de transferencia. Por lo que cuando se realiza una solicitud para guardar un registro, se envía inmediatamente a los servidores de iCloud.


> [!NOTE]
> Debido a la naturaleza "débil" de las comunicaciones de red móvil, donde las conexiones constantemente se quitan o interrumpen, una de las primeras consideraciones, los desarrolladores deben realizar al trabajar con CloudKit es el control de errores.

### <a name="fetching-a-record"></a>Capturando un registro

Con un registro que creó y almacenó correctamente en el servidor de iCloud, use el código siguiente para recuperar el registro:

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

Al igual que en Guardar el registro, el código anterior es asincrónica, sencillo y requiere control de errores excelente.

### <a name="updating-a-record"></a>Actualizar un registro

Después de que se han obtenido un registro de los servidores de iCloud, el código siguiente puede usarse para modificar el registro y guardar los cambios en la base de datos:

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

El `FetchRecord` método de la `PublicDatabase` devuelve un `CKRecord` si la llamada se realizó correctamente. La aplicación, a continuación, modifica el registro y llamadas `SaveRecord` nuevo para escribir los cambios en la base de datos.

En esta sección ha mostrado el ciclo típico que utilizará la aplicación cuando se trabaja con la API de conveniencia CloudKit. La aplicación se guarde los registros en iCloud, recuperar los registros de iCloud, modificar los registros y guardar esos cambios en iCloud.

## <a name="designing-for-scalability"></a>Diseño de la escalabilidad

Hasta ahora en este artículo ha examinado de almacenar y recuperar el modelo de objetos completo de la aplicación desde los servidores de iCloud, cada vez se va a actuar. Aunque este enfoque funciona bien con una pequeña cantidad de datos y una base de usuarios muy pequeño, no escala bien cuando la cantidad de información o el usuario base aumenta.

### <a name="big-data-tiny-device"></a>Macrodatos, dispositivo diminuto

Deja una aplicación los más populares, cuantos más datos en la base de datos y menos factible es tener una memoria caché de datos completas en el dispositivo. Las técnicas siguientes pueden usarse para resolver este problema:

-  **Mantener los datos de gran tamaño en la nube** – CloudKit se ha diseñado para administrar eficazmente los datos de gran tamaño.
-  **Cliente solo debería ver un segmento de los datos** – Coreapplication lo mínimo de los datos necesarios para controlar cualquier tarea en un momento dado.
-  **Pueden cambiar las vistas de cliente** : dado que cada usuario tiene distintas preferencias, el segmento de datos que se muestra puede cambiar entre usuarios y el usuario individual de la vista de cualquier segmento especificado puede ser diferente.
-  **Cliente utiliza las consultas para centrarse en el punto de vista** : las consultas permiten al usuario ver un pequeño subconjunto de un conjunto de datos más grande que existe dentro de la nube.


### <a name="queries"></a>Consultas

Como se indicó anteriormente, las consultas permiten al desarrollador seleccionar un pequeño subconjunto del conjunto de datos más grande que existe en la nube. Las consultas se exponen en el marco de CloudKit a través de la `CKQuery` clase.

Una consulta combina tres cosas: un tipo de registro ( `RecordType`), un predicado ( `NSPredicate`) y, opcionalmente, un Descriptor de ordenación ( `NSSortDescriptors`). Admite la mayoría de CloudKit `NSPredicate`.

#### <a name="supported-predicates"></a>Predicados admitidos

CloudKit admite los siguientes tipos de `NSPredicates` al trabajar con consultas:


1. Coincidencia de registros donde el nombre es igual a un valor almacenado en una variable:
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. Permite la coincidencia de basarse en un valor de clave dinámico, por lo que no tiene la clave que se conoce en tiempo de compilación:
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. Coincidencia de registros donde el valor de registro es mayor que el valor dado:
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Coincidencia de registros donde es la ubicación del registro dentro de 100 metros de la ubicación especificada:
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit es compatible con una búsqueda con tokens. Esta llamada creará dos tokens, uno para `after` y otro para `session`. Devolverá un registro que contiene esas dos tokens:
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
6. Admite CloudKit compuesta predicados combinados mediante el `AND` operador.
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>Crear consultas

El código siguiente puede utilizarse para crear un `CKQuery` en una aplicación de Xamarin iOS 8:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

En primer lugar, crea un predicado para seleccionar solo los registros que coinciden con un nombre determinado. A continuación, crea una consulta que se seleccionará los registros del tipo de registro especificado que coinciden con el predicado.

#### <a name="performing-a-query"></a>Realizar una consulta

Una vez que se ha creado una consulta, use el código siguiente para realizar la consulta y procesar los registros devueltos:

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

El código anterior toma la consulta creada anteriormente y ejecuta en la base de datos pública. Puesto que se especifica ninguna zona de registro, se buscan en todas las zonas. Si se produce ningún error, una matriz de `CKRecords` se devolverán que coincidan con los parámetros de la consulta.

La manera de pensar acerca de las consultas es que son los sondeos y resultan útiles a través de grandes conjuntos de datos de la segmentación. Las consultas, sin embargo, no son adecuadas para grandes conjuntos de datos estático principalmente por las razones siguientes:

-  Son perjudiciales para la duración de la batería del dispositivo.
-  Son incorrectos para el tráfico de red.
-  Son incorrectos para la experiencia del usuario porque la información que vean está limitada por la frecuencia con la aplicación está sondeando la base de datos. Hoy en día, los usuarios esperan las notificaciones de inserción cuando algo cambia.


### <a name="subscriptions"></a>Suscripciones

Cuando se trabaja con grandes conjuntos de datos principalmente estática, la consulta no debe realizarse en el dispositivo cliente, se debe ejecutar en el servidor en nombre del cliente. La consulta debe ejecutarse en segundo plano y se debe ejecutar después de guardar todos los registros, ya sea por el dispositivo actual u otro dispositivo que tocar la misma base de datos.

Por último, debe enviarse una notificación push a todos los dispositivos conectados a la base de datos cuando se ejecuta la consulta del lado servidor.

Las suscripciones se exponen en el marco de CloudKit a través de la `CKSubscription` clase. Combina en un tipo de registro ( `RecordType`), un predicado ( `NSPredicate`) y una notificación Push de Apple ( `Push`).

> [!NOTE]
> Inserciones de CloudKit ligeramente se manejan ya que contienen una carga que contiene información específica de CloudKit como lo que ha provocado la inserción de ocurrir.

#### <a name="how-subscriptions-work"></a>Cómo funcionan las suscripciones

Antes de implementar la suscripción en C# código, vamos a echar una breve descripción de cómo funcionan las suscripciones:

 [![](intro-to-cloudkit-images/image39.png "Información general sobre cómo funcionan las suscripciones")](intro-to-cloudkit-images/image39.png#lightbox)

El gráfico anterior muestra el proceso de suscripción típica como sigue:

1.  El dispositivo cliente crea una nueva suscripción que contiene el conjunto de condiciones que desencadenarán la suscripción y una notificación de inserción se enviará cuando se produce el desencadenador.
2.  La suscripción se envía a la base de datos donde se agrega a la colección de las suscripciones existentes.
3.  Un segundo dispositivo crea un nuevo registro y guarda ese registro en la base de datos.
4.  Busca en la base de datos a través de su lista de suscripciones para ver si el nuevo registro coincide con cualquiera de sus condiciones.
5.  Si se encuentra una coincidencia, se envía la notificación de inserción al dispositivo que registra la suscripción con información sobre el registro que ha provocado que se desencadena.


Con este conocimiento en su lugar, echemos un vistazo a la creación de suscripciones en un Xamarin iOS 8 aplicación.

#### <a name="creating-subscriptions"></a>Creación de suscripciones

El código siguiente puede utilizarse para crear una suscripción:

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

En primer lugar, crea un predicado que proporciona la condición para desencadenar la suscripción. A continuación, crea la suscripción con un tipo de registro específico y establece la opción de cuando se prueba el desencadenador. Por último, define el tipo de notificación que se producirá cuando se desencadena la suscripción y la adjunta la suscripción.

### <a name="saving-subscriptions"></a>Guardando las suscripciones

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

Si el desarrollador que haya usado previamente las notificaciones Push de Apple (APS), el proceso de tratamiento de las notificaciones generadas por CloudKit debe estar familiarizado.

En el `AppDelegate.cs`, invalidar el `ReceivedRemoteNotification` clase como sigue:

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

El código anterior pide CloudKit para analizar la información de usuario en una notificación de CloudKit. A continuación, se extrae información sobre la alerta. Por último, se prueba el tipo de notificación y la notificación se administra en consecuencia.

En esta sección se muestra cómo responder a los datos de gran tamaño, el problema de dispositivo diminuto presentada anteriormente mediante el uso de las consultas y las suscripciones. La aplicación dejar sus datos de gran tamaño en la nube y usar estas tecnologías para proporcionar vistas en este conjunto de datos.

## <a name="cloudkit-user-accounts"></a>Cuentas de usuario de CloudKit

Como se indicó al principio de este artículo, CloudKit se basa en la infraestructura existente de iCloud. La siguiente sección tratará, en detalle, cómo se exponen las cuentas a un desarrollador que utiliza la API de CloudKit.

### <a name="authentication"></a>Authentication

Cuando se trabaja con las cuentas de usuario, la primera consideración es la autenticación. CloudKit admite la autenticación mediante el usuario ha iniciado sesión actualmente iCloud en el dispositivo. La autenticación realiza en segundo plano y se controla con iOS. De este modo, los desarrolladores nunca tienen que preocuparse por los detalles de implementación de la autenticación. Solo probar para ver si un usuario ha iniciado sesión.

### <a name="user-account-information"></a>Información de la cuenta de usuario

CloudKit proporciona la siguiente información de usuario para el desarrollador:

-  **Identidad** : una manera de identificar de forma exclusiva el usuario.
-  **Metadatos** : la capacidad de guardar y recuperar información acerca de los usuarios.
-  **Privacidad** : toda la información se controla en un manor consciente de privacidad. Nada se expone a menos que el usuario ha aceptado.
-  **Detección** : ofrece a los usuarios la capacidad para detectar sus amigos que usan la misma aplicación.


A continuación, veremos estos temas detalladamente.

#### <a name="identity"></a>identidad

Como se indicó anteriormente, CloudKit proporciona una forma de la aplicación identificar un usuario determinado:

 [![](intro-to-cloudkit-images/image40.png "Identificación de forma exclusiva un usuario determinado")](intro-to-cloudkit-images/image40.png#lightbox)

Hay una aplicación cliente que se ejecutan en dispositivos del usuario y todas las bases de datos de usuario privada específica dentro del contenedor de CloudKit. La aplicación cliente se va a vincularse a uno de esos usuarios específicos. Esto se basa en el usuario que ha iniciado sesión en iCloud localmente en el dispositivo.

Dado esto procede de iCloud, hay una avanzada de seguridad de almacén de información del usuario. Y dado que realmente hospeda el contenedor de iCloud, puede poner en correlación los usuarios. En el gráfico anterior, el usuario cuya cuenta de iCloud `user@icloud.com` está vinculado al cliente actual.

Según los contenedores, se crea un identificador de usuario único, generado aleatoriamente y se asociada con la cuenta de iCloud del usuario (dirección de correo electrónico). Este Id. de usuario se devuelve a la aplicación y se puede usar de ninguna manera que antojo, el desarrollador.

> [!NOTE]
> Aplicaciones diferentes que se ejecutan en el mismo dispositivo para el mismo usuario iCloud tendrán distintos identificadores de usuario ya que están conectadas a distintos contenedores de CloudKit.

El siguiente código obtiene el identificador de usuario de CloudKit para tiene iniciada sesión en iCloud de usuario en el dispositivo:

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

El código anterior está solicitando el contenedor de CloudKit para proporcionar el identificador del usuario con sesión iniciada actualmente. Puesto que esta información procede de iCloud Server, la llamada es asincrónica y control de errores es necesario.

#### <a name="metadata"></a>Metadatos

Cada usuario en directo de CloudKit tiene metadatos específicos que los describe. Estos metadatos se representan como un registro de CloudKit:

 [![](intro-to-cloudkit-images/image41.png "Cada usuario en directo de CloudKit tiene metadatos específicos que los describe")](intro-to-cloudkit-images/image41.png#lightbox)

Busca un usuario específico de un contenedor no existe dentro de la base de datos privado es un registro que define ese usuario. Hay varios registros de usuario dentro de la base de datos público, uno para cada usuario del contenedor. Uno de ellos tendrá un identificador de registro que coincida con el Id. de registro del usuario actualmente conectado

Registros de usuario en la base de datos públicos son legibles del mundo. Que se tratan, en su mayor parte, como un registro normal y tienen un tipo de `CKRecordTypeUserRecord`. Estos registros están reservados por el sistema y no están disponibles para las consultas.

Use el siguiente código para tener acceso a un registro de usuario:

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

El código anterior está solicitando la base de datos públicos para devolver el registro de usuario para el usuario que es el Id. que se tiene acceso a anteriormente. Puesto que esta información procede de iCloud Server, la llamada es asincrónica y control de errores es necesario.

#### <a name="privacy"></a>Privacidad

CloudKit fue el diseño, de forma predeterminada, para proteger la privacidad del usuario ha iniciado sesión actualmente. No hay información de identificación personal sobre el usuario se expone de forma predeterminada. Hay algunos casos donde la aplicación solicitará información limitada sobre el usuario.

En estos casos, la aplicación puede solicitar que el usuario revelar esta información. Aparecerá un cuadro de diálogo al usuario que se les solicita a participar en la exposición de información de su cuenta.

#### <a name="discovery"></a>Detección

Suponiendo que el usuario como suscritas en permitir que la aplicación acceso limitado a su información de cuenta de usuario, pueden ser reconocibles para otros usuarios de la aplicación:

 [![](intro-to-cloudkit-images/image42.png "Un usuario puede ser reconocible para otros usuarios de la aplicación")](intro-to-cloudkit-images/image42.png#lightbox)

La aplicación cliente está hablando con un contenedor y el contenedor está hablando con iCloud para acceder a información de usuario. El usuario puede proporcionar una dirección de correo electrónico y la detección puede usarse para obtener información sobre el usuario. Opcionalmente, el identificador de usuario también puede utilizarse para detectar información sobre el usuario.

CloudKit también proporciona una manera de descubrir información acerca de cualquier usuario que pueden ser elementos friend de tiene iniciada sesión en iCloud usuario consultando la libreta de direcciones completa. El proceso de CloudKit de incorporación de cambios en el libro de contacto del usuario y usar las direcciones de correo electrónico para ver si puede encontrar otro usuario de la aplicación que coinciden con las direcciones.

Esto permite que la aplicación aprovechar el libro de contacto del usuario sin que proporciona acceso a ella ni pedir al usuario que apruebe el acceso a los contactos. En ningún momento la información de contacto estará disponible para la aplicación, solo el proceso de CloudKit tiene acceso.

Para recapitular, hay tres tipos diferentes de entradas disponibles para la detección de usuario:

-  **Id. de registro de usuario** – detección puede realizar en el identificador de usuario de tiene iniciada sesión en directo de CloudKit usuario.
-  **Dirección de correo electrónico del usuario** : el usuario puede proporcionar una dirección de correo electrónico y se puede usar para la detección.
-  **Póngase en contacto con el libro** – libreta de direcciones del usuario se puede usar para detectar a los usuarios que tienen la misma dirección de correo electrónico como se muestra en sus contactos de la aplicación.


Detección de usuarios devolverá la siguiente información:

-  **Id. de registro de usuario** -el identificador único de un usuario en la base de datos pública.
-  **Primer nombre y apellidos** : como se almacena en la base de datos pública.


Esta información solo se devolverá para usuarios que han elegido en la detección.

El código siguiente detectará la información sobre el usuario conectado actualmente en iCloud en el dispositivo:

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

Use el código siguiente para consultar todos los usuarios de la libreta de contacto:

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

En esta sección, nos hemos tratado las cuatro áreas principales de acceso a una cuenta de usuario que puede proporcionar CloudKit a una aplicación. Obtengan la identidad del usuario y los metadatos, las directivas de privacidad que están integradas en directo de CloudKit y por último, la capacidad para detectar otros usuarios de la aplicación.

## <a name="the-development-and-production-environments"></a>Los entornos de producción y desarrollo

CloudKit proporciona entornos de desarrollo y producción independientes para los tipos de registro y datos de la aplicación. El entorno de desarrollo es un entorno más flexible que solo está disponible para los miembros de un equipo de desarrollo. Cuando una aplicación agrega un nuevo campo a un registro y guarda el registro en el entorno de desarrollo, el servidor actualiza automáticamente la información de esquema.

El programador puede utilizar esta característica para realizar cambios en un esquema durante el desarrollo, lo que ahorra tiempo. Una salvedad es que después de agregar un campo a un registro, el tipo de datos asociado con ese campo no se puede cambiar mediante programación. Para cambiar el tipo de un campo, el desarrollador debe eliminar el campo de [CloudKit panel](https://icloud.developer.apple.com/dashboard/) y agregarlo de nuevo con el nuevo tipo.

Antes de implementar la aplicación, el desarrollador puede migrar sus datos y esquema para el entorno de producción mediante **CloudKit panel**. Cuando se ejecuta en el entorno de producción, el servidor impide que una aplicación al cambiar mediante programación el esquema. El programador todavía puede hacer cambios con **CloudKit panel** pero intenta agregar campos a un registro en el resultado del entorno de producción en errores.

> [!NOTE]
> IOS Simulator solo funciona con el **entorno de desarrollo**. Cuando el desarrollador está preparado para probar una aplicación en un **entorno de producción**, se requiere un dispositivo iOS físico.


## <a name="shipping-a-cloudkit-enabled-app"></a>Aplicación habilitada para enviar un CloudKit

Antes de enviar una aplicación que usa CloudKit, deberá configurarse para el destino el **entorno de producción CloudKit** o Apple rechazará la aplicación.

Haga lo siguiente:

1. En Visual Studio para Ma, compile la aplicación para **versión** > **dispositivo iOS**: 

    [![](intro-to-cloudkit-images/shipping01.png "Compile la aplicación para lanzamiento")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. Desde el **compilar** menú, seleccione **archivo**: 

    [![](intro-to-cloudkit-images/shipping02.png "Selección de archivo")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. El **archivo** se creará y se muestra en Visual Studio para Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "El archivo se crea y se mostrará")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Inicie **Xcode**.
5. Desde el **ventana** menú, seleccione **organizador**: 

    [![](intro-to-cloudkit-images/shipping04.png "Seleccione el organizador")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Seleccione el archivo de la aplicación y haga clic en el **exportar...**  botón: 

    [![](intro-to-cloudkit-images/shipping05.png "Archivo de la aplicación")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. Seleccione un método para la exportación y haga clic en el **siguiente** botón: 

    [![](intro-to-cloudkit-images/shipping06.png "Seleccione un método para la exportación")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Seleccione el **equipo de desarrollo** desde la lista desplegable y haga clic en el **elegir** botón: 

    [![](intro-to-cloudkit-images/shipping07.png "Seleccione el equipo de desarrollo en la lista desplegable")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Seleccione **producción** desde la lista desplegable y haga clic en el **siguiente** botón: 

    [![](intro-to-cloudkit-images/shipping08.png "Seleccione la producción en la lista desplegable")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Revise la configuración y haga clic en el **exportar** botón: 

    [![](intro-to-cloudkit-images/shipping09.png "Revise la configuración")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Elija una ubicación para generar la aplicación resultante `.ipa` archivo.

El proceso es similar para el envío de la aplicación directamente en iTunes Connect, simplemente haga clic en el **enviar...**  botón en lugar de la exportación... después de seleccionar un archivo en la ventana Galería.

## <a name="when-to-use-cloudkit"></a>Cuándo usar CloudKit

Como hemos visto en este artículo, CloudKit proporciona una manera fácil para una aplicación almacenar y recuperar información de los servidores de iCloud. Dicho esto, CloudKit no obsoletos o dejar de utilizar cualquiera de las herramientas existentes o marcos de trabajo.

### <a name="use-cases"></a>Casos de uso

Los siguientes casos de uso deberían ayudar al programador decida cuándo usar un marco específico de iCloud o tecnología de:

-  **Store pares clave-valor de iCloud** : de forma asincrónica pequeña cantidad de datos para mantener actualizados y es muy útil para trabajar con las preferencias de la aplicación. Sin embargo, está restringido para una cantidad muy pequeña de información.
-  **Unidad de iCloud** : fundamenta iCloud existente en las API de documentos y proporciona una API sencilla para sincronizar los datos no estructurados desde el sistema de archivos. Proporciona una memoria caché completa sin conexión en Mac OS X y es ideal para aplicaciones centradas en documentos.
-  **iCloud Core Data** : permite que los datos se repliquen entre todos los dispositivos del usuario. Los datos son excelentes para mantener privada datos estructurados sincronizados y de usuario único.
-  **CloudKit** : proporciona ambos estructura de datos públicos y de forma masiva y es capaz de controlar el conjunto de datos grande y archivos grandes de datos no estructurados. Su ligada al usuario de la cuenta de iCloud y proporciona cliente dirige la transferencia de datos.


Mantener estos casos de uso en mente, el desarrollador debe elegir la tecnología correcta de iCloud para proporcionar tanto la funcionalidad actual de aplicaciones requeridas y buena escalabilidad para el crecimiento futuro.

## <a name="summary"></a>Resumen

Este artículo trata una introducción rápida a la API de CloudKit. Ha mostrado cómo aprovisionar y configurar una aplicación de iOS de Xamarin para usar CloudKit. Ha cubierto las características de la API de conveniencia CloudKit. Es mostrar cómo diseñar un CloudKit habilita aplicación de escalabilidad, empleando las consultas y las suscripciones. Y, por último, se muestra la información de cuenta de usuario que se expone a una aplicación mediante CloudKit.

## <a name="related-links"></a>Vínculos relacionados

- [CloudKitAtlas (sample)](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Crear un perfil de aprovisionamiento](~/ios/get-started/installation/device-provisioning/index.md)
