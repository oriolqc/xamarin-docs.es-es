---
title: Uso de iCloud con Xamarin.iOS
description: Este documento describe iCloud y su uso en aplicaciones de Xamarin.iOS. Describe el almacenamiento de pares clave-valor, almacenamiento de documentos y copia de seguridad de iCloud.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/09/2016
ms.openlocfilehash: 009e061726f655999c08192b5839a5c962d35e24
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855099"
---
# <a name="using-icloud-with-xamarinios"></a>Uso de iCloud con Xamarin.iOS

La API de almacenamiento de iCloud en iOS 5 permite a las aplicaciones guardar documentos de usuario y datos específicos de la aplicación en una ubicación central y acceso a ellos desde todos los dispositivos del usuario.

Hay cuatro tipos de almacenamiento:

- **Almacenamiento de pares clave-valor** : compartir pequeñas cantidades de datos con la aplicación en un usuario de otros dispositivos.

- **El almacenamiento UIDocument** : para almacenar documentos y otros datos en la cuenta de iCloud del usuario con una subclase de UIDocument.

- **CoreData** -almacenamiento de base de datos de SQLite.

- **Archivos y directorios** : para administrar una gran cantidad de archivos directamente en el sistema de archivos.

Este documento describen los dos primeros tipos (pares clave-valor y UIDocument subclases) y cómo usar esas características de Xamarin.iOS.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

## <a name="requirements"></a>Requisitos

- La última versión estable de Xamarin.iOS
- Xcode 10
- Visual Studio para Mac o Visual Studio de 2019.

## <a name="preparing-for-icloud-development"></a>Preparación para el desarrollo de iCloud

