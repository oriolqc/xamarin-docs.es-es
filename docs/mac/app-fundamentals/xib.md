---
title: archivos de .xib en Xamarin.Mac
description: Este artículo tratan trabajar con archivos de .xib creados en el generador de interfaz de Xcode para crear y mantener las interfaces de usuario para una aplicación Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 3ef536ddb19ed60975368bd022e57c34c6f473dc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792279"
---
# <a name="xib-files-in-xamarinmac"></a>archivos de .xib en Xamarin.Mac

_Este artículo tratan trabajar con archivos de .xib creados en el generador de interfaz de Xcode para crear y mantener las interfaces de usuario para una aplicación Xamarin.Mac._

> [!NOTE]
> Es la mejor manera de crear una interfaz de usuario para una aplicación Xamarin.Mac con guiones gráficos. Esta documentación se ha dejado en su lugar, por motivos históricos y para trabajar con proyectos Xamarin.Mac más antiguos. Para obtener más información, vea nuestra [Introducción a guiones gráficos](~/mac/platform/storyboards/index.md) documentación.

## <a name="overview"></a>Información general

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tener acceso a los mismos elementos de interfaz de usuario y herramientas que un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener las interfaces de usuario (o si lo desea crearlos directamente en código de C#).

Se utiliza un archivo de .xib por macOS para definir elementos de interfaz de usuario de la aplicación (por ejemplo, los menús, Windows, vistas, las etiquetas, campos de texto) que se crean y mantienen de forma gráfica en el generador de interfaz de Xcode.

[![Un ejemplo de la aplicación en ejecución](xib-images/intro01.png "muestra un ejemplo de la aplicación en ejecución")](xib-images/intro01-large.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con archivos de .xib en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, ya que cubre los conceptos claves y las técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` atributos se utiliza para conectarlo las clases de C# a los objetos de Objective-C y la interfaz de usuario de elementos.


## <a name="introduction-to-xcode-and-interface-builder"></a>Introducción a Xcode y a Interface Builder

Como parte de Xcode, Apple ha creado una herramienta denominada Generador de interfaz, que le permite crear visualmente la interfaz de usuario en un diseñador. Xamarin.Mac se integra con fluidez con el generador de interfaz, lo que le permite crear la interfaz de usuario con las mismas herramientas que lo hacen los usuarios de Objective-C de objetivo.


### <a name="components-of-xcode"></a>Componentes de Xcode

Al abrir un archivo de .xib en Xcode desde Visual Studio para Mac, se abre con una **Project Navigator** a la izquierda, el **jerarquía de interfaz** y **Editor de la interfaz** en la parte central y un **propiedades & utilidades** sección de la derecha:

[![Los componentes de la UI de Xcode](xib-images/xcode03.png "los componentes de la UI de Xcode")](xib-images/xcode03-large.png#lightbox)

¡Eche un vistazo a cada uno de estos Xcode secciones hace y cómo se usará para crear la interfaz de la aplicación Xamarin.Mac.


#### <a name="project-navigation"></a>Navegación en el proyecto

Cuando se abre un archivo .xib para su edición en Xcode, Visual Studio para Mac crea un archivo de proyecto de Xcode en segundo plano para comunicar los cambios entre él y Xcode. Más adelante, cuando cambie a Visual Studio para Mac de Xcode, los cambios realizados en este proyecto se sincronizan con el proyecto Xamarin.Mac mediante Visual Studio para Mac.

El **navegación en el proyecto** sección le permite navegar entre todos los archivos que componen este _corrección de compatibilidad_ proyecto Xcode. Normalmente, sólo estará interesado en los archivos .xib en esta lista como **MainMenu.xib** y **MainWindow.xib**.


#### <a name="interface-hierarchy"></a>Jerarquía de interfaz

El **jerarquía de interfaz** sección le permite acceder fácilmente a varias propiedades de clave de la interfaz de usuario, como su **marcadores de posición** y principal **ventana**. También puede utilizar esta sección para obtener acceso a los elementos individuales (vistas) que componen la interfaz de usuario y el ajuste la forma en que están anidadas, arrástrelos alrededor de la jerarquía.


#### <a name="interface-editor"></a>Editor de la interfaz

El **Editor de la interfaz** sección proporciona la superficie en la que se gráficamente diseño del interfaz de usuario. Podrá arrastrar elementos desde la **biblioteca** sección de la **propiedades & utilidades** sección para crear el diseño. Cuando agregue elementos de interfaz de usuario (vistas) a la superficie de diseño, se agregará a la **jerarquía de interfaz** sección en el orden en que aparecen en la **Editor de la interfaz**.


#### <a name="properties--utilities"></a>Propiedades y utilidades

El **propiedades & utilidades** sección se divide en dos secciones principales que se va a trabajar con, **propiedades** (también denominados inspectores) y la **biblioteca**:

![El Inspector de propiedad](xib-images/xcode04.png "el Inspector de propiedad")

Inicialmente en esta sección está prácticamente vacía, sin embargo si se selecciona un elemento en el **Editor de la interfaz** o **jerarquía de interfaz**, **propiedades** se rellenará la sección con información sobre las propiedades que puede ajustar y un elemento determinado.

En la sección **Propiedades** hay 8 *pestañas de inspectores* diferentes, como se muestra en la siguiente ilustración:

[![Información general de todos los inspectores](xib-images/xcode05.png "una visión general de todos los inspectores")](xib-images/xcode05-large.png#lightbox)

De izquierda a derecha, estas pestañas son:

-   **File Inspector** (Inspector de archivos): en esta pestaña se muestra la información del archivo, como el nombre de archivo y la ubicación del archivo Xib que se está editando.
-   **Ayuda rápida**: en la pestaña Ayuda rápida se proporciona ayuda contextual según lo que está seleccionado en Xcode.
-   **Identity Inspector** (Inspector de identidad): en esta pestaña se proporciona información sobre el control o la vista seleccionados.
-   **Atributos Inspector** – el Inspector de atributos permite personalizar varios atributos de la vista de control seleccionada /.
-   **Cambiar el tamaño de Inspector** – el Inspector de tamaño le permite controlar el tamaño y el comportamiento de la vista de control seleccionada y el cambio de tamaño.
-   **Inspector de conexiones** – el Inspector de conexiones muestra las conexiones de salida y la acción de los controles seleccionados. Examinaremos las salidas y las acciones en un momento.
-   **Inspector de enlaces** – el Inspector de enlaces le permite configurar los controles de modo que sus valores se enlazan automáticamente a los modelos de datos.
-   **Ver los efectos Inspector** : Inspector de efectos de la vista permite especificar los efectos en los controles, como las animaciones.

En el **biblioteca** sección, puede buscar los controles y objetos que se colocan gráficamente en el diseñador para crear la interfaz de usuario:

![Un ejemplo del Inspector de biblioteca de](xib-images/xcode06.png "muestra un ejemplo del Inspector de biblioteca")

Ahora que está familiarizado con el Xcode IDE y el generador de interfaz, echemos un vistazo a usarla para crear una interfaz de usuario.


## <a name="creating-and-maintaining-windows-in-xcode"></a>Creación y mantenimiento de windows en Xcode.

Es el método preferido para crear la interfaz de usuario de la aplicación de Xamarin.Mac con guiones gráficos (vea nuestro [Introducción a guiones gráficos](~/mac/platform/storyboards/index.md) documentación para obtener más información) y, por tanto, cualquier nuevo proyecto iniciado sesión Xamarin.Mac usar guiones gráficos de forma predeterminada.

Para cambiar a mediante un .xib basada en interfaz de usuario, haga lo siguiente:

1. Abra Visual Studio para Mac e inicie un nuevo proyecto de Xamarin.Mac.
2. En el **solución Pad**, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...**
3. Seleccione **Mac** > **controlador Windows**:

    ![Agregar un nuevo controlador de ventana](xib-images/setup00.png "agregando un nuevo controlador de ventana")
4. Escriba `MainWindow` para el nombre y haga clic en el **New** botón:

    ![Agregar una nueva ventana principal](xib-images/setup01.png "agregar una nueva ventana principal")
5. Haga doble clic en el proyecto nuevo y seleccione **agregar** > **nuevo archivo...**
6. Seleccione **Mac** > **menú principal**:

    ![Agregar un nuevo menú principal](xib-images/setup02.png "agregar un nuevo menú principal")
7. Deje el nombre como `MainMenu` y haga clic en el **New** botón.
8. En el **solución Pad** seleccione la **Main.storyboard** de archivos, haga clic en y seleccione **quitar**:

    ![Al seleccionar el guión gráfico principal](xib-images/setup03.png "seleccionando el guión gráfico principal")
9. En el cuadro de diálogo Quitar, haga clic en el **eliminar** botón:

    ![Confirmar la eliminación](xib-images/setup04.png "confirmar la eliminación")
10. En el **solución Pad**, haga doble clic en el **Info.plist** archivo para abrirlo y editarlo.
11. Seleccione `MainMenu` desde el **interfaz principal** lista desplegable:

    [![Establecer el menú principal](xib-images/setup05.png "establecer el menú principal")](xib-images/setup05-large.png#lightbox)
12. En el **solución Pad**, haga doble clic en el **MainMenu.xib** archivo para abrirlo y editarlo en el generador de interfaz de Xcode.
13. En el **biblioteca Inspector**, tipo `object` en el campo de búsqueda, a continuación, arrastre una nueva **objeto** a la superficie de diseño:

    [![El menú principal de edición](xib-images/setup06.png "el menú principal de edición")](xib-images/setup06-large.png#lightbox)
14. En el **identidad Inspector**, escriba `AppDelegate` para el **clase**:

    [![Al seleccionar el delegado de la aplicación](xib-images/setup07.png "seleccionando el delegado de aplicación")](xib-images/setup07-large.png#lightbox)
15. Seleccione **propietario del archivo** desde el **jerarquía de la interfaz**, cambie a la **conexión Inspector** y arrastre una línea desde el delegado al que el `AppDelegate` **Objeto** acaba de agregar al proyecto:

    [![Conectar el delegado de la aplicación](xib-images/setup08.png "conectar el delegado de aplicación")](xib-images/setup08-large.png#lightbox)
16. Guardar los cambios y volver a Visual Studio para Mac.

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

Ahora la ventana principal de la aplicación se define en un **.xib** archivo se incluye automáticamente en el proyecto al agregar un controlador de ventana. Para editar el diseño de windows, en la **solución Pad**, haga doble clic en el **MainWindow.xib** archivo:

![Seleccione el archivo MainWindow.xib](xib-images/edit01.png "seleccionando el archivo MainWindow.xib")

Se abrirá el diseño de ventana en el generador de interfaz de Xcode:

[![Editar la MainWindow.xib](xib-images/edit02.png "editar la MainWindow.xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>Flujo de trabajo de ventana estándar

En cualquier ventana que permite crear y trabajar con en la aplicación Xamarin.Mac, el proceso es básicamente el mismo:

1. Para nuevas ventanas que no son los predeterminados que se agregan automáticamente al proyecto, agregue una nueva definición de ventana para el proyecto.
2. Haga doble clic en el archivo .xib para abrir el diseño de ventana para su edición en el generador de interfaz de Xcode.
3. Establecer las propiedades de la ventana requerida en el **atributo Inspector** y **Inspector de tamaño**.
4. Arrastre los controles necesarios para compilar la interfaz y configurarlos en el **Inspector de atributo**.
5. Use la **Inspector de tamaño** para controlar el cambio de tamaño para los elementos de interfaz de usuario.
6. Exponer elementos de interfaz de usuario de la ventana al código de C# a través de distribuidores y acciones.
7. Guardar los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.


### <a name="designing-a-window-layout"></a>Definir un diseño de ventana

El proceso para diseñar una interfaz de usuario en el generador de interfaz es básicamente el mismo para todos los elementos que agregue:

1. Busque el control deseado en el **biblioteca Inspector** y arrástrela a la **Editor de la interfaz** y ubíquelo.
2. Establecer las propiedades de la ventana requerida en el **Inspector de atributo**.
3. Use la **Inspector de tamaño** para controlar el cambio de tamaño para los elementos de interfaz de usuario.
4. Si está utilizando una clase personalizada, establézcalo la **Inspector de identidad**.
5. Exponer los elementos de interfaz de usuario al código de C# a través de distribuidores y acciones.
6. Guardar los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Por ejemplo:

1. En Xcode, arrastre un **botón de comando** de la **sección Biblioteca**:

    [![Si se selecciona un botón de la biblioteca](xib-images/xcode07.png "si se selecciona un botón de la biblioteca")](xib-images/xcode07-large.png#lightbox)
2. Coloque el botón en el **ventana** en el **Editor de la interfaz**:

    [![Agregar un botón a la ventana](xib-images/xcode08.png "agregar un botón a la ventana")](xib-images/xcode08-large.png#lightbox)
3. Haga clic en la propiedad **Título** del **Attribute Inspector** (Inspector de atributos) y cambie el título del botón por `Click Me`:

    ![Establece los atributos del botón](xib-images/xcode09.png "establecer los atributos de botón")
4. Arrastre una **Etiqueta** de la **sección Biblioteca**:

    [![Al seleccionar una etiqueta en la biblioteca](xib-images/xcode10.png "al seleccionar una etiqueta en la biblioteca")](xib-images/xcode10-large.png#lightbox)
5. Coloque la etiqueta en la **Ventana** situada junto al botón en el **Interface Editor** (Editor de la interfaz):

    [![Agregar una etiqueta a la ventana](xib-images/xcode11.png "agregar una etiqueta a la ventana")](xib-images/xcode11-large.png#lightbox)
6. Agarre el controlador derecho de la etiqueta y arrástrelo hasta que esté cerca del borde de la ventana:

    [![Cambiar el tamaño de la etiqueta](xib-images/xcode12.png "cambiar el tamaño de la etiqueta")](xib-images/xcode12-large.png#lightbox)
7. Con la etiqueta sigue seleccionada en el **Editor de la interfaz**, cambie a la **Inspector de tamaño**:

    ![Al seleccionar el Inspector de tamaño](xib-images/xcode13.png "seleccionando el Inspector de tamaño")
8. En el **cuadro Cambiar automáticamente el tamaño** haga clic en el **Dim corchete rojo** a la derecha y la **Dim flecha Horizontal de rojo** en el centro de:

    ![Editar las propiedades de ajuste automático de tamaño](xib-images/xcode14.png "editar las propiedades de ajuste automático de tamaño")
9. Esto garantiza que la etiqueta se estirará para aumentar y reducir tal y como se cambia el tamaño de la ventana de la aplicación en ejecución. El **corchetes de color rojo** y la parte superior e izquierda de la **cuadro Cambiar automáticamente el tamaño** cuadro indicar la etiqueta que esté atascada en su X e Y ubicaciones.
10. Guarde los cambios en la interfaz de usuario

Tal y como estaban cambiando el tamaño y mueva los controles en, se debe que haya observado que el generador de interfaz proporciona sugerencias útiles de complemento que se basan en [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). Estas instrucciones le ayudará a crear aplicaciones de alta calidad que tendrán una apariencia familiar para los usuarios de Mac.

Si observa el **jerarquía de interfaz** sección, tenga en cuenta cómo se muestran el diseño y la jerarquía de los elementos que componen la interfaz de usuario:

![Seleccionar un elemento en la jerarquía de interfaz](xib-images/xcode15.png "seleccionar un elemento en la jerarquía de interfaz")

Desde aquí puede seleccionar elementos para editar o arrastre para reordenar los elementos de interfaz de usuario si es necesario. Por ejemplo, si un elemento de interfaz de usuario que se está cubierto por otro elemento, puede arrastrar a la parte inferior de la lista para que sea el elemento de nivel superior en la ventana.

Para obtener más información sobre cómo trabajar con Windows en una aplicación Xamarin.Mac, visite nuestro [Windows](~/mac/user-interface/window.md) documentación.


## <a name="exposing-ui-elements-to-c-code"></a>Exponer elementos de interfaz de usuario al código de C#

Cuando haya terminado de diseñar la apariencia de la interfaz de usuario en el generador de interfaz, debe exponer elementos de la interfaz de usuario para que resulten accesibles desde el código C#. Para ello, usará las acciones y las salidas.


### <a name="setting-a-custom-main-window-controller"></a>Configurar un controlador de la ventana principal personalizado

Para poder crear salidas y las acciones para exponer elementos de interfaz de usuario al código de C#, la aplicación Xamarin.Mac deberá usar un controlador de ventana personalizados.

Haga lo siguiente:

1. Abra el guión gráfico de la aplicación en el generador de interfaz de Xcode.
2. Seleccione el `NSWindowController` en la superficie de diseño.
3. Cambie a la **identidad Inspector** ver y especificar `WindowController` como el **nombre de la clase**:

    [![Editar el nombre de clase](xib-images/windowcontroller01.png "editar el nombre de clase")](xib-images/windowcontroller01-large.png#lightbox)
4. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar.
5. A **WindowController.cs** archivo se agregará al proyecto en el **panel de solución** en Visual Studio para Mac:

    ![El nuevo nombre de clase en Visual Studio para Mac](xib-images/windowcontroller02.png "el nuevo nombre de clase en Visual Studio para Mac")
6. Vuelva a abrir el guión gráfico en el generador de interfaz de Xcode.
7. El **WindowController.h** archivo estará disponible para su uso:

    [![El archivo .h coincidente en Xcode](xib-images/windowcontroller03.png "el archivo .h correspondiente en Xcode.")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>Distribuidores y acciones

Por lo tanto, ¿cuáles son las salidas y las acciones? En la programación tradicional de interfaz de usuario de .NET, un control de la interfaz de usuario se expone de forma automática como una propiedad cuando se agrega. En Mac es algo diferente, al agregar simplemente un control a una vista, el código no puede obtener acceso a él. El desarrollador debe exponer de forma explícita el elemento de interfaz de usuario al código. En orden hacer esto, Apple nos proporciona dos opciones:

-  **Salidas**: las salidas son análogas a las propiedades. Si conecte un control a un enchufe, se expone al código a través de una propiedad, por lo que puede hacer cosas como adjuntar controladores de eventos, llamar a métodos en ella, etcetera.
-  **Acciones**: las acciones son análogas al patrón de comando en WPF. Por ejemplo, cuando se realiza una acción en un control, supongamos un clic del botón, el control llama automáticamente a un método en el código. Las acciones son eficaz y conveniente porque puede conectar muchos controles para la misma acción.

En Xcode, salidas y las acciones se agregan directamente en código mediante *al arrastrar el Control*. Más concretamente, esto significa que para crear una acción o salida, elija qué elemento de control que le gustaría agregar una acción o salida, mantenga presionada la **Control** situado en el teclado y arrastre dicho control directamente en el código.

Para los desarrolladores de Xamarin.Mac, esto significa que arrastre en los archivos de código auxiliar de Objective-C que corresponden al archivo C# donde desea crear la toma de corriente o acción. Visual Studio para Mac creó un archivo denominado **MainWindow.h** como parte de la corrección de compatibilidad proyecto Xcode genera para utilizar el generador de la interfaz:

[![Un ejemplo de un archivo .h en Xcode](xib-images/xcode16.png "muestra un ejemplo de un archivo .h en Xcode.")](xib-images/xcode16-large.png#lightbox)

Este archivo de código auxiliar .h refleja el **MainWindow.designer.cs** que se agrega automáticamente a un proyecto de Xamarin.Mac cuando un nuevo `NSWindow` se crea. Este archivo se utilizará para sincronizar los cambios realizados por el generador de interfaz y es donde se creará los distribuidores y acciones para que los elementos de interfaz de usuario se exponen al código de C#.


#### <a name="adding-an-outlet"></a>Agregar una salida

Con una comprensión básica de las salidas y las acciones, echemos un vistazo a crear una salida para exponer un elemento de interfaz de usuario en el código de C#.

Haga lo siguiente:

1. En Xcode, en la esquina superior disponible más a la derecha de la pantalla, haga clic en el botón de **doble círculo** para abrir **Assistant Editor** (Editor del asistente):

    [![Al seleccionar el Editor de Ayudante](xib-images/outlet01.png "seleccionando el Editor de Asistente")](xib-images/outlet01-large.png#lightbox)
2. Xcode cambiará a un modo de vista en dos paneles con el **Interface Editor** (Editor de la interfaz) en un lado y un **Editor de código** en el otro.
3. Tenga en cuenta que se ha seleccionado automáticamente Xcode el **MainWindowController.m** un archivo en el **Editor de código**, que no es correcto. Si recuerda de la descripción de lo que las salidas y las acciones que se están por encima, es necesario tener la **MainWindow.h** seleccionado.
4. En la parte superior de la **Editor de código** haga clic en el **vínculo automático** y seleccione la **MainWindow.h** archivo:

    [![Seleccione el archivo .h correcto](xib-images/outlet02.png "al seleccionar el archivo .h correcto")](xib-images/outlet02-large.png#lightbox)
5. Ahora, Xcode debería tener el archivo correcto seleccionado:

    [![El archivo correcto seleccionado](xib-images/outlet03.png "el archivo correcto seleccionado")](xib-images/outlet03-large.png#lightbox)
6. **El último paso es muy importante.** Si no tiene el archivo correcto seleccionado, no podrá crear salidas y las acciones o se expondrán a la clase incorrecta en C#.
7. En el **Editor de la interfaz**, mantenga presionada la tecla el **Control** teclas del teclado y haga clic y arrastre la etiqueta que hemos creado anteriormente en el editor de código justo debajo del `@interface MainWindow : NSWindow { }` código:

    [![Arrastre para crear una nueva toma](xib-images/outlet04.png "arrastrar para crear una nueva salida")](xib-images/outlet04-large.png#lightbox)
8. Aparecerá un cuadro de diálogo. Deje el **conexión** establecido en la toma de corriente y escriba `ClickedLabel` para el **nombre**:

    [![Establecer las propiedades de la toma de corriente](xib-images/outlet05.png "estableciendo las propiedades de salida")](xib-images/outlet05-large.png#lightbox)
9. Haga clic en el **conectar** botón para crear la salida:

    ![La salida completada](xib-images/outlet06.png "la toma de corriente completada")
10. Guarde los cambios en el archivo.


#### <a name="adding-an-action"></a>Agregar una acción

A continuación, echemos un vistazo a la creación de una acción para exponer una interacción del usuario con el elemento de interfaz de usuario en el código de C#.

Haga lo siguiente:

1. Asegúrese de que se está todavía en el **Ayudante para el Editor** y **MainWindow.h** archivo está visible en el **Editor de código**.
2. En el **Editor de la interfaz**, mantenga presionada la tecla el **Control** teclas del teclado y haga clic y arrastre el botón que hemos creado anteriormente en el editor de código justo debajo del `@property (assign) IBOutlet NSTextField *ClickedLabel;` código:

    [![Arrastre para crear una acción](xib-images/action01.png "arrastrar para crear una acción")](xib-images/action01-large.png#lightbox)
3. Cambiar el **conexión** tipo de acción:

    [![Seleccione un tipo de acción](xib-images/action02.png "seleccionar un tipo de acción")](xib-images/action02-large.png#lightbox)
4. Escriba `ClickedButton` como **Nombre**:

    [![Configuración de la acción](xib-images/action03.png "configuración de la acción")](xib-images/action03-large.png#lightbox)
5. Haga clic en el **conectar** botón para crear la acción:

    ![La acción completada](xib-images/action04.png "la acción completada")
6. Guarde los cambios en el archivo.

Con la interfaz de usuario con cable telefónico y expone al código de C#, vuelva a Visual Studio para Mac y permiten sincronizar los cambios de Xcode y del generador de interfaz.


### <a name="writing-the-code"></a>Escribir el código

Con la interfaz de usuario creada y sus elementos de interfaz de usuario expuestas al código a través de distribuidores y acciones, está listo para escribir el código para dar vida a los del programa. Por ejemplo, abra el **MainWindow.cs** archivo para su edición haciendo doble clic en él en el **solución panel**:

[![El archivo MainWindow.cs](xib-images/code01.png "MainWindow.cs el archivo")](xib-images/code01-large.png#lightbox)

Y agregue el código siguiente a la `MainWindow` clase para que funcionen con la salida de ejemplo que haya creado anteriormente:

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

Tenga en cuenta que la `NSLabel` se tiene acceso a en C# con el nombre directo que se asigna en Xcode cuando crea su salida en Xcode, en este caso, se denomina `ClickedLabel`. Se puede tener acceso a cualquier método o propiedad del objeto expuesto del mismo modo que lo haría con cualquier clase normal de C#.

> [!IMPORTANT]
> Debe usar `AwakeFromNib`, en lugar de otro método, como `Initialize`, porque `AwakeFromNib` se denomina _después_ ha cargado el sistema operativo y se crea una instancia de la interfaz de usuario desde el archivo .xib. Si ha intentado tener acceso al control de etiqueta antes de que el archivo .xib vez totalmente cargado y crea una instancia, obtendría un `NullReferenceException` error porque todavía no se crearán el control de etiqueta.

A continuación, agregue la siguiente clase parcial para la `MainWindow` clase:

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

Este código asocia a la acción que ha creado en Xcode y el generador de interfaz y se llamará siempre que el usuario hace clic en el botón.

Algunos elementos de interfaz de usuario generan automáticamente en acciones, por ejemplo, elementos de la barra de menús de forma predeterminada como la **abrir...**  elemento de menú (`openDocument:`). En el **solución Pad**, haga doble clic en el **AppDelegate.cs** archivo para abrirlo y editarlo y agregue el código siguiente el `DidFinishLaunching` método:

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

La línea de clave aquí es `[Export ("openDocument:")]`, indica `NSMenu` que la **AppDelegate** tiene un método `void OpenDialog (NSObject sender)` que responda a la `openDocument:` acción.

Para obtener más información sobre cómo trabajar con menús, vea nuestra [menús](~/mac/user-interface/menu.md) documentación.


## <a name="synchronizing-changes-with-xcode"></a>Sincronizar los cambios con Xcode

Cuando cambie a Visual Studio para Mac de Xcode, los cambios realizados en Xcode se sincronizarán automáticamente con el proyecto Xamarin.Mac.

Si selecciona el **MainWindow.designer.cs** en el **solución Pad** , podrá ver cómo la toma de corriente y acción conectar en el código de C#:

[![Sincronizar los cambios con Xcode](xib-images/sync01.png "sincronizar cambios con Xcode")](xib-images/sync01-large.png#lightbox)

Observe cómo las dos definiciones en el **MainWindow.designer.cs** archivo:

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

Como puede ver, Visual Studio para Mac realiza escuchas para los cambios en el archivo .h y, a continuación, se sincroniza automáticamente los cambios en los respectivos **. designer.cs** archivo exponerlos a la aplicación. También puede observar que **MainWindow.designer.cs** es una clase parcial, para que Visual Studio para Mac no tiene que modificar **MainWindow.cs** que sobrescribirá los cambios que hemos realizado en la clase.

Normalmente nunca debe abrir el **MainWindow.designer.cs** usted mismo, se presenta aquí únicamente con fines formativos.

> [!IMPORTANT]
> En la mayoría de los casos, Visual Studio para Mac automáticamente se verán los cambios realizados en Xcode y sincronizarlas con el proyecto Xamarin.Mac. En caso de que esa sincronización no se realice de forma automática, vuelva a Xcode y después vuelva a Visual Studio para Mac. Normalmente, esto iniciará un ciclo de sincronización.


## <a name="adding-a-new-window-to-a-project"></a>Agregar una nueva ventana a un proyecto

Además de la ventana del documento principal, una aplicación Xamarin.Mac deba mostrar otros tipos de windows para el usuario, como las preferencias de Inspector de paneles. Al agregar una nueva ventana para el proyecto debe utilizar siempre el **ventana cacao con controlador** opción, a medida que esto hace que el proceso de carga de la ventana de la .xib más fácil de archivos.

Para agregar una nueva ventana, haga lo siguiente:

1. En el **solución Pad**, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...** .
2. En el cuadro de diálogo nuevo archivo, seleccione **Xamarin.Mac** > **ventana cacao con controlador**:

    ![Agregar un nuevo controlador de ventana](xib-images/new01.png "agregando un nuevo controlador de ventana")
3. Escriba `PreferencesWindow` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el **PreferencesWindow.xib** archivo para abrirlo y editarlo en el generador de interfaz:

    [![Edición de la ventana en Xcode](xib-images/new02.png "edición de la ventana en Xcode.")](xib-images/new02-large.png#lightbox)
5. Diseñar la interfaz:

    [![Definir el diseño de windows](xib-images/new03.png "definir el diseño de windows")](xib-images/new03-large.png#lightbox)
6. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Agregue el código siguiente a **AppDelegate.cs** para mostrar la ventana nueva:

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

El `var preferences = new PreferencesWindowController ();` línea crea una nueva instancia del controlador de ventana que carga la ventana desde el archivo .xib y lo aumenta. El `preferences.Window.MakeKeyAndOrderFront (this);` línea muestra la ventana nueva para el usuario.

Si ejecuta el código y seleccione el **preferencias...**  desde el **menú aplicación**, se mostrará la ventana:

![Ejecuta la aplicación de ejemplo](xib-images/new04.png "ejecutando la aplicación de ejemplo")

Para obtener más información sobre cómo trabajar con Windows en una aplicación Xamarin.Mac, visite nuestro [Windows](~/mac/user-interface/window.md) documentación.


## <a name="adding-a-new-view-to-a-project"></a>Agregar una nueva vista a un proyecto

Hay ocasiones en que es más fácil de diseño de la ventana se dividen en varios archivos .xib más fácil de administrar. Por ejemplo, como cambiar el contenido de la ventana principal cuando se selecciona un elemento de barra de herramientas en un **ventana Preferencias** o cambiar el contenido en respuesta a un **lista origen** selección.

Al agregar una nueva vista en el proyecto debe utilizar siempre el **vista cacao con controlador** opción, a medida que esto hace que el proceso de carga de la vista de la .xib más fácil de archivos.

Para agregar una nueva vista, haga lo siguiente:

1. En el **solución Pad**, haga doble clic en el proyecto y seleccione **agregar** > **nuevo archivo...** .
2. En el cuadro de diálogo nuevo archivo, seleccione **Xamarin.Mac** > **vista cacao con controlador**:

    ![Agregar una nueva vista](xib-images/view01.png "agregar una nueva vista")
3. Escriba `SubviewTable` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el **SubviewTable.xib** archivo para abrirlo y editarlo en el generador de interfaz y diseñar la interfaz de usuario:

    [![Diseñar la nueva vista en Xcode](xib-images/view02.png "diseñar la nueva vista en Xcode.")](xib-images/view02-large.png#lightbox)
5. Conecte las acciones necesarias y las salidas.
6. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

A continuación modifique la **SubviewTable.cs** y agregue el código siguiente a la **AwakeFromNib** archivo para rellenar la nueva vista cuando se cargue:

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

Edite el archivo .xib de la ventana que se consumen la vista y mostrarlo. Agregar un **vista personalizada** que actuará como contenedor para la vista una vez que se carga en memoria mediante código C# y exponer a un enchufe denominada `ViewContainer`:

[![Crear la salida necesaria](xib-images/view03.png "crear la salida necesaria")](xib-images/view03-large.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

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

Cuando es necesario mostrar una nueva vista cargado desde un archivo .xib en el contenedor de la ventana (la **vista personalizada** agregadas anteriormente), este código controla la eliminación de todas las vistas existentes y cambiarlo de nuevo. Comprueba que ya tiene una vista, si así lo quita de la pantalla. A continuación toma la vista que se ha pasado en (tal y como se carga desde un controlador de vista) cambia de tamaño para ajustarse al área de contenido de y lo agrega al contenido para su presentación.

Para mostrar una vista nueva, utilice el código siguiente:

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

Esto crea una nueva instancia del controlador de vista para la nueva vista que se mostrará, establece su tipo (según lo especificado por la enumeración que se agrega al proyecto) y utiliza la `DisplaySubview` agregado a la clase de la ventana para ver la vista de método. Por ejemplo:

[![Ejecuta la aplicación de ejemplo](xib-images/view04.png "ejecutando la aplicación de ejemplo")](xib-images/view04-large.png#lightbox)

Para obtener más información sobre cómo trabajar con Windows en una aplicación Xamarin.Mac, visite nuestro [Windows](~/mac/user-interface/window.md) y [cuadros de diálogo](~/mac/user-interface/dialog.md) documentación.


## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con archivos de .xib en una aplicación Xamarin.Mac. Hemos visto los diferentes tipos y usos de los archivos de .xib para crear la interfaz de usuario de la aplicación, cómo crear y mantener .xib archivos en Xcode la interfaz de generador y cómo trabajar con archivos de .xib en código C#.


## <a name="related-links"></a>Vínculos relacionados

- [MacImages (ejemplo)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menús](~/mac/user-interface/menu.md)
- [Cuadros de diálogo](~/mac/user-interface/dialog.md)
- [Trabajar con imágenes](~/mac/app-fundamentals/image.md)
- [macOS directrices de interfaz humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
