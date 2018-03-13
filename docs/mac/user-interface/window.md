---
title: Windows
description: "Este artículo explica cómo trabajar con ventanas y paneles en una aplicación Xamarin.Mac. Se describen la creación de ventanas y paneles en Xcode y el generador de interfaz, cargarlos de guiones gráficos y archivos de .xib y trabajar con ellos mediante programación."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: f483fcfa9dfca1eb476ceab2b67e7a03bf4b6354
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="windows"></a>Windows

_Este artículo explica cómo trabajar con ventanas y paneles en una aplicación Xamarin.Mac. Se describen la creación de ventanas y paneles en Xcode y el generador de interfaz, cargarlos de guiones gráficos y archivos de .xib y trabajar con ellos mediante programación._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tener acceso a las mismas ventanas y paneles que un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener su ventanas y paneles (o si lo desea crearlos directamente en código de C#).

En función de su objetivo, una aplicación de Xamarin.Mac puede presentar una o varias ventanas en la pantalla para administrar y coordinar la información se muestra y funciona con. Las funciones principales de windows son:

1. Para proporcionar un área en las vistas y los controles puede colocarse y administrado.
2. Para aceptar y responder a eventos en respuesta a la interacción del usuario con el teclado y el mouse.

Windows puede ser usado en un estado no modal (por ejemplo, un editor de texto que puede tener varios documentos abiertos a la vez) o Modal (por ejemplo, un cuadro de diálogo de exportación que se debe descartar para que pueda continuar la aplicación).

Los paneles son un tipo especial de ventana (una subclase de la base de `NSWindow` clase), que normalmente no sirven para una función auxiliar en una aplicación, como las ventanas de utilidad como inspectores de formato de texto y el selector de colores del sistema.

[![](window-images/intro01.png "Edición de una ventana en Xcode.")](window-images/intro01.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con ventanas y paneles en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Introducción a Windows

Como se mencionó anteriormente, una ventana proporciona un área en la que vistas y los controles pueden colocarse y administra y responde a los eventos en función de interacción del usuario (ya sea mediante un teclado o mouse).

Función de Apple, hay cinco tipos principales de Windows en una aplicación de Mac OS:

- **Ventana de documento** -una ventana de documento contiene datos de usuario basada en archivos, como una hoja de cálculo o un documento de texto.
- **Ventana de la aplicación** -una ventana de aplicación es la ventana principal de una aplicación que no está basado en un documento (por ejemplo, la aplicación de calendario en un equipo Mac).
- **Panel** : un panel queda flotando sobre otras ventanas y proporciona herramientas o abrir controles que los usuarios pueden trabajar con mientras haya documentos. En algunos casos, un panel puede ser translúcido (como al trabajar con gráficos de gran tamaño).
- **Cuadro de diálogo** -un cuadro de diálogo aparece en respuesta a una acción del usuario y normalmente proporciona a los usuarios de maneras pueden completar la acción. Un cuadro de diálogo requiere una respuesta del usuario antes de poder cerrar. (Consulte [trabajar con cuadros de diálogo](~/mac/user-interface/dialog.md))
- **Alertas** -una alerta es un tipo especial de cuadro de diálogo que aparece cuando se produce un problema grave (por ejemplo, un error) o como una advertencia (por ejemplo, la preparación eliminar un archivo). Dado que una alerta es un cuadro de diálogo, también se necesita una respuesta del usuario antes de poder cerrar. (Consulte [trabajar con alertas](~/mac/user-interface/alert.md))

Para obtener más información, consulte el [acerca de Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Principal, clave y ventanas inactivas

Windows en una aplicación Xamarin.Mac puede aspecto y comportamiento diferente en función de cómo el usuario actualmente está interactuando con ellos. El documento o ventana de la aplicación que se encuentra actualmente el foco de atención del usuario en primer lugar se denomina la _ventana principal_. En la mayoría de los casos esta ventana también será el _ventana clave_ (la ventana que está actualmente aceptando proporcionados por el usuario). Pero esto no siempre es el caso, por ejemplo, un selector de Color podría estar abierto y la ventana de clave que el usuario está interactuando con el cambio de estado de un elemento en la ventana de documento (que todavía sería la ventana principal).

La principal y la clave de Windows (si son independientes) siempre están activas, _Windows inactiva_ son ventanas abiertas que no están en primer plano. Por ejemplo, una aplicación de editor de texto puede tener más de un documento abierta a la vez, solo se activará la ventana principal, todos los demás podrían estar inactivos. 

Para obtener más información, consulte el [acerca de Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Nomenclatura de Windows

Cada ventana puede mostrar una barra de título y cuando se muestra el título, suele ser el nombre de la aplicación, el nombre del documento que se está trabajando en o la función de la ventana (por ejemplo, el Inspector). Algunas aplicaciones no muestran una barra de título porque son reconocibles por campo visual y no funcionan con documentos.

Apple recomienda lo siguiente:

- Utilice el nombre de la aplicación para el título de una ventana principal, no del documento. 
- El nombre de una nueva ventana de documento `untitled`. Para el nuevo documento, no incluya un número al título del (como `untitled 1`). Si el usuario crea otro documento antes de guardar y título de la primera, llame a esa ventana `untitled 2`, `untitled 3`, etcetera.

Para obtener más información, consulte el [Windows nomenclatura](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Windows de pantalla completa

En Mac OS, ventana de la aplicación puede ir ocultación de pantalla completa todo, incluidas la barra de menús de la aplicación (que puede mostrarse al mover el cursor a la parte superior de la pantalla) para proporcionar distracción es contenido libre interacción con él.

Apple sugiere las siguientes directrices:

- Determine si tiene sentido durante una ventana para ir a pantalla completa. Las aplicaciones que proporcionan las interacciones breves (por ejemplo, una calculadora) no deben proporcionar un modo de pantalla completa.
- Si la tarea de pantalla completa requiere se muestra la barra de herramientas. Normalmente se oculta la barra de herramientas mientras esté en modo de pantalla completa.
- La ventana de pantalla completa debe tener todas las funciones que los usuarios necesitan completar la tarea.
- Si es posible, evite la interacción de buscador mientras el usuario está en una ventana de pantalla completa.
- Aprovechar el espacio de pantalla mayor sin desplazar el foco fuera de la tarea principal.

Para obtener más información, consulte el [Windows de pantalla completa](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>Paneles

Un Panel es una ventana auxiliar que contiene controles y las opciones que afectan a la selección (por ejemplo, el selector de colores del sistema) o el documento activo:

[![](window-images/panel01.png "Un panel de color")](window-images/panel01.png#lightbox)

Paneles pueden ser _específico de la aplicación_ o _todo el sistema_. Paneles de específico de la aplicación sitúa el cursor sobre la parte superior de las ventanas de documento de la aplicación y desaparecen cuando la aplicación está en segundo plano. Paneles de todo el sistema (como el **fuentes** panel), float encima de todas las ventanas abiertas con independencia de la aplicación. 

Apple sugiere las siguientes directrices:

- En general, utilice un panel estándar, paneles transparentes solo deben usarse con moderación y para tareas intensivas gráficamente.
- Considere la posibilidad de usar un panel de proporcionar a los usuarios un acceso sencillo a controles importantes o información que afecta directamente a su tarea.
- Ocultar y mostrar paneles según sea necesario.
- Paneles siempre deben incluir la barra de título.
- Paneles no deben incluir un botón de minimizar active.

#### <a name="inspectors"></a>inspectores

Las aplicaciones más modernas macOS presentan controles auxiliares y las opciones que afectan al documento activo o selección como _inspectores_ que forman parte de la ventana principal (como el **páginas** aplicación se muestra a continuación), en lugar de usar el Panel de Windows:

[![](window-images/panel02.png "Un inspector de ejemplo")](window-images/panel02.png#lightbox)

Para obtener más información, consulte el [paneles](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) y nuestro [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) aplicación de ejemplo para una implementación completa de un **Inspector interfaz** en una aplicación Xamarin.Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Creación y mantenimiento de Windows en Xcode.

Cuando se crea una nueva aplicación de Xamarin.Mac cacao, obtendrá una ventana en blanco, estándar de forma predeterminada. Esta ventana se define en un `.storyboard` archivo incluida automáticamente en el proyecto. Para editar el diseño de windows, en la **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo:

[![](window-images/edit01.png "Al seleccionar el guión gráfico principal")](window-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en el generador de interfaz de Xcode:

[![](window-images/edit02.png "Edición de la interfaz de usuario en Xcode.")](window-images/edit02.png#lightbox)

En el **atributo Inspector**, hay varias propiedades que puede usar para definir y controlar la ventana:

- **Título** -éste es el texto que se mostrará en la barra de título de la ventana.
- **Autoguardar** -trata la _clave_ que se utilizará para el Id. de la ventana cuando se guarda automáticamente su posición y configuración.
- **Barra de título** -mostrar la ventana de una barra de título.
- **Unified título y la barra de herramientas** : si la ventana incluye una barra de herramientas, debería ser parte de la barra de título.
- **Tamaño de vista de contenido completa** -permite que el área de contenido de la ventana se encuentre por debajo de la barra de título.
- **Sombra** -la ventana tiene una sombra.
- **Con textura** -con textura windows puede usar efectos (por ejemplo, dinamismo) y se pueden mover arrastrando en cualquier parte de su cuerpo.
- **Cerrar** -la ventana tiene un botón de cierre.
- **Minimizar** -la ventana tiene un botón Minimizar.
- **Cambiar el tamaño de** -la ventana tiene un control de cambio de tamaño.
- **Botón de barra de herramientas** -la ventana tiene un botón de barra de herramientas de mostrar u ocultar.
- **Pueden restaurarse** -es la posición y la configuración automáticamente, guardar y restaurar la ventana.
- **Visible en inicie** -la ventana se muestra automáticamente cuando la `.xib` se carga el archivo.
- **Ocultar en desactivar** -se oculta la ventana cuando la aplicación entra en segundo plano.
- **Cuando se cierra la versión** -es la ventana purgada de la memoria cuando está cerrado.
- **Siempre mostrar información sobre herramientas** -son la información sobre herramientas muestre constantemente.
- **Vuelve a calcular el bucle de vista** -es el orden de vista que se vuelven a calcular antes de que se dibuja la ventana.
- **Espacios de**, **Exposé** y **Cycling** -define cómo se comporta la ventana en esos entornos macOS. 
- **Pantalla completa** -determina si esta ventana puede entrar en el modo de pantalla completa. 
- **Animación** -controla el tipo de animación disponible para la ventana.
- **Apariencia** -controla la apariencia de la ventana. Por ahora hay solo un aspecto, aguamarina.

Vea de Apple [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) y [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) documentación para obtener más detalles.

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Establecer la ubicación y tamaño predeterminados

Para establecer la posición inicial de la ventana y controlar su tamaño, cambiar a la **Inspector de tamaño**:

[![](window-images/edit07.png "La ubicación y tamaño predeterminados")](window-images/edit07.png#lightbox)

Desde aquí puede establecer el tamaño inicial de la ventana, asígnele un tamaño mínimo y máximo, establecer la ubicación inicial en la pantalla y los bordes alrededor de la ventana de control.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Configurar un controlador de la ventana principal personalizado

Para poder crear salidas y las acciones para exponer elementos de interfaz de usuario al código de C#, la aplicación Xamarin.Mac deberá usar un controlador de ventana personalizados.

Haga lo siguiente:

1. Abra el guión gráfico de la aplicación en el generador de interfaz de Xcode.
2. Seleccione el `NSWindowController` en la superficie de diseño.
3. Cambie a la **identidad Inspector** ver y especificar `WindowController` como el **nombre de la clase**: 

    [![](window-images/windowcontroller01.png "Establecer el nombre de clase")](window-images/windowcontroller01.png#lightbox)
4. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar.
5. A `WindowController.cs` archivo se agregará al proyecto en el **el Explorador de soluciones** en Visual Studio para Mac: 

    [![](window-images/windowcontroller02.png "Al seleccionar el controlador de windows")](window-images/windowcontroller02.png#lightbox)
6. Vuelva a abrir el guión gráfico en el generador de interfaz de Xcode.
7. El `WindowController.h` archivo estará disponible para su uso: 

    [![](window-images/windowcontroller03.png "Editar el archivo WindowController.h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Agregar elementos de interfaz de usuario

Para definir el contenido de una ventana, arrastre controles desde el **biblioteca Inspector** en el **Editor de la interfaz**. Visite nuestro [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentación para obtener más información acerca de cómo utilizar el generador de interfaz para crear y habilitar los controles.

Por ejemplo, arrastre una barra de herramientas de la **biblioteca Inspector** en la ventana de la **Editor de la interfaz**:

[![](window-images/edit03.png "Seleccionar una barra de herramientas de la biblioteca")](window-images/edit03.png#lightbox)

A continuación, arrastre un **vista de texto** y ajuste su tamaño para rellenar el área situada bajo la barra de herramientas:

[![](window-images/edit04.png "Agregar una vista de texto")](window-images/edit04.png#lightbox)

Puesto que deseamos que la **vista de texto** para reducir y crecer como cambios de tamaño de la ventana, vamos a cambiar a la **Editor de restricciones** y agregue las siguientes restricciones:

[![](window-images/edit05.png "Modificar restricciones")](window-images/edit05.png#lightbox)

Haciendo clic en el para **rojos vigas de** en la parte superior del editor y haga clic en **agregar restricciones de 4**, estamos informar a la vista de texto para ajustarse a la determinada X, Y coordenadas y aumentar o reducir horizontalmente y verticalmente como se cambia el tamaño de la ventana.

Por último, vamos a exponer el **vista de texto** codificar mediante un **toma** (asegurándose de seleccionar la `ViewController.h` archivo):

[![](window-images/edit06.png "Configurar una salida")](window-images/edit06.png#lightbox)

Guardar los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Para obtener más información sobre cómo trabajar con **tomas** y **acciones**, visite nuestro [toma de corriente y acción](~/mac/get-started/hello-mac.md#Outlets_and_Actions) documentación.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Flujo de trabajo de ventana estándar

En cualquier ventana que permite crear y trabajar con en la aplicación Xamarin.Mac, el proceso es básicamente igual que lo que hemos hecho simplemente anteriormente:

1. Para nuevas ventanas que no son los predeterminados que se agregan automáticamente al proyecto, agregue una nueva definición de ventana para el proyecto. Esto se explicará en detalle a continuación.
2. Haga doble clic en el `Main.storyboard` archivo para abrir el diseño de ventana para su edición en el generador de interfaz de Xcode.
3. Arrastre una nueva ventana de diseño de la interfaz de usuario y enlazar la ventana en la ventana principal del sistema mediante _Segues_ (para obtener más información, consulte el [Segues](~/mac/platform/storyboards/indepth.md#Segues) sección de nuestro [trabajar con guiones gráficos](~/mac/platform/storyboards/indepth.md) documentación).
3. Establecer las propiedades de la ventana requerida en el **atributo Inspector** y **Inspector de tamaño**.
4. Arrastre los controles necesarios para compilar la interfaz y configurarlos en el **Inspector de atributo**.
5. Use la **Inspector de tamaño** para controlar el cambio de tamaño para los elementos de interfaz de usuario.
6. Exponer elementos de interfaz de usuario de la ventana a código de C# mediante **tomas** y **acciones**.
7. Guardar los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Ahora que tenemos una ventana básica creada, se examinará los procesos típicos un Xamarin.Mac aplicación hace cuando se trabaja con windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Mostrar la ventana predeterminada

De forma predeterminada, una nueva aplicación de Xamarin.Mac mostrará automáticamente la ventana definida en el `MainWindow.xib` archivo cuando se inicie:

[![](window-images/display01.png "Una ventana de ejemplo que se ejecuta")](window-images/display01.png#lightbox)

Puesto que se modifica el diseño de esa ventana anterior, ahora incluye un barra de herramientas predeterminado y **texto vista** control. La siguiente sección en la `Info.plist` archivo es responsable de mostrar esta ventana:

[![](window-images/display00.png "Edición Info.plist")](window-images/display00.png#lightbox)

El **interfaz principal** desplegable se utiliza para seleccionar el guión gráfico que se usará como la interfaz de usuario principal de la aplicación (en este caso `Main.storyboard`).

Un controlador de vista se agrega automáticamente al proyecto para controlar que Windows principal que se muestra (junto con la vista principal). Se define en el `ViewController.cs` de archivos y se adjuntan a la **propietario del archivo** en el generador de interfaz en el **identidad Inspector**:

[![](window-images/display02.png "Establecer el propietario del archivo")](window-images/display02.png#lightbox)

Nuestra ventana, nos gustaría que tenga un título de `untitled` cuando abre por primera vez así que vamos a reemplazar el `ViewWillAppear` método en el `ViewController.cs` debe ser similar a lo siguiente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> Estamos estableciendo el valor de la ventana `Title` propiedad en el `ViewWillAppear` en lugar del método la `ViewDidLoad` método porque, mientras que la vista se pueden cargar en la memoria, no es todavía crea una instancia completa. Si se intentó obtener acceso a la `Title` propiedad en el `ViewDidLoad` método obtendríamos un `null` excepción puesto que la ventana no se ha construido y cableado telefónico a la propiedad todavía.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Cerrar la ventana mediante programación

Puede haber ocasiones en que desea cerrar mediante programación una ventana en una aplicación Xamarin.Mac, que no sea de tener el usuario, haga clic en la ventana **cerrar** botón o usar un elemento de menú. macOS proporciona dos maneras diferentes para cerrar un `NSWindow` mediante programación: `PerformClose` y `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

Llamar a la `PerformClose` método de una `NSWindow` simula el usuario hace clic en la ventana **cerrar** botón momentáneamente resaltando el botón y, a continuación, cierre la ventana.

Si la aplicación implementa la `NSWindow`del `WillClose` evento, se producirá antes de cerrar la ventana. Si el evento devuelve `false`, a continuación, no se cerrará la ventana. Si la ventana no tiene un **cerrar** botón o no se puede cerrar por cualquier motivo, el sistema operativo emitirá el sonido de alerta.

Por ejemplo:

```csharp
MyWindow.PerformClose(this);
```

Intente cerrar la `MyWindow` `NSWindow` instancia. Si se realizó correctamente, la ventana se cerrará, else se emitirá el sonido de alerta y usará permanecerá abierta.

<a name="Close" />

### <a name="close"></a>Cerrar

Llamar a la `Close` método de una `NSWindow` no simula el usuario hace clic en la ventana **cerrar** botón momentáneamente resaltando el botón, simplemente cierra la ventana.

Una ventana no tiene que ser visibles para cerrar y un `NSWindowWillCloseNotification` notificación se publicarán en el centro de notificaciones predeterminado para la ventana que se va a cerrar.

El `Close` método difiere en dos aspectos importantes de la `PerformClose` método:

1. No intente generar el `WillClose` eventos.
2. No simula el usuario hace clic en el **cerrar** botón momentáneamente resaltando el botón.

Por ejemplo:

```csharp
MyWindow.Close();
```

Para cerrar la `MyWindow` `NSWindow` instancia.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Contenido de Windows modificada

En Mac OS, Apple ha proporcionado una manera de informar al usuario que el contenido de una ventana (`NSWindow`) se ha modificado por el usuario y debe guardarse. Si la ventana contiene contenido modificado, se mostrará un pequeño punto negro en su **cerrar** widget:

[![](window-images/close01.png "Una ventana con el marcador modificado")](window-images/close01.png#lightbox)

Si el usuario intenta cerrar la ventana o salir de la aplicación de Mac mientras hay sin guardar cambios en el contenido de la ventana, se debe presentar un [cuadro de diálogo](~/mac/user-interface/dialog.md) o [hoja Modal](~/mac/user-interface/dialog.md) y permitir al usuario guardar sus cambios primera:

[![](window-images/close02.png "Guarda la hoja que se muestran cuando se cierra la ventana")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Marcar una ventana como modificado

Para marcar una ventana como la necesidad de modificar el contenido, utilice el código siguiente:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

Y una vez que se ha guardado el cambio, borrar el indicador modificado utilizando:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Guardar los cambios antes de cerrar una ventana

Para inspeccionar al usuario cerrar la ventana y se le permite guardar el contenido modificado con antelación, debe crear una subclase de `NSWindowDelegate` e invalidar sus `WindowShouldClose` método. Por ejemplo:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWidowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWidowDelegate (NSWindow window)
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

Para adjuntar una instancia de este delegado a la ventana, utilice el código siguiente:

```csharp
// Set delegate
Window.Delegate = new EditorWidowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Guardar los cambios antes de cerrar la aplicación

Por último, debe comprobar su App Xamarin.Mac para ver si cualquiera de sus ventanas contienen contenido modificado y permite al usuario guardar los cambios antes de salir. Para ello, edite la `AppDelegate.cs` de archivos, invalide el `ApplicationShouldTerminate` método y darle un aspecto similar al siguiente:

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

## <a name="working-with-multiple-windows"></a>Trabajar con varias ventanas

Mayoría de las aplicaciones Mac en función de documento puede editar varios documentos al mismo tiempo. Por ejemplo, un editor de texto puede tener varios archivos de texto abiertos para edición al mismo tiempo. De forma predeterminada, tiene la nueva aplicación de Xamarin.Mac un **archivo** menú con un **New** elemento automáticamente por cable telefónico para la `newDocument:` **acción**.

Vamos a activar este nuevo elemento y permite al usuario abrir varias copias de nuestra ventana principal para editar varios documentos a la vez.

Editar nuestro `AppDelegate.cs` de archivos y agregue lo siguiente calcula la propiedad:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Vamos a usar para realizar un seguimiento del número de archivos que no haya guardado por lo que podemos proporcionar comentarios al usuario (según las directrices de Apple según se explicó anteriormente).

A continuación, agregue el método siguiente:

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

Este código crea una nueva versión de nuestro controlador de ventana, carga la nueva ventana, facilita el principal y la ventana de clave y lo establece el título. Ahora, si se ejecuta la aplicación y seleccione **New** desde el **archivo** menú se abre y se mostrará una nueva ventana del editor:

[![](window-images/display04.png "Se ha agregado una nueva ventana sin título")](window-images/display04.png#lightbox)

Si se abre la **Windows** menú, puede ver la aplicación se seguimiento y control de nuestras ventanas abiertas automáticamente:

[![](window-images/display05.png "El menú de Windows")](window-images/display05.png#lightbox)

Para obtener más información sobre cómo trabajar con menús en una aplicación Xamarin.Mac, visite nuestro [trabajar con menús](~/mac/user-interface/menu.md) documentación.

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Obtención de la ventana actualmente activa

En una aplicación de Xamarin.Mac que puede abrir varias ventanas (documentos), hay ocasiones cuando necesite obtener la ventana actual, más alto (la ventana de clave). El código siguiente devuelve la ventana de clave:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Se puede llamar en cualquier clase o método que necesita tener acceso a la ventana actual, key. Si no hay ninguna ventana está abierta actualmente, devolverá `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>Obtener acceso a todas las ventanas de aplicación

Puede haber ocasiones en que necesite tener acceso a todas las ventanas que la aplicación Xamarin.Mac tiene abiertas. Por ejemplo ver si un archivo que el usuario desea volver a abrir ya está abierto en una ventana existente.

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

En el código de ejemplo estamos convertir cada ventana devuelta a personalizado `ViewController` clase en nuestra aplicación y la comprobación del valor de un personalizado `Path` propiedad con respecto a la ruta de acceso de un archivo que el usuario desea volver a abrir. Si el archivo ya está abierto, ofrecemos esa ventana al frente.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Ajustar el tamaño de la ventana de código

Hay ocasiones cuando la aplicación necesita cambiar el tamaño de una ventana de código. Para cambiar el tamaño y la posición de una ventana, se ajuste del `Frame` propiedad. Al ajustar el tamaño de una ventana, suele ser preciso ajustar su origen, para mantener la ventana en la misma ubicación debido a sistema de coordenadas del macOS.

A diferencia de iOS en la esquina superior izquierda representa (0,0), macOS usa un sistema de coordenadas matemática en la esquina izquierda inferior de la pantalla representa (0,0). En iOS las coordenadas aumentan a medida que mueve hacia abajo, hacia la derecha. En Mac OS, las coordenadas aumenten de valor hacia arriba a la derecha. 

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
> Al ajustar un tamaño de windows y la ubicación en el código, debe asegurarse de que se respetan los tamaños mínimos y máximo que ha configurado en el generador de interfaz. Esto no se aplicarán automáticamente y podrá realizar la ventana mayores o menores que estos límites.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Supervisión de los cambios de tamaño de ventana

Puede haber ocasiones donde es necesario supervisar los cambios en el tamaño de una ventana dentro de la aplicación Xamarin.Mac. Por ejemplo, para volver a dibujar el contenido para ajustarse al tamaño nuevo.

Para supervisar los cambios de tamaño, primero asegúrese de que haya asignado una clase personalizada para el controlador de ventana en el generador de interfaz de Xcode. Por ejemplo, `MasterWindowController` en lo siguiente:

[![](window-images/resize01.png "El Inspector de identidad")](window-images/resize01.png#lightbox)

A continuación, modifique la clase de controlador de ventana personalizados y el monitor del `DidResize` evento en la ventana del controlador para recibir una notificación de cambios de tamaño en vivo. Por ejemplo:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

Si lo desea, puede usar el `DidEndLiveResize` evento sólo recibirá una notificación cuando el usuario ha terminado de cambiar el tamaño de la ventana. Por ejemplo:

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

Cuando se trabaja con las ventanas que representan documentos, `NSWindow` tiene un `DocumentEdited` propiedad que si se establece en `true` muestra un pequeño punto en el botón Cerrar para dar al usuario una indicación de que el archivo se ha modificado y debe guardarse antes de cerrarse.

Editar nuestro `ViewController.cs` de archivos y realice los cambios siguientes:

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

También nos estamos supervisión la `WillClose` evento en la ventana y la comprobación del estado de la `DocumentEdited` propiedad. Si es `true` es necesario dar al usuario la capacidad de guardar los cambios en el archivo. Si se ejecuta la aplicación y escriba algún texto, se mostrará el punto:

[![](window-images/file01.png "Una ventana modificada")](window-images/file01.png#lightbox)

Si se intenta cerrar la ventana, obtenemos una alerta:

[![](window-images/file02.png "Mostrar un proceso de guardar cuadro de diálogo")](window-images/file02.png#lightbox)

Si vamos a cargar un documento desde un archivo podemos establecer el título de la ventana en el archivo con el nombre la `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` método (dado que `path` es una cadena que representa el archivo está abierto). Además, podemos establecer la dirección URL del archivo con el `window.RepresentedUrl = url;` método.

Si la dirección URL señala a un tipo de archivo conocido por el sistema operativo, su icono se mostrará en la barra de título. Si el usuario hace clic en el icono, se mostrará la ruta de acceso al archivo.

Editar la `AppDelegate.cs` de archivos y agregue el método siguiente:

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

Ahora Si ejecutamos nuestra aplicación, seleccione **abrir...**  desde el **archivo** menú, seleccione un archivo de texto de la **abrir** diálogo cuadro y ábralo:

[![](window-images/file03.png "Un cuadro de diálogo Abrir")](window-images/file03.png#lightbox)

Se mostrará el archivo y el título se establecerá con el icono del archivo:

[![](window-images/file04.png "El contenido de un archivo cargado")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Agregar una nueva ventana a un proyecto

Además de la ventana del documento principal, una aplicación Xamarin.Mac deba mostrar otros tipos de windows para el usuario, como las preferencias de Inspector de paneles.

Para agregar una nueva ventana, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz de Xcode.
2. Arrastre una nueva **ventana controlador** desde el **biblioteca** y colóquela en la **superficie de diseño**:

    [![](window-images/new01.png "Al seleccionar un nuevo controlador de ventana en la biblioteca")](window-images/new01.png#lightbox)
3. En el **identidad Inspector**, escriba `PreferencesWindow` para el **Id. de guión gráfico**: 

    [![](window-images/new02.png "Establecer el identificador de guión gráfico")](window-images/new02.png#lightbox)
5. Diseñar la interfaz: 

    [![](window-images/new03.png "Diseñar la interfaz de usuario")](window-images/new03.png#lightbox)
6. Abra el menú de la aplicación (`MacWindows`), seleccione **preferencias...** , Control y haga clic y arrástrelo a la nueva ventana: 

    [![](window-images/new05.png "Crear un segue")](window-images/new05.png#lightbox)
7. Seleccione **mostrar** en el menú emergente.
6. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Si se ejecuta el código y seleccione el **preferencias...**  desde el **menú aplicación**, se mostrará la ventana:

[![](window-images/new04.png "Un menú de las preferencias de ejemplo")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Trabajar con paneles

Como se indicó al principio de este artículo, un panel queda flotando sobre otras ventanas y proporciona herramientas o los controles que los usuarios pueden trabajar con mientras haya documentos abiertos. 

Al igual que cualquier otro tipo de ventana que permite crear y trabajar con en la aplicación Xamarin.Mac, el proceso es básicamente el mismo:

1. Agregar una nueva definición de ventana para el proyecto.
2. Haga doble clic en el `.xib` archivo para abrir el diseño de ventana para su edición en el generador de interfaz de Xcode.
2. Establecer las propiedades de la ventana requerida en el **atributo Inspector** y **Inspector de tamaño**.
4. Arrastre los controles necesarios para compilar la interfaz y configurarlos en el **Inspector de atributo**.
5. Use la **Inspector de tamaño** para controlar el cambio de tamaño para los elementos de interfaz de usuario.
6. Exponer elementos de interfaz de usuario de la ventana a código de C# mediante **tomas** y **acciones**.
7. Guardar los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

En el **atributo Inspector**, tiene las siguientes opciones específicas de paneles:

[![](window-images/panel03.png "El Inspector de atributo")](window-images/panel03.png#lightbox)

- **Estilo** -permiten ajustar el estilo del panel de: Panel Regular (parece una ventana estándar), Panel de utilidad (tiene una barra de título más pequeña), Panel HUD (es translúcido y la barra de título forma parte del fondo).
- **Activación no** -determina en el panel se convierte en la ventana de la clave.
- **Documentar Modal** -si documento Modal, el panel solo flotará sobre las ventanas de la aplicación, else flotará sobre todo.


Para agregar un nuevo Panel, haga lo siguiente:

1. En el **el Explorador de soluciones**, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...** .
2. En el cuadro de diálogo nuevo archivo, seleccione **Xamarin.Mac** > **ventana cacao con controlador**:

    [![](window-images/panels00.png "Agregar un nuevo controlador de ventana")](window-images/panels00.png#lightbox)
3. Escriba `DocumentPanel` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el `DocumentPanel.xib` archivo para abrirlo y editarlo en el generador de interfaz: 

    [![](window-images/new02.png "Del panel de edición")](window-images/new02.png#lightbox)
5. Eliminar la ventana existente y arrastre un Panel desde el **biblioteca Inspector** en el el **Editor de la interfaz**: 

    [![](window-images/panels01.png "Eliminación de la ventana existente")](window-images/panels01.png#lightbox)
6. Enlazar el panel hasta el **propietario del archivo*-**ventana*- **toma**: 

    [![](window-images/panels02.png "Arrastrar para conectar el panel")](window-images/panels02.png#lightbox)
7. Cambie a la **identidad Inspector** y set (clase) del Panel `DocumentPanel`: 

    [![](window-images/panels03.png "Configuración de clase del panel")](window-images/panels03.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.
7. Editar el `DocumentPanel.cs` y cambie la definición de clase a lo siguiente: 

    `public partial class DocumentPanel : NSPanel`
8. Guarde los cambios en el archivo.

Editar la `AppDelegate.cs` de archivos y realice la `DidFinishLaunching` método aspecto similar al siguiente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Si se ejecuta la aplicación, se mostrará el panel:

[![](window-images/panels04.png "El panel de una aplicación en ejecución")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Panel de Windows han quedado en desuso por Apple y debe reemplazarse por **Interfaces del Inspector de**. Para obtener un ejemplo completo de creación de un **Inspector** en una aplicación Xamarin.Mac, visite nuestro [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) aplicación de ejemplo.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con ventanas y paneles en una aplicación Xamarin.Mac. Hemos visto los distintos tipos y usos de Windows y paneles, cómo crear y mantener ventanas y paneles en el generador de interfaz de Xcode y cómo trabajar con ventanas y paneles en código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacWindows (ejemplo)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector (ejemplo)](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con menús](~/mac/user-interface/menu.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
