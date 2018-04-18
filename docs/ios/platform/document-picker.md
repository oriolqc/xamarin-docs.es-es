---
title: Selector de documento
description: El controlador de vista de documento selector concede a los usuarios acceso a archivos que están fuera del espacio aislado de la aplicación. Es un mecanismo sencillo para compartir documentos entre aplicaciones. También permite que los flujos de trabajo más complejos, porque los usuarios pueden editar un documento con varias aplicaciones. Este artículo proporciona una introducción a la utilice el selector de documento en una aplicación de Xamarin.iOS y los cambios en los documentos de iCloud necesarios para asistirla.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: d9b98611c7d269e590ce6fe2ce0270ef71dacf1e
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="document-picker"></a>Selector de documento

_El controlador de vista de documento selector concede a los usuarios acceso a archivos que están fuera del espacio aislado de la aplicación. Es un mecanismo sencillo para compartir documentos entre aplicaciones. También permite que los flujos de trabajo más complejos, porque los usuarios pueden editar un documento con varias aplicaciones. Este artículo proporciona una introducción a la utilice el selector de documento en una aplicación de Xamarin.iOS y los cambios en los documentos de iCloud necesarios para asistirla._

El selector de documento permite que los documentos que se comparten entre aplicaciones. Estos documentos pueden almacenarse en iCloud o en el directorio de una aplicación distinta. Los documentos se comparten a través del conjunto de [extensiones de proveedor de documento](~/ios/platform/extensions.md) el usuario ha instalado en su dispositivo. 

Debido a la dificultad de mantener sincronizados en toda la nube y aplicaciones de documentos, presentan una cierta cantidad de complejidad es necesario.

## <a name="requirements"></a>Requisitos

El siguiente es necesario para completar los pasos que aparecen en este artículo:

-  **Xcode 7 y iOS 8 o versiones más recientes** : Xcode 7 y iOS 8 o API más recientes de Apple deben instalarse y configurarse en el equipo del desarrollador.
-  **Visual Studio o Visual Studio para Mac** – debe instalarse la versión más reciente de Visual Studio para Mac.
-  **Dispositivo iOS** : un dispositivo iOS con iOS 8 o posterior.

## <a name="changes-to-icloud"></a>Cambios en iCloud

Para implementar las nuevas características del selector de documento, los siguientes cambios se realizaron en iCloud de Apple servicio:

-  El demonio de iCloud se ha reescrito completamente con CloudKit.
-  ICloud existente características han sido cambiado el nombre iCloud unidad.
-  Se ha agregado compatibilidad para el sistema operativo Microsoft Windows en iCloud.
-  Se ha agregado una carpeta de iCloud en el buscador de Mac OS.
-  dispositivos iOS pueden tener acceso al contenido de la carpeta de iCloud de Mac OS.

