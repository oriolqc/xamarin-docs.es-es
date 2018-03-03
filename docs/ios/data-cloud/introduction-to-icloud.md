---
title: iCloud
description: "Apple incorporó iCloud en iOS 5 como un servicio para permitir que las aplicaciones almacenar datos en servidores de Apple y que se sincroniza en todos los dispositivos usados por la misma persona (a través de su Id. de Apple). También tiene un componente de copia de seguridad, donde los datos de los dispositivos se copia de seguridad para servidores de Apple. Este documento describe cómo usar algunos de iCloud las API proporcionadas por Apple para almacenar y recuperar datos de sus servidores, con ejemplos de C# para almacenar pares de datos de clave y valor pequeños y para almacenar documentos. También se explica cómo iCloud copia de seguridad puede influir en el diseño de la aplicación."
ms.topic: article
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/09/2016
ms.openlocfilehash: 7e02c92f9c1aafeb97da4905c17898b02362c960
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="icloud"></a>iCloud

_Apple incorporó iCloud en iOS 5 como un servicio para permitir que las aplicaciones almacenar datos en servidores de Apple y que se sincroniza en todos los dispositivos usados por la misma persona (a través de su Id. de Apple). También tiene un componente de copia de seguridad, donde los datos de los dispositivos se copia de seguridad para servidores de Apple. Este documento describe cómo usar algunos de iCloud las API proporcionadas por Apple para almacenar y recuperar datos de sus servidores, con ejemplos de C# para almacenar pares de datos de clave y valor pequeños y para almacenar documentos. También se explica cómo iCloud copia de seguridad puede influir en el diseño de la aplicación._

La API de almacenamiento de iCloud en iOS 5 permite a las aplicaciones guardar documentos de usuario y datos específicos de la aplicación a una ubicación central y tener acceso a los elementos de todos los dispositivos del usuario.

Hay cuatro tipos de almacenamiento:

- **Almacenamiento de información de clave y valor** : para compartir pequeñas cantidades de datos con la aplicación de un usuario de otros dispositivos.

- **Almacenamiento de UIDocument** : para almacenar documentos y otros datos en la cuenta de usuario iCloud usando una subclase de UIDocument.

- **CoreData** -almacenamiento de base de datos de SQLite.

- **Archivos y directorios individuales** : para administrar una gran cantidad de archivos diferentes directamente en el sistema de archivos.

Este documento describen los dos primeros tipos - pares de clave-valor y subclases UIDocument - y cómo usar las características de Xamarin.iOS.

## <a name="requirements"></a>Requisitos

- La versión estable más reciente de Xamarin.iOS
- Xcode 8 o posterior
- Visual Studio para Mac o en Visual Studio 2015 y versiones más reciente.

## <a name="preparing-for-icloud-development"></a>Preparación para el desarrollo de iCloud

