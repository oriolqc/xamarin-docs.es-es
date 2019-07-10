---
title: Menús de Xamarin.Mac
description: En este artículo se explica cómo trabajar con menús en una aplicación de Xamarin.Mac. Describe la creación y mantenimiento de los menús y elementos de menú en Xcode e Interface Builder y trabajar con ellos mediante programación.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 1f2b0c9ee46634ad7395fadfb770dd2da796ada2
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675159"
---
# <a name="menus-in-xamarinmac"></a>Menús de Xamarin.Mac

_En este artículo se explica cómo trabajar con menús en una aplicación de Xamarin.Mac. Describe la creación y mantenimiento de los menús y elementos de menú en Xcode e Interface Builder y trabajar con ellos mediante programación._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, tener acceso a los mismos menús de Cocoa que realiza un desarrollador que trabaja en Objective-C y Xcode. Ya que Xamarin.Mac se integra directamente con Xcode, puede usar Interface Builder de Xcode para crear y mantener sus barras de menús, menús y elementos de menú (o bien, opcionalmente, crearlos directamente en código de C#).

Los menús son una parte integral de la experiencia del usuario de la aplicación de Mac y aparecen normalmente en distintas partes de la interfaz de usuario:

- **Barra de menús de la aplicación** -éste es el menú principal que aparece en la parte superior de la pantalla para cada aplicación de Mac.
- **Menús contextuales** -aparecen cuando el usuario hace clic con botón o un elemento en una ventana de clics de control.
- **La barra de estado** -esta es el área en el extremo derecho de la barra de menús de la aplicación que aparece en la parte superior de la pantalla (a la izquierda del reloj de barra de menú) y crece a la izquierda, como los elementos se agregan a él.
- **Acoplar menú** -menú para cada aplicación en el dock que aparece cuando el usuario hace clic con botón o icono de la aplicación de clics de control, o cuando el usuario hace clic en el icono y contiene el botón del mouse hacia abajo.
- **Botón emergente y listas desplegables** -un botón emergente muestra un elemento seleccionado y presenta una lista de opciones para seleccionar desde cuando el usuario hace clic. Una lista desplegable es un tipo de botón emergente que normalmente se usa para seleccionar comandos específicos del contexto de la tarea actual. Ambos pueden aparecer en cualquier lugar en una ventana.

[![Un menú de ejemplo](menu-images/intro01.png "un menú de ejemplo")](menu-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos de trabajar con elementos de menú en una aplicación de Xamarin.Mac, menús y barras de menús de Cocoa. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` atributos se usa para conexión de seguridad de las clases de C# para objetos de Objective-C y elementos de interfaz de usuario.

## <a name="the-applications-menu-bar"></a>Barra de menús de la aplicación 

A diferencia de las aplicaciones que se ejecutan en el sistema operativo de Windows donde cada ventana puede tener su propia barra de menús conectada a ella, cada aplicación se ejecuta en macOS tiene una barra de menú único que se ejecuta en la parte superior de la pantalla que se usa para todas las ventanas de la aplicación:

[![Una barra de menús](menu-images/appmenu01.png "una barra de menús")](menu-images/appmenu01-large.png#lightbox)

Los elementos de esta barra de menús se activan o desactivan según el contexto actual o el estado de la aplicación y su interfaz de usuario en un momento dado. Por ejemplo: si el usuario selecciona un campo de texto, los elementos de la **editar** menú estarán habilitado como **copia** y **cortar**.

Con arreglo a Apple y de forma predeterminada, todas las aplicaciones macOS tienen un conjunto estándar de los menús y elementos de menú que aparecen en la barra de menús de la aplicación:

- **Menú Apple** : este menú proporciona acceso al sistema amplios elementos que están disponibles para el usuario en todo momento, independientemente de qué aplicación se está ejecutando. No se puede modificar estos elementos por el desarrollador.
- **Menú de la aplicación** : este menú muestra el nombre de la aplicación en negrita y ayuda al usuario a identificar qué aplicación se está ejecutando actualmente. Contiene elementos que se aplican a la aplicación como un todo y no un documento determinado o proceso como salir de la aplicación.
- **Menú archivo** : los elementos que se usan para crear, abrir o guardar documentos que la aplicación funciona con. Si la aplicación no está basado en el documento, este menú puede cambiar el nombre o quitarse.
- **Menú Editar** -contiene comandos como **cortar**, **copia**, y **pegar** que se usan para editar o modificar los elementos de interfaz de usuario de la aplicación.
- **Menú formato** : si la aplicación funciona con texto, este menú contiene comandos para ajustar el formato de texto.
- **Menú Ver** -contiene comandos que afectan a cómo se muestra el contenido (ver) en la interfaz de usuario de la aplicación.
- **Los menús específicos de la aplicación** : estos son los menús que son específicos de la aplicación (por ejemplo, un menú de marcadores para un explorador web). Deben aparecer entre el **vista** y **ventana** menús de la barra.
- **Menú ventana** -contiene comandos para trabajar con windows en la aplicación, así como una lista de las ventanas abiertas actuales.
- **Menú Ayuda** -si la aplicación proporciona ayuda en pantalla, el menú Ayuda debe ser el menú más a la derecha de la barra. 

Para obtener más información acerca de la barra de menús de la aplicación y los menús estándar y los elementos de menú, vea Apple [directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>La barra de menús de la aplicación de forma predeterminada

Siempre que cree un nuevo proyecto de Xamarin.Mac, se obtiene automáticamente una barra de menús de aplicación predeterminada estándar, que tiene los elementos típicos que tuviera una aplicación de macOS (como se ha descrito en la sección anterior). Barra de menús de la aplicación de forma predeterminada se define en el **Main.storyboard** archivo (junto con el resto de la interfaz de usuario de la aplicación) en el proyecto en el **panel de solución**:  

![Seleccione el guión gráfico principal](menu-images/appmenu02.png "seleccione el guión gráfico principal")

Haga doble clic en el **Main.storyboard** archivo para abrirlo para su edición en Interface Builder de Xcode y se mostrará la interfaz de editor de menús:

[![Edición de la interfaz de usuario en Xcode](menu-images/defaultbar01.png "edición de la interfaz de usuario en Xcode")](menu-images/defaultbar01-large.png#lightbox)

Desde aquí, podemos hacer clic en elementos como el **abierto** elemento de menú en el **archivo** menú y editar o ajustar sus propiedades en el **Inspector de atributos**:

[![Edición de los atributos de un menú](menu-images/defaultbar02.png "los atributos de un menú de edición")](menu-images/defaultbar02-large.png#lightbox)

Entraremos al agregar, modificar y eliminar menús y elementos más adelante en este artículo. Por ahora sólo queremos ver qué menús y elementos de menú están disponibles de forma predeterminada y cómo han sido automáticamente expuesto al código mediante un conjunto predefinidas salidas y acciones (para obtener más información, consulte nuestra [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) documentación).

Por ejemplo, si hacemos clic en el **conexión Inspector** para el **abierto** podemos ver está dispuesto automáticamente hasta el elemento de menú el `openDocument:` acción: 

[![Visualización de la acción adjunta](menu-images/defaultbar03.png "ver la acción adjunta")](menu-images/defaultbar03-large.png#lightbox)

Si selecciona el **Respondedor primera** en el **jerarquía de la interfaz** y desplácese hacia abajo en la **conexión Inspector**, y verá la definición de la `openDocument:` acción que el **abierto** elemento de menú se asocia al (junto con varias otras acciones predeterminadas para la aplicación y no se conectan automáticamente hasta controles):

[![Ver acciones asociadas](menu-images/defaultbar04.png "ver todas las acciones adjuntadas")](menu-images/defaultbar04-large.png#lightbox) 

¿Por qué es importante? En la siguiente sección verá cómo funcionan estas acciones definen automáticamente con otros elementos de interfaz de usuario de cacao automáticamente habilitar y deshabilitar elementos de menú, así como, proporcionar una funcionalidad integrada para los elementos.

Más adelante vamos a usar estas acciones integradas para habilitar y deshabilitar los elementos de código y proporcionar nuestra propio funcionalidad cuando se seleccionan.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Funcionalidad del menú integrado

Si fuera la ejecución de una aplicación de Xamarin.Mac recién creada antes de agregar cualquier código o elementos de interfaz de usuario, observará que algunos elementos están automáticamente conectada y habilitados (con totalmente integrada la funcionalidad de forma automática), como el **Quit** de elemento en el **aplicación** menú:

![Un elemento de menú habilitado](menu-images/appmenu03.png "un elemento de menú habilitada")

Aunque las otros elementos de menú, como **cortar**, **copia**, y **pegar** no son:

![Elementos de menú deshabilitados](menu-images/appmenu04.png "elementos de menú deshabilitados")

Vamos a detener la aplicación y haga doble clic en el **Main.storyboard** de archivos en el **panel de solución** para abrirlo y editarlo en Xcode de Interface Builder. A continuación, arrastre un **vista de texto** desde el **biblioteca** en el controlador de vista de la ventana en la **Editor de la interfaz**:

[![Seleccionar una vista de texto de la biblioteca](menu-images/appmenu05.png "seleccionando una vista de texto de la biblioteca")](menu-images/appmenu05-large.png#lightbox)

En el **Editor de restricciones** vamos a anclar la vista de texto con bordes de la ventana y establézcalo donde aumenta y disminuye con la ventana haciendo clic en todos los cuatro rojo vigas en la parte superior del editor y haga clic en el **agregar 4 restricciones** botón:

[![Las restricciones de edición](menu-images/appmenu06.png "las restricciones de edición")](menu-images/appmenu06-large.png#lightbox)

Guarde los cambios en el diseño de la interfaz de usuario y volver a Visual Studio para Mac sincronizar los cambios con el proyecto de Xamarin.Mac. Ahora inicie la aplicación, escriba algún texto en la vista de texto, selecciónelo y abra el **editar** menú:

![Los elementos de menú se automáticamente habilitado o deshabilitado](menu-images/appmenu07.png "los elementos de menú se automáticamente habilitado o deshabilitado")

Observe cómo el **cortar**, **copia**, y **pegar** elementos se habilitan automáticamente y es totalmente funcional, todo ello sin escribir una sola línea de código. 

¿Qué está ocurriendo? Recuerde integrado predefinir las acciones que vienen con cable hasta los elementos de menú predeterminada (como se muestra anteriormente), la mayoría de los elementos de interfaz de usuario de Cocoa que forman parte de Mac OS se ha compilado en enlaces a acciones específicas (como `copy:`). Por lo que cuando se agregan a una ventana, activa y selecciona el elemento de menú correspondiente o artículos vinculados a esa acción se habilitan automáticamente. Si el usuario selecciona ese elemento de menú, se llama la funcionalidad integrada en el elemento de interfaz de usuario y se ejecuta, todo ello sin intervención del programador.

### <a name="enabling-and-disabling-menus-and-items"></a>Habilitar y deshabilitar los menús y elementos

De forma predeterminada, cada vez que se produce un evento de usuario, `NSMenu` automáticamente habilita y deshabilita cada visible menús y elementos en función del contexto de la aplicación. Hay tres maneras de habilitar o deshabilitar un elemento:

- **Habilitación automática de menús** -un elemento de menú está habilitado si `NSMenu` puede encontrar un objeto adecuado que responde a la acción que el elemento es con cable de seguridad en. Por ejemplo, la vista de texto anterior que tenía un enlace integrado para el `copy:` acción.
- **Acciones personalizadas y validateMenuItem:** : para cualquier elemento de menú que se enlaza a un [ventana o vista de acción personalizada de controlador](#Working-with-Custom-Window-Actions), puede agregar el `validateMenuItem:` acción y habilitar o deshabilitar manualmente los elementos de menú.
- **Habilitar menú manual** -establecer manualmente la `Enabled` propiedad de cada uno `NSMenuItem` para habilitar o deshabilitar individualmente cada elemento en un menú.

Para elegir un sistema, establezca el `AutoEnablesItems` propiedad de un `NSMenu`. `true` es automático (comportamiento predeterminado) y `false` es manual. 

> [!IMPORTANT]
> Si decide usar Habilitar menú manual, del menú de los elementos ninguno, incluso aquellos que se controla mediante las clases de AppKit como `NSTextView`, se actualizan automáticamente. Será responsable de la habilitación y deshabilitación de todos los elementos de forma manual en el código.

#### <a name="using-validatemenuitem"></a>Uso de validateMenuItem

Como se indicó anteriormente, para cualquier elemento de menú que se enlaza a un [ventana o la acción personalizada de controlador de vista](#Working-with-Custom-Window-Actions), puede agregar el `validateMenuItem:` acción y habilitar o deshabilitar manualmente los elementos de menú.

En el ejemplo siguiente, la `Tag` propiedad se usará para decidir el tipo de elemento de menú que se habilitarse o deshabilitarse por el `validateMenuItem:` acción según el estado del texto seleccionado en un `NSTextView`. El `Tag` propiedad se estableció en Interface Builder para cada elemento de menú:

![Establecer la propiedad etiqueta](menu-images/validate01.png "estableciendo la propiedad de etiqueta")

Y el código siguiente agregado al controlador de vista:

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

Cuando se ejecuta este código, y no hay texto seleccionado en el `NSTextView`, se deshabilitan los elementos de menú de encapsulado de dos (aunque se conectan a las acciones en el controlador de vista):

![Los elementos que se muestra deshabilitado](menu-images/validate02.png "mostrando elementos deshabilitados")

Si se selecciona una sección de texto y vuelve a abrir el menú, los elementos de menú dos encapsulado estará disponibles:

![Los elementos que muestra habilitado](menu-images/validate03.png "mostrando elementos habilitados")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Habilitar y responder a los elementos de menú en el código

Como hemos visto anteriormente, simplemente agregando elementos de la interfaz de usuario de cacao específicos a nuestro diseño de interfaz de usuario (por ejemplo, un campo de texto), algunos de los elementos de menú predeterminada se habilitará y funcionan de forma automática, sin tener que escribir ningún código. Siguiente Echemos un vistazo a agregar nuestro propio código de C# a nuestro proyecto de Xamarin.Mac para habilitar un elemento de menú y proporcionar una funcionalidad cuando el usuario lo selecciona.

Por ejemplo, digamos que queremos que el usuario para que pueda usar el **abierto** de elemento en el **archivo** menú para seleccionar una carpeta. Puesto que deseamos que ésta sea una función de toda la aplicación y no está limitada a una ventana de dar o elemento de interfaz de usuario, vamos a agregar el código para controlar todo esto a nuestro delegado de la aplicación.

En el **panel de solución**, haga doble clic en el `AppDelegate.CS` archivo para abrirlo y editarlo:

![Seleccionar el delegado de la aplicación](menu-images/appmenu08.png "seleccionando el delegado de la aplicación")

Agregue el código siguiente al método `DidFinishLaunching`:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

Vamos a ejecutar la aplicación ahora y abra el **archivo** menú: 

![El menú archivo](menu-images/appmenu09.png "el menú archivo")

Tenga en cuenta que el **abierto** ahora está habilitado el elemento de menú. Si se selecciona, se mostrará el cuadro de diálogo Abrir:

![Un cuadro de diálogo Abrir](menu-images/appmenu10.png "un cuadro de diálogo Abrir")

Si hacemos clic en el **abierto** botón, se mostrará el mensaje de alerta:

![Un mensaje del cuadro de diálogo de ejemplo](menu-images/appmenu11.png "un mensaje del cuadro de diálogo de ejemplo")

Era de la línea de clave `[Export ("openDocument:")]`, dice `NSMenu` que nuestro **AppDelegate** tiene un método `void OpenDialog (NSObject sender)` que responde a la `openDocument:` acción. Si se recordará anterior, el **abierto** elemento de menú es automáticamente por cable de seguridad para esta acción de forma predeterminada en el generador de interfaz:

[![Visualización de las acciones adjuntadas](menu-images/defaultbar03.png "ver las acciones adjuntadas")](menu-images/defaultbar03-large.png#lightbox)

Siguiente Echemos un vistazo a crear nuestro propio menú, elementos de menú y las acciones y responder a ellas en el código.

### <a name="working-with-the-open-recent-menu"></a>Trabajar con el menú Abrir recientes

De forma predeterminada, el **archivo** menú contiene una **Abrir recientes** elemento que realiza un seguimiento de los últimos archivos varios que el usuario ha abierto con su aplicación. Si está creando un `NSDocument` en función de aplicación de Xamarin.Mac, este menú se controlarán por usted automáticamente. Para cualquier otro tipo de aplicación de Xamarin.Mac, será responsable de administrar y responder manualmente a este elemento de menú.

Para controlar manualmente el **Abrir recientes** menú, primero deberá informar a lo que un nuevo archivo ha abierto o guardado con los siguientes:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Aunque la aplicación no usa `NSDocuments`, seguir usando el `NSDocumentController` para mantener la **Abrir recientes** menú mediante el envío de un `NSUrl` con la ubicación del archivo que se la `NoteNewRecentDocumentURL` método de la `SharedDocumentController`.

A continuación, debe invalidar el `OpenFile` método del delegado de aplicación para abrir cualquier archivo que el usuario selecciona en el **Abrir recientes** menú. Por ejemplo:

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

Devolver `true` si se puede abrir el archivo, lo contrario, devuelven `false` y se mostrará una advertencia integrada para el usuario que no se pudo abrir el archivo.

Porque el nombre de archivo y ruta de acceso devuelven por la **Abrir recientes** menú, es posible que incluya un espacio, necesitamos convertir este carácter antes de crear un `NSUrl` o se producirá un error. Para hacerlo con el código siguiente:

```csharp
filename = filename.Replace (" ", "%20");
```

Por último, crearemos un `NSUrl` que apunta al archivo y use un método auxiliar en la aplicación delegar para abrir una nueva ventana y cargar el archivo:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Para reunir todos los elementos, echemos un vistazo a una implementación de ejemplo en un **AppDelegate.cs** archivo:

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = true;
            dlg.CanChooseDirectories = false;

            if (dlg.RunModal () == 1) {
                // Nab the first file
                var url = dlg.Urls [0];

                if (url != null) {
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

Según los requisitos de la aplicación, es posible que no desea que el usuario para abrir el mismo archivo en más de una ventana al mismo tiempo. En nuestra aplicación de ejemplo, si el usuario elige un archivo que ya está abierto (ya sea desde el **Abrir recientes** o **abrir...** elementos de menú), la ventana que contiene el archivo se pone al frente.

Para lograr esto, hemos usado el código siguiente en el método auxiliar:

```csharp
var path = url.Path;

// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

Hemos diseñado nuestros `ViewController` clase para contener la ruta de acceso al archivo en su `Path` propiedad. A continuación, realizamos un bucle a través de todas las ventanas abiertas actualmente en la aplicación. Si el archivo ya está abierto en una de las ventanas, que se ponga a la parte delantera de todas las demás ventanas mediante:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Si se encuentra ninguna coincidencia, se abre una ventana nueva con el archivo cargado y el archivo se indica en la **Abrir recientes** menú:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions" />

### <a name="working-with-custom-window-actions"></a>Trabajar con acciones de ventana personalizado

Al igual que la integrada **Respondedor primera** acciones que vienen previamente con cable a elementos de menú estándar, puede crear nuevas acciones personalizadas y conectar a ellos a elementos de menú en el generador de interfaz.

En primer lugar, defina una acción personalizada en uno de los controladores de ventana de la aplicación. Por ejemplo:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

A continuación, haga doble clic en el archivo de guión gráfico de la aplicación en el **panel de solución** para abrirlo y editarlo en Xcode de Interface Builder. Seleccione el **primer servicio de respuesta** en el **escena de la aplicación**, a continuación, cambie a la **Inspector de atributos**:

![El Inspector de atributos](menu-images/action01.png "el Inspector de atributos")

Haga clic en el **+** situado en la parte inferior de la **Inspector de atributos** para agregar una nueva acción personalizada:

![Agregar una nueva acción](menu-images/action02.png "agregado una nueva acción")

Asígnele el mismo nombre que la acción personalizada que creó en el controlador de ventana:

![Editar el nombre de acción](menu-images/action03.png "editar el nombre de acción")

Control y haga clic y arrastre desde un elemento de menú a la **Respondedor primera** bajo el **escena de la aplicación**. En la lista emergente, seleccione la nueva acción que acaba de crear (`defineKeyword:` en este ejemplo):

![Asociar una acción](menu-images/action04.png "asociar una acción")

Guarde los cambios en el guión gráfico y vuelva a Visual Studio para Mac sincronizar los cambios. Si ejecuta la aplicación, el elemento de menú que se conectó a la acción personalizada se automáticamente habilitarse o deshabilitarse (en función de la ventana con la acción que esté abierta) y seleccione el elemento de menú se activará la acción:

[![Para probar la nueva acción](menu-images/action05.png "para probar la nueva acción")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Agregar, editar y eliminar menús

Como hemos visto en las secciones anteriores, una aplicación de Xamarin.Mac presenta una serie de menús predeterminados y los elementos de menú que los controles de interfaz de usuario específicos automáticamente activará y responder a preestablecido. También hemos visto cómo agregar código a la aplicación que también habilitará y responder a estos elementos de forma predeterminada.

En esta sección veremos quitar elementos de menú que no es necesario, la reorganización de los menús y agregar nuevos menús, elementos de menú y acciones.

Haga doble clic en el **Main.storyboard** de archivos en el **panel de solución** para abrirlo y editarlo:

[![Edición de la interfaz de usuario en Xcode](menu-images/maint01.png "edición de la interfaz de usuario en Xcode")](menu-images/maint01-large.png#lightbox)

Para nuestra aplicación de Xamarin.Mac específica no vamos a usar el valor predeterminado **vista** menú por lo que vamos a quitarlo. En el **jerarquía de la interfaz** seleccione la **vista** elemento de menú que forma parte de la barra de menús principal:

![Seleccionar el elemento de menú Vista](menu-images/maint02.png "seleccionando el elemento de menú Vista")

Presione la tecla Suprimir o RETROCESO para eliminar el menú. A continuación, no vamos a usar todos los elementos en el **formato** menú y desea mover los elementos que vamos a usar horizontal desde los submenús. En el **jerarquía de la interfaz** seleccione los elementos de menú siguientes:

![Resaltado de varios elementos](menu-images/maint03.png "resaltado varios elementos")

Arrastre los elementos bajo el elemento primario **menú** en el submenú donde actualmente son:

[![Arrastre los elementos de menú al menú primario](menu-images/maint04.png "arrastrando los elementos de menú al menú primario")](menu-images/maint04-large.png#lightbox)

El menú debe ser ahora similar:

[![Los elementos de la nueva ubicación](menu-images/maint05.png "los elementos de la nueva ubicación")](menu-images/maint05-large.png#lightbox)

Siguiente vamos a arrastrar el **texto** submenú out en el **formato** menú y colóquelo en la barra de menús principal entre el **formato** y **ventana** menús:

[![El menú de texto](menu-images/maint06.png "menú el texto")](menu-images/maint06-large.png#lightbox)

Volvamos a atrás en el **formato** menú y eliminar el **fuente** elemento de submenú. A continuación, seleccione el **formato** menú y cámbiele el nombre "Fuentes":

[![El menú de fuentes](menu-images/maint07.png "menú de la fuente")](menu-images/maint07-large.png#lightbox)

A continuación, vamos a crear un menú personalizado de frases predefinido que obtener agregará automáticamente al texto en la vista de texto cuando se seleccionan. En el cuadro de búsqueda en la parte inferior de la **Inspector de biblioteca** tipo en "menu". Así resultará más fácil de buscar y trabajar con todos los elementos de interfaz de usuario del menú:

![El Inspector de biblioteca](menu-images/maint08.png "el Inspector de biblioteca")

Ahora vamos a hacer lo siguiente para crear nuestro menú:

1. Arrastre un **elemento de menú** desde el **Inspector de biblioteca** en la barra de menús entre el **texto** y **ventana** menús: 

    ![Seleccionar un nuevo elemento de menú en la biblioteca](menu-images/maint10.png "seleccionando un nuevo elemento de menú en la biblioteca")
2. Cambie el nombre del elemento "Frases": 

    [![Establecer el nombre del menú](menu-images/maint09.png "establecer el nombre de menú")](menu-images/maint09-large.png#lightbox)
3. A continuación arrastre una **menú** desde el **Inspector de biblioteca**: 

    ![Selección de un menú de la biblioteca](menu-images/maint11.png "seleccionando un menú de la biblioteca")
4. A continuación, quitar **menú** en el nuevo **elemento de menú** nos acaba de crear y cambie su nombre a "Frases": 

    [![El nombre del menú de edición](menu-images/maint12.png "el nombre del menú de edición")](menu-images/maint12-large.png#lightbox)
5. Ahora vamos a cambiar el nombre de la predeterminada tres **elementos de menú** "Address", "Fecha" y "Greeting": 

    [![El menú de frases](menu-images/maint13.png "menú las frases")](menu-images/maint13-large.png#lightbox)
6. Vamos a agregar un cuarto **elemento de menú** arrastrando un **elemento de menú** desde el **Inspector de biblioteca** y llamarlo "Firma": 

    [![Editar el nombre de elemento de menú](menu-images/maint14.png "editar el nombre de elemento de menú")](menu-images/maint14-large.png#lightbox)
7. Guarde los cambios en la barra de menús.

Ahora vamos a crear un conjunto de acciones personalizadas para que nuestros nuevos elementos de menú se exponen al código de C#. En Xcode vamos a pasar a la **Ayudante** vista:

[![Creación de las acciones necesarias](menu-images/maint15.png "creación de las acciones necesarias")](menu-images/maint15-large.png#lightbox)

Vamos a hacer lo siguiente:

1. Tecla Control mientras arrastra desde el **dirección** elemento de menú para el **AppDelegate.h** archivo.
2. Cambiar el **conexión** escriba a **acción**: 

    [![Seleccionar el tipo de acción](menu-images/maint17.png "seleccionando el tipo de acción")](menu-images/maint17-large.png#lightbox)
3. Escriba un **nombre** de "phraseAddress" y presione la **Connect** botón para crear la nueva acción: 

    [![Configurar la acción](menu-images/maint18.png "configurar la acción")](menu-images/maint18-large.png#lightbox)
4. Repita los pasos anteriores para el **fecha**, **saludo**, y **firma** elementos de menú: 

    [![Las acciones completadas](menu-images/maint19.png "las acciones completadas")](menu-images/maint19-large.png#lightbox)
5. Guarde los cambios en la barra de menús.

A continuación, necesitamos crear una salida para la vista de texto para que podemos ajustar su contenido desde el código. Seleccione el **ViewController.h** de archivos en el **Editor del asistente** y crear una nueva salida denominada `documentText`:

[![Creación de una salida](menu-images/maint20.png "creando una salida")](menu-images/maint20-large.png#lightbox)

Vuelva a Visual Studio para Mac sincronizar los cambios de Xcode. A continuación modifique el **ViewController.cs** de archivos y darle un aspecto similar al siguiente:

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

Esto expone el texto de la vista de texto fuera de la `ViewController` clase e informa al delegado de la aplicación cuando la ventana Obtiene o pierde el foco. Ahora, edite el **AppDelegate.cs** de archivos y darle un aspecto similar al siguiente:

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = false;
            dlg.CanChooseDirectories = true;

            if (dlg.RunModal () == 1) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
                    MessageText = "Folder Selected"
                };
                alert.RunModal ();
            }
        }

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

Aquí hemos tomado la `AppDelegate` una parcial para que podemos usar las acciones y salidas que hemos definido en el generador de interfaz de clase. También exponemos un `textEditor` para realizar un seguimiento de la ventana que tiene el foco.

Los siguientes métodos sirven para controlar nuestro menú personalizado y elementos de menú:

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

Ahora, si ejecutamos nuestra aplicación, todos los elementos en el **frase** menú estará activo y se agregará la frase de dar a la vista de texto cuando se selecciona:

![Un ejemplo de la aplicación en ejecución](menu-images/maint21.png "un ejemplo de la aplicación en ejecución")

Ahora que tenemos los aspectos básicos de trabajar con la barra de menús de la aplicación hacia abajo, echemos un vistazo a la creación de un menú contextual de personalizado.

### <a name="creating-menus-from-code"></a>Creación de menús de código

Además de crear menús y elementos de menú con Interface Builder de Xcode, puede que en ocasiones cuando se necesita una aplicación de Xamarin.Mac para crear, modificar o quitar un menú, submenú o elemento de menú de código.

En el ejemplo siguiente, se crea una clase para contener la información acerca de los elementos de menú y submenús que se creará dinámicamente en marcha:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>Agregar menús y elementos

Con esta clase definida, la siguiente rutina analizará una colección de `LanguageFormatCommand`crear objetos y forma recursiva anexando la parte inferior del menú existente (creada en Interface Builder) que se ha pasado en nuevos menús y elementos de menú:

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

Para cualquier `LanguageFormatCommand` objeto que tiene un espacio en blanco `Title` esta rutina de la propiedad, crea un **elemento de menú separador** (una línea fina de color gris) entre las secciones del menú:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Si se proporciona un título, se crea un nuevo elemento de menú con ese título:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Si el `LanguageFormatCommand` objeto contiene el elemento secundario `LanguageFormatCommand` objetos, se crea un submenú y `AssembleMenu` método es llamado para crear ese menú de forma recursiva:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Para cualquier elemento de menú nuevo que no tiene submenús, se agrega código para controlar el elemento de menú seleccionado por el usuario:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Probar la creación de menús

Con todo el código anterior en su lugar, si la siguiente colección de `LanguageFormatCommand` se crearon objetos:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Strong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![](",")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[![](",")](LinkImageHere)"));
```

Y que la colección pasada a la `AssembleMenu` función (con el **formato** menú establece como la base), se crearán los siguientes menús dinámicos y los elementos de menú:

![Los nuevos elementos de menú en la aplicación en ejecución](menu-images/dynamic01.png "los nuevos elementos de menú en la aplicación en ejecución")

#### <a name="removing-menus-and-items"></a>Eliminación de los menús y elementos

Si necesita quitar cualquier menú o elemento de interfaz de usuario de la aplicación, puede usar el `RemoveItemAt` método de la `NSMenu` clase simplemente asignándole el cero según el índice del elemento que se va a quitar.

Por ejemplo, para quitar los menús y elementos de menú creados por la rutina anterior, podría usar el código siguiente:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

En el caso del código anterior, se crean los primeros elementos de cuatro menú en Interface Builder de Xcode y en ubicaciones disponibles en la aplicación, por lo que no se quitan de forma dinámica.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menús contextuales

Los menús contextuales aparecen cuando el usuario hace clic con botón o un elemento en una ventana de clics de control. De forma predeterminada, varios de los elementos de interfaz de usuario integrados en macOS ya tienen menús contextuales conectados a ellas (por ejemplo, la vista de texto). Sin embargo, puede haber ocasiones cuando queremos crear nuestros propio menús contextuales personalizados para un elemento de interfaz de usuario que hemos agregado a una ventana.

Vamos a editar nuestro **Main.storyboard** archivo en Xcode y agregue un **ventana** ventana a nuestro diseño, establezca su **clase** a "NSPanel" en el **delInspectordeidentidad**, agregue un nuevo **Ayudante** elemento a la **ventana** menú y adjuntarlo a la nueva ventana mediante un **mostrar Segue**:

[![Establecer el tipo de segue](menu-images/context01.png "estableciendo el tipo de segue")](menu-images/context01-large.png#lightbox)

Vamos a hacer lo siguiente:

1. Arrastre un **etiqueta** desde el **Inspector de biblioteca** hasta la **Panel** ventana y establezca el texto a "Propiedad": 

    [![Editar el valor de la etiqueta](menu-images/context03.png "editar el valor de la etiqueta")](menu-images/context03-large.png#lightbox)
2. A continuación arrastre una **menú** desde el **Inspector de biblioteca** en el controlador de vista en la jerarquía de vistas y elementos de menú de cambio de nombre los tres predeterminada **documento**, **texto**  y **fuente**:

    [![Los elementos de menú requiere](menu-images/context02.png "los elementos de menú requiere")](menu-images/context02-large.png#lightbox)
3. Ahora control y arrastre desde el **etiqueta de la propiedad** hasta la **menú**:

    [![Arrastrar para crear un segue](menu-images/context04.png "arrastrar para crear un segue")](menu-images/context04-large.png#lightbox)
4. En el cuadro de diálogo emergente, seleccione **menú**: 

    ![Establecer el tipo de segue](menu-images/context05.png "estableciendo el tipo de segue")
5. Desde el **Inspector de identidad**, set (clase) del controlador de vista a "PanelViewController": 

    [![Configuración de la clase segue](menu-images/context10.png "configuración de la clase de segue")](menu-images/context10-large.png#lightbox)
6. Cambie a Visual Studio para Mac sincronizar y después vuelva a Interface Builder.
7. Cambie a la **Editor del asistente** y seleccione el **PanelViewController.h** archivo.
8. Cree una acción para el **documento** elemento de menú llamado `propertyDocument`: 

    [![Configurar la acción](menu-images/context06.png "configurar la acción")](menu-images/context06-large.png#lightbox)
9. Repetir las acciones de creación para los restantes elementos de menú: 

    [![Las acciones necesarias](menu-images/context07.png "las acciones necesarias")](menu-images/context07-large.png#lightbox)
10. Por último, cree una salida para el **etiqueta de la propiedad** llamado `propertyLabel`: 

    [![Configuración de la toma de corriente](menu-images/context08.png "configuración de la toma de corriente")](menu-images/context08-large.png#lightbox)
11. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Editar el **PanelViewController.cs** archivo y agregue el código siguiente:

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

Ahora, si se ejecuta la aplicación y haga doble clic en la etiqueta de propiedad en el panel, veremos nuestra menú contextual personalizado. Si se selecciona y en el menú de elemento, se cambiará el valor de la etiqueta:

![El menú contextual ejecutar](menu-images/context09.png "el menú contextual de ejecución")

Siguiente Echemos un vistazo a la creación de menús de barra de estado.

## <a name="status-bar-menus"></a>Menús de la barra de estado

Los menús de la barra de estado muestran una colección de elementos de menú de estado que proporcionan la interacción con o comentarios para el usuario, como un menú o una imagen que refleja el estado de la aplicación. Menú de barra de estado de la aplicación está habilitado y activo incluso si la aplicación se ejecuta en segundo plano. La barra de estado de todo el sistema se encuentra en el lado derecho de la barra de menús de la aplicación y es la barra de estado sólo está disponible en Mac OS.

Vamos a editar nuestro **AppDelegate.cs** y realice la `DidFinishLaunching` método aspecto parecido al siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` nos da acceso a la barra de estado de todo el sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` crea un nuevo elemento de barra de estado. Desde allí, se crea un menú y un número de elementos de menú y anexar el menú para el elemento de la barra de estado que acabamos de crear. 

Si se ejecuta la aplicación, se mostrará el nuevo elemento de barra de estado. Seleccione un elemento en el menú, cambiará el texto en la vista de texto: 

![El menú de la barra de estado de ejecución](menu-images/statusbar01.png "el menú de la barra de estado de ejecución")

A continuación, veamos cómo crear elementos de menú de acoplamiento personalizado.

## <a name="custom-dock-menus"></a>Menús personalizados de acoplamiento

Cuando el usuario hace clic con botón o icono de la aplicación en el dock de clics de control, aparece el menú de acoplamiento para la aplicación de Mac:

![Menú de acoplamiento de un personalizado](menu-images/dock01.png "personalizada acoplar menú")

Vamos a crear un menú personalizado de acoplamiento para nuestra aplicación haciendo lo siguiente:

1. En Visual Studio para Mac, haga doble clic en de la aplicación proyecto y seleccione **agregar** > **nuevo archivo...** En el cuadro de diálogo nuevo archivo, seleccione **Xamarin.Mac** > **definición de interfaz vacía**, use "DockMenu" para el **nombre** y haga clic en el **nuevo**  botón para crear el nuevo **DockMenu.xib** archivo:

    ![Agregar una definición de interfaz vacía](menu-images/dock02.png "agregar una definición de interfaz vacía")
2. En el **panel de solución**, haga doble clic en el **DockMenu.xib** archivo para abrirlo y editarlo en Xcode. Cree un nuevo **menú** con los siguientes elementos: **Dirección**, **fecha**, **saludo**, y **firma** 

    [![Diseñar la interfaz de usuario](menu-images/dock03.png "diseñar la interfaz de usuario")](menu-images/dock03-large.png#lightbox)
3. A continuación, nos Conectaremos nuestros nuevos elementos de menú a nuestras acciones existentes que creamos para nuestro menú personalizado en el [agregar, modificar y eliminar menús](#Adding,_Editing_and_Deleting_Menus) sección anterior. Cambie a la **conexión Inspector** y seleccione el **Respondedor primera** en el **jerarquía de la interfaz**. Desplácese hacia abajo y busque el `phraseAddress:` acción. Arrastre una línea desde el círculo en esa acción a la **dirección** elemento de menú:

    [![Arrastrar para crear una acción](menu-images/dock04.png "arrastrar para crear una acción")](menu-images/dock04-large.png#lightbox)
4. Repita para todos los demás elementos de menú adjuntarlos a sus acciones correspondientes: 

    [![Las acciones necesarias](menu-images/dock05.png "las acciones necesarias")](menu-images/dock05-large.png#lightbox)
5. A continuación, seleccione el **aplicación** en el **jerarquía de la interfaz**. En el **conexión Inspector**, arrastre una línea desde el círculo el `dockMenu` toma de corriente para el menú que acaba de crear:

    [![Arrastre la conexión de la toma de corriente](menu-images/dock06.png "arrastrando la conexión de la toma de corriente")](menu-images/dock06-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.
7. Haga doble clic en el **Info.plist** archivo para abrirlo y editarlo: 

    [![Modificar el archivo info.plist](menu-images/dock07.png "Modificar el archivo info.plist")](menu-images/dock07-large.png#lightbox)
8. Haga clic en el **origen** ficha en la parte inferior de la pantalla: 

    [![Seleccionar la vista del origen](menu-images/dock08.png "seleccionando la vista del origen")](menu-images/dock08-large.png#lightbox)
9. Haga clic en **agregar nueva entrada**, haga clic en el botón de signo más verde, establezca el nombre de propiedad a "AppleDockMenu" y el valor en "DockMenu" (el nombre de nuestro nuevo archivo .xib sin la extensión): 

    [![Agregando el elemento DockMenu](menu-images/dock09.png "agregando el elemento DockMenu")](menu-images/dock09-large.png#lightbox)

Ahora si se ejecuta nuestra aplicación y haga doble clic en el icono correspondiente en el Dock, se mostrará nuestros nuevos elementos de menú:

![Un ejemplo del menú acoplar ejecutando](menu-images/dock10.png "un ejemplo del menú acoplar ejecutando")

Si se selecciona uno de los elementos personalizados en el menú, se modificará el texto en la vista de texto.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Botón emergente y listas desplegables

Un botón emergente muestra un elemento seleccionado y presenta una lista de opciones para seleccionar desde cuando el usuario hace clic. Una lista desplegable es un tipo de botón emergente que normalmente se usa para seleccionar comandos específicos del contexto de la tarea actual. Ambos pueden aparecer en cualquier lugar en una ventana.

Vamos a crear un botón emergente personalizado para nuestra aplicación haciendo lo siguiente:

1. Editar el **Main.storyboard** archivo en Xcode y arrastre un **botón emergente** desde el **Inspector de biblioteca** hasta la **Panel** ventana hemos creado en el [menús contextuales](#Contextual_Menus) sección: 

    [![Agregar un botón emergente](menu-images/popup01.png "agregar un botón emergente")](menu-images/popup01-large.png#lightbox)
2. Agregar un nuevo elemento de menú y establezca los títulos de los elementos en el menú emergente: **Dirección**, **fecha**, **saludo**, y **firma** 

    [![Configuración de los elementos de menú](menu-images/popup02.png "configurar los elementos de menú")](menu-images/popup02-large.png#lightbox)
3. A continuación, vamos a conectar nuestros nuevos elementos de menú con las acciones existentes que creamos para nuestro menú personalizado en el [agregar, modificar y eliminar menús](#Adding,_Editing_and_Deleting_Menus) sección anterior. Cambie a la **conexión Inspector** y seleccione el **Respondedor primera** en el **jerarquía de la interfaz**. Desplácese hacia abajo y busque el `phraseAddress:` acción. Arrastre una línea desde el círculo en esa acción a la **dirección** elemento de menú: 

    [![Arrastrar para crear una acción](menu-images/popup03.png "arrastrar para crear una acción")](menu-images/popup03-large.png#lightbox)
4. Repita para todos los demás elementos de menú adjuntarlos a sus acciones correspondientes: 

    [![Todas las operaciones necesarias](menu-images/popup04.png "todas las acciones necesarias")](menu-images/popup04-large.png#lightbox)
5. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Ahora si ejecutar la aplicación y seleccione un elemento de la ventana emergente, cambiará el texto en la vista de texto:

![Un ejemplo de la ventana emergente ejecutando](menu-images/popup05.png "muestra un ejemplo de la ventana emergente que se ejecuta")

Puede crear y trabajar con listas desplegables en la misma manera como botones emergentes. En lugar de asociar a la acción existente, puede crear sus propias acciones personalizadas tal como se hizo para el menú contextual en el [menús contextuales](#Contextual_Menus) sección.

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con menús y elementos de menú en una aplicación de Xamarin.Mac. Primero examinamos barra de menús de la aplicación, a continuación, contemplamos crear menús contextuales, a continuación, examinamos los menús de la barra de estado y personalizado acoplar los menús. Por último, hemos tratado los menús emergentes y listas desplegables.


## <a name="related-links"></a>Vínculos relacionados

- [MacMenus (ejemplo)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Directrices de interfaz humana - menús](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introducción a los menús de la aplicación y las listas emergentes](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
