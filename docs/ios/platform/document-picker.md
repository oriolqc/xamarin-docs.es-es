---
title: Selector de documentos en Xamarin.iOS
description: Este documento describe el selector de documentos de iOS y explica cómo usarlo en Xamarin.iOS. Da un vistazo a iCloud, documentos, código de programa de instalación común, las extensiones de proveedor de documento y más.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: ac77bbc27784d1b836f4a1d26365acd65ac63ae7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111400"
---
# <a name="document-picker-in-xamarinios"></a>Selector de documentos en Xamarin.iOS

El selector de documentos permite que los documentos deben compartirse entre aplicaciones. Estos documentos pueden almacenarse en iCloud o en el directorio de una aplicación diferente. Los documentos se comparten a través del conjunto de [extensiones de proveedor de documento](~/ios/platform/extensions.md) el usuario ha instalado en su dispositivo. 

Debido a la dificultad de mantener sincronizados en la nube y aplicaciones de documentos, presentan una cierta complejidad necesaria.

## <a name="requirements"></a>Requisitos

Para completar los pasos que aparecen en este artículo, se requiere lo siguiente:

-  **Xcode 7 y iOS 8 o posterior** : Xcode 7 y iOS 8 o las API más recientes de Apple deben estar instalado y configurado en el equipo del desarrollador.
-  **Visual Studio o Visual Studio para Mac** – debe instalarse la versión más reciente de Visual Studio para Mac.
-  **Dispositivo iOS** : un dispositivo iOS con iOS 8 o posterior.

## <a name="changes-to-icloud"></a>Cambios realizados en iCloud

Para implementar las nuevas características del selector de documentos, los siguientes cambios se realizaron en iCloud Service de Apple:

-  El demonio ha sido reescrito completamente uso directo de CloudKit iCloud.
-  ICloud existente características han sido cambiado de nombre iCloud Drive.
-  Se agregó compatibilidad para sistemas operativos Windows de Microsoft en iCloud.
-  Se ha agregado una carpeta de iCloud en el Finder de Mac OS.
-  dispositivos iOS pueden acceder al contenido de la carpeta de iCloud de Mac OS.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores a tratar correctamente el Reglamento general de protección de datos (RGPD) de la Unión Europea.

## <a name="what-is-a-document"></a>¿Qué es un documento?

Cuando se hace referencia a un documento en iCloud, es una entidad única e independiente y se debería percibir como tales por el usuario. Un usuario que desee modificar el documento o compartirlo con otros usuarios (mediante el uso de correo electrónico, por ejemplo).

Hay varios tipos de archivos que el usuario inmediatamente reconocerán como documentos, como páginas, archivos discurso de apertura o números. Sin embargo, iCloud no se limita a este concepto. Por ejemplo, el estado de un juego (como una coincidencia de ajedrez) se puede tratar como un documento y almacenado en iCloud. Este archivo se pueden pasar entre los dispositivos del usuario y que puedan recoger un juego donde lo dejó en un dispositivo diferente.

## <a name="dealing-with-documents"></a>Trabajar con documentos

Antes de profundizar en el código necesario para usar el selector de documentos con Xamarin, este artículo se va a cubrir los procedimientos recomendados para trabajar con documentos de iCloud y varias de las modificaciones realizadas en las API existentes necesarios para admitir el selector de documentos.

### <a name="using-file-coordination"></a>Uso de coordinación de archivo

