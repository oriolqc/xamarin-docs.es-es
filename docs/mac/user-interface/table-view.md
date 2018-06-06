---
title: Vistas de tabla en Xamarin.Mac
description: Este artículo explica cómo trabajar con vistas de tabla en una aplicación Xamarin.Mac. Se describe la creación de vistas de tabla en Xcode y el generador de interfaz e interactuar con ellos en el código.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: da26810869f23b8861ffb4409248c56bff12a521
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793235"
---
# <a name="table-views-in-xamarinmac"></a>Vistas de tabla en Xamarin.Mac

_Este artículo explica cómo trabajar con vistas de tabla en una aplicación Xamarin.Mac. Se describe la creación de vistas de tabla en Xcode y el generador de interfaz e interactuar con ellos en el código._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tendrá acceso a la misma tabla que ve un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener las vistas de tabla (o si lo desea crearlos directamente en código de C#).

Una vista de tabla muestra los datos en formato tabular que contiene una o varias columnas de información en varias filas. Según el tipo de vista de tabla que se está creando, el usuario puede ordenar por columna, reorganizar las columnas, agregar columnas, quitar columnas o editar los datos contenidos en la tabla.

[![](table-view-images/intro01.png "Tabla de ejemplo")](table-view-images/intro01.png#lightbox)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con vistas de tabla en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Introducción a las vistas de tabla

Una vista de tabla muestra los datos en formato tabular que contiene una o varias columnas de información en varias filas. Vistas de la tabla se muestran dentro de vistas de desplazamiento (`NSScrollView`) y a partir de macOS 10.7, puede usar cualquier `NSView` en lugar de las celdas (`NSCell`) para mostrar las filas y columnas. Es decir, todavía puede usar `NSCell` sin embargo, normalmente deberá subclase `NSTableCellView` y crear las columnas y filas personalizadas.

Una vista de tabla no almacena sus propios datos, en su lugar se basa en un origen de datos (`NSTableViewDataSource`) para proporcionar tanto las filas y las columnas necesarias, según sea necesario.

Se puede personalizar el comportamiento de una vista de tabla proporcionando una subclase del delegado de la vista de tabla (`NSTableViewDelegate`) para admitir la administración de la columna de tabla, escriba para seleccionar la funcionalidad, selección de filas y de edición, seguimiento personalizado y vistas personalizadas para las columnas individuales y filas.

Al crear vistas de tabla, Apple sugiere lo siguiente:

* Permitir al usuario ordenar la tabla haciendo clic en los encabezados de columna.
* Crear encabezados de columna son los sustantivos o frases nominales cortas que describen los datos que se muestran en esa columna.

Para obtener más información, consulte el [vistas contenido](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) sección de Apple [directrices de interfaz humana de OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Crear y mantener las vistas de tabla en Xcode.

Cuando se crea una nueva aplicación de Xamarin.Mac cacao, obtendrá una ventana en blanco, estándar de forma predeterminada. Esta ventana se define en un `.storyboard` archivo incluida automáticamente en el proyecto. Para editar el diseño de windows, en la **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo:

[![](table-view-images/edit01.png "Al seleccionar el guión gráfico principal")](table-view-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en el generador de interfaz de Xcode:

[![](table-view-images/edit02.png "Edición de la interfaz de usuario en Xcode.")](table-view-images/edit02.png#lightbox)

Tipo de `table` en el **Inspector de biblioteca** cuadro de búsqueda para que resulten más fáciles de encontrar los controles de vista de tabla:

[![](table-view-images/edit03.png "Seleccionar una vista de tabla de la biblioteca")](table-view-images/edit03.png#lightbox)

Arrastre el controlador de vista en una vista de tabla el **Editor de la interfaz**, asegúrese de rellenar el área de contenido del controlador de vista y establézcalo en donde se reduce y crece a la ventana de la **Editor de restricciones de**:

[![](table-view-images/edit04.png "Modificar restricciones")](table-view-images/edit04.png#lightbox)

Seleccione la vista de tabla en la **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[![](table-view-images/edit05.png "El Inspector de atributo")](table-view-images/edit05.png#lightbox)

- **Modo de contenido** -le permite usar cualquiera de las vistas (`NSView`) o celdas (`NSCell`) para mostrar los datos en las filas y columnas. A partir de macOS 10.7, debe usar las vistas.
- **Flote hasta llegar al grupo de filas** : si `true`, la vista de tabla, se dibujará celdas agrupadas como si sean flotantes.
- **Columnas** -define el número de columnas mostradas.
- **Encabezados de** : si `true`, las columnas tendrán encabezados.
- **Reordenación de** : si `true`, el usuario podrá arrastrar reordenar las columnas de la tabla.
- **Cambiar el tamaño de** : si `true`, el usuario podrá arrastrar encabezados de columna para cambiar el tamaño de las columnas.
- **Tamaño de las columnas** -controla cómo la tabla automáticamente las columnas de tamaño.
- **Resaltar** -controla el tipo de resaltado en la tabla que se utiliza cuando se selecciona una celda.
- **Sombrear las filas alternativas** : si `true`, alguna otra fila tendrá un color de fondo diferente.
- **Cuadrícula horizontal** -selecciona el tipo de borde que se dibujan entre las celdas horizontalmente.
- **Cuadrícula vertical** -selecciona el tipo de borde que se dibujan entre las celdas verticalmente.
- **Color de cuadrícula** -establece el color de borde de celda.
- **Fondo** -establece el color de fondo de celda.
- **Selección** -le permiten controlar cómo el usuario puede seleccionar las celdas de la tabla como:
    - **Varios** : si `true`, el usuario puede seleccionar varias filas y columnas.
    - **Columna** : si `true`, el usuario puede seleccionar las columnas.
    - **Escriba Select** : si `true`, el usuario puede escribir un carácter para seleccionar una fila.
    - **Vacía** : si `true`, el usuario no es necesario que seleccione una fila o columna, la tabla permite ninguna selección en absoluto.
- **Autoguardar** -guardar el nombre que el formato de tablas automáticamente es bajo.
- **Información de la columna** : si `true`, el orden y el ancho de las columnas se guardarán automáticamente.
- **Saltos de línea** : seleccione cómo la celda controla los saltos de línea.
- **Trunca la última línea Visible** : si `true`, la celda se truncará en el puede datos no caben dentro de sus límites.

> [!IMPORTANT]
> A menos que se está realizando el mantenimiento de una aplicación heredada de Xamarin.Mac, `NSView` vistas en función de la tabla debe usarse por `NSCell` en función de las vistas de tabla. `NSCell` se considera heredada y no puede ser compatible en el futuro.

Seleccione una columna de tabla en la **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[![](table-view-images/edit06.png "El Inspector de atributo")](table-view-images/edit06.png#lightbox)

- **Título** -establece el título de la columna.
- **Alineación** -establecer la alineación del texto dentro de las celdas.
- **Fuente de título** -selecciona la fuente para el texto de encabezado de la celda.
- **Criterio de ordenación** -es la clave utilizada para ordenar los datos de la columna. Deje en blanco si el usuario no puede ordenar esta columna.
- **Selector de** -es el **acción** utilizada para realizar la ordenación. Deje en blanco si el usuario no puede ordenar esta columna.
- **Orden** -es el criterio de ordenación para los datos de las columnas.
- **Cambiar el tamaño de** -selecciona el tipo de cambio de tamaño de la columna.
- **Puede editar** : si `true`, el usuario puede editar las celdas de una tabla en función de celda.
- **Oculta** : si `true`, la columna está oculta.

También puede cambiar el tamaño de la columna arrastrando su identificador (centrado verticalmente en el lado derecho de la columna) dejados o la derecha.

Vamos a seleccionar la cada columna en la vista de tabla y proporcione la primera columna un **título** de `Product` y la segunda se `Details`.

Seleccione una vista de la celda de tabla (`NSTableViewCell`) en el **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[![](table-view-images/edit07.png "El Inspector de atributo")](table-view-images/edit07.png#lightbox)

Estas son todas las propiedades de una vista estándar. También tiene la opción de cambiar el tamaño de las filas de esta columna aquí.

Seleccione una celda de la vista de tabla (de forma predeterminada, se trata de un `NSTextField`) en el **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[![](table-view-images/edit08.png "El Inspector de atributo")](table-view-images/edit08.png#lightbox)

Tendrá todas las propiedades de un campo de texto estándar para establecer aquí. De forma predeterminada, un campo de texto estándar se utiliza para mostrar los datos de una celda de una columna.

Seleccione una vista de la celda de tabla (`NSTableFieldCell`) en el **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[![](table-view-images/edit09.png "El Inspector de atributo")](table-view-images/edit09.png#lightbox)

Estas opciones más importantes son:

- **Diseño** : seleccione cómo se distribuyen las celdas de esta columna.
- **Usa el modo de una sola línea** : si `true`, la celda se limita a una sola línea.
- **Ancho del diseño en tiempo de ejecución primera** : si `true`, la celda preferirán el ancho establecido para él (manual o automáticamente) cuando se muestra la primera vez que se ejecuta la aplicación.
- **Acción** -controla cuándo la edición **acción** se envía para la celda.
- **Comportamiento** -define si hay una celda editable o seleccionable.
- **Rich texto** : si `true`, la celda puede mostrar texto con formato y estilo.
- **Deshacer** : si `true`, la celda asume la responsabilidad de es deshacer el comportamiento.

Seleccione la vista de la celda de tabla (`NSTableFieldCell`) en la parte inferior de una columna de tabla en la **jerarquía de interfaz**:

[![](table-view-images/edit10.png "Seleccionar la vista de la celda de tabla")](table-view-images/edit10.png#lightbox)

Esto le permite editar la vista de celda de tabla utilizada como base _patrón_ para todas las celdas que se crea para la columna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Adición de acciones y las salidas

Simplemente como cualquier otro control de interfaz de usuario de cacao, es necesario exponer la vista de tabla y se las columnas y las celdas al uso de código de C# **acciones** y **tomas** (según la funcionalidad necesaria).

El proceso es el mismo para cualquier elemento de vista de tabla que se va a exponer:

1. Cambie a la **Ayudante Editor** y asegúrese de que el `ViewController.h` se selecciona el archivo: 

    [![](table-view-images/edit11.png "El Editor de Asistente")](table-view-images/edit11.png#lightbox)
2. Seleccione la vista de tabla en la **jerarquía de interfaz**, control y haga clic y arrastre hasta el `ViewController.h` archivo.
3. Crear un **toma** para llama a la vista de tabla `ProductTable`: 

    [![](table-view-images/edit13.png "Configurar una salida")](table-view-images/edit13.png#lightbox)
4. Crear **tomas** para las columnas de tablas también denominada `ProductColumn` y `DetailsColumn`: 

    [![](table-view-images/edit14.png "Configurar una salida")](table-view-images/edit14.png#lightbox)
5. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

A continuación, se escribirá la visualización de código algunos datos de la tabla cuando se ejecuta la aplicación.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Rellenar la vista de tabla

Con la vista de tabla diseñado en el generador de interfaz y la expone a través de un **toma**, a continuación se necesita crear el código de C# para rellenarla.

En primer lugar, vamos a crear un nuevo `Product` clase para contener la información de las filas individuales. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `Product` para el **nombre** y haga clic en el **New** botón:

[![](table-view-images/populate01.png "Crear una clase vacía")](table-view-images/populate01.png#lightbox)

Realizar el `Product.cs` archivo aspecto similar al siguiente:

```csharp
using System;

namespace MacTables
{
    public class Product
    {
        #region Computed Propoperties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        #endregion

        #region Constructors
        public Product ()
        {
        }

        public Product (string title, string description)
        {
            this.Title = title;
            this.Description = description;
        }
        #endregion
    }
}

```

A continuación, necesitamos crear una subclase de `NSTableDataSource` para proporcionar los datos de nuestra tabla según se solicite. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `ProductTableDataSource` para el **nombre** y haga clic en el **New** botón.

Editar la `ProductTableDataSource.cs` de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
    public class ProductTableDataSource : NSTableViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetRowCount (NSTableView tableView)
        {
            return Products.Count;
        }
        #endregion
    }
}

```

Esta clase tiene el almacenamiento para los elementos de la vista de la tabla e invalida el `GetRowCount` para devolver el número de filas en la tabla.

Por último, necesitamos crear una subclase de `NSTableDelegate` para proporcionar el comportamiento de la tabla. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `ProductTableDelegate` para el **nombre** y haga clic en el **New** botón.

Editar la `ProductTableDelegate.cs` de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
    public class ProductTableDelegate: NSTableViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductTableDataSource DataSource;
        #endregion

        #region Constructors
        public ProductTableDelegate (ProductTableDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = DataSource.Products [(int)row].Title;
                break;
            case "Details":
                view.StringValue = DataSource.Products [(int)row].Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Cuando se crea una instancia de la `ProductTableDelegate`, pasamos también en una instancia de la `ProductTableDataSource` que proporciona los datos para la tabla. El `GetViewForItem` método es responsable de devolver una vista (datos) para mostrar la celda de una determinada columna y fila. Si es posible, se volverán a una vista existente para mostrar la celda, si no se debe crear una nueva vista.

Para rellenar la tabla, editar la `ViewController.cs` de archivos y realice la `AwakeFromNib` método aspecto similar al siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

Si se ejecuta la aplicación, se muestra lo siguiente:

[![](table-view-images/populate02.png "Ejecutar una aplicación de ejemplo")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Ordenar por columna

Vamos a permitir al usuario ordenar los datos en la tabla haciendo clic en un encabezado de columna. En primer lugar, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione el `Product` columna, escriba `Title` para el **criterio de ordenación**, `compare:` para el **Selector** y seleccione `Ascending` para el **orden**:

[![](table-view-images/sort01.png "Configuración de la clave de ordenación")](table-view-images/sort01.png#lightbox)

Seleccione el `Details` columna, escriba `Description` para el **criterio de ordenación**, `compare:` para el **Selector** y seleccione `Ascending` para el **orden**:

[![](table-view-images/sort02.png "Configuración de la clave de ordenación")](table-view-images/sort02.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Ahora vamos a editar el `ProductTableDataSource.cs` de archivos y agregue los métodos siguientes:

```csharp
public void Sort(string key, bool ascending) {

    // Take action based on key
    switch (key) {
    case "Title":
        if (ascending) {
            Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
        } else {
            Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
        }
        break;
    case "Description":
        if (ascending) {
            Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
        } else {
            Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
        }
        break;
    }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    if (oldDescriptors.Length > 0) {
        // Update sort
        Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    } else {
        // Grab current descriptors and update sort
        NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
        Sort (tbSort[0].Key, tbSort[0].Ascending); 
    }
            
    // Refresh table
    tableView.ReloadData ();
}
```

El `Sort` permite el método ordenar los datos en el origen de datos basado en un determinado `Product` campo de clase en orden ascendente o descendente. Reemplazados `SortDescriptorsChanged` se llama al método cada vez que el uso que hace clic en un encabezado de columna. Se pasará el **clave** valor que se establece en el generador de interfaz y el criterio de ordenación para esa columna.

Si se ejecuta la aplicación y haga clic en los encabezados de columna, las filas se ordenará por esa columna:

[![](table-view-images/sort03.png "Ejecutar una aplicación de ejemplo")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selección de filas

Si desea permitir al usuario seleccionar una sola fila, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de tabla en la **jerarquía de la interfaz** y desactive el **varios** casilla de verificación en la **Inspector de atributo**:

[![](table-view-images/select01.png "El Inspector de atributo")](table-view-images/select01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.


A continuación, edite la `ProductTableDelegate.cs` de archivos y agregue el método siguiente:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

Esto le permitirá al usuario seleccionar una sola fila en la vista de tabla. Devolver `false` para el `ShouldSelectRow` para cualquier fila que no desea que el usuario para que pueda seleccionar o `false` para cada fila si no desea que el usuario para que pueda seleccionar todas las filas.

La vista de tabla (`NSTableView`) contiene los siguientes métodos para trabajar con la selección de filas:

- `DeselectRow(nint)` -Anula la selección de la fila especificada en la tabla.
- `SelectRow(nint,bool)` -Selecciona la fila especificada. Pasar `false` para el segundo parámetro seleccionar solo una fila cada vez.
- `SelectedRow` -Devuelve la fila actual seleccionada en la tabla.
- `IsRowSelected(nint)` -Devuelve `true` si se selecciona la fila especificada.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selección múltiple de filas

Si desea permitir al usuario seleccionar un varias filas, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de tabla en la **jerarquía de la interfaz** y comprobar la **varios** casilla de verificación en la **Inspector de atributo**:

[![](table-view-images/select02.png "El Inspector de atributo")](table-view-images/select02.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.


A continuación, edite la `ProductTableDelegate.cs` de archivos y agregue el método siguiente:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

Esto le permitirá al usuario seleccionar una sola fila en la vista de tabla. Devolver `false` para el `ShouldSelectRow` para cualquier fila que no desea que el usuario para que pueda seleccionar o `false` para cada fila si no desea que el usuario para que pueda seleccionar todas las filas.

La vista de tabla (`NSTableView`) contiene los siguientes métodos para trabajar con la selección de filas:

- `DeselectAll(NSObject)` -Anula la selección de todas las filas de la tabla. Use `this` para el primer parámetro enviar en el objeto que realiza la selección. 
- `DeselectRow(nint)` -Anula la selección de la fila especificada en la tabla.
- `SelectAll(NSobject)` -Selecciona todas las filas de la tabla. Use `this` para el primer parámetro enviar en el objeto que realiza la selección.
- `SelectRow(nint,bool)` -Selecciona la fila especificada. Pasar `false` para el segundo parámetro, anule la selección y seleccione una única fila, pasar `true` para ampliar la selección e incluir esta fila.
- `SelectRows(NSIndexSet,bool)` -Selecciona el conjunto de filas especificado. Pasar `false` para el segundo parámetro, anule la selección y seleccione sólo un estas filas, pasar `true` para ampliar la selección e incluir estas filas.
- `SelectedRow` -Devuelve la fila actual seleccionada en la tabla.
- `SelectedRows` -Devuelve un `NSIndexSet` que contiene los índices de las filas seleccionadas.
- `SelectedRowCount` -Devuelve el número de filas seleccionadas.
- `IsRowSelected(nint)` -Devuelve `true` si se selecciona la fila especificada.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo para seleccionar las filas

Si desea permitir que el usuario escribe un carácter con la vista de la tabla seleccionada y seleccione la primera fila que tiene ese carácter, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de tabla en la **jerarquía de la interfaz** y compruebe el **Seleccione tipo** checkbox en el **Inspector de atributo**:

[![](table-view-images/type01.png "Establecer el tipo de selección")](table-view-images/type01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Ahora vamos a editar el `ProductTableDelegate.cs` de archivos y agregue el método siguiente:

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
    nint row = 0;
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains(searchString)) return row;

        // Increment row counter
        ++row;
    }

    // If not found select the first row
    return 0;
}
```

El `GetNextTypeSelectMatch` método toma el determinado `searchString` y devuelve la fila de la primera `Product` que tenga esta cadena en su `Title`.

Si se ejecuta la aplicación y escribir un carácter, se selecciona una fila:

[![](table-view-images/type02.png "Ejecutar una aplicación de ejemplo")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Reordenar columnas

Si desea permitir al usuario arrastrar reordenar las columnas en la vista de tabla, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de tabla en la **jerarquía de interfaz** y comprobar la **reaprovisionamiento** casilla de verificación en la **Inspector de atributo**:

[![](table-view-images/reorder01.png "El Inspector de atributo")](table-view-images/reorder01.png#lightbox)

Si se proporciona un valor para el **Autoguardado** propiedad y compruebe el **información de la columna** campo, los cambios que realice en el diseño de la tabla se guardará automáticamente para que podamos y restauración la próxima vez que la aplicación se ejecuta.

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Ahora vamos a editar el `ProductTableDelegate.cs` de archivos y agregue el método siguiente:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

El `ShouldReorder` método debe devolver `true` para cualquier columna que va a permitir que arrastre reordenan en la `newColumnIndex`, else return `false`;

Si se ejecuta la aplicación, podemos arrastrar alrededor de los encabezados de columna para reordenar las columnas:

[![](table-view-images/reorder02.png "Un ejemplo de las columnas reordenados")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Editar las celdas

Si desea permitir al usuario editar los valores de una celda determinada, edite la `ProductTableDelegate.cs` y cambie el `GetViewForItem` método tal como se indica a continuación:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = true;

        view.EditingEnded += (sender, e) => {
                    
            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.Tag].Title = view.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.Tag].Description = view.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Ahora si se ejecuta la aplicación, el usuario puede editar las celdas de la vista de tabla:

[![](table-view-images/editing01.png "Un ejemplo de editar una celda")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Uso de imágenes en las vistas de tabla

Para incluir una imagen como parte de la celda en una `NSTableView`, deberá cambiar cómo se devuelven los datos de la vista de tabla `NSTableViewDelegate's` `GetViewForItem` método que se usa un `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Para obtener más información, consulte el [utilizando imágenes con vistas de tabla](~/mac/app-fundamentals/image.md) sección de nuestro [trabajar con imágenes](~/mac/app-fundamentals/image.md) documentación.

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Agregar un botón Eliminar para una fila

En función de los requisitos de la aplicación, puede haber ocasiones donde debe proporcionar un botón de acción para cada fila de la tabla. Como un ejemplo de esto, vamos a ampliar el ejemplo de vista de la tabla creado anteriormente para incluir un **eliminar** botón en cada fila.

En primer lugar, edite el `Main.storyboard` en el generador de interfaz de Xcode, seleccione la vista de tabla y aumentar el número de columnas a tres (3). A continuación, cambie la **título** de la nueva columna a `Action`:

[![](table-view-images/delete01.png "Editar el nombre de columna")](table-view-images/delete01.png#lightbox)

Guarde los cambios en el guión gráfico y vuelva a Visual Studio para Mac sincronizar los cambios.

A continuación, edite la `ViewController.cs` de archivos y agregue el siguiente método público:

```csharp
public void ReloadTable ()
{
    ProductTable.ReloadData ();
}
```

En el mismo archivo, modifique la creación del delegado de la vista de tabla nuevo dentro de `ViewDidLoad` método tal como se indica a continuación:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

Ahora, edite el `ProductTableDelegate.cs` archivo para incluir una conexión privada con el controlador de vista y para tomar el controlador como un parámetro al crear una nueva instancia del delegado:

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
    this.Controller = controller;
    this.DataSource = datasource;
}
#endregion
```

A continuación, agregue el siguiente nuevo método privado a la clase:

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
    // Add to view
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);

    // Configure
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    // Wireup events
    view.TextField.EditingEnded += (sender, e) => {

        // Take action based on type
        switch (view.Identifier) {
        case "Product":
            DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
            break;
        case "Details":
            DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
            break;
        }
    };

    // Tag view
    view.TextField.Tag = row;
}
```

Esto lleva a cabo todos las configuraciones de la vista de texto que se realizaba anteriormente en el `GetViewForItem` método y los coloca en una ubicación única, que se puede llamar (como la última columna de la tabla no incluye una vista de texto, pero un botón).

Por último, edite el `GetViewForItem` método y darle un aspecto similar al siguiente:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();

        // Configure the view
        view.Identifier = tableColumn.Title;

        // Take action based on title
        switch (tableColumn.Title) {
        case "Product":
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
            ConfigureTextField (view, row);
            break;
        case "Details":
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
            ConfigureTextField (view, row);
            break;
        case "Action":
            // Create new button
            var button = new NSButton (new CGRect (0, 0, 81, 16));
            button.SetButtonType (NSButtonType.MomentaryPushIn);
            button.Title = "Delete";
            button.Tag = row;

            // Wireup events
            button.Activated += (sender, e) => {
                // Get button and product
                var btw = sender as NSButton;
                var product = DataSource.Products [(int)btn.Tag];

                // Configure alert
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
                    MessageText = $"Delete {product.Title}?",
                };
                alert.AddButton ("Cancel");
                alert.AddButton ("Delete");
                alert.BeginSheetForResponse (Controller.View.Window, (result) => {
                    // Should we delete the requested row?
                    if (result == 1001) {
                        // Remove the given row from the dataset
                        DataSource.Products.RemoveAt((int)btn.Tag);
                        Controller.ReloadTable ();
                    }
                });
            };

            // Add to view
            view.AddSubview (button);
            break;
        }

    }

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tag.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        view.TextField.Tag = row;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        view.TextField.Tag = row;
        break;
    case "Action":
        foreach (NSView subview in view.Subviews) {
            var btw = subview as NSButton;
            if (btw != null) {
                btn.Tag = row;
            }
        }
        break;
    }

    return view;
}
```

Echemos un vistazo a varias secciones de este código con más detalle. Primero, si un nuevo `NSTableViewCell` es que se creó la acción se realiza basándose en el nombre de la columna. Para las dos primeras columnas (**producto** y **detalles**), el nuevo `ConfigureTextField` se llama al método.

Para el **acción** columna, un nuevo `NSButton` se crea y se agrega a la celda como una vista secundaria:

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

El botón `Tag` propiedad se utiliza para contener el número de la fila que se está procesando actualmente. Este número se usará más adelante cuando el usuario solicita una fila va a eliminar en el botón `Activated` eventos:

```csharp
// Wireup events
button.Activated += (sender, e) => {
    // Get button and product
    var btw = sender as NSButton;
    var product = DataSource.Products [(int)btn.Tag];

    // Configure alert
    var alert = new NSAlert () {
        AlertStyle = NSAlertStyle.Informational,
        InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
        MessageText = $"Delete {product.Title}?",
    };
    alert.AddButton ("Cancel");
    alert.AddButton ("Delete");
    alert.BeginSheetForResponse (Controller.View.Window, (result) => {
        // Should we delete the requested row?
        if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
        }
    });
};
```

Al principio del controlador de eventos, obtenemos el botón y el producto que se encuentra en la fila de tabla determinada. A continuación, se presenta una alerta al usuario confirmar la eliminación de la fila. Si el usuario decide eliminar la fila, se quita la fila especificada del origen de datos y se vuelve a cargar la tabla:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Por último, si se va a reutilizar la celda de la vista de tabla en lugar de crearse de nuevo, el siguiente código configura basándose en la columna que se está procesando:

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
case "Action":
    foreach (NSView subview in view.Subviews) {
        var btw = subview as NSButton;
        if (btw != null) {
            btn.Tag = row;
        }
    }
    break;
}

```

Para el **acción** columna, se examinan todas las vistas de Sub hasta la `NSButton` se encuentra, es `Tag` propiedad se actualiza para señalar a la fila actual.

Con estos cambios en su lugar, cuando la aplicación se ejecuta cada fila tendrá un **eliminar** botón:

[![](table-view-images/delete02.png "La vista de tabla con botones de eliminación")](table-view-images/delete02.png#lightbox)

Cuando el usuario hace clic en un **eliminar** botón, aparecerá una alerta en el que se le pedirá que elimine la fila especificada:

[![](table-view-images/delete03.png "Una alerta de supresión de fila")](table-view-images/delete03.png#lightbox)

Si el usuario elige eliminar, se quitará la fila y se volverá a dibujar en la tabla:

[![](table-view-images/delete04.png "La tabla después de elimina la fila")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Vistas de tabla de enlace de datos

Mediante el uso de técnicas de codificación de clave-valor y enlace de datos en la aplicación Xamarin.Mac, puede reducir considerablemente la cantidad de código que se debe escribir y mantener para rellenar y trabajar con elementos de interfaz de usuario. También tiene la ventaja de separar aún más los datos de copia de seguridad (_modelo de datos_) de la parte delantera terminar la interfaz de usuario (_Model-View-Controller_), provocando fáciles de mantener, aplicación más flexible diseño.

Codificación de clave y valor (KVC) es un mecanismo para tener acceso a propiedades de un objeto indirectamente, mediante claves (cadenas con formato especial) para identificar las propiedades en lugar de obtener acceso a ellos a través de las variables de instancia o métodos de descriptor de acceso (`get/set`). Mediante la implementación de clave y valor de codificación compatible con descriptores de acceso en la aplicación Xamarin.Mac, obtendrá acceso a las características de otras macOS como observar de clave y valor (KVO), enlace de datos, datos básicos, enlaces de cacao y scriptability.

Para obtener más información, consulte el [enlace de datos de la vista de tabla](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) sección de nuestro [enlace de datos y valores de clave de codificación](~/mac/app-fundamentals/databinding.md) documentación.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con vistas de tabla en una aplicación Xamarin.Mac. Se vio los distintos tipos y los usos de vistas de tabla, cómo crear y mantener las vistas de tabla en el generador de interfaz de Xcode y cómo trabajar con vistas de la tabla en el código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacTables (ejemplo)](https://developer.xamarin.com/samples/mac/MacTables/)
- [MacImages (ejemplo)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de esquema](~/mac/user-interface/outline-view.md)
- [Listas de origen](~/mac/user-interface/source-list.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