> [!IMPORTANT]
> Apple [proporciona herramientas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ayudar a los desarrolladores controlen correctamente normativa General de protección de datos (GDPR) de la Unión Europea.

## <a name="what-is-a-document"></a>¿Qué es un documento?

Cuando se hace referencia a un documento en iCloud, es una entidad única e independiente y se debería percibir como tal por el usuario. Un usuario que desee modificar el documento o compartirlo con otros usuarios (mediante correo electrónico, por ejemplo).

Hay varios tipos de archivos que el usuario inmediatamente reconocerán como documentos, como las páginas de archivos Keynote o números. Sin embargo, no se limita a este concepto en iCloud. Por ejemplo, el estado de un juego (por ejemplo, una coincidencia de ajedrez) se puede tratar como un documento y almacenan en iCloud. Este archivo podría pasarse entre dispositivos de un usuario y que puedan recoger un juego donde lo dejó en un dispositivo diferente.

## <a name="dealing-with-documents"></a>Trabajar con documentos

Antes de profundizar en el código necesario para usar el selector de documento con Xamarin, este artículo se va a cubre las prácticas recomendadas para trabajar con documentos de iCloud y algunas de las modificaciones realizadas en las API existentes deben para admitir el selector de documento.

### <a name="using-file-coordination"></a>Uso de coordinación de archivo

Dado que un archivo se puede modificar desde varias ubicaciones diferentes, debe utilizarse la coordinación para evitar la pérdida de datos.

 [![](document-picker-images/image1.png "Uso de coordinación de archivo")](document-picker-images/image1.png#lightbox)

¡Eche un vistazo a la ilustración anterior:

1.  Un dispositivo iOS mediante la coordinación de archivo crea un nuevo documento y lo guarda en la carpeta de iCloud.
2.  iCloud guarda el archivo modificado en la nube para su distribución a todos los dispositivos.
3.  Un Mac conectado ve el archivo modificado en la carpeta iCloud y coordinación de archivo utiliza para copiar los cambios en el archivo.
4.  Un dispositivo mediante el archivo coordinación no realiza un cambio en el archivo y lo guarda en la carpeta de iCloud. Estos cambios se replican al instante en el resto de dispositivos.

Suponga original dispositivo iOS o Mac estaba editando el archivo, ahora se pierde y sobrescribe con la versión del archivo desde el dispositivo coordinado sus cambios. Para evitar la pérdida de datos, archivo de coordinación es obligatorio cuando se trabaja con documentos basados en la nube.

### <a name="using-uidocument"></a>Usar UIDocument

 `UIDocument` simplifica las cosas (o `NSDocument` en macOS) haciendo todo el trabajo pesado para el programador. Proporciona integra en archivo coordinación con las colas de segundo plano para evitar bloquear interfaz de usuario de la aplicación.

 `UIDocument` expone varias API de alto nivel que facilitan el esfuerzo de desarrollo de una aplicación de Xamarin para cualquier propósito el desarrollador requiere.

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

La `GenericTextDocument` clase presentado anteriormente se usará en este artículo cuando se trabaja con el selector de documento y documentos externos en una aplicación de Xamarin.iOS 8.

## <a name="asynchronous-file-coordination"></a>Coordinación de archivos asincrónica

iOS 8 proporciona varias características nuevas de coordinación de archivos asincrónica a través de las nuevas API de coordinación de archivo. Antes de iOS 8, todas las API de coordinación archivo existente eran totalmente sincrónicas. Esto significaba que el desarrollador era responsable de implementar su propio fondo puesta en cola para evitar la coordinación de archivo desde el bloqueo de la interfaz de usuario de la aplicación.

El nuevo `NSFileAccessIntent` clase contiene una dirección URL que apunta al archivo y varias opciones para controlar el tipo de coordinación necesaria. El código siguiente muestra cómo mover un archivo de una ubicación a otra mediante intentos:

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

## <a name="discovering-and-listing-documents"></a>Detectar y enumerar los documentos

La manera de detectar y mostrar documentos es mediante el uso de las existentes `NSMetadataQuery` API. En esta sección se tratará nuevas características agregadas a `NSMetadataQuery` que facilitan el trabajo con documentos incluso más fácil que nunca.

### <a name="existing-behavior"></a>Comportamiento de existente

Antes de iOS 8, `NSMetadataQuery` resultó lenta a los cambios de archivo local recogida como: elimina, crea y cambia el nombre.

 [![](document-picker-images/image2.png "Información general de cambios de archivo local NSMetadataQuery")](document-picker-images/image2.png#lightbox)

En el diagrama anterior:

1.  Para los archivos que ya existen en el contenedor de la aplicación, `NSMetadataQuery` existentes `NSMetadata` registros creados previamente y puestos en cola para que estén disponibles de manera instantánea a la aplicación.
1.  La aplicación crea un nuevo archivo en el contenedor de la aplicación.
1.  Hay un retraso antes de `NSMetadataQuery` ve la modificación en el contenedor de la aplicación y crea los necesarios `NSMetadata` registro.


Debido al retraso en la creación de la `NSMetadata` registros, la aplicación tenía que abrir orígenes de dos datos: uno para los cambios de archivo local y otro para la nube según los cambios.

### <a name="stitching"></a>Unión

En iOS 8, `NSMetadataQuery` es más fácil de usar directamente con una nueva característica denominada composición:

 [![](document-picker-images/image3.png "NSMetadataQuery con una nueva característica denominada composición")](document-picker-images/image3.png#lightbox)

Uso de composición en el diagrama anterior:

1.  Al igual que antes, para los archivos que ya existen en el contenedor de la aplicación, `NSMetadataQuery` existentes `NSMetadata` registros creados previamente y cola de impresión.
1.  La aplicación crea un nuevo archivo en el contenedor de aplicación mediante la coordinación de archivo.
1.  Un enlace en el contenedor de la aplicación ve la modificación y las llamadas `NSMetadataQuery` para crear los necesarios `NSMetadata` registro.
1.  El `NSMetadata` se crea directamente después del archivo de registro y se pone a disposición de la aplicación.


Mediante el uso de la composición la aplicación ya no tiene que abrir un origen de datos para supervisar local y los cambios de archivo basado en la nube. Ahora puede depender de la aplicación `NSMetadataQuery` directamente.

> [!IMPORTANT]
> Unión sólo funciona si la aplicación está usando la coordinación de archivo tal como se presenta en la sección anterior. Si no se está usando el archivo coordinación, las API predeterminado al comportamiento anterior iOS 8 existente.




### <a name="new-ios-8-metadata-features"></a>Nuevas características de metadatos de iOS 8

Se agregaron las siguientes características nuevas a `NSMetadataQuery` en iOS 8:

-   `NSMetatadataQuery` Ahora se puede enumerar los documentos no locales almacenados en la nube.
-  Se agregaron nuevas API para tener acceso a información de metadatos en los documentos en la nube. 
-  Hay una nueva `NSUrl_PromisedItems` API que para tener acceso a los atributos de archivo de los archivos que pueden tengan o no su contenido disponible localmente.
-  Utilice la `GetPromisedItemResourceValue` método para obtener información sobre un determinado archivo o use el `GetPromisedItemResourceValues` método para obtener información sobre más de un archivo a la vez.


Se han agregado dos nuevos indicadores de coordinación de archivo para tratar los metadatos:

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Con las marcas de la anteriores, el contenido del archivo de documento no es necesario que estén disponibles localmente para que puedan utilizarse.

El segmento de código siguiente muestra cómo utilizar `NSMetadataQuery` para consultar la existencia de un archivo concreto y generar el archivo si no existe:

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

Apple considera que la mejor experiencia de usuario al enumerar los documentos de una aplicación consiste en utilizar las vistas previas. Esto proporciona el contexto de los usuarios finales, para que pueden identificar rápidamente el documento que desea trabajar.

Antes de iOS 8, que muestra las vistas previas del documento requiere una implementación personalizada. Familiarizado con iOS 8 son atributos de sistema de archivos que permiten a los programadores a trabajar rápidamente con las miniaturas de documentos.

#### <a name="retrieving-document-thumbnails"></a>Recuperar las miniaturas de documentos 

Mediante una llamada a la `GetPromisedItemResourceValue` o `GetPromisedItemResourceValues` métodos, `NSUrl_PromisedItems` API, un `NSUrlThumbnailDictionary`, se devuelve. La única clave actualmente en este diccionario es el `NSThumbnial1024X1024SizeKey` y su coincidencia `UIImage`.

#### <a name="saving-document-thumbnails"></a>Guardando las miniaturas de documentos

Es la manera más fácil para guardar una vista en miniatura mediante `UIDocument`. Mediante una llamada a la `GetFileAttributesToWrite` método de la `UIDocument` y establecer la vista en miniatura, se guardará automáticamente una vez el archivo del documento. El demonio de iCloud verá este cambio y propagarlo a iCloud. En Mac OS X, las miniaturas se generan automáticamente para que el programador mediante el complemento Quick Look.

Con los conceptos básicos sobre cómo trabajar con documentos de iCloud basándose en su lugar, junto con las modificaciones de la API existente, estamos preparados para implementar el controlador de vista de selector de documento en un Xamarin de iOS 8 aplicación móvil.


## <a name="enabling-icloud-in-xamarin"></a>Habilitar iCloud en Xamarin

Antes de que el selector de documento puede usarse en una aplicación de Xamarin.iOS, compatibilidad con iCloud debe habilitarse en la aplicación y a través de Apple. 

En el siguiente tutorial de pasos del proceso de aprovisionamiento de iCloud.

1. Crear un contenedor de iCloud.
2. Crear un identificador de aplicación que contiene el servicio de aplicaciones de iCloud.
3. Crear un perfil de aprovisionamiento que incluye este ID. App

El [trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) guía se describen los dos primeros pasos. Para crear un perfil de aprovisionamiento, siga los pasos descritos en la [perfil de aprovisionamiento](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile) guía.



En el siguiente tutorial de pasos del proceso de configuración de la aplicación para iCloud:

Haga lo siguiente:

1.  Abra el proyecto en Visual Studio para Mac o en Visual Studio.
2.  En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione opciones.
3.  En el cuadro de diálogo Opciones, seleccione **iOS aplicación**, asegúrese de que el **identificador de paquete** coincide con la que se definió en **Id. de aplicación** creado anteriormente para la aplicación. 
4.  Seleccione **agrupación de firma de iOS**, seleccione la **Developer identidad** y **perfil de aprovisionamiento de** creada anteriormente.
5.  Haga clic en el **Aceptar** botón para guardar los cambios y cerrar el cuadro de diálogo.
6.  Haga doble clic en `Entitlements.plist` en el **el Explorador de soluciones** para abrirlo en el editor.

    > [!IMPORTANT]
    > En Visual Studio debe abrir el editor de derechos con el botón secundario en él, seleccione **abrir con...** y seleccione el Editor de lista de propiedades

7.  Comprobar **habilitar iCloud** , **iCloud documentos** , **almacenamiento de información de clave y valor** y **CloudKit** .
8.  Asegúrese del **contenedor** existe para la aplicación (como creada anteriormente). Ejemplo: `iCloud.com.your-company.AppName`
9.  Guarde los cambios en el archivo.

Para obtener más información sobre los derechos que hacen referencia a la [trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) guía.

Con la instalación anterior en su lugar, ahora puede usar la aplicación documentos basados en la nube y el nuevo controlador de vista de selector de documento.

## <a name="common-setup-code"></a>Código de configuración comunes

Antes de comenzar con el controlador de vista de selector de documento, hay algún código de programa de instalación estándar necesario. Iniciar mediante la modificación de la aplicación `AppDelegate.cs` de archivos y darle un aspecto similar al siguiente:

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
                    // Yes, inform caller and save location the the Application Container
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
> El código anterior incluye el código de la sección lista de documentos y descubra anterior. Se presenta aquí en su totalidad, tal y como aparecería en una aplicación real. Para simplificar, este ejemplo funciona con un único archivo codificado de forma rígida (`test.txt`) únicamente.

El código anterior expone varios métodos abreviados de unidad de iCloud para hacerlas más fácil trabajar con el resto de la aplicación.

A continuación, agregue el código siguiente para cualquier contenedor de vista que se va a utilizar el selector de documento o trabajar con documentos en la nube o vista:

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

Esto agrega un acceso directo para ir a la `AppDelegate` y tener acceso a los métodos abreviados de iCloud creados anteriormente.

Con este código en su lugar, echemos un vistazo a implementar el controlador de vista de selector de documento en una aplicación de iOS 8 de Xamarin.

## <a name="using-the-document-picker-view-controller"></a>Utiliza el controlador de vista de selector de documento

Antes de iOS 8, era muy difícil de acceder a documentos desde otra aplicación, porque no había forma de detectar documentos fuera de la aplicación desde dentro de la aplicación.

### <a name="existing-behavior"></a>Comportamiento de existente

 [![](document-picker-images/image31.png "Información general de comportamiento existente")](document-picker-images/image31.png#lightbox)

¡Eche un vistazo a tener acceso a un documento externo antes de iOS 8:

1.  En primer lugar, el usuario tendría que abrir la aplicación que creó el documento.
1.  El documento está seleccionado y el `UIDocumentInteractionController` se usa para enviar el documento a la nueva aplicación.
1.  Por último, se coloca una copia del documento original en el contenedor de la nueva aplicación.


Desde allí está disponible para la segunda aplicación abrir y editar el documento.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Detectar documentos fuera contenedor de una aplicación

En iOS 8, una aplicación es capaz de acceder a documentos fuera de su propio contenedor de aplicación con facilidad:

 [![](document-picker-images/image32.png "Detectar documentos fuera contenedor de una aplicación")](document-picker-images/image32.png#lightbox)

Con el nuevo iCloud selector de documento ( `UIDocumentPickerViewController`), una aplicación de iOS directamente puede detectar y tener acceso fuera de su contenedor de la aplicación. El `UIDocumentPickerViewController` proporciona un mecanismo para que el usuario conceder acceso a y editarlos detectó documentos a través de permisos.

Una aplicación debe participar en para que sus documentos aparecen en el selector de documento de iCloud y estará disponible para otras aplicaciones detectar y trabajar con ellos. Para que una aplicación de iOS 8 Xamarin compartir su contenedor de la aplicación, editarlo `Info.plist` un archivo en un editor de texto estándar y agregue las dos líneas siguientes a la parte inferior del diccionario (entre el `<dict>...</dict>` etiquetas):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

El `UIDocumentPickerViewController` proporciona un excelente nueva interfaz de usuario que permite al usuario elegir documentos. Para mostrar el controlador de vista de selector de documento en una aplicación de iOS 8 Xamarin, realice lo siguiente:

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
> El programador debe llamar a la `StartAccessingSecurityScopedResource` método de la `NSUrl` para poder acceder a un documento externo. El `StopAccessingSecurityScopedResource` método debe llamarse para liberar el bloqueo de seguridad tan pronto como se ha cargado el documento.

### <a name="sample-output"></a>Resultados del ejemplo

Este es un ejemplo de cómo el código anterior podría mostrar un selector de documento cuando se ejecuta en un dispositivo iPhone:

1.  El usuario inicia la aplicación y se muestra la interfaz principal:   
 
    [![](document-picker-images/image33.png "Se muestra la interfaz principal")](document-picker-images/image33.png#lightbox)
1.  Las derivaciones de usuario la **acción** situado en la parte superior de la pantalla y se le pedirá que seleccione un **documento proveedor** en la lista de proveedores disponibles:   
 
    [![](document-picker-images/image34.png "Seleccione un proveedor de documento de la lista de proveedores disponibles")](document-picker-images/image34.png#lightbox)
1.  El **documento selector View Controller** se muestra para el elemento **documento proveedor**:   
 
    [![](document-picker-images/image35.png "Se muestra el controlador de vista de selector de documento")](document-picker-images/image35.png#lightbox)
1.  El usuario puntea en un **carpeta de documentos de** para mostrar su contenido:   
 
    [![](document-picker-images/image36.png "El contenido de la carpeta de documentos")](document-picker-images/image36.png#lightbox)
1.  El usuario selecciona un **documento** y **documento selector** está cerrado.
1.  Se vuelve a mostrar la interfaz principal, el **documento** se carga desde el contenedor externo y su contenido se muestra.


La presentación real del controlador de vista de documento selector depende de los proveedores de documento que el usuario ha instalado en el dispositivo y qué modo de selector de documento ha sido implemente. El ejemplo anterior está usando el modo de apertura, los otros tipos de modo se describen en detalle a continuación.

## <a name="managing-external-documents"></a>Administrar documentos externos

Tal y como se dijo anteriormente, antes de iOS 8, una aplicación sólo puede tener acceso a documentos que formaban parte de su contenedor de aplicación. En iOS 8 una aplicación puede tener acceso a documentos de orígenes externos:

 [![](document-picker-images/image37.png "Información general sobre administración de documentos externos")](document-picker-images/image37.png#lightbox)

Cuando el usuario selecciona un documento de un origen externo, se escribe un documento de referencia en el contenedor de aplicación que señala al documento original.

Para ayudar a agregar esta nueva capacidad en las aplicaciones existentes, varias características nuevas se agregaron a la `NSMetadataQuery` API. Normalmente, una aplicación usa el ámbito de documento ubicuo para listar los documentos que se encuentran dentro de su contenedor de la aplicación. Uso de este ámbito, únicamente los documentos dentro del contenedor de la aplicación continuará para mostrarse.

Usando el nuevo ámbito de documento externo ubicuo devolverá documentos que viven fuera del contenedor de la aplicación y devuelven los metadatos para ellos. El `NSMetadataItemUrlKey` apuntará a la dirección URL donde se encuentra realmente el documento.

A veces, una aplicación no desea trabajar con los documentos que se va a señalado por la referencia de th. En su lugar, la aplicación desea trabajar directamente con el documento de referencia. Por ejemplo, la aplicación puede mostrar el documento en la carpeta de la aplicación en la interfaz de usuario, o para permitir al usuario mover las referencias dentro de una carpeta.

En iOS 8, un nuevo `NSMetadataItemUrlInLocalContainerKey` se ha proporcionado para tener acceso al documento de referencia directamente. Esta clave apunta a la referencia real al documento externo en un contenedor de la aplicación.

El `NSMetadataUbiquitousItemIsExternalDocumentKey` se usa para comprobar si un documento es externo al contenedor de la aplicación. El `NSMetadataUbiquitousItemContainerDisplayNameKey` se utiliza para tener acceso al nombre del contenedor al que se aloja la copia original de un documento externo.

### <a name="why-document-references-are-required"></a>¿Por qué se requieren referencias de documento

La razón principal que iOS 8 usa referencias para tener acceso a documentos externos es para la seguridad. No hay ninguna aplicación se le concede acceso al contenedor de la otra aplicación. El selector de documento puede hacer eso, porque se está ejecutando fuera de proceso y tiene acceso al sistema de ancho.

Es la única forma de llegar a un documento fuera del contenedor de la aplicación mediante el selector de documento y, si la dirección URL devuelta por el selector es el ámbito de seguridad. La dirección URL con ámbito de seguridad contiene suficiente información para seleccionada del documento junto con los derechos de ámbito necesario para conceder acceso a una aplicación para el documento.

Es importante tener en cuenta que si la dirección URL con ámbito de seguridad se serializa en una cadena y, a continuación, deserializa, se perdería la información de seguridad y el archivo se debería inaccesible desde la dirección URL. La característica de referencia de documento proporciona un mecanismo para volver a los archivos que señala estas direcciones URL.

Por tanto, si la aplicación adquiere un `NSUrl` desde uno de los documentos de referencia, ya tiene el ámbito de seguridad adjunto y puede utilizarse para tener acceso al archivo. Por este motivo, se sugiere alta que el programador utilice `UIDocument` porque controla toda esta información y procesos para ellos.

### <a name="using-bookmarks"></a>Utilizar marcadores

No siempre es factible para enumerar los documentos de una aplicación para que reciba un documento específico, por ejemplo, al realizar la restauración del estado. iOS 8 proporciona un mecanismo para crear marcadores que se dirigen directamente un documento determinado.

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

La API existente de marcador se usa para crear un marcador en una existente `NSUrl` que puede guardar y cargar para proporcionar acceso directo a un archivo externo. El código siguiente restaurará un marcador que se ha creado anteriormente:

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

## <a name="open-vs-import-mode-and-the-document-picker"></a>Abra vs. Modo de importación y el selector de documento

El controlador de vista de documento selector ofrece dos modos diferentes de operación:

1.  **Abrir modo** : en este modo, cuando el usuario selecciona y documento externo, el selector de documento se creará un marcador de un ámbito de seguridad en el contenedor de la aplicación.   
 
    [![](document-picker-images/image37.png "Marcador en el contenedor de la aplicación de ámbito de seguridad")](document-picker-images/image37.png#lightbox)
1.  **Modo de importación** : en este modo, cuando el usuario selecciona y documento externo, el selector de documento no crear un marcador, pero en su lugar, copie el archivo en una ubicación temporal y proporcionar el acceso a la aplicación al documento en esta ubicación:   
 
    [![](document-picker-images/image38.png "El selector de documento se copie el archivo en una ubicación temporal y proporcionará acceso a la aplicación al documento en esta ubicación")](document-picker-images/image38.png#lightbox)   
 Una vez que termine la aplicación por algún motivo, la ubicación temporal se vacía y el archivo quitado. Si la aplicación necesita mantener el acceso al archivo, debe hacer una copia y lo coloca en su contenedor de la aplicación.


El modo de apertura es útil cuando la aplicación desea colaborar con otra aplicación y compartir los cambios realizados en el documento con esa aplicación. El modo de importación se utiliza cuando la aplicación no desea compartir sus modificaciones en un documento con otras aplicaciones.

## <a name="making-a-document-external"></a>Para marcar un documento externo

Como se mencionó anteriormente, una aplicación de iOS 8 no tiene acceso a los contenedores de fuera de su propio contenedor de la aplicación. La aplicación puede escribir en su propio contenedor localmente o en una ubicación temporal y luego usar un modo de documento especial para mover el documento resultante fuera del contenedor de la aplicación a un usuario elegido ubicación.

Para mover un documento a una ubicación externa, haga lo siguiente:

1.  En primer lugar, cree un nuevo documento en una ubicación local o temporal.
1.  Crear un `NSUrl` que apunte al nuevo documento.
1.  Abra un nuevo controlador de vista de selector de documento y pasarle el `NSUrl` con el modo de `MoveToService` . 
1.  Una vez que el usuario elige una nueva ubicación, se moverá el documento desde su ubicación actual a la nueva ubicación.
1.  Un documento de referencia se escribirán en contenedor de la aplicación de la aplicación para que el archivo todavía son accesibles mediante la aplicación de creación.


El siguiente código se puede utilizar para mover un documento a una ubicación externa: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

El documento de referencia devuelto por el proceso anterior es exactamente el mismo que uno creado por el modo de apertura del selector de documento. Sin embargo, hay veces que la aplicación puede que desee mover un documento sin mantener una referencia a él.

Para mover un documento sin generar una referencia, utilice el `ExportToService` modo. Ejemplo: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Cuando se usa el `ExportToService` modo, el documento se copia en el contenedor externo y se deja la copia existente en su ubicación original.

## <a name="document-provider-extensions"></a>Extensiones de proveedor de documento

Con iOS 8, Apple desea que el usuario final para poder acceder a ninguno de los documentos en la nube, con independencia de que realmente existen. Para lograr este objetivo, iOS 8 proporciona un mecanismo de extensión de proveedor de documento nuevo.

### <a name="what-is-a-document-provider-extension"></a>¿Qué es una extensión de proveedor de documento?

En pocas palabras, una extensión de proveedor de documento es un medio para un desarrollador o un tercero, para proporcionar un almacenamiento de documentos alternativo de aplicación que son accesibles en la misma manera como la ubicación de almacenamiento de iCloud existente.

El usuario puede seleccionar una de estas ubicaciones de almacenamiento alternativa en el selector de documento y pueden utilizar los modos de acceso mismo exacto (abrir, importar, mover o exportar) para trabajar con archivos en esa ubicación.

Esto se implementa mediante dos extensiones diferentes:

-  **Extensión de selector de documento** : proporciona un `UIViewController` subclase que proporciona una interfaz gráfica para el usuario elegir un documento desde una ubicación de almacenamiento alternativa. Esta subclase se mostrará como parte del controlador de vista de selector de documento.
-  **Proporcionar la extensión de archivo** : se trata de una extensión de ajeno a la interfaz de usuario que se encarga de proporcionar realmente el contenido de archivos. Estas extensiones se proporcionan mediante la coordinación de archivo ( `NSFileCoordinator` ). Este es otro caso importante donde se requiere la coordinación de archivo.


El siguiente diagrama muestra el flujo de datos típico cuando se trabaja con las extensiones de proveedor de documento:

 [![](document-picker-images/image39.png "Este diagrama muestra el flujo de datos habitual cuando se trabaja con las extensiones de proveedor de documento")](document-picker-images/image39.png#lightbox)

Se produce el siguiente proceso:

1.  La aplicación presenta un controlador de selector de documento para permitir al usuario seleccionar un archivo para que funcione con.
1.  El usuario selecciona una ubicación de archivo alternativo y personalizado `UIViewController` extensión se llama para mostrar la interfaz de usuario.
1.  El usuario selecciona un archivo en esta ubicación y la dirección URL se pasa hacia el selector de documento.
1.  El selector de documento selecciona la dirección URL del archivo y lo devuelve a la aplicación para el usuario para que funcione en.
1.  La dirección URL se pasa al Coordinador de archivo para devolver el contenido de archivos a la aplicación.
1.  El Coordinador de archivo llama a la extensión de proveedor de archivo personalizado para recuperar el archivo.
1.  El contenido del archivo se devuelve al Coordinador de archivo.
1.  El contenido del archivo se devuelve a la aplicación.


### <a name="security-and-bookmarks"></a>Seguridad y marcadores

En esta sección tendrá un rápido vistazo a cómo acceso los archivos persistentes y seguridad mediante works marcadores con extensiones de proveedor de documento. A diferencia de iCloud proveedor de documento, que guarda automáticamente los marcadores y seguridad en el contenedor de la aplicación, las extensiones de proveedor de documento no porque no son una parte del sistema de referencia de documento.

Por ejemplo: en un entorno empresarial que proporciona su propio almacén de datos segura de toda la empresa, los administradores no deseen información corporativa confidencial acceder a él o procesados por servidores iCloud pública. Por lo tanto, no se puede usar el sistema de referencia de documento integradas.

El sistema de marcador puede seguir usándose y es responsabilidad de la extensión de proveedor de archivo para procesar correctamente una dirección URL marcada y devolver el contenido del documento que señala se.

Por motivos de seguridad, iOS 8 tiene un nivel de aislamiento que se conserva la información acerca de qué aplicación tiene acceso a qué identificador dentro de qué proveedor de archivo. Debe tenerse en cuenta que todo el acceso de archivo está controlado por este nivel de aislamiento.

El siguiente diagrama muestra el flujo de datos cuando se trabaja con marcadores y una extensión de proveedor de documento:

 [![](document-picker-images/image40.png "Este diagrama muestra el flujo de datos cuando se trabaja con marcadores y una extensión de proveedor de documento")](document-picker-images/image40.png#lightbox)

Se produce el siguiente proceso:

1.  La aplicación está a punto de entrar en el fondo y necesita conserva su estado. Llama a `NSUrl` para crear un marcador en un archivo de almacenamiento alternativa.
1.  `NSUrl` llama a la extensión de archivo del proveedor para obtener una dirección URL persistente en el documento. 
1.  La extensión del proveedor de archivo devuelve la dirección URL como una cadena a la `NSUrl` .
1.  El `NSUrl` empaqueta la dirección URL en un marcador de posición y lo devuelve a la aplicación.
1.  Cuando la aplicación se activa después de que se está en segundo plano y se necesita restaurar el estado, pasa el marcador `NSUrl` .
1.  `NSUrl` llama a la extensión de archivo del proveedor con la dirección URL del archivo.
1.  El proveedor de extensiones de archivo tiene acceso al archivo y devuelve la ubicación del archivo que se `NSUrl` .
1.  La ubicación del archivo es empaquetar junto con información de seguridad y se devuelven a la aplicación.


Desde aquí, la aplicación puede tener acceso al archivo y trabajar con ellos como normal.

### <a name="writing-files"></a>Escribir en archivos

En esta sección se echar un vistazo rápido de cómo escribir archivos en una ubicación alternativa con un extensión de proveedor de documento funciona. La aplicación de iOS usará la coordinación de archivo para guardar información en el disco en el contenedor de la aplicación. Poco después de que se ha escrito correctamente el archivo, se notificará la extensión de archivo del proveedor del cambio.

En este momento, la extensión de archivo del proveedor puede iniciar cargar el archivo en la ubicación alternativa (o marcar el archivo como carga desfasada y que necesite).

### <a name="creating-new-document-provider-extensions"></a>Crear nuevas extensiones de proveedor de documento

Crear nuevas extensiones de proveedor de documento está fuera del ámbito de este artículo introductorio. Esta información está aquí para mostrar, en función de las extensiones de que un usuario ha cargado en su dispositivo iOS, una aplicación puede tener acceso a ubicaciones de almacenamiento de documento fuera de Apple proporciona la ubicación de iCloud.

El desarrollador debe tener en cuenta este hecho cuando se utiliza el selector de documento y trabajar con documentos externos. No debe suponer se hospedan esos documentos en iCloud.

Para obtener más información acerca de cómo crear un proveedor de almacenamiento o una extensión de selector de documento, consulte el [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md) documento.

## <a name="migrating-to-icloud-drive"></a>Migrar a la unidad de iCloud

En iOS 8, los usuarios pueden elegir continuar con la existente iCloud sistema de documentos que se utilizan en iOS 7 (y con sistemas anteriores) o pueden optar por migrar documentos existentes en el nuevo mecanismo de unidad de iCloud.

En Mac OS X Yosemite, Apple no proporciona el hacia atrás compatibilidad por lo que se deben migrar todos los documentos en iCloud unidad o ya no se actualiza en todos los dispositivos.

Después de una cuenta de usuario se ha migrado a iCloud unidad, solo los dispositivos con iCloud unidad podrá propagar los cambios a los documentos a través de los dispositivos.

> [!IMPORTANT]
> Los desarrolladores deben tener en cuenta que las nuevas características que se describen en este artículo solo están disponibles si la cuenta de usuario se ha migrado a la unidad de iCloud. 

## <a name="summary"></a>Resumen

En este artículo se trata los cambios en las API necesarias para admitir iCloud unidad y el nuevo controlador de vista de documento selector de iCloud existente. Ha cubierto coordinación de archivo y por qué es importante al trabajar con documentos en la nube. Tiene cubierto la configuración necesaria para habilitar documentos basados en la nube en una aplicación de Xamarin.iOS y proporciona una introducción a trabajar con documentos fuera del contenedor de aplicación de una aplicación mediante el controlador de vista de selector de documento.

Además, este artículo trata brevemente extensiones de proveedor de documento y ¿por qué el desarrollador debe ser consciente de ellos al escribir aplicaciones que pueden controlar documentos basados en la nube.

## <a name="related-links"></a>Vínculos relacionados

- [DocPicker (ejemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introducción a las extensiones de aplicación](~/ios/platform/extensions.md)