Dado que un archivo se puede modificar desde varias ubicaciones diferentes, coordinación debe usarse para evitar la pérdida de datos.

 [![](document-picker-images/image1.png "Uso de coordinación de archivo")](document-picker-images/image1.png#lightbox)

Echemos un vistazo a la ilustración anterior:

1.  Un dispositivo iOS mediante la coordinación de archivo crea un nuevo documento y lo guarda en la carpeta de iCloud.
2.  iCloud guarda el archivo modificado en la nube para su distribución a todos los dispositivos.
3.  Un Mac conectado ve el archivo modificado en la carpeta de iCloud y coordinación del archivo utiliza para copiar los cambios en el archivo.
4.  Un dispositivo mediante el archivo de coordinación no realiza un cambio en el archivo y lo guarda en la carpeta de iCloud. Estos cambios se replican al instante en el resto de dispositivos.

Se supone el original estaba editando el archivo, el dispositivo iOS o Mac ahora están perdidos y se sobrescribe con la versión del archivo desde el dispositivo no coordinado de sus cambios. Para evitar la pérdida de datos, la coordinación del archivo es obligatoria cuando se trabaja con documentos basados en la nube.

### <a name="using-uidocument"></a>Uso de UIDocument

 `UIDocument` simplifica las cosas (o `NSDocument` en macOS) haciendo todo el trabajo pesado para el desarrollador. Proporciona lo compila en archivos coordinación con las colas en segundo plano para evitar el bloqueo de la interfaz de usuario de la aplicación.

 `UIDocument` expone varias API de alto nivel que facilitan el trabajo de desarrollo de una aplicación de Xamarin para cualquier finalidad que el desarrollador requiere.

El código siguiente crea una subclase de `UIDocument` para implementar un documento basado en texto genérico que puede usarse para almacenar y recuperar el texto de iCloud:

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

La `GenericTextDocument` clase presentada más arriba se usará en este artículo cuando se trabaja con el selector de documentos y documentos externos en una aplicación Xamarin.iOS 8.

## <a name="asynchronous-file-coordination"></a>Coordinación de archivos asincrónica

iOS 8 proporciona varias características nuevas de coordinación asincrónica de archivo a través de las nuevas API de coordinación de archivo. Antes de iOS 8, todas las API existentes de coordinación de archivo eran totalmente sincrónicas. Esto significaba que el desarrollador era responsable de implementar su propio fondo puesta en cola para evitar que coordinación del archivo de bloqueo de la interfaz de usuario de la aplicación.

El nuevo `NSFileAccessIntent` clase contiene una dirección URL que apunta al archivo y varias opciones para controlar el tipo de coordinación necesaria. El código siguiente muestra cómo mover un archivo de una ubicación a otra mediante las intenciones:

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>Detectar y enumerar documentos

Es la manera de detectar y lista de documentos mediante el uso de las existentes `NSMetadataQuery` API. Esta sección explica las nuevas características agregadas a `NSMetadataQuery` que hacen que trabajar con documentos incluso más fácil que nunca.

### <a name="existing-behavior"></a>Comportamiento existente

Antes de iOS 8, `NSMetadataQuery` como era lento a los cambios de archivo local de recogida: elimina, crea y cambia el nombre.

 [![](document-picker-images/image2.png "Información general de los cambios de archivo local NSMetadataQuery")](document-picker-images/image2.png#lightbox)

En el diagrama anterior:

1.  Para los archivos que ya existen en el contenedor de aplicaciones, `NSMetadataQuery` existentes `NSMetadata` registros creados previamente y puestos en cola para que estén disponibles al instante a la aplicación.
1.  La aplicación crea un nuevo archivo en el contenedor de aplicación.
1.  Hay un retraso antes de `NSMetadataQuery` ve la modificación en el contenedor de aplicación y crea necesario `NSMetadata` registro.


A causa del retraso en la creación de la `NSMetadata` registros, la aplicación tenía que tiene dos abrir orígenes de datos: uno para los cambios del archivo local y otro para la nube según los cambios.

### <a name="stitching"></a>Unión

En iOS 8, `NSMetadataQuery` es más fácil de usar directamente con una nueva característica denominada composiciones:

 [![](document-picker-images/image3.png "NSMetadataQuery con una nueva característica denominada composiciones")](document-picker-images/image3.png#lightbox)

Uso de composición en el diagrama anterior:

1.  Como antes, para los archivos que ya existen en el contenedor de aplicaciones, `NSMetadataQuery` existentes `NSMetadata` registros creados previamente y puestos en cola.
1.  La aplicación crea un nuevo archivo en el contenedor de aplicación mediante la coordinación de archivo.
1.  Un enlace en el contenedor de la aplicación ve la modificación y llama a `NSMetadataQuery` para crear los `NSMetadata` registro.
1.  El `NSMetadata` se crea directamente después del archivo de registro y está disponible para la aplicación.


Mediante el uso de composiciones la aplicación ya no tiene que abrir un origen de datos para supervisar local y los cambios de archivo basado en la nube. Ahora puede depender de la aplicación `NSMetadataQuery` directamente.

> [!IMPORTANT]
> Unión solo funciona si la aplicación está usando la coordinación del archivo tal como se presenta en la sección anterior. Si no se está usando el archivo coordinación, las API de forma predeterminada al comportamiento anterior a iOS 8 existente.




### <a name="new-ios-8-metadata-features"></a>Nuevas características de los metadatos de iOS 8

Se han agregado las siguientes características nuevas para `NSMetadataQuery` en iOS 8:

-   `NSMetatadataQuery` puede enumerar los documentos no locales almacenados en la nube.
-  Se agregaron nuevas API para tener acceso a información de metadatos en los documentos basados en la nube. 
-  Hay un nuevo `NSUrl_PromisedItems` API que se va a tener acceso a los atributos de archivo de los archivos que pueden tengan o no su contenido disponible localmente.
-  Utilice la `GetPromisedItemResourceValue` método para obtener información sobre un determinado archivo o use el `GetPromisedItemResourceValues` método para obtener información en más de un archivo a la vez.


Para tratar con los metadatos se han agregado dos nuevos indicadores de coordinación de archivo:

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Con las marcas anteriores, el contenido del archivo de documento no es necesario que estén disponibles localmente para que se utilicen.

El segmento de código siguiente muestra cómo usar `NSMetadataQuery` para consultar la existencia de un archivo concreto y generar el archivo si no existe:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;


#region Static Properties
public const string TestFilename = "test.txt"; 
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),           NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path 
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>Miniaturas de documentos

Apple se siente que es la mejor experiencia de usuario cuando se muestran los documentos de una aplicación usar las versiones preliminares. Esto proporciona el contexto de los usuarios finales, para que pueden identificar rápidamente el documento que desea trabajar.

Antes de iOS 8, que muestra vistas previas del documento requiere una implementación personalizada. Nuevo en iOS 8 son atributos del sistema de archivos que permiten al desarrollador trabajar rápidamente con las miniaturas de documentos.

#### <a name="retrieving-document-thumbnails"></a>Recuperando las miniaturas de documentos 

Mediante una llamada a la `GetPromisedItemResourceValue` o `GetPromisedItemResourceValues` métodos, `NSUrl_PromisedItems` API, un `NSUrlThumbnailDictionary`, se devuelve. La única clave actualmente en este diccionario es el `NSThumbnial1024X1024SizeKey` y su coincidencia `UIImage`.

#### <a name="saving-document-thumbnails"></a>Guardando las miniaturas de documentos

Es la manera más fácil para guardar una vista en miniatura mediante `UIDocument`. Mediante una llamada a la `GetFileAttributesToWrite` método de la `UIDocument` y establecer la miniatura, se guardará automáticamente cuando es el archivo del documento. El demonio de iCloud verá este cambio y propagarla a iCloud. En Mac OS X, se generan automáticamente miniaturas para los desarrolladores mediante el complemento de un vistazo rápido.

Con los conceptos básicos de trabajar con documentos de iCloud basándose en su lugar, junto con las modificaciones a las API existente, estamos preparados para implementar el controlador de vista del selector de documentos en un ejemplo de Xamarin iOS 8 aplicación móvil.


## <a name="enabling-icloud-in-xamarin"></a>Habilitar iCloud en Xamarin

Antes de que el selector de documentos se pueden usar en una aplicación de Xamarin.iOS, soporte técnico de iCloud debe estar habilitada tanto en la aplicación a través de Apple. 

El siguiente tutorial de pasos del proceso de aprovisionamiento de iCloud.

1. Crear un contenedor de iCloud.
2. Crear un identificador de aplicación que contiene el servicio de aplicación de iCloud.
3. Crear un perfil de aprovisionamiento que incluya este ID. App

El [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) guía le guía a través de los dos primeros pasos. Para crear un perfil de aprovisionamiento, siga los pasos descritos en la [perfil de aprovisionamiento](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) guía.



El siguiente tutorial de pasos del proceso de configuración de la aplicación de iCloud:

Haga lo siguiente:

1.  Abra el proyecto en Visual Studio para Mac o Visual Studio.
2.  En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione opciones.
3.  En el cuadro de diálogo Opciones, seleccione **aplicación de iOS**, asegúrese de que el **identificador de paquete** coincide con el que se definió en **Id. de aplicación** creado anteriormente para la aplicación. 
4.  Seleccione **firma de lote de iOS**, seleccione el **Developer Identity** y **perfil de aprovisionamiento** creado anteriormente.
5.  Haga clic en el **Aceptar** botón para guardar los cambios y cerrar el cuadro de diálogo.
6.  Haga doble clic en `Entitlements.plist` en el **el Explorador de soluciones** para abrirlo en el editor.

    > [!IMPORTANT]
    > En Visual Studio puede deberá abrir el editor de derechos con el botón secundario en él, seleccione **abrir con...** y seleccione el Editor de la lista de propiedades

7.  Comprobar **habilitar iCloud** , **documentos de iCloud** , **almacenamiento de pares clave-valor** y **CloudKit** .
8.  Asegúrese del **contenedor** existe para la aplicación (tal y como se creó anteriormente). Ejemplo: `iCloud.com.your-company.AppName`
9.  Guarde los cambios en el archivo.

Para obtener más información sobre los derechos que hacen referencia a la [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guía.

Con la configuración anterior en su lugar, ahora puede usar la aplicación documentos basados en la nube y el nuevo controlador de vista de selector de documentos.

## <a name="common-setup-code"></a>Código de programa de instalación común

Antes de comenzar con el controlador de vista del selector de documento, hay código de configuración estándar necesario. Iniciar mediante la modificación de la aplicación `AppDelegate.cs` de archivo y dele un aspecto similar al siguiente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt"; 
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }


        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");  

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path 
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {   
                    // Yes, inform caller and save location the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();
                
        }
        
        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }
        
        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }
        
        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }
        
        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> El código anterior incluye el código de la sección Descubra y documentos de la lista anterior. Se presenta aquí en su totalidad, tal como aparecería en una aplicación real. Por motivos de simplicidad, este ejemplo funciona con un único archivo codificado de forma rígida (`test.txt`) solo.

