---
title: Menús
description: Este artículo explica cómo trabajar con menús en una aplicación Xamarin.Mac. Describe cómo crear y mantener los menús y elementos de menú en Xcode y el generador de interfaz y trabajar con ellos mediante programación.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 50c9cf333ff7965bbdfbb964a2301e677eb6aa59
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="menus"></a>Menús

_Este artículo explica cómo trabajar con menús en una aplicación Xamarin.Mac. Describe cómo crear y mantener los menús y elementos de menú en Xcode y el generador de interfaz y trabajar con ellos mediante programación._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tener acceso a los mismos menús cacao encargada de un desarrollador que trabaja en Objective C y Xcode. Porque Xamarin.Mac se integra directamente en Xcode, puede utilizar el generador de interfaz de Xcode para crear y mantener su barras de menús, menús y elementos de menú (o si lo desea crearlos directamente en código de C#).

Los menús son una parte integral de la experiencia del usuario de la aplicación de Mac y normalmente aparecen en distintas partes de la interfaz de usuario:

- **Barra de menús de la aplicación** -éste es el menú principal que aparece en la parte superior de la pantalla para cada aplicación de Mac.
- **Menús contextuales** -aparecen cuando el usuario hace clic con botón o un elemento en una ventana de clics de control.
- **La barra de estado** -este es el área en el extremo derecho de la barra de menús de la aplicación que aparece en la parte superior de la pantalla (a la izquierda del reloj de barra de menú) y crece a la izquierda como elementos se agregan a él.
- **Acoplar menú** -el menú para cada aplicación en el área que aparece cuando el usuario hace clic con botón o control y hace clic en el icono de la aplicación, o cuando el usuario hace clic en el icono y mantiene presionado el botón del mouse (ratón).
- **Botón emergente y listas desplegables** -un botón emergente muestra un elemento seleccionado y presenta una lista de opciones para seleccionar entre cuando el usuario hace clic. Una lista desplegable es un tipo de botón emergente que normalmente se utiliza para seleccionar comandos específicos del contexto de la tarea actual. Puede aparecer en cualquier parte en una ventana.

[![Un menú de ejemplo](menu-images/intro01.png "un menú de ejemplo")](menu-images/intro01-large.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con elementos de menú en una aplicación Xamarin.Mac, menús y barras de menús de cacao. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` atributos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

## <a name="the-applications-menu-bar"></a>Barra de menús de la aplicación 

A diferencia de aplicaciones que se ejecutan en el sistema operativo de Windows donde cada ventana puede tener su propia barra de menús asociada a él, cada aplicación se ejecuta en Mac OS tiene una barra de menús único que se ejecuta en la parte superior de la pantalla que se utiliza para todas las ventanas de la aplicación:

[![Una barra de menús](menu-images/appmenu01.png "una barra de menús")](menu-images/appmenu01-large.png#lightbox)

Elementos de esta barra de menús se activa o desactiva basándose en el contexto actual o el estado de la aplicación y su interfaz de usuario en un momento dado. Por ejemplo: si el usuario selecciona un campo de texto, los elementos en el **editar** menú estarán habilitado como **copia** y **cortar**.

Con arreglo a Apple y de forma predeterminada, todas las aplicaciones de Mac OS tienen un conjunto estándar de los menús y elementos de menú que aparecen en la barra de menús de la aplicación:

- **Menú Apple** -este menú proporciona acceso al sistema amplios elementos que están disponibles para el usuario en todo momento, independientemente de qué aplicación se está ejecutando. No se puede modificar estos elementos por el desarrollador.
- **Menú de la aplicación** -este menú muestra el nombre de la aplicación en negrita y ayuda al usuario a identificar qué aplicación se está ejecutando actualmente. Contiene elementos que se aplican a la aplicación como un todo y no un documento determinado o un proceso como salir de la aplicación.
- **Menú archivo** : elementos que se utilizan para crear, abrir o guardar los documentos que la aplicación funcione con. Si la aplicación no está basado en el documento, puede cambiar el nombre o quitar este menú.
- **Menú Edición** -contiene comandos como **cortar**, **copia**, y **pegar** que se utilizan para editar o modificar los elementos de interfaz de usuario de la aplicación.
- **Menú formato** : si la aplicación funciona con texto, este menú contiene comandos para ajustar el formato de texto.
- **Menú Ver** -contiene comandos que afectan a cómo se muestra el contenido (ver) en la interfaz de usuario de la aplicación.
- **Los menús específicos de la aplicación** : se trata de los menús que son específicos de la aplicación (por ejemplo, un menú de marcadores para un explorador web). Deben aparecer entre el **vista** y **ventana** menús en la barra.
- **Menú ventana** -contiene comandos para trabajar con ventanas en la aplicación, así como una lista de las ventanas abiertas actuales.
- **Menú Ayuda** -si la aplicación proporciona ayuda en pantalla, el menú Ayuda debe ser el menú más a la derecha en la barra. 

Para obtener más información acerca de la barra de menús de la aplicación y los menús estándar y los elementos de menú, vea de Apple [directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>La barra de menús de la aplicación de forma predeterminada

Siempre que cree un nuevo proyecto de Xamarin.Mac, obtendrá automáticamente una barra de menús de aplicación predeterminado estándar, que tiene los elementos típicos que tuviera una aplicación macOS (como se ha descrito en la sección anterior). Barra de menús de la aplicación predeterminada se define en el **Main.storyboard** archivo (junto con el resto de la interfaz de usuario de la aplicación) en el proyecto en el **solución panel**:  

![Seleccione el guión gráfico principal](menu-images/appmenu02.png "seleccionar el guión gráfico principal")

Haga doble clic en el **Main.storyboard** archivo que desee abrir para editar en el generador de interfaz de Xcode y se mostrarán con la interfaz de editor de menús:

[![Edición de la interfaz de usuario en Xcode](menu-images/defaultbar01.png "edición de la interfaz de usuario en Xcode.")](menu-images/defaultbar01-large.png#lightbox)

Desde aquí nos podemos hacer clic en elementos como el **abiertos** elemento de menú en el **archivo** menú y editar o ajustar sus propiedades en el **atributos Inspector**:

[![Edición de atributos de un menú](menu-images/defaultbar02.png "editar atributos de un menú")](menu-images/defaultbar02-large.png#lightbox)

Nos pondremos en Agregar, modificar y eliminar menús y elementos más adelante en este artículo. Para que ahora simplemente desea ver qué menús y elementos de menú están disponibles de forma predeterminada y cómo se han automáticamente expuestos al código a través de un conjunto de distribuidores predefinidos y acciones (para obtener más información, consulte nuestro [salidas y las acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) documentación).

Por ejemplo, si hacemos clic en el **conexión Inspector** para el **abiertos** podemos ver se conecta automáticamente hasta el elemento de menú la `openDocument:` acción: 

[![Ver la acción adjuntada](menu-images/defaultbar03.png "ver la acción adjunta")](menu-images/defaultbar03-large.png#lightbox)

Si selecciona el **primer servicio de respuesta** en el **jerarquía de interfaz** y desplácese hacia abajo en la **conexión Inspector**, y verá la definición de la `openDocument:` acción que el **abiertos** elemento de menú se adjunta a (junto con varias otras acciones predeterminadas para la aplicación y no se conectan automáticamente a controles):

[![Ver todos los adjuntos acciones](menu-images/defaultbar04.png "ver todas las acciones asociadas")](menu-images/defaultbar04-large.png#lightbox) 

¿Por qué es importante? En la siguiente sección verá cómo funcionan estas acciones define automáticamente a otros elementos de la interfaz de usuario de cacao automáticamente habilitar y deshabilitar elementos de menú, así como, proporcionar una funcionalidad integrada para los elementos.

Más adelante se usará estas acciones integradas para habilitar y deshabilitar elementos de código y proporcionar nuestra propia funcionalidad cuando se seleccionan.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Funcionalidad del menú integrados

Si fuera la ejecución de una aplicación de Xamarin.Mac recién creado antes de agregar los elementos de interfaz de usuario o el código, observará que algunos elementos están automáticamente con cable telefónico y habilita (con totalmente integrada la funcionalidad de forma automática), como el **Quit** de elemento en el **aplicación** menú:

![Un elemento de menú habilitado](menu-images/appmenu03.png "un elemento de menú habilitado")

Aunque otros elementos de menú, como **cortar**, **copia**, y **pegar** no son:

![Deshabilitar elementos de menú](menu-images/appmenu04.png "deshabilitar elementos de menú")

Vamos a detener la aplicación y haga doble clic en el **Main.storyboard** un archivo en el **solución Pad** para abrirlo y editarlo en Xcode del generador de interfaz. A continuación, arrastre un **vista de texto** desde el **biblioteca** en el controlador de vista de la ventana en la **Editor de la interfaz**:

[![Seleccionar una vista de texto de la biblioteca](menu-images/appmenu05.png "seleccionar una vista de texto de la biblioteca")](menu-images/appmenu05-large.png#lightbox)

En el **Editor de restricciones de** vamos a anclar la vista de texto con bordes de la ventana y establézcalo donde aumenta y disminuye con la ventana haciendo clic en todos los cuatro rojos vigas de en la parte superior del editor y haga clic en el **agregar restricciones de 4** botón:

[![Las restricciones de edición](menu-images/appmenu06.png "las restricciones de edición")](menu-images/appmenu06-large.png#lightbox)

Guarde los cambios en el diseño de la interfaz de usuario y volver a Visual Studio para Mac sincronizar los cambios con el proyecto Xamarin.Mac. Iniciar la aplicación, escriba algún texto en la vista de texto, selecciónelo y abra ahora el **editar** menú:

![Los elementos de menú están automáticamente activadas o desactivadas](menu-images/appmenu07.png "los elementos de menú se automáticamente habilitado o deshabilitado")

Observe cómo la **cortar**, **copia**, y **pegar** elementos están habilitados automáticamente y es totalmente funcional, todo ello sin escribir una sola línea de código. 

¿Qué está sucediendo aquí? Recuerde integrado predefinir las acciones que vienen con cable hasta los elementos de menú predeterminado (tal y como se presenta anteriormente), la mayoría de los elementos de la interfaz de usuario de cacao que forman parte de Mac OS tienen integrados enlaces para realizar determinadas acciones (como `copy:`). Por lo que cuando se agregan a una ventana, activa y seleccionados, el elemento de menú correspondiente o elementos adjuntados a dicha acción se habilitan automáticamente. Si el usuario selecciona ese elemento de menú, la funcionalidad integrada en el elemento de interfaz de usuario se llama y se ejecuta, todo ello sin intervención del programador.

### <a name="enabling-and-disabling-menus-and-items"></a>Habilitar y deshabilitar menús y elementos

De forma predeterminada, cada vez que se produce un evento de usuario, `NSMenu` automáticamente habilita y deshabilita cada visible menús y elementos en función del contexto de la aplicación. Hay tres maneras de habilitar o deshabilitar un elemento:

- **Habilitación automática de menús** -un elemento de menú está habilitado si `NSMenu` puede encontrar un objeto adecuado que responde a la acción que el elemento es con cable telefónico al. Por ejemplo, la vista de texto anterior que tenía un enlace integrado para la `copy:` acción.
- **Acciones personalizadas y validateMenuItem:** : para cualquier elemento de menú que se enlaza a un [ventana o vista de acción personalizada de controlador](#Working-with-Custom-Window-Actions), puede agregar el `validateMenuItem:` acción y habilitar o deshabilitar manualmente elementos de menú.
- **Menú manual habilitar** -establecer manualmente la `Enabled` propiedad de cada `NSMenuItem` para habilitar o deshabilitar individualmente cada elemento de un menú.

Para elegir un sistema, establezca el `AutoEnablesItems` propiedad de un `NSMenu`. `true` es automático (comportamiento predeterminado) y `false` es manual. 

> [!IMPORTANT]
> Si decide usar el menú manual habilitar, ninguno de los menús de elementos, incluso aquellos que estén controlados por clases, AppKit como `NSTextView`, se actualizan automáticamente. Será responsable de habilitar y deshabilitar todos los elementos manualmente en el código.

#### <a name="using-validatemenuitem"></a>Usar validateMenuItem

Como se mencionó anteriormente, para cualquier elemento de menú que se enlaza a un [ventana o acción personalizada de controlador de vista](#Working-with-Custom-Window-Actions), puede agregar el `validateMenuItem:` acción y habilitar o deshabilitar manualmente elementos de menú.

En el ejemplo siguiente, la `Tag` propiedad se usarán para decidir el tipo de elemento de menú que se pueden activar/desactivar por la `validateMenuItem:` acción según el estado del texto seleccionado en un `NSTextView`. El `Tag` propiedad se ha establecido en el generador de interfaz para cada elemento de menú:

![Establecer la propiedad etiqueta](menu-images/validate01.png "establecer la propiedad de etiqueta")

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

Cuando se ejecuta este código, y no hay texto seleccionado en el `NSTextView`, se deshabilitan los elementos de menú de dos encapsulado (aunque se especifique se conectan a las acciones en el controlador de vista):

![Mostrar deshabilitado elementos](menu-images/validate02.png "mostrando elementos deshabilitados")

Si se selecciona una sección de texto y vuelva a abrir el menú, los elementos de menú de dos ajuste estará disponibles:

![Mostrar habilitado elementos](menu-images/validate03.png "mostrando habilitado elementos")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Habilitar y responder a los elementos de menú en el código

Como hemos visto anteriormente, simplemente agregando elementos de la interfaz de usuario de cacao específicos para el diseño de interfaz de usuario (por ejemplo, un campo de texto), algunos de los elementos de menú de forma predeterminada se habilitará y funcionan de forma automática, sin tener que escribir ningún código. Siguiente Echemos un vistazo a agregar nuestro propio código de C# a nuestro proyecto Xamarin.Mac para habilitar un elemento de menú y proporcionar una funcionalidad cuando el usuario lo selecciona.

Por ejemplo, permitir que decir que se desea que el usuario para poder usar el **abiertos** de elemento en el **archivo** menú para seleccionar una carpeta. Puesto que deseamos que este valor es una función de toda la aplicación y no está limitado a una determinada ventana o elemento de interfaz de usuario, vamos a agregar el código para controlar esto a nuestro delegado de la aplicación.

En el **solución Pad**, haga doble clic en el `AppDelegate.CS` archivo para abrirlo y editarlo:

![Al seleccionar el delegado de la aplicación](menu-images/appmenu08.png "seleccionando el delegado de aplicación")

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

Vamos a ejecutar la aplicación y abra el **archivo** menú: 

![El menú archivo](menu-images/appmenu09.png "el menú archivo")

Tenga en cuenta que la **abiertos** ahora está habilitado el elemento de menú. Si se selecciona, se mostrará el cuadro de diálogo Abrir:

![Un cuadro de diálogo Abrir](menu-images/appmenu10.png "un cuadro de diálogo Abrir")

Si hacemos clic en el **abiertos** botón, se mostrará el mensaje de alerta:

![Un mensaje de diálogo de ejemplo](menu-images/appmenu11.png "un mensaje de diálogo de ejemplo")

La línea clave aquí es `[Export ("openDocument:")]`, indica `NSMenu` que nuestro **AppDelegate** tiene un método `void OpenDialog (NSObject sender)` que responda a la `openDocument:` acción. Si recordará desde arriba, el **abiertos** elemento de menú es automáticamente con cable telefónico para esta acción de forma predeterminada en el generador de interfaz:

[![Ver las acciones asociadas](menu-images/defaultbar03.png "ver las acciones asociadas")](menu-images/defaultbar03-large.png#lightbox)

Siguiente Echemos un vistazo a crear nuestra propia menú, elementos de menú y las acciones y responder a ellas en el código.

### <a name="working-with-the-open-recent-menu"></a>Trabajar con el menú Abrir recientes

De forma predeterminada, el **archivo** menú contiene una **Abrir recientes** elemento que realiza un seguimiento de los últimos archivos varios que el usuario ha abierto con la aplicación. Si está creando un `NSDocument` Xamarin.Mac aplicación, basada en este menú se administrarán para automáticamente. Para cualquier otro tipo de aplicación Xamarin.Mac, será responsable de administrar y responder a este elemento de menú manualmente.

Para controlar manualmente la **Abrir recientes** menú, primero deberá informar a lo que un nuevo archivo se ha abrir o guardar con los siguientes:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Aunque la aplicación no usa `NSDocuments`, seguir usando el `NSDocumentController` para mantener la **Abrir recientes** menú mediante el envío de un `NSUrl` con la ubicación del archivo que se la `NoteNewRecentDocumentURL` método de la `SharedDocumentController`.

A continuación, debe invalidar el `OpenFile` método del delegado de aplicación para abrir cualquier archivo que el usuario selecciona en el **abrir reciente** menú. Por ejemplo:

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

Devolver `true` si se puede abrir el archivo, lo contrario devuelve `false` y se mostrará una advertencia integrada para el usuario que no se pudo abrir el archivo.

Porque el nombre de archivo y ruta de acceso devuelven por la **Abrir recientes** menú, puede incluir un espacio, necesitamos convertir este carácter antes de crear un `NSUrl` o se producirá un error. Para hacerlo con el código siguiente:

```csharp
filename = filename.Replace (" ", "%20");
```

Por último, creamos un `NSUrl` que apunta al archivo y use un método auxiliar en la aplicación de delegado para abrir una nueva ventana y cargar el archivo en él:

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

En función de los requisitos de la aplicación, podría no desea que el usuario para abrir el mismo archivo en más de una ventana a la vez. En nuestra aplicación de ejemplo, si el usuario elige un archivo que ya está abierto (ya sea desde la **abrir reciente** o **abrir...** elementos de menú), la ventana que contiene el archivo se pone en primer plano.

Para lograr esto, hemos usado el código siguiente en el método de aplicación auxiliar:

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

Hemos diseñado nuestra `ViewController` clase para contener la ruta de acceso al archivo en su `Path` propiedad. A continuación, realizamos un recorrido por todas las ventanas abiertas en la aplicación. Si el archivo ya está abierto en una de las ventanas, se pone en la parte delantera de todas las demás ventanas mediante:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Si se encuentra ninguna coincidencia, se abre una ventana nueva con el archivo de carga y el archivo se indica en la **Abrir recientes** menú:

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

### <a name="working-with-custom-window-actions"></a>Trabajar con acciones de ventana personalizados

Al igual que la integrada **primera respuesta** acciones que vienen previamente con cable para elementos de menú estándar, puede crear nuevas acciones personalizadas y ellos de la conexión a los elementos de menú en el generador de interfaz.

En primer lugar, defina una acción personalizada en uno de los controladores de la ventana de la aplicación. Por ejemplo:

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

A continuación, haga doble clic en el archivo del guión gráfico de la aplicación en el **solución Pad** para abrirlo y editarlo en Xcode del generador de interfaz. Seleccione el **primer servicio de respuesta** en el **escena de aplicación**, a continuación, cambie a la **Inspector de atributos**:

![El Inspector de atributos](menu-images/action01.png "el Inspector de atributos")

Haga clic en el **+** situado en la parte inferior de la **atributos Inspector** para agregar una nueva acción personalizada:

![Agregar una nueva acción](menu-images/action02.png "agregar una nueva acción")

Asígnele el mismo nombre que la acción personalizada que creó en el controlador de ventana:

![Editar el nombre de acción](menu-images/action03.png "editar el nombre de acción")

Control y haga clic y arrastre desde un elemento de menú para el **primera respuesta** en el **aplicación escena**. En la lista emergente, seleccione la acción nueva que acaba de crear (`defineKeyword:` en este ejemplo):

![Adjuntar una acción](menu-images/action04.png "adjuntar una acción")

Guarde los cambios en el guión gráfico y vuelva a Visual Studio para Mac sincronizar los cambios. Si se ejecuta la aplicación, el elemento de menú que se conectó a la acción personalizada se automáticamente habilitarse o deshabilitarse (en función de la ventana con la acción que se va a abrir) y seleccionar el elemento de menú se activará desactivar la acción:

[![Para probar la nueva acción](menu-images/action05.png "para probar la nueva acción")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Agregar, modificar y eliminar menús

Como hemos visto en las secciones anteriores, una aplicación de Xamarin.Mac incluye una serie de valores preestablecida de menús predeterminados y los elementos de menú que los controles de interfaz de usuario específicos automáticamente se activan y responder a. También hemos visto cómo agregar código a la aplicación que también habilitará y responder a estos elementos de forma predeterminada.

En esta sección, veremos quitar elementos de menú que no es necesario, la reorganización de los menús y agregando nuevos menús, elementos de menú y acciones.

Haga doble clic en el **Main.storyboard** un archivo en el **solución Pad** para abrirlo y editarlo:

[![Edición de la interfaz de usuario en Xcode](menu-images/maint01.png "edición de la interfaz de usuario en Xcode.")](menu-images/maint01-large.png#lightbox)

Para nuestra aplicación Xamarin.Mac específica no vamos a usar el valor predeterminado **vista** menú por lo que vamos a quitarlo. En el **jerarquía de interfaz** seleccione la **vista** elemento de menú que forma parte de la barra de menús principal:

![Seleccionar el elemento de menú de vista](menu-images/maint02.png "seleccionando el elemento de menú de vista")

Presione la tecla SUPR o RETROCESO para eliminar el menú. A continuación, no vamos a usar todos los elementos en el **formato** menú y desea mover los elementos que vamos a utilizar desde los menús de sub. En el **jerarquía de interfaz** seleccione los elementos de menú siguientes:

![Resaltado de varios elementos](menu-images/maint03.png "resaltado varios elementos")

Arrastre los elementos bajo el elemento primario **menú** en el submenú que actualmente son:

[![Arrastre los elementos de menú al menú primario](menu-images/maint04.png "arrastrando los elementos de menú al menú principal")](menu-images/maint04-large.png#lightbox)

El menú debe ser ahora similar:

[![Los elementos de la nueva ubicación](menu-images/maint05.png "los elementos en la nueva ubicación")](menu-images/maint05-large.png#lightbox)

Siguiente arrastre el **texto** submenú out en el **formato** menú y lo coloca en la barra de menús principal entre el **formato** y **ventana** menús:

[![El menú de texto](menu-images/maint06.png "menú el texto")](menu-images/maint06-large.png#lightbox)

Volvamos atrás en el **formato** menú y elimine la **fuente** elemento de submenú. A continuación, seleccione la **formato** menú y cámbiele el nombre "Fuente":

[![El menú de fuentes](menu-images/maint07.png "menú de la fuente")](menu-images/maint07-large.png#lightbox)

A continuación, vamos a crear un menú personalizado de frases predefinido que automáticamente obtener anexa el texto en la vista de texto cuando se seleccionan. En el cuadro de búsqueda en la parte inferior de la **biblioteca Inspector** tipo en "menú". Esto hará que sea más fácil buscar y trabajar con todos los elementos de interfaz de usuario de menú:

![El Inspector de biblioteca](menu-images/maint08.png "el Inspector de biblioteca")

Ahora vamos a hacer lo siguiente para crear el menú:

1. Arrastre un **plato** desde el **biblioteca Inspector** en la barra de menús entre el **texto** y **ventana** menús: 

    ![Al seleccionar un nuevo elemento de menú en la biblioteca](menu-images/maint10.png "al seleccionar un nuevo elemento de menú en la biblioteca")
2. Cambiar el nombre del elemento "Frases": 

    [![Establecer el nombre del menú](menu-images/maint09.png "establecer el nombre de menú")](menu-images/maint09-large.png#lightbox)
3. A continuación arrastre una **menú** desde el **Inspector de biblioteca**: 

    ![Seleccionar un menú de la biblioteca](menu-images/maint11.png "seleccionar un menú de la biblioteca")
4. A continuación, quitar **menú** en el nuevo **plato** se acaba de crear y cambie su nombre a "Frases": 

    [![Editar el nombre del menú](menu-images/maint12.png "editar el nombre de menú")](menu-images/maint12-large.png#lightbox)
5. Ahora vamos a cambiar el nombre de la predeterminada de tres **elementos de menú** "Address", "Fecha" y "Greeting": 

    [![El menú de frases](menu-images/maint13.png "menú de las frases")](menu-images/maint13-large.png#lightbox)
6. Vamos a agregar un cuarto **plato** arrastrando un **plato** desde el **biblioteca Inspector** y llamarlo "Firma": 

    [![Editar el nombre de elemento de menú](menu-images/maint14.png "editar el nombre de elemento de menú")](menu-images/maint14-large.png#lightbox)
7. Guarde los cambios en la barra de menús.

Ahora vamos a crear un conjunto de acciones personalizadas para que nuestro nuevos elementos de menú se exponen al código de C#. En Xcode centremos la **Ayudante** vista:

[![Creación de las acciones necesarias](menu-images/maint15.png "crear las acciones necesarias")](menu-images/maint15-large.png#lightbox)

Vamos a hacer lo siguiente:

1. Control y arrastre desde el **dirección** elemento de menú para el **AppDelegate.h** archivo.
2. Cambiar el **conexión** escriba a **acción**: 

    [![Seleccionar el tipo de acción](menu-images/maint17.png "seleccionando el tipo de acción")](menu-images/maint17-large.png#lightbox)
3. Escriba un **nombre** de "phraseAddress" y presione el **conectar** botón para crear la nueva acción: 

    [![Configuración de la acción](menu-images/maint18.png "configuración de la acción")](menu-images/maint18-large.png#lightbox)
4. Repita los pasos anteriores para la **fecha**, **saludo**, y **firma** elementos de menú: 

    [![Las acciones completadas](menu-images/maint19.png "las acciones completadas")](menu-images/maint19-large.png#lightbox)
5. Guarde los cambios en la barra de menús.

A continuación, necesitamos crear una salida para la vista de texto de modo que podemos ajustar su contenido desde el código. Seleccione el **ViewController.h** un archivo en el **Ayudante para el Editor** y crear una nueva toma denominada `documentText`:

[![Creación de una toma de corriente](menu-images/maint20.png "crear una salida")](menu-images/maint20-large.png#lightbox)

Vuelva a Visual Studio para Mac sincronizar los cambios de Xcode. A continuación modifique la **ViewController.cs** de archivos y darle un aspecto similar al siguiente:

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

Esto expone el texto de la vista de texto fuera de la `ViewController` clase e informa al delegado de la aplicación cuando la ventana Obtiene o pierde el foco. Editar ahora el **AppDelegate.cs** de archivos y darle un aspecto similar al siguiente:

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

Aquí hemos simplificado la `AppDelegate` un parcial clase por lo que podemos usar las acciones y las salidas que se definen en el generador de interfaz. También exponemos un `textEditor` para realizar un seguimiento de la ventana que tiene actualmente el foco.

Los siguientes métodos sirven para administrar nuestros menú personalizado y elementos de menú:

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

![Un ejemplo de la aplicación en ejecución](menu-images/maint21.png "muestra un ejemplo de la aplicación en ejecución")

Ahora que tenemos los conceptos básicos sobre cómo trabajar con la barra de menús de la aplicación hacia abajo, echemos un vistazo a la creación de un menú contextual personalizado.

### <a name="creating-menus-from-code"></a>Crear menús desde el código

Además de crear los menús y elementos de menú con el generador de interfaz de Xcode, puede haber ocasiones cuando se necesita una aplicación de Xamarin.Mac para crear, modificar o quitar un menú, un submenú o un elemento de menú desde el código.

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

Con esta clase definida, la rutina siguiente analizará una colección de `LanguageFormatCommand`crear objetos y forma recursiva, adjúntelos a la parte inferior del menú existente (creado en el generador de interfaz) que se ha pasado en nuevos menús y elementos de menú:

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

Para cualquier `LanguageFormatCommand` objeto que tiene un espacio en blanco `Title` propiedad, esta rutina, se crea un **elemento de menú separador** (una línea fina de color gris) entre las secciones del menú:

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Si no se proporciona un título, se crea un nuevo elemento de menú con ese título:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Si el `LanguageFormatCommand` objeto contiene secundarios `LanguageFormatCommand` objetos, se crea un submenú y `AssembleMenu` método es recursivamente llamado para generar ese menú:

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Para cualquier nuevo elemento de menú que no tiene submenús, se agregará código para controlar el elemento de menú seleccionado por el usuario:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Probar la creación de menú

Con todo el código anterior en su lugar, si la siguiente colección de `LanguageFormatCommand` los objetos se crearon:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Stong","**","**"));
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
FormattingCommands.Add(new LanguageFormatCommand("Image","![]("-")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[ ![]("-")](linkimagehere/index.md)"));
```

Y que la colección pasada a la `AssembleMenu` (función) (con el **formato** menú establece como base), se crearán los siguientes menús dinámicos y elementos de menú:

![Los nuevos elementos de menú en la aplicación en ejecución](menu-images/dynamic01.png "los nuevos elementos de menú en la aplicación en ejecución")

#### <a name="removing-menus-and-items"></a>Quitar los menús y elementos

Si necesita quitar un menú o un elemento de menú de la interfaz de usuario de la aplicación, puede usar el `RemoveItemAt` método de la `NSMenu` clase simplemente proporcionando el cero según el índice del elemento que se va a quitar.

Por ejemplo, para quitar los menús y elementos de menú creados por la rutina anterior, podría utilizar el siguiente código:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

En el caso del código anterior, los primeros elementos de cuatro menú se crean en el generador de interfaz de Xcode y ubicaciones disponibles en la aplicación, por lo que no se quitan de forma dinámica.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menús contextuales

Menús contextuales aparecen cuando el usuario hace clic con botón o un elemento en una ventana de clics de control. De forma predeterminada, varios de los elementos de interfaz de usuario integrados en macOS ya tienen menús contextuales conectados a ellas (por ejemplo, la vista de texto). Sin embargo, puede haber veces cuando desea volver a crear nuestros propia menús contextuales personalizados para un elemento de interfaz de usuario que hemos agregado a una ventana.

Editar nuestro **Main.storyboard** un archivo en Xcode y agregue un **ventana** ventana para el diseño, establezca su **clase** a "NSPanel" en la **identidad Inspector**, agregue un nuevo **Ayudante** elemento a la **ventana** menú y adjuntarlo a la nueva ventana utilizando un **mostrar desplazará tranquilamente**:

[![Establecer el tipo de segue](menu-images/context01.png "estableciendo el tipo de segue")](menu-images/context01-large.png#lightbox)

Vamos a hacer lo siguiente:

1. Arrastre un **etiqueta** desde el **biblioteca Inspector** en el **Panel** ventana y establecer su texto como "Propiedad": 

    [![Editar el valor de la etiqueta](menu-images/context03.png "editar el valor de la etiqueta")](menu-images/context03-large.png#lightbox)
2. A continuación arrastre una **menú** desde el **biblioteca Inspector** en el controlador de vista en la jerarquía de vista y cambiar el nombre de los tres elementos de menú de predeterminado **documento**, **texto**  y **fuente**:

    [![Los elementos de menú requiere](menu-images/context02.png "los elementos de menú necesarios")](menu-images/context02-large.png#lightbox)
3. Ahora control y arrastre desde el **propiedad etiqueta** en el **menú**:

    [![Arrastre para crear un segue](menu-images/context04.png "arrastrar para crear un segue")](menu-images/context04-large.png#lightbox)
4. En el cuadro de diálogo emergente, seleccione **menú**: 

    ![Establecer el tipo de segue](menu-images/context05.png "estableciendo el tipo de segue")
5. Desde el **identidad Inspector**, set (clase) del controlador de vista a "PanelViewController": 

    [![Configuración de la clase segue](menu-images/context10.png "configuración de la clase segue")](menu-images/context10-large.png#lightbox)
6. Vuelva a Visual Studio para Mac sincronizar y vuelva al generador de interfaz.
7. Cambie a la **Asistente Editor** y seleccione la **PanelViewController.h** archivo.
8. Crear una acción para el **documento** el elemento de menú denominado `propertyDocument`: 

    [![Configuración de la acción](menu-images/context06.png "configuración de la acción")](menu-images/context06-large.png#lightbox)
9. Repetir las acciones de creación de los elementos de menú restantes: 

    [![Las acciones necesarias](menu-images/context07.png "las acciones necesarias")](menu-images/context07-large.png#lightbox)
10. Finalmente, cree una toma de corriente para el **propiedad etiqueta** denominado `propertyLabel`: 

    [![Configuración de la toma de corriente](menu-images/context08.png "configurar la salida")](menu-images/context08-large.png#lightbox)
11. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Editar la **PanelViewController.cs** de archivos y agregue el código siguiente:

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

Ahora, si se ejecuta la aplicación y haga doble clic en la etiqueta de propiedad en el panel, veremos nuestra personalizado del menú contextual. Si se selecciona y elemento en el menú, cambiará el valor de la etiqueta:

![El menú contextual ejecutando](menu-images/context09.png "el menú contextual que se ejecuta")

Siguiente Echemos un vistazo a crear menús de la barra de estado.

## <a name="status-bar-menus"></a>Menús de la barra de estado

Los menús de la barra de estado muestran una colección de elementos de menú de estado que proporcionan la interacción con o comentarios para el usuario, como un menú o una imagen reflejar el estado de la aplicación. Menú de barra de estado de la aplicación está habilitado y active incluso si la aplicación se ejecuta en segundo plano. La barra de estado de todo el sistema reside en el lado derecho de la barra de menús de la aplicación y es la barra de estado sólo está disponible en Mac OS.

Editar nuestro **AppDelegate.cs** de archivos y realice la `DidFinishLaunching` método aspecto similar al siguiente:

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` nos da acceso a la barra de estado de todo el sistema. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` crea un nuevo elemento de barra de estado. Desde allí, se crea un menú y un número de elementos de menú y anexar el menú para el elemento de barra de estado que acabamos de crear. 

Si se ejecuta la aplicación, se mostrará el nuevo elemento de barra de estado. Seleccionar un elemento en el menú cambiará el texto en la vista de texto: 

![El menú de barra de estado de ejecución](menu-images/statusbar01.png "el menú de barra de estado de ejecución")

A continuación, echemos un vistazo a crear elementos de menú de acoplamiento personalizado.

## <a name="custom-dock-menus"></a>Menús personalizados de acoplamiento

Cuando el usuario hace clic con botón o icono de la aplicación de la base de clics de control, aparece el menú de acoplamiento para su aplicación de Mac:

![Un personalizado acoplar menú](menu-images/dock01.png "personalizado acoplar menú")

Vamos a crear un menú personalizado acoplar para nuestra aplicación haciendo lo siguiente:

1. En Visual Studio para Mac, haga doble clic en de la aplicación proyecto y seleccione **agregar** > **nuevo archivo...** En el cuadro de diálogo de archivo nuevo, seleccione **Xamarin.Mac** > **definición de interfaz vacía**, use "DockMenu" para el **nombre** y haga clic en el **nuevo**  botón para crear un nuevo **DockMenu.xib** archivo:

    ![Agregar una definición de interfaz vacía](menu-images/dock02.png "agregar una definición de interfaz vacía")
2. En el **solución Pad**, haga doble clic en el **DockMenu.xib** archivo para abrirlo y editarlo en Xcode. Crear un nuevo **menú** con los siguientes elementos: **dirección**, **fecha**, **saludo**, y **firma** 

    [![Diseñar la interfaz de usuario](menu-images/dock03.png "diseñar la interfaz de usuario")](menu-images/dock03-large.png#lightbox)
3. A continuación, vamos a conectar nuestros nuevos elementos de menú con nuestras acciones existentes que creamos para nuestro menú personalizado en la [agregar, editar y eliminar menús](#Adding,_Editing_and_Deleting_Menus) sección anterior. Cambie a la **conexión Inspector** y seleccione la **primer servicio de respuesta** en el **jerarquía de la interfaz**. Desplácese hacia abajo y busque el `phraseAddress:` acción. Arrastre una línea desde el círculo en esa acción en la **dirección** elemento de menú:

    [![Arrastrar para conectar una acción](menu-images/dock04.png "arrastrar para conectar una acción")](menu-images/dock04-large.png#lightbox)
4. Repetir para todos los demás elementos de menú adjuntarlos a sus acciones correspondientes: 

    [![Las acciones necesarias](menu-images/dock05.png "las acciones necesarias")](menu-images/dock05-large.png#lightbox)
5. A continuación, seleccione la **aplicación** en el **jerarquía de la interfaz**. En el **conexión Inspector**, arrastre una línea desde el círculo el `dockMenu` toma al menú que acabamos de crear:

    [![Arrastrar la conexión de la toma de corriente](menu-images/dock06.png "arrastrando el conectar la salida")](menu-images/dock06-large.png#lightbox)
6. Guardar los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.
7. Haga doble clic en el **Info.plist** archivo para abrirlo y editarlo: 

    [![Modificar el archivo info.plist](menu-images/dock07.png "Modificar el archivo info.plist")](menu-images/dock07-large.png#lightbox)
8. Haga clic en el **origen** ficha en la parte inferior de la pantalla: 

    [![Al seleccionar la vista del origen](menu-images/dock08.png "al seleccionar la vista del origen")](menu-images/dock08-large.png#lightbox)
9. Haga clic en **agregar nueva entrada**, haga clic en el botón de signo más verde, establezca el nombre de propiedad a "AppleDockMenu" y el valor en "DockMenu" (el nombre de nuestro nuevo archivo .xib sin la extensión): 

    [![Agregar el elemento DockMenu](menu-images/dock09.png "agregando el elemento DockMenu")](menu-images/dock09-large.png#lightbox)

Ahora si se ejecuta la aplicación y haga doble clic en su icono en el área, se mostrará nuestros nuevos elementos de menú:

![Un ejemplo del menú acoplar ejecutando](menu-images/dock10.png "muestra un ejemplo del menú acoplar ejecutando")

Si se selecciona uno de los elementos personalizados en el menú, el texto de la vista de texto se va a modificar.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Botón emergente y listas desplegables

Un botón emergente muestra un elemento seleccionado y presenta una lista de opciones para seleccionar entre cuando el usuario hace clic. Una lista desplegable es un tipo de botón emergente que normalmente se utiliza para seleccionar comandos específicos del contexto de la tarea actual. Puede aparecer en cualquier parte en una ventana.

Vamos a crear un botón emergente personalizado para nuestra aplicación haciendo lo siguiente:

1. Editar la **Main.storyboard** archivo en Xcode y arrastre un **botón emergente** desde el **biblioteca Inspector** en el **Panel** ventana hemos creado en el [menús contextuales](#Contextual_Menus) sección: 

    [![Agregar un botón de menú emergente](menu-images/popup01.png "agregar un botón de menú emergente")](menu-images/popup01-large.png#lightbox)
2. Agregar un nuevo elemento de menú y establecer los títulos de los elementos en el menú emergente para: **dirección**, **fecha**, **saludo**, y **firma** 

    [![Configuración de los elementos de menú](menu-images/popup02.png "configuración de los elementos de menú")](menu-images/popup02-large.png#lightbox)
3. A continuación, vamos a conectar nuestros nuevos elementos de menú con las acciones existentes que hemos creado para nuestro menú personalizado en la [agregar, editar y eliminar menús](#Adding,_Editing_and_Deleting_Menus) sección anterior. Cambie a la **conexión Inspector** y seleccione la **primer servicio de respuesta** en el **jerarquía de la interfaz**. Desplácese hacia abajo y busque el `phraseAddress:` acción. Arrastre una línea desde el círculo en esa acción en la **dirección** elemento de menú: 

    [![Arrastrar para conectar una acción](menu-images/popup03.png "arrastrar para conectar una acción")](menu-images/popup03-large.png#lightbox)
4. Repetir para todos los demás elementos de menú adjuntarlos a sus acciones correspondientes: 

    [![Todas las operaciones necesarias](menu-images/popup04.png "todas las operaciones necesarias")](menu-images/popup04-large.png#lightbox)
5. Guardar los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Ahora si se ejecuta la aplicación y seleccione un elemento de la ventana emergente, cambiará el texto de la vista de texto:

![Un ejemplo de la ventana emergente ejecutando](menu-images/popup05.png "muestra un ejemplo de la ventana emergente de ejecución")

Puede crear y trabajar con listas desplegables de la misma manera como botones emergente. En lugar de adjuntar a la acción existente, puede crear sus propias acciones personalizadas al igual que hicimos con el menú contextual en el [menús contextuales](#Contextual_Menus) sección.

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con menús y elementos de menú en una aplicación Xamarin.Mac. En primer lugar, examinamos barra de menús de la aplicación, a continuación, explicamos crear menús contextuales, a continuación, examinamos los menús de la barra de estado y personalizado acoplar los menús. Por último, se tratan los menús emergentes y listas desplegables.


## <a name="related-links"></a>Vínculos relacionados

- [MacMenus (ejemplo)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Directrices de interfaz humana - menús](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Introducción a los menús de la aplicación y listas emergentes](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
