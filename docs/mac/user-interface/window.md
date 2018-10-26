---
title: Windows en Xamarin.Mac
description: En este artículo se explica cómo trabajar con ventanas y paneles en una aplicación de Xamarin.Mac. Describe la creación de ventanas y paneles en Xcode e Interface Builder, cargarlos desde los guiones gráficos y archivos .xib y trabajar con ellos mediante programación.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 2d129c72366224cedca26df6fa1499f65d04e92d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106980"
---
# <a name="windows-in-xamarinmac"></a>Windows en Xamarin.Mac

_En este artículo se explica cómo trabajar con ventanas y paneles en una aplicación de Xamarin.Mac. Describe la creación de ventanas y paneles en Xcode e Interface Builder, cargarlos desde los guiones gráficos y archivos .xib y trabajar con ellos mediante programación._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, tener acceso a la misma Windows y paneles que un desarrollador que trabaje en *Objective-C* y *Xcode* does. Ya que Xamarin.Mac se integra directamente con Xcode, puede usar Xcode _Interface Builder_ para crear y mantener sus paneles y Windows (u opcionalmente crearlas directamente en código de C#).

Según su propósito, una aplicación de Xamarin.Mac puede presentar uno o varios de Windows en la pantalla para administrar y coordinar la información se muestra y funciona con. Las funciones principales de una ventana son:

1. Para proporcionar un área en la que las vistas y los controles se pueden colocar y administrar.
2. Para aceptar y responder a eventos en respuesta a la interacción del usuario con el teclado y el mouse.

Windows pueden ser utilizado en un estado no modal (por ejemplo, un editor de texto que puede tener varios documentos abiertos a la vez) o Modal (por ejemplo, un cuadro de diálogo de exportación que se debe descartar para que pueda continuar la aplicación).

Los paneles son un tipo especial de ventana (una subclase de la base de `NSWindow` clase), que suelen servir una función auxiliar en una aplicación, como ventanas de utilidad, como los inspectores de formato de texto y el selector de colores del sistema.

[![](window-images/intro01.png "Edición de una ventana en Xcode")](window-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos de trabajar con Windows y paneles en una aplicación de Xamarin.Mac. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` comandos se usa para conexión de seguridad de las clases de C# para objetos de Objective-C y elementos de interfaz de usuario.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introducción a Windows

Como se indicó anteriormente, una ventana proporciona un área en la que las vistas y los controles se pueden colocar y administrar y responde a eventos en función de interacción del usuario (ya sea mediante el teclado o mouse).

Función de Apple, hay cinco tipos principales de Windows en una aplicación de macOS:

- **Ventana de documento** -una ventana de documento contiene datos de usuario basados en archivos como una hoja de cálculo o un documento de texto.
- **Ventana de la aplicación** -una ventana de la aplicación es la ventana principal de una aplicación que no está basado en un documento (por ejemplo, la aplicación de calendario en un equipo Mac).
- **Panel** : un panel de flota sobre otras ventanas y proporciona herramientas o abrir los controles que los usuarios pueden trabajar con mientras haya documentos. En algunos casos, un panel puede estar translúcido (por ejemplo, cuando trabaja con gráficos de gran tamaño).
- **Cuadro de diálogo** -un cuadro de diálogo aparece en respuesta a una acción del usuario y normalmente se proporciona a los usuarios de maneras pueden completar la acción. Un cuadro de diálogo requiere una respuesta del usuario para que se pueda cerrar. (Consulte [trabajar con cuadros de diálogo](~/mac/user-interface/dialog.md))
- **Alertas** -una alerta es un tipo especial de cuadro de diálogo que aparece cuando se produce un problema grave (por ejemplo, un error) o como una advertencia (como preparación eliminar un archivo). Dado que una alerta es un cuadro de diálogo, también requiere una respuesta del usuario para que se pueda cerrar. (Consulte [trabajar con alertas](~/mac/user-interface/alert.md))

Para obtener más información, consulte el [sobre Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Principal, clave y Windows inactiva

Windows en una aplicación de Xamarin.Mac pueden buscar y se comportan de forma distinta según cómo el usuario actualmente interactúa con ellos. El documento o ventana de la aplicación que se encuentra actualmente el foco de atención del usuario, la más importante que se llama a la _ventana principal_. En la mayoría de los casos esta ventana también será el _ventana clave_ (la ventana que actualmente está aceptando proporcionados por el usuario). Pero esto no siempre es así, por ejemplo, un selector de colores podría estar abierto y la ventana de la clave que el usuario está interactuando con para cambiar el estado de un elemento en la ventana de documento (que todavía sería la ventana principal).

El principal y clave de Windows (si son independientes) siempre están activas, _Windows inactiva_ son ventanas abiertas que no están en primer plano. Por ejemplo, una aplicación de editor de texto puede tener más de un documento abierto en un momento, solo la ventana principal está activa, todos los demás estarán inactivos. 

Para obtener más información, consulte el [sobre Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Nomenclatura de Windows

Una ventana puede mostrar una barra de título y cuando se muestra el título, normalmente es el nombre de la aplicación, el nombre del documento que se está trabajando o la función de la ventana (por ejemplo, el Inspector). Algunas aplicaciones no mostrar una barra de título porque son reconocibles por visión y no funcionan con documentos.

Apple sugiere las siguientes directrices:

- Utilice el nombre de la aplicación para el título de una ventana principal, que no sean documentos. 
- Nombre de una nueva ventana de documento `untitled`. Para el nuevo documento, no anexar un número en el título (como `untitled 1`). Si el usuario crea otro documento antes de guardar y título de la primera, llamar a esa ventana `untitled 2`, `untitled 3`, etcetera.

Para obtener más información, consulte el [Windows nomenclatura](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Windows de pantalla completa

En macOS, ventana de la aplicación puede ir ocultación de pantalla completa todo incluido en la barra de menús de la aplicación (que puede mostrarse al mover el cursor a la parte superior de la pantalla) para proporcionar la distracción es contenido gratuito interacción con él.

Apple sugiere las siguientes directrices:

- Determine si tiene sentido para una ventana Ir a pantalla completa. Las aplicaciones que proporcionan las interacciones breves (por ejemplo, una calculadora) no deben proporcionar un modo de pantalla completa.
- Si la tarea de pantalla completa requiere se muestra la barra de herramientas. Normalmente se oculta la barra de herramientas mientras esté en modo de pantalla completa.
- La ventana de pantalla completa debe tener todas las funciones que los usuarios necesitan completar la tarea.
- Si es posible, evite la interacción de Finder mientras el usuario está en una ventana de pantalla completa.
- Aproveche el espacio de pantalla mayor sin desplazar la atención lejos de la tarea principal.

Para obtener más información, consulte el [Windows de pantalla completa](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>Paneles

Un Panel es una ventana auxiliar que contiene los controles y las opciones que afectan a la selección (por ejemplo, el selector de colores del sistema) o el documento activo:

[![](window-images/panel01.png "Un panel de colores")](window-images/panel01.png#lightbox)

Los paneles pueden ser _específico de la aplicación_ o _todo el sistema_. Paneles específicos de la aplicación flotar encima de las ventanas de documento de la aplicación y desaparecerá cuando la aplicación está en segundo plano. Paneles de todo el sistema (como el **fuentes** panel), float encima de todas las ventanas abiertas con independencia de la aplicación. 

Apple sugiere las siguientes directrices:

- En general, utilice un panel estándar, paneles transparentes solo deben usarse con moderación y para las tareas con muchos gráficos.
- Considere el uso de un panel para proporcionar a los usuarios un acceso sencillo a controles importantes o información que afecta directamente a su tarea.
- Ocultar y mostrar paneles según sea necesario.
- Paneles siempre deben incluir la barra de título.
- Los paneles no deben incluir un botón Minimizar activo.

#### <a name="inspectors"></a>Inspectores

Las aplicaciones más modernas de macOS presentan controles auxiliares y las opciones que afectan al documento activo o selección como _inspectores_ que forman parte de la ventana principal (como el **páginas** aplicación se muestra a continuación), en lugar de usar el Panel de Windows:

[![](window-images/panel02.png "Un inspector de ejemplo")](window-images/panel02.png#lightbox)

Para obtener más información, consulte el [paneles](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) y nuestro [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) aplicación de ejemplo para una implementación completa de un **Inspector interfaz** en una aplicación de Xamarin.Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Creación y mantenimiento de Windows en Xcode

Cuando se crea una nueva aplicación de Xamarin.Mac Cocoa, obtendrá una ventana en blanco, estándar de forma predeterminada. Este windows se define en un `.storyboard` archivo incluido automáticamente en el proyecto. Para editar el diseño de windows, en el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo:

[![](window-images/edit01.png "Seleccione el guion gráfico principal")](window-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en Interface Builder de Xcode:

[![](window-images/edit02.png "Edición de la interfaz de usuario en Xcode")](window-images/edit02.png#lightbox)

En el **Inspector de atributos**, hay varias propiedades que puede usar para definir y controlar la ventana:

- **Título** -éste es el texto que se mostrará en la barra de título de la ventana.
- **Autoguardar** -se trata la _clave_ que se utilizará para el Id. de la ventana cuando se guarda automáticamente su posición y configuración.
- **Barra de título** -la ventana Mostrar una barra de título.
- **Unified título y la barra de herramientas** : si la ventana incluye una barra de herramientas, debe formar parte de la barra de título.
- **Tamaño de vista de contenido completa** -permite que el área de contenido de la ventana para estar bajo la barra de título.
- **Sombra** -la ventana tiene una sombra.
- **Con textura** -con textura windows pueden usar efectos (por ejemplo, el dinamismo) y se pueden mover arrastrando en cualquier lugar en su cuerpo.
- **Cerrar** -la ventana tiene un botón Cerrar.
- **Minimizar** -la ventana tiene un botón Minimizar.
- **Cambiar el tamaño de** -la ventana tiene un control de cambio de tamaño.
- **Botón de barra de herramientas** -la ventana tiene un botón de barra de herramientas mostrar u ocultar.
- **Que se pueden restaurar** -es la posición y la configuración automáticamente, guardar y restaurar la ventana.
- **Visible al iniciar** -la ventana aparece automáticamente cuando el `.xib` se carga el archivo.
- **Ocultar en desactivar** -la ventana está oculto cuando la aplicación entra en segundo plano.
- **Liberar cuando cierra** -es la ventana de purga de la memoria cuando está cerrado.
- **Siempre mostrar informaciones sobre herramientas** -son la información sobre herramientas muestre constantemente.
- **Vuelve a calcular el bucle de vista** -es el orden de la vista a calcular antes de que se dibuja la ventana.
- **Espacios**, **Exposé** y **Cycling** -todos los definen cómo se comporta la ventana en esos entornos de macOS. 
- **Pantalla completa** -determina si esta ventana puede entrar en el modo de pantalla completa. 
- **Animación** -controla el tipo de animación disponible para la ventana.
- **Apariencia** -controla la apariencia de la ventana. Por ahora, hay solo un aspecto, aguamarina.

Consulte Apple [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) y [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) documentación para obtener más detalles.

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Establecer la ubicación y tamaño predeterminados

Para establecer la posición inicial de la ventana y controlar su tamaño, cambie a la **Inspector de tamaño**:

[![](window-images/edit07.png "La ubicación y tamaño predeterminados")](window-images/edit07.png#lightbox)

Desde aquí puede establecer el tamaño inicial de la ventana, asígnele un tamaño máximo y mínimo, establecer la ubicación inicial en la pantalla y los bordes alrededor de la ventana de control.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Configuración de un controlador de ventana principal personalizado

Para poder crear salidas y acciones para exponer elementos de interfaz de usuario al código de C#, la aplicación de Xamarin.Mac se deberá usar un controlador de ventana personalizada.

Haga lo siguiente:

1. Abra el guión gráfico de la aplicación en Interface Builder de Xcode.
2. Seleccione el `NSWindowController` en la superficie de diseño.
3. Cambie a la **Inspector de identidad** permite ver y especificar `WindowController` como el **nombre de la clase**: 

    [![](window-images/windowcontroller01.png "Establecer el nombre de clase")](window-images/windowcontroller01.png#lightbox)
4. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar.
5. Un `WindowController.cs` archivo se agregará al proyecto en el **el Explorador de soluciones** en Visual Studio para Mac: 

    [![](window-images/windowcontroller02.png "Al seleccionar el controlador de windows")](window-images/windowcontroller02.png#lightbox)
6. Vuelva a abrir el guión gráfico de Interface Builder de Xcode.
7. El `WindowController.h` archivo estará disponible para su uso: 

    [![](window-images/windowcontroller03.png "Edite el archivo WindowController.h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Agregar elementos de interfaz de usuario

Para definir el contenido de una ventana, arrastre controles desde el **Inspector de biblioteca** hasta la **Editor de la interfaz**. Consulte nuestra [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) documentación para obtener más información sobre cómo usar Interface Builder para crear y habilitar los controles.

Por ejemplo, vamos a arrastrar una barra de herramientas desde el **Inspector de biblioteca** en la ventana en la **Editor de la interfaz**:

[![](window-images/edit03.png "Selección de una barra de herramientas de la biblioteca")](window-images/edit03.png#lightbox)

A continuación, arrastre un **vista de texto** y ajuste su tamaño para rellenar el área bajo la barra de herramientas:

[![](window-images/edit04.png "Agregar una vista de texto")](window-images/edit04.png#lightbox)

Puesto que deseamos que el **vista de texto** para reducir y crecer según los cambios de tamaño de la ventana, vamos a pasar a la **Editor de restricciones** y agregue las siguientes restricciones:

[![](window-images/edit05.png "Limitaciones de edición")](window-images/edit05.png#lightbox)

Al hacer clic en el para **Red I-Beams** en la parte superior del editor y haga clic en **agregar 4 restricciones**, indicamos a la vista de texto para ajustarse a la X dado, Y las coordenadas y aumentar o reducir horizontalmente y verticalmente como se cambia el tamaño de la ventana.

Por último, vamos a exponer el **vista de texto** codificar mediante un **toma** (asegurándose de seleccionar la `ViewController.h` archivo):

[![](window-images/edit06.png "Configurar una salida")](window-images/edit06.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Para obtener más información sobre cómo trabajar con **salidas** y **acciones**, consulte nuestra [toma de corriente y acción](~/mac/get-started/hello-mac.md#outlets-and-actions) documentación.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Flujo de trabajo de ventana estándar

Cualquier ventana que permite crear y trabajar con en la aplicación de Xamarin.Mac, el proceso es básicamente igual que lo que hemos hecho simplemente anteriormente:

1. Para las nuevas ventanas que no son los predeterminados que se agregan automáticamente al proyecto, agregue una nueva definición de ventana al proyecto. Esto se explicará en detalle a continuación.
2. Haga doble clic en el `Main.storyboard` archivo para abrir el diseño de ventana para su edición en Interface Builder de Xcode.
3. Arrastre una nueva ventana de diseño de la interfaz de usuario y de enlace de la ventana en la ventana principal del sistema mediante _objetos Segue_ (para obtener más información, consulte el [objetos Segue](~/mac/platform/storyboards/indepth.md#Segues) sección de nuestra [trabajar con guiones gráficos](~/mac/platform/storyboards/indepth.md) documentación).
3. Establezca las propiedades de ventana necesaria el **Inspector de atributos** y **Inspector de tamaño**.
4. Arrastre los controles necesarios para crear la interfaz y configurarlos en el **Inspector de atributos**.
5. Use la **Inspector de tamaño** para controlar el cambio de tamaño para los elementos de interfaz de usuario.
6. Exponer elementos de interfaz de usuario de la ventana al código de C# a través de **salidas** y **acciones**.
7. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Ahora que tenemos una ventana básica creada, se examinarán los procesos típicos un Xamarin.Mac hace la aplicación cuando se trabaja con windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Mostrar la ventana predeterminada

De forma predeterminada, una nueva aplicación de Xamarin.Mac mostrará automáticamente la ventana definida en el `MainWindow.xib` archivo cuando se inicia:

[![](window-images/display01.png "Una ventana de ejemplo que ejecuta")](window-images/display01.png#lightbox)

Puesto que hemos modificado el diseño de ventana anterior, ahora incluye una barra de herramientas predeterminado y **vista de texto** control. La siguiente sección en la `Info.plist` es responsable de mostrar esta ventana de archivo:

[![](window-images/display00.png "Edición de Info.plist")](window-images/display00.png#lightbox)

El **interfaz principal** lista desplegable se utiliza para seleccionar el guión gráfico que se usará como la interfaz de usuario de la aplicación principal (en este caso `Main.storyboard`).

Un controlador de vista se agrega automáticamente al proyecto para controlar ese Windows principal que se muestra (junto con su vista principal). Se define en el `ViewController.cs` detectados y se adjuntan a los **propietario del archivo** en Interface Builder en la **Inspector de identidad**:

[![](window-images/display02.png "Establecer el propietario del archivo")](window-images/display02.png#lightbox)

Para la ventana, nos gustaría que tenga un título de `untitled` cuando lo abra por primera vez así que vamos a reemplazar el `ViewWillAppear` método en el `ViewController.cs` para ser similar al siguiente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> Estamos configurando el valor de la ventana `Title` propiedad en el `ViewWillAppear` método en lugar de la `ViewDidLoad` método porque, mientras que la vista se puede cargar en memoria, no todavía totalmente creada la instancia. Si se ha intentado obtener acceso a la `Title` propiedad en el `ViewDidLoad` método obtendríamos un `null` excepción puesto que la ventana no se ha construido y con cable de seguridad para la propiedad aún.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Al cerrar una ventana mediante programación

Puede haber ocasiones en que desea cerrar mediante programación una ventana en una aplicación de Xamarin.Mac, aparte de tener el usuario, haga clic en la ventana **cerrar** botón o usar un elemento de menú. macOS proporciona dos maneras diferentes para cerrar un `NSWindow` mediante programación: `PerformClose` y `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

Una llamada a la `PerformClose` método de un `NSWindow` simula el usuario hace clic en la ventana **cerrar** botón momentáneamente resalta el botón y, a continuación, cierre la ventana.

Si la aplicación se implementa el `NSWindow`del `WillClose` eventos, se producirá antes de cerrar la ventana. Si el evento devuelve `false`, entonces no se cerrará la ventana. Si la ventana no tiene un **cerrar** botón o no se puede cerrar por cualquier motivo, el sistema operativo emitirá el sonido de alerta.

Por ejemplo:

```csharp
MyWindow.PerformClose(this);
```

Intente cerrar la `MyWindow` `NSWindow` instancia. Si se realizó correctamente, se cerrará la ventana, lo contrario, se emitirá el sonido de alerta y usará permanecerá abierta.

<a name="Close" />

### <a name="close"></a>Cerrar

Una llamada a la `Close` método de un `NSWindow` no simula el usuario hace clic en la ventana **cerrar** botón momentáneamente, resaltando el botón, simplemente cierra la ventana.

Una ventana no tiene que ser visibles para cerrarse y un `NSWindowWillCloseNotification` notificación se publicará en el centro de notificaciones predeterminado para la ventana que se va a cerrar.

El `Close` método difiere en dos aspectos importantes de la `PerformClose` método:

1. No intenta generar el `WillClose` eventos.
2. No simula el usuario hace clic en el **cerrar** botón momentáneamente resaltando el botón.

Por ejemplo:

```csharp
MyWindow.Close();
```

Para cerrar el `MyWindow` `NSWindow` instancia.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Contenido modificado de Windows

En macOS, Apple ha proporcionado una manera de informar al usuario que el contenido de una ventana (`NSWindow`) se ha modificado por el usuario y debe guardarse. Si la ventana contiene contenido modificado, se mostrará un pequeño punto negro en su **cerrar** widget:

[![](window-images/close01.png "Una ventana con el marcador de modificado")](window-images/close01.png#lightbox)

Si el usuario intenta cerrar la ventana o salir de la aplicación de Mac, aunque hay que no haya guardado los cambios en el contenido de la ventana, debe presentar un [cuadro de diálogo](~/mac/user-interface/dialog.md) o [hoja Modal](~/mac/user-interface/dialog.md) y permitir al usuario guardar los cambios realizados primera:

[![](window-images/close02.png "Guardar hoja se muestra cuando se cierra la ventana")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Marcar una ventana como modificado

Para marcar una ventana como la necesidad de modificar el contenido, use el código siguiente:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

Y una vez que se ha guardado el cambio, desactive la marca modificada utilizando:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Guardar los cambios antes de cerrar una ventana

Para ver el usuario al cerrar una ventana y se le permite guardar contenido modificado con antelación, deberá crear una subclase de `NSWindowDelegate` e invalidar sus `WindowShouldClose` método. Por ejemplo:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWindowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on resu;t
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

Usar el código siguiente para asociar una instancia de este delegado a la ventana:

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Guardar los cambios antes de cerrar la aplicación

Por último, debe comprobar su App Xamarin.Mac para ver si alguno de sus Windows contienen contenido modificado y permitir al usuario guardar los cambios antes de salir. Para ello, edite su `AppDelegate.cs` de archivo, reemplace el `ApplicationShouldTerminate` método y darle un aspecto similar al siguiente:

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>Trabajar con varios Windows

Mayoría de las aplicaciones Mac en función de documento puede editar varios documentos al mismo tiempo. Por ejemplo, un editor de texto puede tener varios archivos de texto Abrir para editarlo al mismo tiempo. De forma predeterminada, nuestra nueva aplicación de Xamarin.Mac tiene un **archivo** menú con un **New** elemento automáticamente por cable de seguridad para el `newDocument:` **acción**.

Vamos a activar este nuevo elemento y permite al usuario abrir varias copias de nuestra ventana principal para editar varios documentos a la vez.

Vamos a editar nuestro `AppDelegate.cs` archivo y agregue la siguiente propiedad calculada:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Vamos a usar para realizar un seguimiento del número de archivos no guardados por lo que podemos proporcionar comentarios al usuario (según las directrices de Apple según se explicó anteriormente).

A continuación, agregue el siguiente método:

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Este código crea una nueva versión de nuestro controlador de ventana, carga la nueva ventana, hace que sea el principal y ventana de la clave y lo establece el título. Ahora, si se ejecute la aplicación y seleccione **New** desde el **archivo** menú se abre y se mostrará una nueva ventana del editor:

[![](window-images/display04.png "Se ha agregado una nueva ventana sin título")](window-images/display04.png#lightbox)

Si se abre el **Windows** menú, puede ver la aplicación se seguimiento y controlar nuestra ventanas abiertas automáticamente:

[![](window-images/display05.png "El menú de windows")](window-images/display05.png#lightbox)

Para obtener más información sobre cómo trabajar con menús en una aplicación de Xamarin.Mac, consulte nuestra [trabajar con menús](~/mac/user-interface/menu.md) documentación.

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Introducción a la ventana activa

En una aplicación de Xamarin.Mac que puede abrir varias ventanas (documentos), hay veces cuando necesite hacer que la ventana actual, de nivel superior (la ventana de claves). El código siguiente devuelve la ventana de clave:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Se puede llamar en cualquier clase o método que debe tener acceso a la ventana actual, la clave. Si no hay ninguna ventana está abierta, devolverá `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>Obtener acceso a todos los de aplicación de Windows

Puede haber ocasiones donde necesite tener acceso a todas las ventanas que la aplicación de Xamarin.Mac tiene actualmente abierto. Por ejemplo ver si un archivo que el usuario desea abrir ya está abierto en una ventana de salida.

El `NSApplication.SharedApplication` mantiene un `Windows` propiedad que contiene una matriz de todas las ventanas abiertas en la aplicación. Puede recorrer en iteración esta matriz para tener acceso a todas las ventanas activas de la aplicación. Por ejemplo:

```csharp
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

En el ejemplo de código se van a convertir cada ventana devuelta a la personalizada `ViewController` clase en nuestra aplicación y la comprobación del valor de un personalizado `Path` propiedad con respecto a la ruta de acceso de un archivo que el usuario desea abrir. Si el archivo ya está abierto, se la llevamos esa ventana al frente.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Ajustar el tamaño de ventana de código

Hay veces cuando la aplicación necesita cambiar el tamaño de una ventana de código. Para cambiar el tamaño y posición de una ventana, ajustar de `Frame` propiedad. Al ajustar el tamaño de una ventana, normalmente deberá ajustar también su origen, para mantener la ventana en la misma ubicación debido a sistema de coordenadas de macOS.

A diferencia de iOS en la esquina superior izquierda representa (0,0), macOS usa un sistema de coordenadas matemáticos donde la esquina inferior izquierda de la pantalla representa (0,0). En iOS las coordenadas de aumentan la medida que se desplaza hacia abajo, hacia la derecha. En macOS, las coordenadas aumenten en valores hacia arriba a la derecha. 

Ejemplo de código siguiente cambia el tamaño de una ventana:

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> Al ajustar un tamaño de windows y la ubicación en el código, deberá asegurarse de que respetan los tamaños mínimos y máximo que se configuraron en el generador de interfaz. Esto no se aplicarán automáticamente y podrá hacer que la ventana mayores o menores que estos límites.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Supervisión de los cambios de tamaño de ventana

Puede haber ocasiones en que deba supervisar los cambios en el tamaño de una ventana dentro de una aplicación de Xamarin.Mac. Por ejemplo, para volver a dibujar el contenido para ajustarse al tamaño nuevo.

Para supervisar los cambios de tamaño, en primer lugar asegúrese de que haya asignado una clase personalizada para el controlador de ventana en Interface Builder de Xcode. Por ejemplo, `MasterWindowController` en lo siguiente:

[![](window-images/resize01.png "El Inspector de identidad")](window-images/resize01.png#lightbox)

A continuación, modifique la clase de controlador de ventana personalizados y el monitor del `DidResize` eventos en la ventana del controlador para recibir una notificación de cambios de tamaño en vivo. Por ejemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Si lo desea, puede usar el `DidEndLiveResize` evento solo se recibirá una notificación cuando el usuario ha terminado de cambiar el tamaño de la ventana. Por ejemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>Configuración de una ventana del título y representa el archivo

Cuando se trabaja con windows que representan documentos, `NSWindow` tiene un `DocumentEdited` propiedad que si se establece en `true` muestra un pequeño punto en el botón Cerrar para proporcionar al usuario una indicación de que el archivo se ha modificado y debe guardarse antes de cerrarse.

Vamos a editar nuestro `ViewController.cs` de archivos y realice los cambios siguientes:

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

También se están supervisando el `WillClose` eventos en la ventana y la comprobación del estado de la `DocumentEdited` propiedad. Si es `true` necesitamos proporcionar al usuario la capacidad de guardar los cambios en el archivo. Si se ejecuta nuestra aplicación y escriba algún texto, se mostrará el punto:

[![](window-images/file01.png "Una ventana modificada")](window-images/file01.png#lightbox)

Si se intenta cerrar la ventana, obtenemos una alerta:

[![](window-images/file02.png "Mostrar una operación de guardar cuadro de diálogo")](window-images/file02.png#lightbox)

Si estamos cargando un documento desde un archivo podemos establecer el título de la ventana en el archivo con el nombre del `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` método (dado que `path` es una cadena que representa el archivo está abierto). Además, podemos establecer la dirección URL del archivo con el `window.RepresentedUrl = url;` método.

Si la dirección URL apunta a un tipo de archivo conocido por el sistema operativo, su icono se mostrará en la barra de título. Si el usuario hace clic en el icono, se mostrará la ruta de acceso al archivo.

Vamos a editar el `AppDelegate.cs` archivo y agregue el siguiente método:

```csharp
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
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

Ahora, si ejecutamos nuestra aplicación, seleccione **abrir...**  desde el **archivo** menú, seleccione un archivo de texto desde el **abrir** diálogo cuadro y ábralo:

[![](window-images/file03.png "Un cuadro de diálogo Abrir")](window-images/file03.png#lightbox)

Se mostrará el archivo y el título se establecerá con el icono del archivo:

[![](window-images/file04.png "El contenido de un archivo cargado")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Agregar una nueva ventana a un proyecto

Aparte de la ventana de documento principal, una aplicación de Xamarin.Mac posible que deba mostrar otros tipos de windows para el usuario, como las preferencias o Inspector de paneles.

Para agregar una nueva ventana, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en Interface Builder de Xcode.
2. Arrastre una nueva **controlador de ventana** desde el **biblioteca** y colóquela en la **superficie de diseño**:

    [![](window-images/new01.png "Seleccionar un nuevo controlador de ventana en la biblioteca")](window-images/new01.png#lightbox)
3. En el **Inspector de identidad**, escriba `PreferencesWindow` para el **identificador de guión gráfico**: 

    [![](window-images/new02.png "Establecer el identificador de guión gráfico")](window-images/new02.png#lightbox)
5. Diseñar la interfaz: 

    [![](window-images/new03.png "Diseñar la interfaz de usuario")](window-images/new03.png#lightbox)
6. Abra el menú de la aplicación (`MacWindows`), seleccione **preferencias...** , Control y haga clic y arrastre a la nueva ventana: 

    [![](window-images/new05.png "Creación de un segue")](window-images/new05.png#lightbox)
7. Seleccione **mostrar** en el menú emergente.
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Si se ejecuta el código y seleccione el **preferencias...**  desde el **menú aplicación**, se mostrará la ventana:

[![](window-images/new04.png "Un menú preferencias de ejemplo")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Trabajar con paneles

Como se indicó al principio de este artículo, un panel flota sobre otras ventanas y proporciona las herramientas o los controles que los usuarios pueden trabajar con mientras haya documentos abiertos. 

Al igual que cualquier otro tipo de ventana que permite crear y trabajar con en la aplicación de Xamarin.Mac, el proceso es básicamente el mismo:

1. Agregar una nueva definición de ventana al proyecto.
2. Haga doble clic en el `.xib` archivo para abrir el diseño de ventana para su edición en Interface Builder de Xcode.
2. Establezca las propiedades de ventana necesaria el **Inspector de atributos** y **Inspector de tamaño**.
4. Arrastre los controles necesarios para crear la interfaz y configurarlos en el **Inspector de atributos**.
5. Use la **Inspector de tamaño** para controlar el cambio de tamaño para los elementos de interfaz de usuario.
6. Exponer elementos de interfaz de usuario de la ventana al código de C# a través de **salidas** y **acciones**.
7. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

En el **Inspector de atributos**, tiene las siguientes opciones específicas de paneles:

[![](window-images/panel03.png "El Inspector de atributos")](window-images/panel03.png#lightbox)

- **Estilo** -permiten ajustar el estilo del panel de: Panel Regular (parece una ventana estándar), Panel de utilidad (tiene una barra de título más pequeña), Panel HUD (es translúcido y la barra de título es parte del fondo).
- **Activación no** -determina en el panel se convierte en la ventana de la clave.
- **Documentar Modal** -si documento Modal, el panel solo estará por encima de windows de la aplicación, else flota por encima de todo.


Para agregar un nuevo Panel, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...** .
2. En el cuadro de diálogo nuevo archivo, seleccione **Xamarin.Mac** > **ventana de Cocoa con controlador**:

    [![](window-images/panels00.png "Agregar un nuevo controlador de ventana")](window-images/panels00.png#lightbox)
3. Escriba `DocumentPanel` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el `DocumentPanel.xib` archivo para abrirlo y editarlo en Interface Builder: 

    [![](window-images/new02.png "Edición del panel")](window-images/new02.png#lightbox)
5. Eliminar la ventana existente y arrastre un Panel desde el **Inspector de biblioteca** en el **Editor de la interfaz**: 

    [![](window-images/panels01.png "Eliminación de la ventana existente")](window-images/panels01.png#lightbox)
6. Enlazar el panel hasta el **propietario del archivo** - **ventana** - **toma**: 

    [![](window-images/panels02.png "Arrastrar para crear el panel")](window-images/panels02.png#lightbox)
7. Cambie a la **Inspector de identidad** y set (clase) del Panel `DocumentPanel`: 

    [![](window-images/panels03.png "Configuración de clase del panel")](window-images/panels03.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.
7. Editar el `DocumentPanel.cs` de archivo y cambie la definición de clase a lo siguiente: 

    `public partial class DocumentPanel : NSPanel`
8. Guarde los cambios en el archivo.

Editar el `AppDelegate.cs` y realice la `DidFinishLaunching` método aspecto parecido al siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Si ejecutamos nuestra aplicación, se mostrará el panel:

[![](window-images/panels04.png "El panel en una aplicación en ejecución")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Panel de Windows han quedado en desuso por Apple y debe reemplazarse por **Interfaces del Inspector de**. Para obtener un ejemplo completo de creación de un **Inspector** en una aplicación de Xamarin.Mac, consulte nuestra [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) aplicación de ejemplo.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con Windows y paneles en una aplicación de Xamarin.Mac. Se ha visto los distintos tipos y usos de Windows y los paneles, cómo crear y mantener Windows y paneles en Interface Builder de Xcode y cómo trabajar con Windows y los paneles en código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacWindows (ejemplo)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector (ejemplo)](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con menús](~/mac/user-interface/menu.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