Las aplicaciones deben configurarse para usar iCloud en el [Portal de aprovisionamiento de Apple](https://developer.apple.com/account/ios/overview.action) y el propio proyecto. Antes de desarrollar para iCloud (o probar los ejemplos) siga los pasos siguientes.

Para configurar correctamente una aplicación para tener acceso a iCloud:

-   **Encontrar su TeamID** -inicio de sesión a [developer.apple.com](https://developer.apple.com) y visite el **el centro de miembros > su cuenta > Resumen de la cuenta de desarrollador** para obtener el Id. de equipo (o identificador Individual para los desarrolladores ). Será una cadena de 10 caracteres ( **A93A5CM278** por ejemplo): Esto forma parte del "identificador de contenedor".

-   **Crear un nuevo identificador de aplicación** - para crear un identificador de aplicación, siga los pasos descritos en la [aprovisionamiento para la sección de tecnologías de Store de la Guía de aprovisionamiento de dispositivos](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)y no olvide consultar **iCloud** como un servicio permitidos:

 [![](introduction-to-icloud-images/icloud-sml.png "Comprobación de iCloud como un servicio permitido")](introduction-to-icloud-images/icloud.png#lightbox)

- **Crear un nuevo perfil de aprovisionamiento** - para crear un perfil de aprovisionamiento, siga los pasos descritos en la [guía aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Agregue el identificador del contenedor a Entitlements.plist** -el formato de identificador del contenedor es `TeamID.BundleID`. Para obtener más información, consulte el [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guía.

- **Configurar las propiedades del proyecto** - en Info.plist archivo garantizar el **identificador de paquete** coincide con el **Id. de agrupación** conjunto cuando [creación de un identificador de aplicación ](~/ios/deploy-test/provisioning/capabilities/index.md); Firma de lote usa iOS un **perfil de aprovisionamiento** que contienen un identificador de aplicación con App Service, iCloud y **derechos personalizados** archivo seleccionado. Esto puede realizarse en Visual Studio en el panel de propiedades del proyecto.

- **Habilitar iCloud en el dispositivo** : vaya a **configuración > iCloud** y asegúrese de que el dispositivo se ha iniciado sesión.
Seleccionar y activar el **documentos y datos** opción.

- **Debe usar un dispositivo para probar iCloud** -no funcionará en el simulador.
De hecho, realmente se necesitan dos o más todos los dispositivos firmados con el mismo ID de Apple para ver iCloud en acción.


## <a name="key-value-storage"></a>Almacenamiento de pares clave-valor

Almacenamiento de pares clave-valor está diseñada para pequeñas cantidades de datos que un usuario podría desear dispositivos conserven en distintas: por ejemplo, la última página que ven en un libro o una revista. No se debe usar almacenamiento de pares clave-valor para los datos de copia de seguridad.

Existen algunas limitaciones a tener en cuenta al usar el almacenamiento de pares clave-valor:

- **Tamaño máximo de la clave** -nombres de clave no pueden tener más de 64 bytes.

- **Tamaño máximo del valor** -no se puede almacenar más de 64 kilobytes en un solo valor.

- **Tamaño máximo de almacén de pares clave-valor para una aplicación** -aplicaciones solo pueden almacenar hasta 64 KB de datos de pares clave-valor en total. Se producirá un error si intenta establecer claves más allá de ese límite y se conservará el valor anterior.

- **Tipos de datos** : solo los tipos básicos como cadenas, números y valores booleanos se pueden almacenar.

El **iCloudKeyValue** en el ejemplo se muestra cómo funciona. El código de ejemplo crea una clave llamada para cada dispositivo: puede establecer esta clave en un dispositivo y ver el valor se propagan a otros usuarios. También crea una clave denominada "Compartido", que puede modificarse en cualquier dispositivo, si edita en muchos dispositivos a la vez, iCloud determinará que el valor "gana" (con una marca de tiempo en el cambio) y se propagarán.

Esta captura de pantalla muestra el ejemplo en uso. Cuando se reciben las notificaciones de cambio de iCloud que se imprime en la vista de texto desplazable en la parte inferior de la pantalla y actualizados en los campos de entrada.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "El flujo de mensajes entre dispositivos")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Establecimiento y recuperación de datos

Este código muestra cómo establecer un valor de cadena.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

Una llamada a sincronizar garantiza que el valor se conserva en el almacenamiento en disco local solo. La sincronización en iCloud sucede en segundo plano y no se puede "forzar" código de aplicación. Con buena conectividad de red a menudo se realizará la sincronización en 5 segundos, sin embargo, si la red es deficiente (o desconectada) una actualización puede tardar mucho más.

Puede recuperar un valor con este código:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

El valor se recupera del almacén de datos local: este método no intenta ponerse en contacto con los servidores de iCloud para obtener el valor "latest". iCloud actualizará el almacén de datos local según su propia programación.

### <a name="deleting-data"></a>Eliminar datos

Para quitar completamente un par de clave y valor, use el método Remove similar al siguiente:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Observación de cambios

Una aplicación también puede recibir notificaciones cuando cambian los valores por iCloud agregando un observador a la `NSNotificationCenter.DefaultCenter`.
El siguiente código de **KeyValueViewController.cs** `ViewWillAppear` método muestra cómo realizar escuchas para las notificaciones y crear una lista de los cuales se han cambiado las claves:

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

El código, a continuación, puede realizar alguna acción con la lista de claves modificadas, como actualizar una copia local de ellos o actualizar la interfaz de usuario con los nuevos valores.

Cambio posibles causas son: ServerChange (0), InitialSyncChange (1) o QuotaViolationChange (2). Puede obtener acceso a la razón y realizar un procesamiento diferente si es necesario (por ejemplo, es posible que deba quitar algunas claves como resultado de una *QuotaViolationChange*).

## <a name="document-storage"></a>Almacenamiento de documentos

Almacenamiento de documentos de iCloud está diseñado para administrar los datos que es importantes para la aplicación (y el usuario). Puede usar para administrar archivos y otros datos que la aplicación debe ejecutarse, mientras que al mismo tiempo que proporciona copia de seguridad en iCloud y funcionalidad de uso compartido entre todos los dispositivos del usuario.

Este diagrama muestra cómo encaja entre sí. Cada dispositivo tiene datos guardados en almacenamiento local (la UbiquityContainer) y el demonio se encarga de enviar y recibir datos en la nube de iCloud del sistema operativo. Todo el acceso de archivo a la UbiquityContainer debe hacerse a través de FilePresenter/FileCoordinator para impedir el acceso simultáneo. La `UIDocument` clase implementa ellos; en este ejemplo se muestra cómo usar UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "La información general sobre almacenamiento de documentos")](introduction-to-icloud-images/icloud-overview.png#lightbox)

El ejemplo iCloudUIDoc implementa un sencillo `UIDocument` subclase que contiene un único campo de texto. El texto se representa en un `UITextView` y ediciones se propagan por iCloud a otros dispositivos con un mensaje de notificación que se muestran en rojo. El código de ejemplo no se ocupa de las características más avanzadas de iCloud como la resolución de conflictos.

Esta captura de pantalla muestra la aplicación de ejemplo - después de cambiar el texto y presione **UpdateChangeCount** se sincroniza el documento a través de iCloud a otros dispositivos.

 [![](introduction-to-icloud-images/iclouduidoc.png "Esta captura de pantalla muestra la aplicación de ejemplo después de cambiar el texto y presione UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Hay cinco partes en el ejemplo iCloudUIDoc:

1. **Obtener acceso a la UbiquityContainer** -determinar si se habilita iCloud y si es así la ruta de acceso al área de almacenamiento de iCloud de su aplicación.

1. **Crear una subclase UIDocument** -crear una clase para intermediar entre los objetos de modelo y de almacenamiento de iCloud.

1. **Búsqueda y apertura de documentos de iCloud** -usar `NSFileManager` y `NSPredicate` para buscar documentos de iCloud y abrirlos.

1. **Visualización de documentos de iCloud** -exponer propiedades de su `UIDocument` para que puedan interactuar con los controles de interfaz de usuario.

1. **Guardar documentos de iCloud** -Asegúrese de que los cambios realizados en la interfaz de usuario se guardan en disco y iCloud.

Todas las operaciones de iCloud ejecutar (o debe ejecutarse) asincrónicamente por lo que no bloqueen mientras se espera algún evento. Verá tres formas diferentes de lograrlo en el ejemplo:

 **Subprocesos** : en `AppDelegate.FinishedLaunching` la llamada inicial a `GetUrlForUbiquityContainer` se realiza en otro subproceso para evitar bloquear el subproceso principal.

 **NotificationCenter** : registrar las notificaciones cuando asincrónica de las operaciones, como `NSMetadataQuery.StartQuery` completa.

 **Controladores de finalización** : pasando los métodos para ejecutar en la realización de operaciones asincrónicas, como `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>Obtener acceso a la UbiquityContainer

Es el primer paso para usar el almacenamiento de documentos de iCloud determinar si se habilita iCloud y si es así la ubicación del "contenedor ubicuidad" (el directorio donde se almacenan los archivos de iCloud en el dispositivo).

Este código está en el `AppDelegate.FinishedLaunching` método del ejemplo.

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

Aunque el ejemplo no lo hace, Apple recomienda llamar a GetUrlForUbiquityContainer cada vez que una aplicación se pone en primer plano.

### <a name="creating-a-uidocument-subclass"></a>Crear una subclase UIDocument

Todos los archivos de iCloud y directorios (ie. todo lo almacenado en el directorio UbiquityContainer) deben administrarse mediante los métodos NSFileManager, implementa el protocolo NSFilePresenter y escritura a través de un NSFileCoordinator.
La manera más sencilla de hacer todo eso no es escribirlo usted mismo, pero la subclase UIDocument que lo hace todo.

Hay solo dos métodos que debe implementar en una subclase UIDocument para trabajar con iCloud:

- **LoadFromContents** -pasa el NSData de contenido del archivo para que pueda desempaquetar en sus clase de modelo/es.

- **ContentsForType** -solicitud para proporcionar la representación NSData de las clase de modelo/es para guardar en disco (y la nube).

Este código de ejemplo de **iCloudUIDoc\MonkeyDocument.cs** se muestra cómo implementar UIDocument.

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

El modelo de datos en este caso es muy sencillo: un único campo de texto. El modelo de datos puede ser tan complejo como sea necesario, como un documento Xml o datos binarios. El rol principal de la implementación de UIDocument consiste en traducir entre las clases de modelo y una representación NSData que se puede guardar/cargar en el disco.

### <a name="finding-and-opening-icloud-documents"></a>Buscar y abrir documentos de iCloud

La aplicación de ejemplo solo se ocupa un único archivo - test.txt - por lo que el código en **AppDelegate.cs** crea un `NSPredicate` y `NSMetadataQuery` buscar específicamente para ese nombre de archivo. El `NSMetadataQuery` se ejecuta de forma asincrónica y envía una notificación cuando haya terminado. `DidFinishGathering` llama al observador de notificación, se detiene la consulta y llama a LoadDocument, que usa el `UIDocument.Open` método con un controlador de finalización para intentar cargar el archivo y lo mostrará en un `MonkeyDocumentViewController`.

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>Visualización de documentos de iCloud

Mostrar un UIDocument no debería ser diferente a cualquier otra clase de modelo
- las propiedades se muestran en controles de interfaz de usuario, posiblemente editado por el usuario y, a continuación, volver a escribir en el modelo.

En el ejemplo **iCloudUIDoc\MonkeyDocumentViewController.cs** muestra el texto MonkeyDocument en un `UITextView`. `ViewDidLoad` realiza escuchas para la notificación enviada el `MonkeyDocument.LoadFromContents` método. `LoadFromContents` se llama cuando iCloud tiene nuevos datos para el archivo, para que la notificación indica que se ha actualizado el documento.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

El controlador de notificación de código de ejemplo llama a un método para actualizar la interfaz de usuario: en este caso sin detección de conflictos o resolución.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Guardar documentos de iCloud

Para agregar un UIDocument en iCloud puede llamar a `UIDocument.Save` directamente (para documentos nuevos) o mover un archivo existente con `NSFileManager.DefaultManager.SetUbiquitious`. El código de ejemplo crea un nuevo documento directamente en el contenedor de ubicuidad con este código (hay dos finalización controladores en este caso, uno para el `Save` operación y otro para la apertura):

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

Los cambios posteriores en el documento no "Guardar" directamente, en su lugar, le indicamos los `UIDocument` que ha cambiado con `UpdateChangeCount`, y automáticamente programará una operación de guardar para la operación de disco:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Administración de documentos de iCloud

Los usuarios pueden administrar los documentos de iCloud en el **documentos** directorio del "contenedor ubicuidad" fuera de la aplicación a través de la configuración; pueden ver la lista de archivos y de deslizar rápidamente para eliminar. Código de la aplicación debe ser capaz de controlar la situación donde se eliminan documentos por el usuario. No almacene datos internos de la aplicación en el **documentos** directory.

 [![](introduction-to-icloud-images/icloudstorage.png "Administración de flujo de trabajo de documentos de iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Los usuarios también recibirá advertencias diferentes cuando intenta quitar una aplicación habilitada para iCloud desde sus dispositivos, para informarles del estado de los documentos de iCloud relacionados con esa aplicación.

 [![](introduction-to-icloud-images/icloud-delete1.png "Cuadro de diálogo de ejemplo cuando el usuario intenta quitar una aplicación habilitada para iCloud de su dispositivo")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Cuadro de diálogo de ejemplo cuando el usuario intenta quitar una aplicación habilitada para iCloud de su dispositivo")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>copia de seguridad de iCloud

Aunque copias de seguridad de iCloud no es una característica que tiene acceso directamente a los desarrolladores, la forma de diseñar la aplicación puede afectar a la experiencia del usuario.
Apple proporciona [directrices de almacenamiento de datos de iOS](https://developer.apple.com/icloud/documentation/data-storage/) para los desarrolladores deberán seguir en sus aplicaciones de iOS.

La consideración más importante es que si la aplicación almacena archivos grandes que no son generados por el usuario (por ejemplo, una aplicación de lectores de la revista que almacene hundred-plus megabytes de contenido por cada problema). Apple prefiere no almacenar a este tipo de datos donde se se copia de seguridad en iCloud y rellenar innecesariamente la cuota de iCloud del usuario.

Las aplicaciones que almacenan grandes cantidades de datos similar al siguiente o bien deben almacenar en uno de los directorios de usuario que no sea la copia de seguridad (p ej. Las memorias caché o tmp) o use `NSFileManager.SetSkipBackupAttribute` para aplicar una marca a esos archivos para que iCloud los omite durante las operaciones de copia de seguridad.

## <a name="summary"></a>Resumen

Este artículo presenta la nueva característica de iCloud incluida en iOS 5. Examinan los pasos necesarios para configurar el proyecto para usar iCloud y, a continuación, se proporcionan ejemplos de cómo implementar características de iCloud.

El almacenamiento de pares clave-valor en el ejemplo se muestra cómo se puede usar iCloud para almacenar una pequeña cantidad de datos de forma similar a la forma en que se almacenan NSUserPreferences. El ejemplo UIDocument mostró cómo más datos complejos se pueden almacenar y sincronizar en varios dispositivos a través de iCloud.

Por último incluye una breve explicación sobre cómo la adición de copia de seguridad de iCloud debe influir en el diseño de aplicaciones.



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iCloud (ejemplo)](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [Código de ejemplo de seminarios de iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [Seminario diapositivas de iCloud](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud Storage](https://support.apple.com/kb/HT4847)