El código anterior expone varios métodos abreviados de unidad de iCloud para que sean más fáciles trabajar con el resto de la aplicación.

A continuación, agregue el siguiente código para cualquier contenedor de vista que se va a utilizar el selector de documentos o trabajar con documentos basados en la nube o la vista:

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Esto agrega un acceso directo para llegar a la `AppDelegate` y tener acceso a los métodos abreviados de iCloud creados anteriormente.

Con este código en su lugar, echemos un vistazo a la implementación del controlador de vista de selector de documento en una aplicación de Xamarin iOS 8.

## <a name="using-the-document-picker-view-controller"></a>Usar el controlador de vista del selector de documentos

Antes de iOS 8, era muy difícil de obtener acceso a documentos desde otra aplicación porque no había manera de detectar los documentos fuera de la aplicación desde dentro de la aplicación.

### <a name="existing-behavior"></a>Comportamiento existente

 [![](document-picker-images/image31.png "Información general de comportamiento existente")](document-picker-images/image31.png#lightbox)

Echemos un vistazo a tener acceso a un documento externo antes de iOS 8:

1.  En primer lugar el usuario tendría que abrir la aplicación que originalmente creó el documento.
1.  El documento está seleccionado y el `UIDocumentInteractionController` se usa para enviar el documento a la nueva aplicación.
1.  Por último, se coloca una copia del documento original en el contenedor de la nueva aplicación.


A partir de ahí está disponible para la segunda aplicación abrir y editar el documento.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Detección de documentos fuera de contenedor la aplicación en un

En iOS 8, una aplicación es capaz de acceder a documentos fuera de su propio contenedor de aplicaciones con facilidad:

 [![](document-picker-images/image32.png "Detección de documentos fuera de contenedor la aplicación en un")](document-picker-images/image32.png#lightbox)

Con el nuevo selector de documentos de iCloud ( `UIDocumentPickerViewController`), una aplicación de iOS directamente puede detectar y tener acceso fuera de su contenedor de aplicación. El `UIDocumentPickerViewController` proporciona un mecanismo para el usuario conceder acceso a y editarlos detectados documentos a través de los permisos.

Una aplicación debe participar en para que sus documentos se muestran en el selector de documentos de iCloud y estén disponibles para otras aplicaciones a detectarlos y trabajar con ellos. Para que una aplicación Xamarin iOS 8 comparten su contenedor de aplicación, editarla `Info.plist` en un editor de texto estándar y agréguele las dos líneas siguientes a la parte inferior del diccionario (entre el `<dict>...</dict>` etiquetas):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

El `UIDocumentPickerViewController` proporciona una excelente interfaz de usuario que permite al usuario elegir documentos. Para mostrar el controlador de vista del selector de documentos en una aplicación Xamarin iOS 8, realice lo siguiente:

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> El programador debe llamar a la `StartAccessingSecurityScopedResource` método de la `NSUrl` poder acceder a un documento externo. El `StopAccessingSecurityScopedResource` método debe llamarse para liberar el bloqueo de seguridad tan pronto como se ha cargado el documento.

### <a name="sample-output"></a>Resultados del ejemplo

Este es un ejemplo de cómo el código anterior mostrará un selector de documentos cuando se ejecuta en un dispositivo iPhone:

1.  El usuario inicia la aplicación y se muestra la interfaz principal:   
 
    [![](document-picker-images/image33.png "Se muestra la interfaz principal")](document-picker-images/image33.png#lightbox)
1.  El usuario pulsa el **acción** situado en la parte superior de la pantalla y se le pide que seleccione un **documento proveedor** en la lista de proveedores disponibles:   
 
    [![](document-picker-images/image34.png "Seleccione un proveedor de documentos en la lista de proveedores disponibles")](document-picker-images/image34.png#lightbox)
1.  El **controlador de vista de documento selector** aparece seleccionado **documento proveedor**:   
 
    [![](document-picker-images/image35.png "Se muestra el controlador de vista del selector de documentos")](document-picker-images/image35.png#lightbox)
1.  El usuario puntea un **carpeta de documentos** para mostrar su contenido:   
 
    [![](document-picker-images/image36.png "El contenido de la carpeta de documentos")](document-picker-images/image36.png#lightbox)
1.  El usuario selecciona un **documento** y **selector de documentos** está cerrado.
1.  Se vuelve a mostrar la interfaz principal, el **documento** se carga desde el contenedor externo y su contenido de muestra.


La presentación real del controlador de vista de documento selector depende de los proveedores de documento que el usuario ha instalado en el dispositivo y qué modo selector de documento ha sido implementar. El ejemplo anterior está usando el modo de apertura, los otros tipos de modo que se tratarán en detalle a continuación.

## <a name="managing-external-documents"></a>Administración de documentos externos

Como se dijo anteriormente, antes de iOS 8, una aplicación solo puede acceder a los documentos que formaban parte de su contenedor de aplicación. En iOS 8 una aplicación puede tener acceso a documentos de orígenes externos:

 [![](document-picker-images/image37.png "Información general sobre administración de documentos externos")](document-picker-images/image37.png#lightbox)

Cuando el usuario selecciona un documento desde un origen externo, se escribe un documento de referencia en el contenedor de aplicaciones que apunta al documento original.

Para ayudar a agregar esta nueva funcionalidad en las aplicaciones existentes, se han agregado varias características nuevas para el `NSMetadataQuery` API. Normalmente, una aplicación usa el ámbito de documento ubicuo para listar los documentos que se encuentran dentro de su contenedor de aplicación. Con este ámbito, solo los documentos dentro del contenedor de la aplicación seguirá mostrarse.

Utilizando el nuevo ámbito de documento externo ubicuo devolverá documentos que residen fuera del contenedor de aplicación y devuelve los metadatos para ellos. El `NSMetadataItemUrlKey` apuntará a la dirección URL donde se encuentra realmente el documento.

A veces, una aplicación no desea trabajar con los documentos que se apunta referencia th. En su lugar, la aplicación desea trabajar directamente con el documento de referencia. Por ejemplo, la aplicación puede mostrar el documento en la carpeta de la aplicación en la interfaz de usuario, o para permitir al usuario mover las referencias dentro de una carpeta.

En iOS 8, un nuevo `NSMetadataItemUrlInLocalContainerKey` se ha proporcionado para tener acceso al documento de referencia directamente. Esta clave apunta a la referencia real al documento externo en un contenedor de aplicación.

El `NSMetadataUbiquitousItemIsExternalDocumentKey` se usa para probar si un documento es externo al contenedor de la aplicación. El `NSMetadataUbiquitousItemContainerDisplayNameKey` se usa para tener acceso al nombre del contenedor que se aloja la copia original de un documento externo.

### <a name="why-document-references-are-required"></a>¿Por qué se requieren referencias a un documento

La razón principal que iOS 8 usa referencias para tener acceso a documentos externos es la seguridad. No hay ninguna aplicación se concede acceso al contenedor de la otra aplicación. Solo el selector de documentos puede hacer eso, porque se está ejecutando fuera de proceso y tiene acceso al sistema de ancho.

Es la única manera de llegar a un documento fuera del contenedor de aplicación mediante el selector de documento y si la dirección URL devuelta por el selector es el ámbito de seguridad. La dirección URL con ámbito de seguridad contiene suficiente información para que se selecciona el documento junto con los derechos de ámbito necesario para conceder acceso a una aplicación en el documento.

Es importante tener en cuenta que si la dirección URL con ámbito de seguridad se serializa en una cadena y, a continuación, deserializa, se perdería la información de seguridad y el archivo serían inaccesible desde la dirección URL. La característica de referencia de documento proporciona un mecanismo para volver a los archivos que apunta a estas direcciones URL.

Por tanto, si la aplicación adquiere un `NSUrl` desde uno de los documentos de referencia, ya tiene el ámbito de seguridad adjunto y se puede usar para tener acceso al archivo. Por este motivo, se sugiere alta que el programador utilice `UIDocument` porque controla toda esta información y procesos para ellos.

### <a name="using-bookmarks"></a>Utilizar marcadores

No siempre es factible para enumerar los documentos de la aplicación para volver a un documento específico, por ejemplo, al realizar la restauración del estado. iOS 8 proporciona un mecanismo para crear marcadores que se dirigen directamente un documento determinado.

El código siguiente creará un marcador de un `UIDocument`del `FileUrl` propiedad:

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

La API existente de marcador se utiliza para crear un marcador en una existente `NSUrl` que se puede guardar y cargar para proporcionar acceso directo a un archivo externo. El código siguiente restaurará a un marcador que se creó anteriormente:

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>Abra vs. Modo de importación y el selector de documentos

El controlador de vista del selector de documentos ofrece dos modos diferentes de operación:

1.  **Para abrir el modo** : en este modo, cuando el usuario selecciona y documento externo, el selector de documentos se creará un marcador de ámbito de seguridad en el contenedor de la aplicación.   
 
    [![](document-picker-images/image37.png "Marcador en el contenedor de aplicaciones con ámbito de seguridad")](document-picker-images/image37.png#lightbox)
1.  **Modo de importación** : en este modo, cuando el usuario selecciona y documento externo, el selector de documentos no crea un marcador, pero en su lugar, copiará el archivo en una ubicación temporal y proporcionan acceso a la aplicación al documento en esta ubicación:   
 
    [![](document-picker-images/image38.png "El selector de documentos se copie el archivo en una ubicación temporal y proporcionan acceso a la aplicación al documento en esta ubicación")](document-picker-images/image38.png#lightbox)   
 Una vez que la aplicación finaliza por algún motivo, se vacía la ubicación temporal y el archivo quitado. Si la aplicación necesita mantener el acceso al archivo, debe realizar una copia y lo coloca en su contenedor de aplicación.


El modo de apertura es útil cuando la aplicación desea colaborar con otra aplicación y compartir los cambios realizados en el documento con la aplicación. El modo de importación se usa cuando la aplicación no desea compartir sus modificaciones a un documento con otras aplicaciones.

## <a name="making-a-document-external"></a>Hacer que un documento externo

Como se mencionó anteriormente, una aplicación de iOS 8 no tiene acceso a los contenedores fuera de su propio contenedor de aplicación. La aplicación puede escribir su propio contenedor localmente o en una ubicación temporal y luego usar un modo especial de documento para mover el documento resultante fuera del contenedor de aplicación a un usuario elige la ubicación.

Para mover un documento a una ubicación externa, realice lo siguiente:

1.  En primer lugar, cree un nuevo documento en una ubicación local o temporal.
1.  Crear un `NSUrl` que señala al nuevo documento.
1.  Abra un nuevo controlador de vista del selector de documento y pasarle el `NSUrl` con el modo de `MoveToService` . 
1.  Una vez que el usuario elige una nueva ubicación, el documento se moverá desde su ubicación actual a la nueva ubicación.
1.  Un documento de referencia se escribirán en el contenedor de aplicaciones de la aplicación para que el archivo todavía puede obtenerse mediante la aplicación de creación.


El código siguiente puede usarse para mover un documento a una ubicación externa: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

El documento de referencia devuelto por el proceso anterior es exactamente el mismo que uno creado por el modo de apertura del selector de documentos. Sin embargo, hay veces que la aplicación que desee mover un documento sin necesidad de mantener una referencia a él.

Para mover un documento sin generar una referencia, use el `ExportToService` modo. Ejemplo: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Cuando se usa el `ExportToService` modo, se copia el documento en el contenedor externo y la copia existente se deja en su ubicación original.

## <a name="document-provider-extensions"></a>Extensiones de proveedor de documento

Con iOS 8, Apple desea que el usuario final para tener acceso a cualquiera de los documentos basados en la nube, con independencia de dónde se encuentren realmente. Para lograr este objetivo, iOS 8 proporciona un nuevo mecanismo de extensión de proveedor de documento.

### <a name="what-is-a-document-provider-extension"></a>¿Qué es una extensión de proveedor del documento?

En pocas palabras, una extensión de proveedor de documento es una manera para un desarrollador o un tercero, para proporcionar a un almacenamiento de documentos alternativa de aplicación que se puede acceder en la misma manera como ubicación de almacenamiento de iCloud existente.

El usuario puede seleccionar una de estas ubicaciones de almacenamiento alternativo en el selector de documento y pueden utilizar los modos de acceso mismo exacto (abrir, importar, mover o Export) para trabajar con archivos en esa ubicación.

Esto se implementa mediante dos extensiones diferentes:

-  **Extensión del selector de documentos** : proporciona un `UIViewController` subclase que proporciona una interfaz gráfica para el usuario elija un documento desde una ubicación de almacenamiento alternativa. Esta subclase se mostrará como parte del controlador de vista de selector de documentos.
-  **Proporcionar la extensión de archivo** : se trata de una extensión que no son de interfaz de usuario que se encarga de proporcionar realmente el contenido de archivos. Estas extensiones se proporcionan mediante la coordinación de archivo ( `NSFileCoordinator` ). Se trata de otro caso importante donde se requiere la coordinación de archivo.


El siguiente diagrama muestra el flujo de datos más habituales al trabajar con las extensiones de proveedor de documento:

 [![](document-picker-images/image39.png "Este diagrama muestra el flujo de datos más habituales cuando se trabaja con las extensiones de proveedor de documento")](document-picker-images/image39.png#lightbox)

Se produce el siguiente proceso:

1.  La aplicación presenta un controlador de selector de documentos para permitir al usuario seleccionar un archivo para que funcione con.
1.  El usuario selecciona una ubicación de archivo alternativo y personalizado `UIViewController` extensión se llama para mostrar la interfaz de usuario.
1.  El usuario selecciona un archivo desde esta ubicación y la dirección URL se pasa al selector de documentos.
1.  El selector de documentos se selecciona la dirección URL del archivo y lo devuelve a la aplicación para el usuario para que funcione en.
1.  La dirección URL se pasa al archivo de coordinador para devolver el contenido de los archivos a la aplicación.
1.  El archivo de coordinador llama a la extensión de proveedor de archivo personalizado para recuperar el archivo.
1.  Se devuelve el contenido del archivo al archivo de coordinador.
1.  El contenido del archivo se devuelve a la aplicación.


### <a name="security-and-bookmarks"></a>Seguridad y marcadores

En esta sección tardará un vistazo a cómo acceso los archivos persistentes y seguridad a través de works marcadores con extensiones de proveedor de documento. A diferencia de iCloud proveedor de documento, que guarda automáticamente los marcadores y seguridad en el contenedor de aplicaciones, las extensiones de proveedor de documento no porque no son una parte del sistema de referencia de documento.

Por ejemplo: en un entorno empresarial que proporciona su propio almacén de datos segura en toda la empresa, los administradores no deseen información corporativa confidencial acceder o procesó iCloud servidores público. Por lo tanto, no se puede utilizar el sistema de referencia de documento integradas.

Puede seguir usando el sistema de marcador y es responsabilidad de la extensión de proveedor de archivo para procesar correctamente una dirección URL del marcador y devolver el contenido del documento apuntado a él.

Por motivos de seguridad, iOS 8 tiene una capa de aislamiento que conserva la información sobre qué aplicaciones tiene acceso a qué identificador dentro de qué proveedor de archivos. Debe tenerse en cuenta que todo el acceso de archivo está controlado por este nivel de aislamiento.

El siguiente diagrama muestra el flujo de datos al trabajar con marcadores y una extensión de proveedor de documento:

 [![](document-picker-images/image40.png "Este diagrama muestra el flujo de datos al trabajar con marcadores y una extensión de proveedor de documento")](document-picker-images/image40.png#lightbox)

Se produce el siguiente proceso:

1.  La aplicación está a punto de entrar en segundo plano y se debe conservar su estado. Llama a `NSUrl` para crear un marcador a un archivo de almacenamiento alternativa.
1.  `NSUrl` llama a la extensión de proveedor de archivo para obtener una URL persistente al documento. 
1.  La extensión de archivo del proveedor, se devuelve la dirección URL como una cadena a la `NSUrl` .
1.  El `NSUrl` empaqueta la dirección URL en un marcador y lo devuelve a la aplicación.
1.  Cuando la aplicación se activa después de que se está en segundo plano y se debe restaurar el estado, pasa el marcador `NSUrl` .
1.  `NSUrl` llama a la extensión de archivo del proveedor con la dirección URL del archivo.
1.  El proveedor de extensión de archivo tiene acceso al archivo y devuelve la ubicación del archivo que se `NSUrl` .
1.  La ubicación del archivo se incluye con la información de seguridad y devuelve a la aplicación.


Desde aquí, la aplicación puede tener acceso al archivo y trabajar con él como normal.

### <a name="writing-files"></a>Escritura de archivos

En esta sección tendrá una visión rápida de cómo escribir archivos en una ubicación alternativa con una extensión de proveedor de documentos de works. La aplicación de iOS usará la coordinación de archivo para guardar información en el disco en el contenedor de la aplicación. Poco después de que se ha escrito correctamente el archivo, la extensión de proveedor de archivo se notificará el cambio.

En este momento, la extensión de archivo del proveedor puede comenzar a cargar el archivo en una ubicación alternativa (o marcar el archivo como carga de datos sucio y que requiere).

### <a name="creating-new-document-provider-extensions"></a>Creación de nuevas extensiones de proveedor de documento

Creación de nuevas extensiones de proveedor de documento está fuera del ámbito de este artículo introductorio. Esta información está aquí para mostrar, en función de las extensiones de que un usuario ha cargado en su dispositivo iOS, una aplicación puede tener acceso a ubicaciones de almacenamiento de documento fuera de la manzana que proporciona la ubicación de iCloud.

El desarrollador debe tener en cuenta este hecho al usar el selector de documentos y trabajar con documentos externos. No debe suponer esos documentos se hospedan en iCloud.

Para obtener más información sobre la creación de un proveedor de almacenamiento o la extensión de selector de documentos, consulte el [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md) documento.

## <a name="migrating-to-icloud-drive"></a>Migración a la unidad de iCloud

En iOS 8, los usuarios pueden elegir continuar usado el sistema de documentos con iCloud existente en iOS 7 (y los sistemas anteriores) o puede optar por migrar los documentos existentes en el nuevo mecanismo de unidad de iCloud.

En Mac OS X Yosemite, Apple no proporciona la hacia atrás compatibilidad por lo que se deben migrar todos los documentos en iCloud unidad o dejarán de actualizarse en todos los dispositivos.

Después de una cuenta de usuario se ha migrado a la unidad de iCloud, solo los dispositivos con iCloud unidad podrá propagar los cambios a los documentos a través de esos dispositivos.

> [!IMPORTANT]
> Los desarrolladores deben tener en cuenta que las nuevas características que se tratan en este artículo solo están disponibles si la cuenta de usuario se ha migrado a la unidad de iCloud. 

## <a name="summary"></a>Resumen

En este artículo ha cubierto los cambios en las API necesarias para admitir iCloud unidad y el nuevo controlador de vista de selector de documentos de iCloud existente. Ha cubierto la coordinación de archivo y por qué es importante al trabajar con documentos basados en la nube. Se trata la configuración necesaria para habilitar documentos basados en la nube en una aplicación de Xamarin.iOS y proporciona una introducción a trabajar con documentos fuera del contenedor de aplicación de una aplicación mediante el controlador de vista del selector de documentos.

Además, en este artículo trata brevemente las extensiones de proveedor de documento y por qué el desarrollador debe ser consciente de ellos al escribir aplicaciones que pueden controlar documentos basados en la nube.

## <a name="related-links"></a>Vínculos relacionados

- [DocPicker (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md)
