---
title: "Controles estándar"
description: "En este artículo se explica cómo trabajar con los controles de AppKit estándar, como botones, etiquetas, campos de texto, casillas de verificación y segmentada controles en una aplicación Xamarin.Mac. Describe los agrega a una interfaz con el generador de interfaz e interactuar con ellos en el código."
ms.topic: article
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: e887026b4f87d2e1bf8c7647a7845765ce8b886c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="standard-controls"></a>Controles estándar

_En este artículo se explica cómo trabajar con los controles de AppKit estándar, como botones, etiquetas, campos de texto, casillas de verificación y segmentada controles en una aplicación Xamarin.Mac. Describe los agrega a una interfaz con el generador de interfaz e interactuar con ellos en el código._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tendrá acceso a la misma AppKit que controla un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener los controles de Appkit (o si lo desea crearlos directamente en código de C#).

Los controles de AppKit son los elementos de interfaz de usuario que se utilizan para crear la interfaz de usuario de la aplicación Xamarin.Mac. Se están compuestos de elementos como botones, etiquetas, campos de texto, casillas de verificación y controles segmentados y producir la acciones o resultados visibles cuando un usuario manipula.

[![](standard-controls-images/intro01.png "La pantalla principal de aplicación de ejemplo")](standard-controls-images/intro01.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con controles de AppKit en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>Introducción a los controles y vistas

macOS (anteriormente conocido como Mac OS X) proporciona un conjunto estándar de controles de interfaz de usuario mediante el marco de trabajo AppKit. Se están compuestos de elementos como botones, etiquetas, campos de texto, casillas de verificación y controles segmentados y producir la acciones o resultados visibles cuando un usuario manipula.

Todos los controles de AppKit tienen un aspecto estándar e integrado que será adecuado para la mayoría de los casos, algunos especifican un aspecto alternativo para usarlo en un área del marco de ventana o en un _intensidad efecto_ contexto, como en un área de la barra lateral o en un Widget de centro de notificaciones.

Apple sugerir las siguientes directrices al trabajar con controles de AppKit:

- Evite mezclar tamaños de control en la misma vista.
- En general, evite cambiar el tamaño de los controles verticalmente.
- Usar la fuente del sistema y el tamaño del texto apropiado dentro de un control.
- Utilice el espaciado entre los controles adecuado.

Para obtener más información, consulte Declinatoria la [acerca de los controles y las vistas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>Usar controles en un marco de ventana

Hay un subconjunto de los controles de AppKit que incluyan un estilo de presentación que permite que se incluyen en el área de marco de una ventana. Para obtener un ejemplo, consulte barra de herramientas de la aplicación de correo electrónico:

[![](standard-controls-images/mailapp.png "Un marco de ventana de Mac")](standard-controls-images/mailapp.png#lightbox)

- **Botón de textura de ida y vuelta** - A `NSButton` con un estilo de `NSTexturedRoundedBezelStyle`.
- **Con textura redondea segmentados Control** - A `NSSegmentedControl` con un estilo de `NSSegmentStyleTexturedRounded`.
- **Con textura redondea segmentados Control** - A `NSSegmentedControl` con un estilo de `NSSegmentStyleSeparated`.
- **Menú emergente con textura de ida y vuelta** - A `NSPopUpButton` con un estilo de `NSTexturedRoundedBezelStyle`.
- **Menú de lista desplegable de textura de ida y vuelta** - A `NSPopUpButton` con un estilo de `NSTexturedRoundedBezelStyle`.
- **Barra de búsqueda** - A `NSSearchField`.

Apple sugerir las siguientes directrices al trabajar con controles de AppKit en un marco de ventana:

- No utilice estilos de control específicos de marco de ventana en el cuerpo de la ventana.
- No use los controles de ventana cuerpo o estilos en el marco de ventana.

Para obtener más información, consulte Declinatoria la [acerca de los controles y las vistas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>Crear una interfaz de usuario en el generador de interfaz

Cuando se crea una nueva aplicación de Xamarin.Mac cacao, obtendrá una ventana en blanco, estándar de forma predeterminada. Esta ventana se define en un `.storyboard` archivo incluida automáticamente en el proyecto. Para editar el diseño de windows, en la **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo:

[![](standard-controls-images/edit01.png "Seleccionar el guión gráfico principal en el Explorador de soluciones")](standard-controls-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en el generador de interfaz de Xcode:

[![](standard-controls-images/edit02.png "Editar el guión gráfico en Xcode.")](standard-controls-images/edit02.png#lightbox)

Para crear la interfaz de usuario, podrá arrastrar los elementos de interfaz de usuario (controles AppKit) de la **biblioteca Inspector** a la **Editor de la interfaz** en el generador de interfaz. En el ejemplo siguiente, un **Dividir verticalmente, vista** control ha sido fármaco desde el **biblioteca Inspector** y se coloca en la ventana de la **Editor de la interfaz**:

[![](standard-controls-images/edit03.png "Seleccionar una vista dividida de la biblioteca")](standard-controls-images/edit03.png#lightbox)

Para obtener más información acerca de cómo crear una interfaz de usuario en el generador de interfaz, vea nuestra [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentación.

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>Ajuste de tamaño y colocar

Una vez que se ha incluido un control en la interfaz de usuario, use la **editor de restricciones de** para establecer su ubicación y tamaño escribiendo valores manualmente y controlan el modo en que el control se coloca automáticamente y tamaño cuando la ventana primaria o vista se cambia el tamaño:

[![](standard-controls-images/edit04.png "Establecer las restricciones")](standard-controls-images/edit04.png#lightbox)

Use la **rojos vigas de** alrededor del contorno de la **Autoresizing** cuadro a _stick_ un control a una ubicación especificada (x, y). Por ejemplo: 

[![](standard-controls-images/edit05.png "Edición de una restricción")](standard-controls-images/edit05.png#lightbox)

Especifica que el control seleccionado (en el **vista de jerarquía** & **Editor de la interfaz**) se bloqueará en la ubicación superior y derecha de la ventana o vista, tal y como se cambia el tamaño o mover. 

Otros elementos de las propiedades del control de editor como el alto y ancho:

[![](standard-controls-images/edit06.png "Si se establece el alto")](standard-controls-images/edit06.png#lightbox)

También puede controlar la alineación de los elementos con restricciones mediante la **alineación Editor**:

[![](standard-controls-images/edit07.png "El Editor de alineación")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> A diferencia de iOS donde (0,0) es la esquina superior izquierda de esquina de la pantalla, en macOS (0,0) es la esquina inferior izquierda. Esto es porque macOS usa un sistema de coordenadas matemático con los valores numéricos que aumenten de valor hacia arriba y hacia la derecha. Debe tener esto en cuenta al colocar los controles de AppKit en una interfaz de usuario.

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>Configuración de una clase personalizada

Hay ocasiones al trabajar con controles de AppKit que necesitará en subclase y un control existente y crear propia versión personalizada de esa clase. Por ejemplo, define una versión personalizada de la lista de orígenes:

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Donde la `[Register("SourceListView")]` instrucción expone la `SourceListView` clase para Objective-C, para que sea se puede utilizar en el generador de interfaz. Para obtener más información, consulte el [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) de documento, se explica la `Register` y `Export` comandos usados conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

Con el código anterior en su lugar, puede arrastrar un AppKit Control, del tipo base que se extiende, en la superficie de diseño (en el ejemplo siguiente, un **lista origen**), cambie a la **Inspector de identidad** y establecer el **clase personalizada** en el nombre que expuso a Objective-C (ejemplo `SourceListView`):

[![](standard-controls-images/edit10.png "Configuración de una clase personalizada en Xcode.")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>Exponer las salidas y acciones

Para poder acceder a un AppKit Control en código C#, debe exponer como un **toma** o y **acción**. Para hacerlo, seleccione el control determinado en la vista la **jerarquía de la interfaz** o **Editor de la interfaz** y cambie a la **Ayudante para la vista** (asegúrese de que tiene la `.h`de la ventana seleccionada para su edición):

[![](standard-controls-images/edit11.png "Al seleccionar el archivo correcto para editar")](standard-controls-images/edit11.png#lightbox)

Control y arrastre desde el control de AppKit en el asigne `.h` archivo para empezar a crear un **toma** o **acción**:

[![](standard-controls-images/edit12.png "Arrastre para crear una acción o salida")](standard-controls-images/edit12.png#lightbox)

Seleccione el tipo de riesgo de crear y asignar el **toma** o **acción** una **nombre**: 

[![](standard-controls-images/edit13.png "Configuración de la toma de corriente o acción")](standard-controls-images/edit13.png#lightbox)


Para obtener más información sobre cómo trabajar con **tomas** y **acciones**, vea el [salidas y las acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) sección de nuestro [Introducción a la interfaz y Xcode El generador de](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) documentación.

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>Sincronizar los cambios con Xcode

Cuando cambie a Visual Studio para Mac de Xcode, los cambios realizados en Xcode se sincronizarán automáticamente con el proyecto Xamarin.Mac.

Si selecciona el `SplitViewController.designer.cs` en el **el Explorador de soluciones** , podrá ver cómo la **toma** y **acción** conectarse en el código de C#:

[![](standard-controls-images/sync01.png "Sincronizar los cambios con Xcode")](standard-controls-images/sync01.png#lightbox)

Observe cómo la definición en el `SplitViewController.designer.cs` archivo:

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

Se alineen con la definición en el `MainWindow.h` archivo en Xcode:

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

Como puede ver, Visual Studio para Mac realiza escuchas para los cambios en el `.h` de archivos y, a continuación, se sincroniza automáticamente los cambios en los respectivos `.designer.cs` archivo exponerlos a la aplicación. También puede observar que `SplitViewController.designer.cs` es una clase parcial, para que Visual Studio para Mac no tiene que modificar `SplitViewController.cs ` que sobrescribirá los cambios que hemos realizado en la clase.

Normalmente nunca debe abrir el `SplitViewController.designer.cs` usted mismo, se presenta aquí únicamente con fines formativos.

> [!IMPORTANT]
> En la mayoría de los casos, Visual Studio para Mac automáticamente se verán los cambios realizados en Xcode y sincronizarlas con el proyecto Xamarin.Mac. En caso de que esa sincronización no se realice de forma automática, vuelva a Xcode y después vuelva a Visual Studio para Mac. Normalmente, esto iniciará un ciclo de sincronización.

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>Trabajar con botones

AppKit proporciona varios tipos de botón que puede utilizarse en el diseño de la interfaz de usuario. Para obtener más información, consulte el [botones](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons01.png "Un ejemplo de los diferentes tipos de botones")](standard-controls-images/buttons01.png#lightbox)

Si un botón se haya expuesto a través de un **toma**, el código siguiente responderá a los que se presiona:

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

Para los botones que se han expuesto a través de **acciones**, un `public partial` método automáticamente se creará automáticamente con el nombre que eligió en Xcode. Para responder a la **acción**, complete el método parcial en la clase que la **acción** se definió en. Por ejemplo:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

Para los botones que tienen un estado (como **en** y **desactivar**), el estado se puede comprobar o establecer con el `State` propiedad con respecto a la `NSCellStateValue` enum. Por ejemplo:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

Donde `NSCellStateValue` puede ser:

- **En** : el botón se inserta o se selecciona el control (por ejemplo, una comprobación en una casilla de verificación).
- **Desactivar** : el botón no está presionado o no se selecciona el control.
- **Mixto** -una mezcla de **en** y **desactivar** Estados.

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>Marcar un botón predeterminado y establecer clave equivalente

Para cualquier botón que haya agregado a un diseño de la interfaz de usuario, puede marcar ese botón como el _predeterminado_ botón que se activará cuando el usuario presiona el **Return/ENTRAR** clave en el teclado. En Mac OS, este botón recibirá un color de fondo azul de forma predeterminada.

Para establecer un botón como valor predeterminado, selecciónelo en el generador de interfaz de Xcode. Después, en el **Inspector de atributo**, seleccione el **equivalente de clave** campo y presione la **Return/ENTRAR** clave:

[![](standard-controls-images/buttons03.png "Editar el equivalente de clave")](standard-controls-images/buttons03.png#lightbox)

De igual forma, puede asignar cualquier secuencia de teclas que puede usarse para activar el botón con el teclado en lugar del mouse. Por ejemplo, presionando las teclas de comando-C en la imagen anterior.

Cuando se ejecute la aplicación y la ventana con el botón es clave y centra, si el usuario presiona comando-C, se activará la acción del botón (como-si el usuario hace clic en el botón).

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>Trabajar con las casillas y botones de Radio

AppKit proporciona varios tipos de casillas de verificación y los grupos de botones de Radio que pueden utilizarse en el diseño de la interfaz de usuario. Para obtener más información, consulte el [botones](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/buttons02.png "Un ejemplo de los tipos de casilla de verificación disponibles")](standard-controls-images/buttons02.png#lightbox)


Casillas y botones de Radio (expone a través de **tomas**) tienen un estado (como **en** y **desactivar**), el estado se puede comprobar o establecer con el `State` propiedad con respecto a la `NSCellStateValue` enum. Por ejemplo:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

Donde `NSCellStateValue` puede ser:

- **En** : el botón se inserta o se selecciona el control (por ejemplo, una comprobación en una casilla de verificación).
- **Desactivar** : el botón no está presionado o no se selecciona el control.
- **Mixto** -una mezcla de **en** y **desactivar** Estados.

Para seleccionar un botón en un grupo de botones de Radio, exponer el botón de Radio para seleccionar como un **toma** y establecer su `State` propiedad. Por ejemplo:

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

Para obtener una colección de botones de radio para actuar como un grupo y controlan automáticamente el estado seleccionado, cree un nuevo **acción** y conectar cada botón del grupo a ella:

![](standard-controls-images/buttons04.png "Crear una nueva acción")

A continuación, asigne un nombre único `Tag` a cada botón de radio en el **Inspector de atributo**:

![](standard-controls-images/buttons05.png "Edición de una etiqueta de botón de radio")

Guardar los cambios y vuelva a Visual Studio para Mac, agregue el código para controlar la **acción** que todos los botones de radio están asociados con:

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

Puede usar el `Tag` propiedad para ver qué botón de radio está seleccionado.

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>Trabajar con controles de menú

AppKit proporciona varios tipos de controles de menú que puede utilizarse en el diseño de la interfaz de usuario. Para obtener más información, consulte el [controles de menú](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/menu01.png "Algunos ejemplos de controles de menú")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>Proporciona datos de Control de menú

Los controles de menú disponibles para macOS puede establecerse para rellenar la lista desplegable de una lista interna (que puede ser predefinida en el generador de interfaz o rellenar a través del código), o proporcionando su propio origen de datos personalizado y externo.

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>Trabajar con datos internos

Además de definir elementos en el generador de interfaz, los controles de menú (como `NSComboBox`), proporcionan un conjunto completo de métodos que permiten agregar, editar o eliminar los elementos de la lista interna que mantienen:

- `Add` : Agrega un nuevo elemento al final de la lista.
- `GetItem` -Devuelve el elemento en el índice especificado.
- `Insert` : Inserta un nuevo elemento en la lista en la ubicación especificada.
- `IndexOf` -Devuelve el índice del elemento especificado.
- `Remove` : Quita el elemento especificado de la lista.
- `RemoveAll` -Quita todos los elementos de la lista.
- `RemoveAt` : Quita el elemento en el índice especificado.
- `Count` -Devuelve el número de elementos de la lista.

> [!IMPORTANT]
> Si está utilizando un origen de datos externo (`UsesDataSource = true`), una llamada a cualquiera de los métodos anteriores se iniciará una excepción.

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>Trabajar con un origen de datos externo

En lugar de utilizar los datos internos integrados para proporcionar las filas para el Control de menú, opcionalmente, puede usar un origen de datos externo y proporcionar su propio almacén de copias de seguridad de los elementos (por ejemplo, una base de datos de SQLite).

Para trabajar con un origen de datos externo, podrá crear una instancia de origen de datos del Control de menú (`NSComboBoxDataSource` por ejemplo) e invalide varios métodos para proporcionar los datos necesarios:

- `ItemCount` -Devuelve el número de elementos de la lista.
- `ObjectValueForItem` -Devuelve el valor del elemento de un índice determinado.
- `IndexOfItem` -Devuelve el índice para el valor del elemento determinada.
- `CompletedString` -Devuelve el primer valor de elemento coincidente para el valor del elemento escritos parcialmente. Solo se llama a este método si se ha habilitado la función Autocompletar (`Completes = true`).

Vea la [bases de datos y cuadros combinados](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes) sección de la [trabajar con bases de datos](~/mac/app-fundamentals/databases.md) documento para obtener más detalles.

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>Ajustar la apariencia de la lista

Los métodos siguientes están disponibles para ajustar la apariencia del Control de menú:

- `HasVerticalScroller` -If `true`, el control mostrará una barra de desplazamiento vertical. 
- `VisibleItems` -Ajuste el número de elementos que se muestran cuando se abre el control. El valor predeterminado es cinco (5).
- `IntercellSpacing` -Ajustar la cantidad de espacio alrededor de un elemento determinado, proporcionando un `NSSize` donde el `Width` especifica los márgenes izquierdos y derecho y el `Height` especifica el espacio antes y después de un elemento.
- `ItemHeight` : Especifica el alto de cada elemento en la lista.

Para los tipos de lista desplegable de `NSPopupButtons`, el primer elemento de menú proporciona el título para el control. Por ejemplo: 

[![](standard-controls-images/menu02.png "Un control de menú de ejemplo")](standard-controls-images/menu02.png#lightbox)

Para cambiar el título, exponer este elemento como un **toma** y use código similar al siguiente:

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>Manipular los elementos seleccionados

Los siguientes métodos y propiedades permiten manipular los elementos seleccionados en la lista del Control de menú:

- `SelectItem` -Selecciona el elemento en el índice especificado.
- `Select` -Seleccione el valor del elemento especificado.
- `DeselectItem` -Anula la selección del elemento en el índice especificado.
- `SelectedIndex` -Devuelve el índice del elemento actualmente seleccionado.
- `SelectedValue` -Devuelve el valor del elemento actualmente seleccionado.

Use la `ScrollItemAtIndexToTop` para presentar el elemento en el índice especificado en la parte superior de la lista y `ScrollItemAtIndexToVisible` se desplace a la lista hasta que el elemento en el índice especificado está visible.

<a name="Responding to Events" />

### <a name="responding-to-events"></a>Respuesta a eventos

Los controles de menú proporcionan los siguientes eventos para responder a la interacción del usuario:

- `SelectionChanged` -Se llama cuando el usuario ha seleccionado un valor de la lista.
- `SelectionIsChanging` -Se llama antes de que el nuevo elemento seleccionado por el usuario se convierte en la selección activa.
- `WillPopup` -Se llama antes de que se muestre la lista desplegable de elementos.
- `WillDismiss` -Se llama antes de cerrar la lista desplegable de elementos.

Para `NSComboBox` controles, incluyen todos los mismos eventos como la `NSTextField`, como el `Changed` eventos que se llama cada vez que el usuario edita el valor del texto en el cuadro combinado.

Si lo desea, puede responder la un elementos de menú de datos internos definidos en el generador de interfaz que se selecciona adjuntando el elemento a una **acción** y use código similar al siguiente para responder a **acción** que se va a desencadenados por el usuario:

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

Para obtener más información sobre cómo trabajar con menús y controles de menú, vea nuestra [menús](~/mac/user-interface/menu.md) y [botón emergente y listas desplegables](~/mac/user-interface/menu.md) documentación.

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>Trabajar con controles de selección

AppKit proporciona varios tipos de controles de selección que puede utilizarse en el diseño de la interfaz de usuario. Para obtener más información, consulte el [controles de selección](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/select01.png "Algunos ejemplos de controles de selección")](standard-controls-images/select01.png#lightbox)

Hay dos maneras para realizar el seguimiento cuando un Control de selección tiene la interacción del usuario, si se exponen como un **acción**. Por ejemplo:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

O adjuntando una **delegado** a la `Activated` eventos. Por ejemplo:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

Para establecer o leer el valor de un Control de selección, utilice el `IntValue` propiedad. Por ejemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

Los controles de Especialidad (por ejemplo, Color y bien imagen también) tienen propiedades específicas de sus tipos de valor. Por ejemplo:

```csharp
CollorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

El `NSDatePicker` tiene las propiedades siguientes para trabajar directamente con la fecha y hora:

- **DateValue** -el valor de fecha y hora actual como un `NSDate`.
- **Local** -la ubicación del usuario como un `NSLocal`.
- **TimeInterval** -el valor de hora como un `Double`.
- **Zona horaria** -zona de horaria del usuario como un `NSTimeZone`.

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>Trabajar con controles de indicador

AppKit proporciona varios tipos de controles de indicador que pueden utilizarse en el diseño de la interfaz de usuario. Para obtener más información, consulte el [indicador controles](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/level01.png "Algunos ejemplos de controles de indicador")](standard-controls-images/level01.png#lightbox)

Hay dos maneras de hacer un seguimiento cuando un Control de indicador tiene interacción del usuario, ya sea mediante la exposición de como un **acción** o un **toma** y adjuntar un **delegado** a la `Activated`eventos. Por ejemplo:

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

Para leer o establecer el valor del indicador de Control, use la `DoubleValue` propiedad. Por ejemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

El indeterminado y los indicadores de progreso asincrónico deben animarse cuando aparezca. Use la `StartAnimation` método para iniciar la animación cuando se muestran. Por ejemplo:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

Llamar a la `StopAnimation` método detendrá la animación.

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>Trabajar con controles de texto

AppKit proporciona varios tipos de controles de texto que puede utilizarse en el diseño de la interfaz de usuario. Para obtener más información, consulte el [controles de texto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/). 

[![](standard-controls-images/text01.png "Algunos ejemplos de controles de texto")](standard-controls-images/text01.png#lightbox)

Para los campos de texto (`NSTextField`), los siguientes eventos se pueden utilizar para realizar un seguimiento de la interacción del usuario:

- **Cambiar** -se desencadena siempre que el usuario cambia el valor del campo. Por ejemplo, en cada carácter escrito.
- **EditingBegan** -se desencadena cuando el usuario selecciona el campo para su edición.
- **EditingEnded** : cuando el usuario presiona la tecla ENTRAR en el campo o deja el campo.

Use la `StringValue` propiedad para leer o establecer el valor del campo. Por ejemplo:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

Para los campos que mostrar o modificar los valores numéricos, puede usar el `IntValue` propiedad. Por ejemplo:

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

Un `NSTextView` proporciona una completa texto destacado editar y mostrar el área con el formato integrados. Al igual que un `NSTextField`, use el `StringValue` propiedad para leer o establecer el valor de la región.

Para obtener un ejemplo de un ejemplo complejo de trabajar con vistas de texto en una aplicación Xamarin.Mac, consulte el [aplicación de ejemplo de SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter es un editor de código fuente simple que proporciona compatibilidad con la finalización de código y el resaltado de sintaxis simple.

El código de SourceWriter se ha comentado completamente y, si están disponibles, se han proporcionado vínculos de métodos o tecnologías clave a información relevante en la documentación de las guías de Xamarin.Mac.

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>Trabajar con vistas de contenido

AppKit proporciona varios tipos de vistas de contenido que pueden utilizarse en el diseño de la interfaz de usuario. Para obtener más información, consulte el [vistas contenido](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

[![](standard-controls-images/content01.png "Una vista de contenido de ejemplo")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

Un popover es un elemento de interfaz de usuario transitorio que proporciona la funcionalidad que está directamente relacionado con un determinado un control o un área en la pantalla. Un popover queda flotando sobre la ventana que contiene el control o el área que se relacionan con, y su borde incluye una flecha para indicar el punto desde el que se dedujo.

Para crear un popover, realice lo siguiente:

1. Abra la `.storyboard` archivo de la ventana que desea agregar un popover a haga doble clic en el **el Explorador de soluciones**
2. Arrastre un **ver controlador** desde el **biblioteca Inspector** en el **Editor de la interfaz**: 

    [![](standard-controls-images/content02.png "Seleccionar un controlador de vista de la biblioteca")](standard-controls-images/content02.png#lightbox)
4. Definir el tamaño y el diseño de la **vista personalizada**: 

    [![](standard-controls-images/content04.png "Editar la presentación")](standard-controls-images/content04.png#lightbox)
5. Control y haga clic y arrastre desde el origen de la ventana emergente en la **View Controller**: 

    [![](standard-controls-images/content05.png "Arrastre para crear un segue")](standard-controls-images/content05.png#lightbox)
6. Seleccione **Popover** en el menú emergente: 

    [![](standard-controls-images/content06.png "Establecer el tipo de segue")](standard-controls-images/content06.png#lightbox)
7. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

<a name="Tab_Views" />

### <a name="tab-views"></a>Ficha vistas

Ficha vistas consta de una lista de pestaña (que parece similar a un Control segmentados) se combina con un conjunto de vistas que se denominan _paneles_. Cuando el usuario selecciona una nueva pestaña, se mostrará el panel que se conectan a él. Cada panel contiene su propio conjunto de controles.

Cuando se trabaja con una vista de ficha en el generador de interfaz de Xcode, use la **atributo Inspector** para establecer el número de pestañas:

[![](standard-controls-images/content08.png "Editar el número de fichas")](standard-controls-images/content08.png#lightbox)

Seleccione cada pestaña en el **jerarquía de interfaz** para establecer su **título** y agregar los elementos de interfaz de usuario a su **panel**:

[![](standard-controls-images/content09.png "Edición de las fichas en Xcode.")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>Controles de AppKit de enlace de datos

Mediante el uso de técnicas de codificación de clave-valor y enlace de datos en la aplicación Xamarin.Mac, puede reducir considerablemente la cantidad de código que se debe escribir y mantener para rellenar y trabajar con elementos de interfaz de usuario. También tiene la ventaja de separar aún más los datos de copia de seguridad (_modelo de datos_) de la parte delantera terminar la interfaz de usuario (_Model-View-Controller_), provocando fáciles de mantener, aplicación más flexible diseño.

Codificación de clave y valor (KVC) es un mecanismo para tener acceso a propiedades de un objeto indirectamente, mediante claves (cadenas con formato especial) para identificar las propiedades en lugar de obtener acceso a ellos a través de las variables de instancia o métodos de descriptor de acceso (`get/set`). Mediante la implementación de clave y valor de codificación compatible con descriptores de acceso en la aplicación Xamarin.Mac, obtendrá acceso a las características de otras macOS como observar de clave y valor (KVO), enlace de datos, datos básicos, enlaces de cacao y scriptability.

Para obtener más información, consulte el [enlace de datos complejos](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding) sección de nuestro [enlace de datos y valores de clave de codificación](~/mac/app-fundamentals/databinding.md) documentación.


<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con los controles de AppKit estándar, como botones, etiquetas, campos de texto, casillas de verificación y controles segmentada en una aplicación Xamarin.Mac. Lo cubre agregarlas a un diseño de la interfaz de usuario en el generador de interfaz de Xcode, exponerlas al código mediante las salidas y las acciones y trabajar con controles de AppKit en código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacControls (ejemplo)](https://developer.xamarin.com/samples/mac/MacControls/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Acerca de los controles y vistas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)
