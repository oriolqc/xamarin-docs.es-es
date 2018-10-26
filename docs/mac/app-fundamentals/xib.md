---
title: archivos .xib en Xamarin.Mac
description: En este artículo se explica cómo trabajar con archivos .xib creados en Xcode Interface Builder para crear y mantener interfaces de usuario para una aplicación de Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 45eeee745b133646aef0f775bc879fa6a5d867c7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109437"
---
# <a name="xib-files-in-xamarinmac"></a>archivos .xib en Xamarin.Mac

_En este artículo se explica cómo trabajar con archivos .xib creados en Xcode Interface Builder para crear y mantener interfaces de usuario para una aplicación de Xamarin.Mac._

> [!NOTE]
> Es la mejor manera de crear una interfaz de usuario para una aplicación de Xamarin.Mac con guiones gráficos. Esta documentación se ha dejado en su lugar, por motivos históricos y para trabajar con proyectos de Xamarin.Mac más antiguos. Para obtener más información, consulte nuestra [Introducción a guiones gráficos](~/mac/platform/storyboards/index.md) documentación.

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, podrá tener acceso a los mismos elementos de interfaz de usuario y herramientas que un desarrollador que trabaja *Objective-C* y *Xcode* does. Ya que Xamarin.Mac se integra directamente con Xcode, puede usar Xcode _Interface Builder_ para crear y mantener las interfaces de usuario (o bien, opcionalmente, crearlos directamente en código de C#).

Se usa un archivo .xib MacOS para definir los elementos de interfaz de usuario de la aplicación (por ejemplo, menús, Windows, las vistas, las etiquetas, campos de texto) que se crean y mantienen de forma gráfica en Interface Builder de Xcode.

[![Un ejemplo de la aplicación en ejecución](xib-images/intro01.png "un ejemplo de la aplicación en ejecución")](xib-images/intro01-large.png#lightbox)

En este artículo, trataremos los aspectos básicos de trabajar con archivos .xib en una aplicación de Xamarin.Mac. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, ya que abarca los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` atributos se utiliza para conectar las clases de C# para objetos de Objective-C y la interfaz de usuario de elementos.


## <a name="introduction-to-xcode-and-interface-builder"></a>Introducción a Xcode y a Interface Builder

Como parte de Xcode, Apple ha creado una herramienta denominada Interface Builder, que le permite crear la interfaz de usuario visualmente en un diseñador. Xamarin.Mac se integra con fluidez con el generador de interfaz, lo que le permite crear la interfaz de usuario con las mismas herramientas que lo hacen los usuarios de Objective-C de objetivo.


### <a name="components-of-xcode"></a>Componentes de Xcode

Al abrir un archivo .xib en Xcode desde Visual Studio para Mac, se abrirá con un **Project Navigator** a la izquierda, el **jerarquía de la interfaz** y **Editor de la interfaz** en el medio y un **propiedades y utilidades** sección de la derecha:

[![Los componentes de la UI de Xcode](xib-images/xcode03.png "los componentes de la UI de Xcode")](xib-images/xcode03-large.png#lightbox)

Echemos un vistazo a lo que cada una de estas secciones Xcode hace y cómo se usará para crear la interfaz de la aplicación de Xamarin.Mac.


#### <a name="project-navigation"></a>Navegación del proyecto

Al abrir un archivo .xib para editarlo en Xcode, Visual Studio para Mac crea un archivo de proyecto de Xcode en segundo plano para comunicar los cambios entre él y Xcode. Más adelante, cuando cambie a Visual Studio para Mac de Xcode, los cambios realizados en este proyecto se sincronizan con el proyecto de Xamarin.Mac con Visual Studio para Mac.

El **navegación del proyecto** sección permite navegar entre todos los archivos que componen este _correcciones de compatibilidad_ proyecto Xcode. Normalmente, solo estarán interesados en esta lista, como los archivos .xib **MainMenu.xib** y **MainWindow.xib**.


#### <a name="interface-hierarchy"></a>Jerarquía de la interfaz

El **jerarquía de la interfaz** sección le permite acceder fácilmente a varias propiedades clave de la interfaz de usuario, como su **marcadores de posición** y principal **ventana**. También puede utilizar esta sección para tener acceso a los elementos individuales (vistas) que hacen de la interfaz de usuario y ajustar la manera en que se anidan, al arrastrarlos alrededor de la jerarquía.


#### <a name="interface-editor"></a>Editor de la interfaz

El **Editor de la interfaz** sección proporciona la superficie en la que se gráficamente diseño del interfaz de usuario. Podrá arrastrar elementos desde el **biblioteca** sección de la **propiedades y utilidades** sección para crear el diseño. Cuando agregue elementos de interfaz de usuario (vistas) a la superficie de diseño, se agregarán a la **jerarquía de la interfaz** sección en el orden en que aparecen en la **Editor de la interfaz**.


#### <a name="properties--utilities"></a>Propiedades y utilidades

El **propiedades y utilidades** sección se divide en dos secciones principales que se trabajará con, **propiedades** (también denominada inspectores) y el **biblioteca**:

![El Inspector de propiedad](xib-images/xcode04.png "el Inspector de propiedad")

Inicialmente en esta sección está casi vacía, pero si selecciona un elemento en el **Editor de la interfaz** o **jerarquía de la interfaz**, el **propiedades** se rellenará la sección con información sobre las propiedades que puede ajustar y el elemento especificado.

En la sección **Propiedades** hay 8 *pestañas de inspectores* diferentes, como se muestra en la siguiente ilustración:

[![Información general de todos los inspectores](xib-images/xcode05.png "una visión general de todos los inspectores")](xib-images/xcode05-large.png#lightbox)

De izquierda a derecha, estas pestañas son:

-   **File Inspector** (Inspector de archivos): en esta pestaña se muestra la información del archivo, como el nombre de archivo y la ubicación del archivo Xib que se está editando.
-   **Ayuda rápida**: en la pestaña Ayuda rápida se proporciona ayuda contextual según lo que está seleccionado en Xcode.
-   **Identity Inspector** (Inspector de identidad): en esta pestaña se proporciona información sobre el control o la vista seleccionados.
-   **Inspector de atributos** – el Inspector de atributos permite personalizar varios atributos de la vista o control seleccionados.
-   **Size Inspector** – el Inspector de tamaño le permite controlar el tamaño y el comportamiento de la vista de control seleccionada o el cambio de tamaño.
-   **Inspector de conexiones** – el Inspector de conexiones se muestran las conexiones de salida y la acción de los controles seleccionados. Examinaremos las salidas y acciones en un momento.
-   **Inspector de enlaces** – el Inspector de enlaces le permite configurar los controles para que sus valores se enlazan automáticamente a los modelos de datos.
-   **View Effects Inspector** – The View Effects Inspector le permite especificar los efectos en los controles, como animaciones.

En el **biblioteca** sección, encontrará controles y objetos que se colocan gráficamente en el diseñador para crear la interfaz de usuario:

![Un ejemplo de Inspector de biblioteca](xib-images/xcode06.png "muestra un ejemplo del Inspector de biblioteca")

Ahora que ya está familiarizado con el IDE de Xcode e Interface Builder, echemos un vistazo a usarlo para crear una interfaz de usuario.


## <a name="creating-and-maintaining-windows-in-xcode"></a>Creación y mantenimiento de windows en Xcode

Es el método preferido para crear la interfaz de usuario de una aplicación de Xamarin.Mac con guiones gráficos (consulte nuestra [Introducción a guiones gráficos](~/mac/platform/storyboards/index.md) documentación para obtener más información) y, como resultado, cualquier nuevo proyecto se inició sesión Xamarin.Mac Utilice guiones gráficos de forma predeterminada.

Para cambiar al usar un .xib basadas en la interfaz de usuario, haga lo siguiente:

1. Abra Visual Studio para Mac e iniciar un nuevo proyecto de Xamarin.Mac.
2. En el **panel de solución**, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...**
3. Seleccione **Mac** > **Windows controlador**:

    ![Agregar un nuevo controlador de ventana](xib-images/setup00.png "agregando un nuevo controlador de ventana")
4. Escriba `MainWindow` para el nombre y haga clic en el **New** botón:

    ![Agregar una nueva ventana de Main](xib-images/setup01.png "agregando una nueva ventana principal")
5. Haga doble clic en el proyecto nuevo y seleccione **agregar** > **nuevo archivo...**
6. Seleccione **Mac** > **menú principal**:

    ![Agregar un nuevo menú principal](xib-images/setup02.png "agregando un nuevo menú principal")
7. Deje el nombre como `MainMenu` y haga clic en el **New** botón.
8. En el **panel de solución** seleccione la **Main.storyboard** de archivos, haga clic en y seleccione **quitar**:

    ![Seleccione el guion gráfico principal](xib-images/setup03.png "seleccionando el guión gráfico principal")
9. En el cuadro de diálogo Quitar, haga clic en el **eliminar** botón:

    ![Confirmar la eliminación](xib-images/setup04.png "confirmar la eliminación")
10. En el **panel de solución**, haga doble clic en el **Info.plist** archivo para abrirlo y editarlo.
11. Seleccione `MainMenu` desde el **interfaz principal** lista desplegable:

    [![Establecer el menú principal](xib-images/setup05.png "establecer el menú principal")](xib-images/setup05-large.png#lightbox)
12. En el **panel de solución**, haga doble clic en el **MainMenu.xib** archivo para abrirlo y editarlo en Interface Builder de Xcode.
13. En el **Inspector de biblioteca**, tipo `object` en el campo de búsqueda, a continuación, arrastre una nueva **objeto** a la superficie de diseño:

    [![El menú principal de edición](xib-images/setup06.png "el menú principal de edición")](xib-images/setup06-large.png#lightbox)
14. En el **Inspector de identidad**, escriba `AppDelegate` para el **clase**:

    [![Seleccionar el delegado de la aplicación](xib-images/setup07.png "seleccionando el delegado de la aplicación")](xib-images/setup07-large.png#lightbox)
15. Seleccione **propietario del archivo** desde el **jerarquía de la interfaz**, cambie a la **conexión Inspector** y arrastre una línea desde el delegado al que el `AppDelegate` **Objeto** acaba de agregar al proyecto:

    [![Conectar el delegado de la aplicación](xib-images/setup08.png "conectar el delegado de la aplicación")](xib-images/setup08-large.png#lightbox)
16. Guardar los cambios y vuelva a Visual Studio para Mac.

Con todos estos cambios en su lugar, edite el **AppDelegate.cs** de archivos y darle un aspecto similar al siguiente:

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

Ahora la ventana principal de la aplicación se define en un **.xib** archivo incluido automáticamente en el proyecto al agregar un controlador de ventana. Para editar el diseño de windows, en el **panel de solución**, haga doble clic en el **MainWindow.xib** archivo:

![Seleccionar el archivo MainWindow.xib](xib-images/edit01.png "seleccionando el archivo MainWindow.xib")

Se abrirá el diseño de ventana en Interface Builder de Xcode:

[![Editar el MainWindow.xib](xib-images/edit02.png "el MainWindow.xib de edición")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>Flujo de trabajo de ventana estándar

Cualquier ventana que permite crear y trabajar con en la aplicación de Xamarin.Mac, el proceso es básicamente el mismo:

1. Para las nuevas ventanas que no son los predeterminados que se agregan automáticamente al proyecto, agregue una nueva definición de ventana al proyecto.
2. Haga doble clic en el archivo .xib para abrir el diseño de ventana para su edición en Interface Builder de Xcode.
3. Establezca las propiedades de ventana necesaria el **Inspector de atributos** y **Inspector de tamaño**.
4. Arrastre los controles necesarios para crear la interfaz y configurarlos en el **Inspector de atributos**.
5. Use la **Inspector de tamaño** para controlar el cambio de tamaño para los elementos de interfaz de usuario.
6. Exponer elementos de interfaz de usuario de la ventana a C# código mediante salidas y acciones.
7. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.


### <a name="designing-a-window-layout"></a>Definir un diseño de ventana

El proceso para diseñar una interfaz de usuario en el generador de interfaz es básicamente el mismo para todos los elementos que agregue:

1. Busque el control deseado en el **Inspector de biblioteca** y arrástrelo el **Editor de la interfaz** y colóquelo.
2. Establezca las propiedades de ventana necesaria el **Inspector de atributos**.
3. Use la **Inspector de tamaño** para controlar el cambio de tamaño para los elementos de interfaz de usuario.
4. Si usa una clase personalizada, establecerla en el **Inspector de identidad**.
5. Exponer los elementos de interfaz de usuario para C# código mediante salidas y acciones.
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Por ejemplo:

1. En Xcode, arrastre un **botón de comando** de la **sección Biblioteca**:

    [![Selección de un botón de la biblioteca](xib-images/xcode07.png "al seleccionar un botón de la biblioteca")](xib-images/xcode07-large.png#lightbox)
2. Coloque el botón en el **ventana** en el **Editor de la interfaz**:

    [![Agregar un botón a la ventana](xib-images/xcode08.png "agregar un botón a la ventana")](xib-images/xcode08-large.png#lightbox)
3. Haga clic en la propiedad **Título** del **Attribute Inspector** (Inspector de atributos) y cambie el título del botón por `Click Me`:

    ![Establece los atributos del botón](xib-images/xcode09.png "establece los atributos del botón")
4. Arrastre una **Etiqueta** de la **sección Biblioteca**:

    [![Selección de una etiqueta en la biblioteca](xib-images/xcode10.png "selección de una etiqueta en la biblioteca")](xib-images/xcode10-large.png#lightbox)
5. Coloque la etiqueta en la **Ventana** situada junto al botón en el **Interface Editor** (Editor de la interfaz):

    [![Agregar una etiqueta a la ventana](xib-images/xcode11.png "agregar una etiqueta a la ventana")](xib-images/xcode11-large.png#lightbox)
6. Agarre el controlador derecho de la etiqueta y arrástrelo hasta que esté cerca del borde de la ventana:

    [![Cambiar el tamaño de la etiqueta](xib-images/xcode12.png "cambiar el tamaño de la etiqueta")](xib-images/xcode12-large.png#lightbox)
7. Con la etiqueta sigue seleccionada en el **Editor de la interfaz**, cambie a la **Inspector de tamaño**:

    ![Seleccionar el Inspector de tamaño](xib-images/xcode13.png "seleccionando el Inspector de tamaño")
8. En el **cuadro de tamaño automático** haga clic en el **Dim corchete rojo** situado a la derecha y el **Dim flecha Horizontal de rojo** en el centro:

    ![Editar las propiedades de ajuste automático de tamaño](xib-images/xcode14.png "editar las propiedades de tamaño automático")
9. Esto garantiza que la etiqueta se adaptará aumente y reduzca la ventana se cambia el tamaño de la aplicación en ejecución. El **rojo corchetes** y la parte superior e izquierda de la **cuadro de tamaño automático** cuadro indicar a la etiqueta que se bloquee en su X e Y ubicaciones.
10. Guarde los cambios en la interfaz de usuario

Tal como eran el cambio de tamaño y mover controles, se debe que haya observado que Interface Builder ofrece sugerencias de ajuste útiles basadas en [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Estas instrucciones le ayudará a crear aplicaciones de alta calidad que tendrán una apariencia familiar para los usuarios de Mac.

Si observa la **jerarquía de la interfaz** sección, tenga en cuenta cómo se muestran el diseño y la jerarquía de los elementos que componen la interfaz de usuario:

![Seleccionar un elemento en la jerarquía de interfaz](xib-images/xcode15.png "seleccionar un elemento en la jerarquía de interfaz")

Desde aquí puede seleccionar elementos para editarlos o arrastrarlos para volver a ordenar los elementos de interfaz de usuario si es necesario. Por ejemplo, si un elemento de interfaz de usuario que se estaba cubierto por otro elemento, podría arrastrarlo a la parte inferior de la lista para convertirlo en el elemento superior en la ventana.

Para obtener más información sobre cómo trabajar con Windows en una aplicación de Xamarin.Mac, consulte nuestra [Windows](~/mac/user-interface/window.md) documentación.


## <a name="exposing-ui-elements-to-c-code"></a>Exponer elementos de interfaz de usuario al C# código

Cuando haya terminado de diseñar la apariencia y comportamiento de la interfaz de usuario en el generador de interfaz, deberá exponer los elementos de la interfaz de usuario para que sean accesibles desde C# código. Para ello, usará las acciones y salidas.


### <a name="setting-a-custom-main-window-controller"></a>Configuración de un controlador de ventana principal personalizado

Para poder crear salidas y acciones para exponer elementos de interfaz de usuario al código de C#, la aplicación de Xamarin.Mac se deberá usar un controlador de ventana personalizada.

Haga lo siguiente:

1. Abra el guión gráfico de la aplicación en Interface Builder de Xcode.
2. Seleccione el `NSWindowController` en la superficie de diseño.
3. Cambie a la **Inspector de identidad** permite ver y especificar `WindowController` como el **nombre de la clase**:

    [![Editar el nombre de clase](xib-images/windowcontroller01.png "editar el nombre de clase")](xib-images/windowcontroller01-large.png#lightbox)
4. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar.
5. Un **WindowController.cs** archivo se agregará al proyecto en el **panel de solución** en Visual Studio para Mac:

    ![El nuevo nombre de clase en Visual Studio para Mac](xib-images/windowcontroller02.png "el nuevo nombre de clase en Visual Studio para Mac")
6. Vuelva a abrir el guión gráfico de Interface Builder de Xcode.
7. El **WindowController.h** archivo estará disponible para su uso:

    [![El archivo .h coincidente en Xcode](xib-images/windowcontroller03.png "el correspondiente archivo .h en Xcode")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>Salidas y acciones

¿Cuáles son las salidas y acciones? En la programación tradicional de interfaz de usuario de .NET, un control de la interfaz de usuario se expone de forma automática como una propiedad cuando se agrega. En Mac es algo diferente, al agregar simplemente un control a una vista, el código no puede obtener acceso a él. El desarrollador debe exponer de forma explícita el elemento de interfaz de usuario al código. Para ello, Apple nos ofrece dos opciones:

-  **Salidas**: las salidas son análogas a las propiedades. Si conectar un control a una salida, se expone al código a través de una propiedad, por lo que puede hacer cosas como agregar controladores de eventos, llamar a métodos en ella, etcetera.
-  **Acciones**: las acciones son análogas al patrón de comando en WPF. Por ejemplo, cuando se realiza una acción en un control, por ejemplo haga clic en un botón, el control llama automáticamente a un método en el código. Las acciones son eficaces y convenientes porque puede conectar muchos controles a la misma acción.

En Xcode, se agregan directamente en código mediante salidas y acciones *al arrastrar el Control*. Más concretamente, esto significa que para crear una acción o salida, elija el elemento de control que le gustaría agregar una acción y la toma de corriente, mantenga presionada la **Control** situado en el teclado y arrastrará ese control directamente en el código.

Para los desarrolladores de Xamarin.Mac, esto significa que arrastre los archivos de código auxiliar de Objective-C que corresponden a la C# archivo donde desea crear la salida o la acción. Visual Studio para Mac ha creado un archivo denominado **MainWindow.h** como parte de la corrección de compatibilidad de proyecto de Xcode que ha generado para usar Interface Builder:

[![Un ejemplo de un archivo .h en Xcode](xib-images/xcode16.png "un ejemplo de un archivo .h en Xcode")](xib-images/xcode16-large.png#lightbox)

Este archivo .h de código auxiliar refleja el **MainWindow.designer.cs** que se agrega automáticamente a un proyecto de Xamarin.Mac cuando un nuevo `NSWindow` se crea. Este archivo se usará para sincronizar los cambios realizados por Interface Builder y es donde se creará la salidas y acciones para que los elementos de interfaz de usuario están expuestos a C# código.


#### <a name="adding-an-outlet"></a>Agregar una salida

Con una comprensión básica de salidas y acciones, echemos un vistazo a la creación de una salida para exponer un elemento de interfaz de usuario para su C# código.

Haga lo siguiente:

1. En Xcode, en la esquina superior disponible más a la derecha de la pantalla, haga clic en el botón de **doble círculo** para abrir **Assistant Editor** (Editor del asistente):

    [![Seleccione el Editor del asistente](xib-images/outlet01.png "seleccionando el Editor del Asistente")](xib-images/outlet01-large.png#lightbox)
2. Xcode cambiará a un modo de vista en dos paneles con el **Interface Editor** (Editor de la interfaz) en un lado y un **Editor de código** en el otro.
3. Tenga en cuenta que Xcode ha seleccionado automáticamente el **MainWindowController.m** de archivos en el **Editor de código**, lo cual es incorrecto. Si recuerda de qué salidas y acciones están por encima de la descripción, necesitamos tener la **MainWindow.h** seleccionado.
4. En la parte superior de la **Editor de código** haga clic en el **vínculo automático** y seleccione el **MainWindow.h** archivo:

    [![Seleccionar el archivo .h correcto](xib-images/outlet02.png "seleccionando el archivo .h correcto")](xib-images/outlet02-large.png#lightbox)
5. Ahora, Xcode debería tener el archivo correcto seleccionado:

    [![El archivo correcto seleccionado](xib-images/outlet03.png "el archivo correcto seleccionado")](xib-images/outlet03-large.png#lightbox)
6. **El último paso es muy importante.** Si no tiene seleccionado el archivo correcto, no podrá crear salidas y acciones o se expondrán a la clase incorrecta en C#!
7. En el **Editor de la interfaz**, mantenga presionada la **Control** teclas del teclado y haga clic y arrastre la etiqueta se ha creado anteriormente al editor de código justo debajo del `@interface MainWindow : NSWindow { }` código:

    [![Arrastrar para crear una nueva salida](xib-images/outlet04.png "arrastrar para crear una nueva salida")](xib-images/outlet04-large.png#lightbox)
8. Aparecerá un cuadro de diálogo. Deje el **conexión** establecido en salida y escriba `ClickedLabel` para el **nombre**:

    [![Establecer las propiedades de la toma de corriente](xib-images/outlet05.png "estableciendo las propiedades de salida")](xib-images/outlet05-large.png#lightbox)
9. Haga clic en el **Connect** botón para crear la salida:

    ![La toma de corriente completado](xib-images/outlet06.png "la toma de corriente completada")
10. Guarde los cambios en el archivo.


#### <a name="adding-an-action"></a>Agregar una acción

A continuación, echemos un vistazo a la creación de una acción para exponer una interacción del usuario con el elemento de interfaz de usuario para su C# código.

Haga lo siguiente:

1. Asegúrese de que está todavía en el **Editor del asistente** y **MainWindow.h** archivo está visible en el **Editor de código**.
2. En el **Editor de la interfaz**, mantenga presionada la **Control** teclas del teclado y haga clic y arrastre el botón se ha creado anteriormente al editor de código justo debajo del `@property (assign) IBOutlet NSTextField *ClickedLabel;` código:

    [![Arrastrar para crear una acción](xib-images/action01.png "arrastrar para crear una acción")](xib-images/action01-large.png#lightbox)
3. Cambiar el **conexión** tipo de acción:

    [![Seleccione un tipo de acción](xib-images/action02.png "seleccione un tipo de acción")](xib-images/action02-large.png#lightbox)
4. Escriba `ClickedButton` como **Nombre**:

    [![Configurar la acción](xib-images/action03.png "configurar la acción")](xib-images/action03-large.png#lightbox)
5. Haga clic en el **Connect** botón para crear la acción:

    ![La acción completada](xib-images/action04.png "la acción completada")
6. Guarde los cambios en el archivo.

Con la interfaz de usuario conectada y expuestos a C# de código, vuelva a Visual Studio para Mac y permita que se sincronicen los cambios de Xcode e Interface Builder.


### <a name="writing-the-code"></a>Escribir el código

Con la interfaz de usuario creada y sus elementos de interfaz de usuario expuestos al código mediante salidas y acciones, está listo para escribir el código para que el programa cobren vida. Por ejemplo, abra el **MainWindow.cs** archivo para modificarlo, haga doble clic en el **panel de solución**:

[![El archivo MainWindow.cs](xib-images/code01.png "MainWindow.cs el archivo")](xib-images/code01-large.png#lightbox)

Y agregue el código siguiente a la `MainWindow` clase para trabajar con la salida de ejemplo que creó anteriormente:

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

Tenga en cuenta que el `NSLabel` se accede en C# por el nombre directo que se le ha asignado en Xcode cuando creó su salida en Xcode, en este caso, se llama `ClickedLabel`. Se puede tener acceso a cualquier método o propiedad del objeto expuesto de la misma manera que lo haría con cualquier normal C# clase.

> [!IMPORTANT]
> Deberá usar `AwakeFromNib`, en lugar de otro método como `Initialize`, porque `AwakeFromNib` se denomina _después_ el sistema operativo que haya cargado y crea una instancia de la interfaz de usuario desde el archivo .xib. Si ha intentado tener acceso al control de etiqueta antes de que los archivos .xib totalmente cargado y crea una instancia, obtendría un `NullReferenceException` error porque todavía no se crearía el control de etiqueta.

A continuación, agregue la siguiente clase parcial para el `MainWindow` clase:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Este código asocia a la acción que creó en Xcode e Interface Builder y se llamará siempre que el usuario hace clic en el botón.

Algunos elementos de interfaz de usuario generan automáticamente en acciones, por ejemplo, elementos en la barra de menús de forma predeterminada como la **abrir...**  elemento de menú (`openDocument:`). En el **panel de solución**, haga doble clic en el **AppDelegate.cs** archivo para abrirlo para su edición y agregue el código siguiente el `DidFinishLaunching` método:

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

La línea de clave aquí es `[Export ("openDocument:")]`, dice `NSMenu` que la **AppDelegate** tiene un método `void OpenDialog (NSObject sender)` que responde a la `openDocument:` acción.

Para obtener más información sobre cómo trabajar con menús, vea nuestra [menús](~/mac/user-interface/menu.md) documentación.


## <a name="synchronizing-changes-with-xcode"></a>Sincronizar los cambios con Xcode

Cuando cambie a Visual Studio para Mac de Xcode, los cambios realizados en Xcode se sincronizarán automáticamente con el proyecto de Xamarin.Mac.

Si selecciona el **MainWindow.designer.cs** en el **panel de solución** , podrá ver cómo nuestra salida y la acción se han conectado copia nuestra C# código:

[![Sincronizar los cambios con Xcode](xib-images/sync01.png "sincronizar los cambios con Xcode")](xib-images/sync01-large.png#lightbox)

Observe cómo las dos definiciones del **MainWindow.designer.cs** archivo:

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

Se alineen con las definiciones en el **MainWindow.h** archivo en Xcode:

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

Como puede ver, Visual Studio para Mac escucha los cambios realizados en el archivo .h y, a continuación, se sincroniza automáticamente esos cambios en los respectivos **. designer.cs** archivo exponerlos a la aplicación. También puede observar que **MainWindow.designer.cs** es una clase parcial, para que Visual Studio para Mac no tiene que modificar **MainWindow.cs** que sobrescribiría los cambios que hemos realizado en la clase.

Normalmente nunca tendrá que abrir el **MainWindow.designer.cs** usted mismo, se ha presentado aquí solo con fines educativos.

> [!IMPORTANT]
> En la mayoría de los casos, Visual Studio para Mac automáticamente todos los cambios realizados en Xcode y sincronizarlas con el proyecto de Xamarin.Mac. En caso de que esa sincronización no se realice de forma automática, vuelva a Xcode y después vuelva a Visual Studio para Mac. Normalmente, esto iniciará un ciclo de sincronización.


## <a name="adding-a-new-window-to-a-project"></a>Agregar una nueva ventana a un proyecto

Aparte de la ventana de documento principal, una aplicación de Xamarin.Mac posible que deba mostrar otros tipos de windows para el usuario, como las preferencias o Inspector de paneles. Al agregar una nueva ventana para el proyecto debe usar siempre el **ventana de Cocoa con controlador** opción, ya que esto hace que el proceso de carga de la ventana desde el .xib más fácil de archivos.

Para agregar una nueva ventana, haga lo siguiente:

1. En el **panel de solución**, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...** .
2. En el cuadro de diálogo nuevo archivo, seleccione **Xamarin.Mac** > **ventana de Cocoa con controlador**:

    ![Agregar un nuevo controlador de ventana](xib-images/new01.png "agregando un nuevo controlador de ventana")
3. Escriba `PreferencesWindow` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el **PreferencesWindow.xib** archivo para abrirlo y editarlo en Interface Builder:

    [![Edición de la ventana en Xcode](xib-images/new02.png "edición de la ventana en Xcode")](xib-images/new02-large.png#lightbox)
5. Diseñar la interfaz:

    [![En el diseño de windows](xib-images/new03.png "en el diseño de windows")](xib-images/new03-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Agregue el código siguiente al **AppDelegate.cs** para mostrar la ventana nueva:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

El `var preferences = new PreferencesWindowController ();` línea crea una nueva instancia del controlador de ventana que se carga la ventana desde el archivo .xib y lo aumenta. El `preferences.Window.MakeKeyAndOrderFront (this);` línea muestra la ventana de nuevo al usuario.

Si ejecuta el código y seleccione el **preferencias...**  desde el **menú aplicación**, se mostrará la ventana:

![Ejecutar la aplicación de ejemplo](xib-images/new04.png "ejecutando la aplicación de ejemplo")

Para obtener más información sobre cómo trabajar con Windows en una aplicación de Xamarin.Mac, consulte nuestra [Windows](~/mac/user-interface/window.md) documentación.


## <a name="adding-a-new-view-to-a-project"></a>Agregar una nueva vista a un proyecto

Hay ocasiones en que es más fácil de diseño de la ventana se dividen en varias, archivos .xib más fáciles de administrar. Por ejemplo, como cambiar el contenido de la ventana principal cuando se selecciona un elemento de barra de herramientas en un **ventana Preferencias** o intercambiar contenido en respuesta a un **lista origen** selección.

Al agregar una nueva vista en el proyecto debe usar siempre el **vista de Cocoa con controlador** opción, ya que esto hace que el proceso de carga de la vista desde el .xib más fácil de archivos.

Para agregar una nueva vista, haga lo siguiente:

1. En el **panel de solución**, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...** .
2. En el cuadro de diálogo nuevo archivo, seleccione **Xamarin.Mac** > **vista de Cocoa con controlador**:

    ![Agregar una nueva vista](xib-images/view01.png "agregar una nueva vista")
3. Escriba `SubviewTable` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el **SubviewTable.xib** archivo para abrirlo y editarlo en Interface Builder y diseñar la interfaz de usuario:

    [![Diseño de la nueva vista en Xcode](xib-images/view02.png "diseñar la nueva vista en Xcode")](xib-images/view02-large.png#lightbox)
5. Conectar las acciones necesarias y las salidas.
6. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

A continuación modifique el **SubviewTable.cs** y agregue el código siguiente a la **AwakeFromNib** archivo para rellenar la nueva vista cuando se cargue:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

Agregue una enumeración para el proyecto para realizar un seguimiento de la vista que actualmente se va a mostrar. Por ejemplo, **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

Edite el archivo .xib de la ventana que se consume la vista y mostrarla. Agregar un **vista personalizada** que actuará como contenedor para la vista cuando se haya cargado en la memoria por C# de código y exponerlo a una salida denominada `ViewContainer`:

[![Creación de la toma de corriente requiere](xib-images/view03.png "crear la salida necesaria")](xib-images/view03-large.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

A continuación, edite el archivo .cs de la ventana que se va a mostrar la nueva vista (por ejemplo, **MainWindow.cs**) y agregue el código siguiente:

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

Cuando es necesario mostrar una vista nueva cargado desde un archivo .xib en contenedor la ventana (el **vista personalizada** agregadas anteriormente), este código controla la eliminación de todas las vistas existentes y su intercambio para el nuevo. Comprueba que ya tiene una vista, si así lo quita de la pantalla. A continuación toma la vista que se ha pasado en (tal y como se carga desde un controlador de vista) cambia de tamaño para ajustarse al área de contenido de y lo agrega al contenido para su presentación.

Para mostrar una nueva vista, use el código siguiente:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Esto crea una nueva instancia del controlador de vista para la nueva vista que se mostrará, establece su tipo (según lo especificado por la enumeración que se agrega al proyecto) y usa el `DisplaySubview` agregado a la clase de la ventana para mostrar la vista de método. Por ejemplo:

[![Ejecutar la aplicación de ejemplo](xib-images/view04.png "ejecutando la aplicación de ejemplo")](xib-images/view04-large.png#lightbox)

Para obtener más información sobre cómo trabajar con Windows en una aplicación de Xamarin.Mac, consulte nuestra [Windows](~/mac/user-interface/window.md) y [cuadros de diálogo](~/mac/user-interface/dialog.md) documentación.


## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con archivos .xib en una aplicación de Xamarin.Mac. Se ha visto los diferentes tipos y usos de archivos .xib para crear la interfaz de usuario de la aplicación, cómo crear y mantener .xib los archivos de Xcode de la interfaz generador y cómo trabajar con archivos .xib en C# código.


## <a name="related-links"></a>Vínculos relacionados

- [MacImages (ejemplo)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menús](~/mac/user-interface/menu.md)
- [Cuadros de diálogo](~/mac/user-interface/dialog.md)
- [Trabajar con imágenes](~/mac/app-fundamentals/image.md)
- [Directrices de interfaz humana de macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