Las aplicaciones deben configurarse para usar iCloud en el [Portal de aprovisionamiento de Apple](https://developer.apple.com/account/ios/overview.action) y el propio proyecto. Antes de desarrollar para iCloud (o probar los ejemplos) siga los pasos siguientes.

Para configurar correctamente una aplicación para tener acceso a iCloud:

-   **Buscar su TeamID** -inicio de sesión a [developer.apple.com](http://developer.apple.com) y visite el **centro de usuarios registrados > su cuenta > Resumen de la cuenta de desarrollador** para obtener el Id. de equipo (o identificador Individual para los desarrolladores únicos ). Será una cadena de 10 caracteres ( **A93A5CM278** por ejemplo)-Esto forma parte de la "identificador del contenedor".

-   **Crear un nuevo identificador de aplicación** - para crear un identificador de aplicación, siga los pasos descritos en la [aprovisionamiento para la sección de tecnologías de almacenamiento de la Guía de aprovisionamiento de dispositivos](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)y no olvide comprobar **iCloud** como un servicio permitido:

 [ ![](introduction-to-icloud-images/icloud-sml.png "Comprobar iCloud como un servicio permitido")](introduction-to-icloud-images/icloud.png)

- **Crear un nuevo perfil de aprovisionamiento** - para crear un perfil de aprovisionamiento, siga los pasos descritos en la [Guía de aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile) .

- **Agregar el identificador del contenedor a Entitlements.plist** -el formato de identificador del contenedor es `TeamID.BundleID`. Para obtener más información, consulte el [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guía.

- **Configurar las propiedades del proyecto** : en el Info.plist archivo garantizar la **identificador de paquete** coincide con la **Id. del lote** establecer cuando [crear un identificador de aplicación ](~/ios/deploy-test/provisioning/capabilities/index.md); Firma de paquete usa iOS un **perfil de aprovisionamiento de** que contienen un identificador de aplicación con el servicio de aplicaciones, iCloud y **derechos personalizados** archivo seleccionado. Esto puede todos hacerse en Visual Studio en el panel de propiedades del proyecto.

- **Habilitar iCloud en el dispositivo** : vaya a **configuración > iCloud** y asegúrese de que el dispositivo está registrado.
Seleccione y activar el **documentos y datos** opción.

- **Debe utilizar un dispositivo para probar iCloud** -no funcionará en el simulador.
De hecho, necesita realmente dos o más dispositivos todos iniciados sesión con el mismo identificador de Apple para ver iCloud en acción.


## <a name="key-value-storage"></a>Valor de clave de almacenamiento

Valor de clave de almacenamiento está diseñado para pequeñas cantidades de datos que un usuario podría interesarte persistente a través de dispositivos: por ejemplo, la última página que ven en un libro o una revista. Valor de clave de almacenamiento no debe usarse para los datos de copia de seguridad.

Existen algunas limitaciones a tener en cuenta al usar el almacenamiento de valores de clave:

- **Tamaño máximo de la clave** -nombres de las claves no se pueden tener más de 64 bytes.

- **Tamaño del valor máximo** -no se puede almacenar más de 64 kilobytes en un solo valor.

- **Tamaño máximo de los almacenes de clave y valor para una aplicación** -solo las aplicaciones pueden almacenar hasta 64 KB de datos de clave y valor en total. Se producirá un error si se intenta establecer claves más allá de ese límite y se conservará el valor anterior.

- **Tipos de datos** : solo los tipos básicos como cadenas, números y valores booleanos se pueden almacenar.

El **iCloudKeyValue** en el ejemplo se muestra cómo funciona. El código de ejemplo crea una clave con el nombre de cada dispositivo: puede establecer esta clave en un dispositivo y ver el valor se propagan a otros usuarios. También crea una clave denominada "Compartido", que se puede editar en cualquier dispositivo - si edita en varios dispositivos al mismo tiempo, iCloud decidirá que valor "gana" (con una marca de tiempo en el cambio) y se propagarán.

Esta captura de pantalla muestra el ejemplo en uso. Cuando se reciben las notificaciones de cambio de iCloud se imprimen en la vista de texto de desplazamiento en la parte inferior de la pantalla y se actualiza en los campos de entrada.



 [ ![](introduction-to-icloud-images/icloud-kv-arrows.png "El flujo de mensajes entre los dispositivos")](introduction-to-icloud-images/icloud-kv-arrows.png)

### <a name="setting-and-retrieving-data"></a>Establecer y recuperar datos

Este código muestra cómo establecer un valor de cadena.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

Llamar a Synchronize asegura que el valor se conserva en el almacenamiento de disco local solo. La sincronización en iCloud sucede en segundo plano y no se puede "forzar" por código de aplicación. Con buena conectividad de red a menudo se realizará la sincronización en 5 segundos, sin embargo, si la red es deficiente (o desconectada) una actualización puede tardar mucho más.

Puede recuperar un valor con este código:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

El valor se recupera del almacén de datos local, este método no intenta ponerse en contacto con servidores de iCloud para obtener el valor "más reciente". iCloud actualizará el almacén de datos local según su propia programación.

### <a name="deleting-data"></a>Eliminar datos

Para quitar completamente un par de clave y valor, utilice el método Remove similar al siguiente:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Obedezca a cambios

Una aplicación puede recibir notificaciones cuando se cambian los valores por iCloud agregando un observador a la `NSNotificationCenter.DefaultCenter`.
El siguiente código **KeyValueViewController.cs** `ViewWillAppear` método muestra cómo realizar escuchas para las notificaciones y crear una lista de los cuales se cambiaron las claves:

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

El código, a continuación, puede realizar alguna acción con la lista de claves modificadas, como actualizar una copia local de los mismos o actualizar la interfaz de usuario con los nuevos valores.

Cambio posibles razones son: ServerChange (0), InitialSyncChange (1) o QuotaViolationChange (2). Puede tener acceso a la razón y realizar un procesamiento diferente si es necesario (por ejemplo, necesitará quitar algunas claves como resultado de un *QuotaViolationChange*).

## <a name="document-storage"></a>Almacenamiento de documentos

Almacenamiento de documentos de iCloud está diseñado para administrar los datos que es importantes para la aplicación (y el usuario). Se puede utilizar para administrar archivos y otros datos que necesita la aplicación para que se ejecute, al mismo tiempo, proporcionar la copia de seguridad en iCloud y funcionalidad de uso compartido entre todos los dispositivos del usuario.

Este diagrama muestra cómo encaja cada pieza. Cada dispositivo tiene datos guardados en el almacenamiento local (la UbiquityContainer) y iCloud del sistema operativo que demonio se encarga de enviar y recibir datos en la nube. Todo el acceso de archivo a la UbiquityContainer debe hacerse a través de FilePresenter/FileCoordinator para evitar el acceso simultáneo. La `UIDocument` clase implementa los automáticamente; en este ejemplo se muestra cómo usar UIDocument.

 [ ![](introduction-to-icloud-images/icloud-overview.png "La información general de almacenamiento de documentos")](introduction-to-icloud-images/icloud-overview.png)

El ejemplo iCloudUIDoc implementa un sencillo `UIDocument` subclase que contiene un único campo de texto. El texto se representa en un `UITextView` y ediciones se propagan por iCloud a otros dispositivos con un mensaje de notificación se muestra en rojo. El código de ejemplo no se ocupa de las características más avanzadas de iCloud como la resolución de conflictos.

Esta captura de pantalla muestra la aplicación de ejemplo - después de cambiar el texto y presione **UpdateChangeCount** se sincroniza el documento a través de iCloud a otros dispositivos.

 [ ![](introduction-to-icloud-images/iclouduidoc.png "Esta captura de pantalla muestra la aplicación de ejemplo después de cambiar el texto y presione UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png)

Hay cinco partes en el ejemplo de iCloudUIDoc:

1. **Obtener acceso a la UbiquityContainer** -determinar si está habilitado iCloud y si es así la ruta de acceso al área de almacenamiento de iCloud de su aplicación.

1. **Crear una subclase de UIDocument** -crear una clase para intermedio entre el almacenamiento de iCloud y los objetos de modelo.

1. **Buscar y abrir documentos de iCloud** -usar `NSFileManager` y `NSPredicate` para buscar documentos de iCloud y abrirlos.

1. **Mostrar documentos de iCloud** -exponen propiedades de su `UIDocument` para que puedan interactuar con los controles de interfaz de usuario.

1. **Guardar documentos de iCloud** -Asegúrese de que los cambios realizados en la interfaz de usuario se conservan en disco y en iCloud.

Todas las operaciones de iCloud ejecutar (o se debe ejecutar) forma asincrónica para que no bloqueen mientras se espera que ocurra algo para. Verá tres formas diferentes de conseguirlo en el ejemplo:

 **Subprocesos** : en `AppDelegate.FinishedLaunching` la llamada inicial a `GetUrlForUbiquityContainer` se realiza en otro subproceso para evitar bloquear el subproceso principal.

 **NotificationCenter** - registrar para las notificaciones cuando asincrónica las operaciones como `NSMetadataQuery.StartQuery` completa.

 **Controladores de finalización** : pasando métodos para que se ejecute en la realización de operaciones asincrónicas como `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>Obtener acceso a la UbiquityContainer

Es el primer paso para utilizar iCloud almacenamiento de documentos determinar si iCloud está habilitada y si es así la ubicación de la "contenedor" ubicuidad"(es decir, el directorio donde se almacenan los archivos de iCloud en el dispositivo).

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

### <a name="creating-a-uidocument-subclass"></a>Crear una subclase de UIDocument

Todos los archivos de iCloud y directorios (ie. cualquier elemento almacenado en el directorio UbiquityContainer) deben administrarse mediante métodos NSFileManager, implementar el protocolo NSFilePresenter y escritura a través de un NSFileCoordinator.
La manera más sencilla de hacer todo eso no es escribirlo usted mismo, pero subclase UIDocument que lo hace todo por usted.

Hay solo dos métodos que debe implementar en una subclase de UIDocument para trabajar con iCloud:

- **LoadFromContents** -pasa en el NSData del contenido del archivo para que pueda desempaquetar en sus clase de modelo/es.

- **ContentsForType** -solicitud para proporcionar la representación NSData de las clase de modelo/es para guardar en disco (y la nube).

Este código de ejemplo de **iCloudUIDoc\MonkeyDocument.cs** muestra cómo implementar UIDocument.

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

El modelo de datos en este caso es muy simple: un único campo de texto. El modelo de datos puede ser tan complejo como sea necesario, como un documento Xml o datos binarios. El rol principal de la implementación de UIDocument consiste en traducir entre las clases de modelo y una representación de NSData que se puede guardar o cargar en el disco.

### <a name="finding-and-opening-icloud-documents"></a>Buscar y abrir documentos de iCloud

La aplicación de ejemplo solo se ocupa de un único archivo - test.txt - por lo que el código en **AppDelegate.cs** crea un `NSPredicate` y `NSMetadataQuery` para buscar específicamente para ese nombre de archivo. El `NSMetadataQuery` se ejecuta de forma asincrónica y envía una notificación cuando termina. `DidFinishGathering` llama al observador de notificación, se detiene la consulta y llama a LoadDocument, que utiliza el `UIDocument.Open` método con un controlador de finalización para intentar cargar el archivo y mostrarlo en un `MonkeyDocumentViewController`.

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

### <a name="displaying-icloud-documents"></a>Mostrar documentos de iCloud

Mostrar un UIDocument no debe ser diferente a cualquier otra clase de modelo
- propiedades se muestran en los controles de interfaz de usuario, posiblemente editado por el usuario y, a continuación, vuelven a escribir en el modelo.

En el ejemplo **iCloudUIDoc\MonkeyDocumentViewController.cs** muestra el texto MonkeyDocument en un `UITextView`. `ViewDidLoad` realiza escuchas para la notificación enviada el `MonkeyDocument.LoadFromContents` método. `LoadFromContents` se llama cuando iCloud tiene nuevos datos para el archivo, por lo que notificación indica que el documento se ha actualizado.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

El controlador de notificación de código de ejemplo llama a un método para actualizar la interfaz de usuario: en este caso sin la detección de conflictos o resolución.

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

Para agregar una UIDocument en iCloud puede llamar a `UIDocument.Save` directamente (para documentos nuevos) o mover un archivo existente mediante `NSFileManager.DefaultManager.SetUbiquitious`. El código de ejemplo crea un nuevo documento directamente en el contenedor de ubicuidad con este código (hay dos finalización controladores aquí, uno para la `Save` operación y otro para abrir):

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

Los cambios subsiguientes en el documento no se "guardan" directamente, en su lugar, le indicamos los `UIDocument` que ha cambiado con `UpdateChangeCount`, y automáticamente programará un proceso de guardar para la operación de disco:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Administrar documentos de iCloud

Los usuarios pueden administrar los documentos en iCloud el **documentos** directorio de la "contenedor" ubicuidad"fuera de la aplicación a través de la configuración; pueden ver la lista de archivos y deslice el dedo para eliminar. Código de la aplicación debe ser capaz de controlar la situación donde se eliminan documentos por el usuario. No almacene datos de aplicación interna en la **documentos** directory.

 [ ![](introduction-to-icloud-images/icloudstorage.png "Administrar el flujo de trabajo de iCloud documentos")](introduction-to-icloud-images/icloudstorage.png)



Los usuarios también recibirá advertencias diferentes cuando intenta quitar una aplicación habilitada para iCloud desde sus dispositivos, para informarles del estado de los documentos de iCloud relacionados con esa aplicación.

 [ ![](introduction-to-icloud-images/icloud-delete1.png "Cuadro de diálogo de ejemplo cuando el usuario intenta quitar una aplicación habilitada para iCloud de su dispositivo")](introduction-to-icloud-images/icloud-delete1.png)

 [ ![](introduction-to-icloud-images/icloud-delete2.png "Cuadro de diálogo de ejemplo cuando el usuario intenta quitar una aplicación habilitada para iCloud de su dispositivo")](introduction-to-icloud-images/icloud-delete2.png)

## <a name="icloud-backup"></a>copia de seguridad de iCloud

Mientras la copia de seguridad en iCloud no es una característica que se tiene acceso directamente por los desarrolladores, la manera de diseñar la aplicación puede afectar a la experiencia del usuario.
Apple proporciona [iOS directrices de almacenamiento de datos](http://developer.apple.com/icloud/documentation/data-storage/) para los desarrolladores deben seguir para sus aplicaciones de iOS.

La consideración más importante es que si la aplicación almacena archivos de gran tamaño que no son generados por el usuario (por ejemplo, una aplicación de lector revista que almacena hundred-plus megabytes de contenido por cada problema). Apple prefiere que no almacenar a este tipo de datos donde se se copia de seguridad en iCloud y rellenar innecesariamente la cuota del usuario iCloud.

Las aplicaciones que almacenan grandes cantidades de datos, como esto deberían almacenar ya sea en uno de los directorios de usuario que no es copia de seguridad (p. ej. Las memorias caché o tmp) o use `NSFileManager.SetSkipBackupAttribute` para aplicar una marca en dichos archivos para que iCloud los omite durante las operaciones de copia de seguridad.

## <a name="summary"></a>Resumen

En este artículo se incorpora la nueva característica de iCloud incluida en iOS 5. Examinan los pasos necesarios para configurar el proyecto para usar iCloud y, a continuación, se proporcionan ejemplos de cómo implementar características de iCloud.

El almacenamiento de valores de clave en el ejemplo se muestra cómo se puede utilizar iCloud para almacenar una pequeña cantidad de datos de forma similar a la manera en que se almacenan NSUserPreferences. El ejemplo UIDocument se ha explicado cómo más datos complejos pueden se almacenan y sincronizan entre varios dispositivos a través de iCloud.

Por último, incluye una breve explicación de cómo la adición de copia de seguridad de iCloud debe influir en el diseño de aplicaciones.



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iCloud (ejemplo)](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [Código de ejemplo de seminario iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [Seminario diapositivas de iCloud](http://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Almacenamiento de iCloud](http://support.apple.com/kb/HT4847)
