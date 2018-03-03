---
title: Vistas de esquema
description: "Este artículo explica cómo trabajar con vistas de esquema en una aplicación Xamarin.Mac. Describe cómo crear y mantener las vistas de esquema en Xcode y el generador de interfaz y trabajar con ellos mediante programación."
ms.topic: article
ms.prod: xamarin
ms.assetid: 675B9405-D9A7-49F0-94AD-417F10A71D11
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a125c2991c9b8c2453fad396c1d0baebe10be015
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="outline-views"></a>Vistas de esquema

_Este artículo explica cómo trabajar con vistas de esquema en una aplicación Xamarin.Mac. Describe cómo crear y mantener las vistas de esquema en Xcode y el generador de interfaz y trabajar con ellos mediante programación._

Cuando se trabaja con C# y .NET en una aplicación Xamarin.Mac, tiene acceso al mismo esquema que ve un desarrollador que trabaja *Objective-C* y *Xcode* does. Dado que Xamarin.Mac se integra directamente en Xcode, puede usar del Xcode _interfaz generador_ para crear y mantener las vistas de esquema (o si lo desea crearlos directamente en código de C#).

Una vista de esquema es un tipo de tabla que permite al usuario expandir o contraer las filas de datos jerárquicos. Como una vista de tabla, una vista de esquema muestra datos de un conjunto de elementos relacionados, con filas que representan los elementos individuales y las columnas que representan los atributos de los elementos. A diferencia de una vista de tabla, los elementos en una vista de esquema no están en una lista plana, están organizados en una jerarquía, como archivos y carpetas en un disco duro.

[ ![](outline-view-images/populate03.png "Ejecutar una aplicación de ejemplo")](outline-view-images/populate03.png)

En este artículo, se tratarán los conceptos básicos sobre cómo trabajar con vistas de esquema en una aplicación Xamarin.Mac. Se recomienda trabajar a través de la [Hola, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode y el generador de interfaz](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) y [distribuidores y acciones](~/mac/get-started/hello-mac.md#Outlets_and_Actions) secciones, tal como se explica conceptos clave y técnicas que usaremos en este artículo.

Puede echar un vistazo a la [clases de C# exponer / métodos para Objective-C](~/mac/internals/how-it-works.md) sección de la [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento también se explica la `Register` y `Export` comandos Usar conexión de seguridad de las clases de C# para Objective-C objetos y elementos de interfaz de usuario.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Introducción a las vistas de esquema

Una vista de esquema es un tipo de tabla que permite al usuario expandir o contraer las filas de datos jerárquicos. Como una vista de tabla, una vista de esquema muestra datos de un conjunto de elementos relacionados, con filas que representan los elementos individuales y las columnas que representan los atributos de los elementos. A diferencia de una vista de tabla, los elementos en una vista de esquema no están en una lista plana, están organizados en una jerarquía, como archivos y carpetas en un disco duro.

Si un elemento en una vista de esquema contiene otros elementos, se puede expandir o contraer por el usuario. Un elemento expandible muestra un triángulo, que señala a la derecha cuando el elemento está contraído y apunta hacia abajo cuando el elemento está expandido. Al hacer clic en el triángulo hace que el elemento expandir o contraer.

La vista de esquema (`NSOutlineView`) es una subclase de la vista de tabla (`NSTableView`) y como tal, hereda la mayor parte de su comportamiento de su clase primaria. Como resultado, muchas operaciones admitidas por una vista de tabla, como la selección de filas o columnas, cambiar la posición de las columnas arrastrando los encabezados de columna, etc., también se admiten por una vista de esquema. Una aplicación de Xamarin.Mac tiene el control de estas características y puede configurar los parámetros de la vista de esquema (ya sea en código o el generador de interfaz) para activar o desactivar determinadas operaciones.

Una vista de esquema no almacena sus propios datos, en su lugar se basa en un origen de datos (`NSOutlineViewDataSource`) para proporcionar tanto las filas y las columnas necesarias, según sea necesario.

Se puede personalizar el comportamiento de la vista de esquema proporcionando una subclase del delegado de la vista de esquema (`NSOutlineViewDelegate`) para admitir la administración de la columna de esquema, escriba para seleccionar la funcionalidad, selección de filas y de edición, seguimiento personalizado y vistas personalizadas de cliente individual las columnas y filas.

Dado que una vista esquema comparte gran parte de su comportamiento y la funcionalidad con una vista de tabla, puede ir a través de nuestro [vistas de la tabla](~/mac/user-interface/table-view.md) documentación antes de continuar con este artículo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Crear y mantener las vistas de esquema en Xcode.

Cuando se crea una nueva aplicación de Xamarin.Mac cacao, obtendrá una ventana en blanco, estándar de forma predeterminada. Esta ventana se define en un `.storyboard` archivo incluida automáticamente en el proyecto. Para editar el diseño de windows, en la **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo:

[ ![](outline-view-images/edit01.png "Al seleccionar el guión gráfico principal")](outline-view-images/edit01.png)

Se abrirá el diseño de ventana en el generador de interfaz de Xcode:

[ ![](outline-view-images/edit02.png "Edición de la interfaz de usuario en Xcode.")](outline-view-images/edit02.png)

Tipo de `outline` en el **Inspector de biblioteca** cuadro de búsqueda para que resulten más fáciles de encontrar los controles de vista de esquema:

[ ![](outline-view-images/edit03.png "Seleccionar una vista de esquema de la biblioteca")](outline-view-images/edit03.png)

Arrastre el controlador de vista en una vista de esquema el **Editor de la interfaz**, asegúrese de rellenar el área de contenido del controlador de vista y establézcalo en donde se reduce y crece a la ventana de la **Editor de restricciones de**:

[ ![](outline-view-images/edit04.png "Las restricciones de edición")](outline-view-images/edit04.png)

Seleccione la vista de esquema en el **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[ ![](outline-view-images/edit05.png "El Inspector de atributo")](outline-view-images/edit05.png)

- **Columna de esquema** : la columna de la tabla en la que se muestran los datos jerárquicos.
- **Columna de esquema de autoguardado** : si `true`, la columna de esquema se guardará automáticamente y se restauran entre ejecuciones de la aplicación.
- **Sangría** -la cantidad para aplicar sangría a las columnas en un elemento expandido.
- **Sangría sigue celdas** : si `true`, la marca de sangría se aplicará sangría junto con las celdas.
- **Autoguardar Expandir elementos** : si `true`, el estado contraído o expandido de los elementos se guardarán automáticamente y se restauran entre ejecuciones de la aplicación.
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
> A menos que se está realizando el mantenimiento de una aplicación heredada de Xamarin.Mac, `NSView` basado en vistas de esquema debe usarse por `NSCell` en función de las vistas de tabla. `NSCell` se considera heredada y no puede ser compatible en el futuro.

Seleccione una columna de tabla en la **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[ ![](outline-view-images/edit06.png "El Inspector de atributo")](outline-view-images/edit06.png)

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

[ ![](outline-view-images/edit07.png "El Inspector de atributo")](outline-view-images/edit07.png)

Estas son todas las propiedades de una vista estándar. También tiene la opción de cambiar el tamaño de las filas de esta columna aquí.

Seleccione una celda de la vista de tabla (de forma predeterminada, se trata de un `NSTextField`) en el **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[ ![](outline-view-images/edit08.png "El Inspector de atributo")](outline-view-images/edit08.png)

Tendrá todas las propiedades de un campo de texto estándar para establecer aquí. De forma predeterminada, un campo de texto estándar se utiliza para mostrar los datos de una celda de una columna.

Seleccione una vista de la celda de tabla (`NSTableFieldCell`) en el **jerarquía de interfaz** y las propiedades siguientes están disponibles en la **Inspector de atributo**:

[ ![](outline-view-images/edit09.png "El Inspector de atributo")](outline-view-images/edit09.png)

Estas opciones más importantes son:

- **Diseño** : seleccione cómo se distribuyen las celdas de esta columna.
- **Usa el modo de una sola línea** : si `true`, la celda se limita a una sola línea.
- **Ancho del diseño en tiempo de ejecución primera** : si `true`, la celda preferirán el ancho establecido para él (manual o automáticamente) cuando se muestra la primera vez que se ejecuta la aplicación.
- **Acción** -controla cuándo la edición **acción** se envía para la celda.
- **Comportamiento** -define si hay una celda editable o seleccionable.
- **Rich texto** : si `true`, la celda puede mostrar texto con formato y estilo.
- **Deshacer** : si `true`, la celda asume la responsabilidad de es deshacer el comportamiento.

Seleccione la vista de la celda de tabla (`NSTableFieldCell`) en la parte inferior de una columna de tabla en la **jerarquía de interfaz**:

[ ![](outline-view-images/edit11.png "Seleccionar la vista de la celda de tabla")](outline-view-images/edit10.png)

Esto le permite editar la vista de celda de tabla utilizada como base _patrón_ para todas las celdas que se crea para la columna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Adición de acciones y las salidas

Simplemente como cualquier otro control de interfaz de usuario de cacao, es necesario exponer la vista de esquema y se las columnas y las celdas al uso de código de C# **acciones** y **tomas** (según la funcionalidad necesaria).

El proceso es el mismo para cualquier elemento de vista de esquema que se va a exponer:

1. Cambie a la **Ayudante Editor** y asegúrese de que el `ViewController.h` se selecciona el archivo: 

    [ ![](outline-view-images/edit11.png "Seleccione el archivo .h correcto")](outline-view-images/edit11.png)
2. Seleccione la vista de esquema en el **jerarquía de interfaz**, control y haga clic y arrastre hasta el `ViewController.h` archivo.
3. Crear un **toma** para la vista de esquema denominado `ProductOutline`: 

    [ ![](outline-view-images/edit13.png "Configurar una salida")](outline-view-images/edit13.png)
4. Crear **tomas** para las columnas de tablas también denominada `ProductColumn` y `DetailsColumn`: 

    [ ![](outline-view-images/edit14.png "Configurar una salida")](outline-view-images/edit14.png)
5. Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

A continuación, se escribirá la visualización de código algunos datos para el esquema cuando se ejecuta la aplicación.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Rellenar la vista de esquema

Con la vista de esquema diseñado en el generador de interfaz y la expone a través de un **toma**, a continuación se necesita crear el código de C# para rellenarla.

En primer lugar, vamos a crear un nuevo `Product` clase para incluir la información en las filas individuales y grupos de productos de sub. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `Product` para el **nombre** y haga clic en el **New** botón:

[ ![](outline-view-images/populate01.png "Crear una clase vacía")](outline-view-images/populate01.png)

Realizar el `Product.cs` archivo aspecto similar al siguiente:

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

A continuación, necesitamos crear una subclase de `NSOutlineDataSource` para proporcionar los datos de nuestro esquema según se solicite. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `ProductOutlineDataSource` para el **nombre** y haga clic en el **New** botón.

Editar la `ProductTableDataSource.cs` de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }
                
        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }
        
        }
        #endregion
    }
}
```

Esta clase tiene el almacenamiento para los elementos de la vista de esquema e invalida el `GetChildrenCount` para devolver el número de filas en la tabla. El `GetChild` devuelve un elemento primario o secundario específico (como se solicitó la vista de esquema) y la `ItemExpandable` define el elemento especificado como un elemento primario o un elemento secundario.

Por último, necesitamos crear una subclase de `NSOutlineDelegate` para proporcionar el comportamiento para el esquema. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `ProductOutlineDelegate` para el **nombre** y haga clic en el **New** botón.

Editar la `ProductOutlineDelegate.cs` de archivos y darle un aspecto similar al siguiente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Cuando se crea una instancia de la `ProductOutlineDelegate`, pasamos también en una instancia de la `ProductOutlineDataSource` que proporciona los datos para el esquema. El `GetView` método es responsable de devolver una vista (datos) para mostrar la celda de una determinada columna y fila. Si es posible, se volverán a una vista existente para mostrar la celda, si no se debe crear una nueva vista.

Para rellenar el contorno, editar la `MainWindow.cs` de archivos y realice la `AwakeFromNib` método aspecto similar al siguiente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

Si se ejecuta la aplicación, se muestra lo siguiente:

[ ![](outline-view-images/populate02.png "La vista contraída")](outline-view-images/populate02.png)

Si se expande un nodo en la vista de esquema, tendrá un aspecto similar al siguiente:

[ ![](outline-view-images/populate03.png "La vista ampliada")](outline-view-images/populate03.png)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Ordenar por columna

Vamos a permitir al usuario ordenar los datos en el esquema haciendo clic en un encabezado de columna. En primer lugar, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione el `Product` columna, escriba `Title` para el **criterio de ordenación**, `compare:` para el **Selector** y seleccione `Ascending` para el **orden**:

[ ![](outline-view-images/sort01.png "Establecer el orden de clave de ordenación")](outline-view-images/sort01.png)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Ahora vamos a editar el `ProductOutlineDataSource.cs` de archivos y agregue los métodos siguientes:

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

El `Sort` permite el método ordenar los datos en el origen de datos basado en un determinado `Product` campo de clase en orden ascendente o descendente. Reemplazados `SortDescriptorsChanged` se llama al método cada vez que el uso que hace clic en un encabezado de columna. Se pasará el **clave** valor que se establece en el generador de interfaz y el criterio de ordenación para esa columna.

Si se ejecuta la aplicación y haga clic en los encabezados de columna, las filas se ordenará por esa columna:

[ ![](outline-view-images/sort02.png "Ejemplo de salida ordenada")](outline-view-images/sort02.png)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selección de filas

Si desea permitir al usuario seleccionar una sola fila, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de esquema en el **jerarquía de la interfaz** y desactive el **varios** casilla de verificación en la **Inspector de atributo**:

[ ![](outline-view-images/select01.png "El Inspector de atributo")](outline-view-images/select01.png)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.


A continuación, edite la `ProductOutlineDelegate.cs` de archivos y agregue el método siguiente:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Esto le permitirá al usuario seleccionar una sola fila en la vista de esquema. Devolver `false` para el `ShouldSelectItem` para cualquiera de los elementos que no desea que el usuario para que pueda seleccionar o `false` para cada elemento si no desea que el usuario para que pueda seleccionar todos los elementos.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selección múltiple de filas

Si desea permitir al usuario seleccionar un varias filas, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de esquema en el **jerarquía de la interfaz** y comprobar la **varios** casilla de verificación en la **Inspector de atributo**:

[ ![](outline-view-images/select02.png "El Inspector de atributo")](outline-view-images/select02.png)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.


A continuación, edite la `ProductOutlineDelegate.cs` de archivos y agregue el método siguiente:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Esto le permitirá al usuario seleccionar una sola fila en la vista de esquema. Devolver `false` para el `ShouldSelectRow` para cualquiera de los elementos que no desea que el usuario para que pueda seleccionar o `false` para cada elemento si no desea que el usuario para que pueda seleccionar todos los elementos.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo para seleccionar las filas

Si desea permitir que el usuario escribe un carácter con la vista de esquema seleccionado y seleccione la primera fila que tiene ese carácter, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de esquema en el **jerarquía de la interfaz** y compruebe el **Seleccione tipo** checkbox en el **Inspector de atributo**:

[ ![](outline-view-images/type01.png "Editar el tipo de fila")](outline-view-images/type01.png)

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Ahora vamos a editar el `ProductOutlineDelegate.cs` de archivos y agregue el método siguiente:

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

El `GetNextTypeSelectMatch` método toma el determinado `searchString` y devuelve el elemento de la primera `Product` que tenga esta cadena en su `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Reordenar columnas

Si desea permitir al usuario arrastrar reordenar las columnas en la vista de esquema, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de esquema en el **jerarquía de interfaz** y comprobar la **reaprovisionamiento** casilla de verificación en la **Inspector de atributo**:

[ ![](outline-view-images/reorder01.png "El Inspector de atributo")](outline-view-images/reorder01.png)

Si se proporciona un valor para el **Autoguardado** propiedad y compruebe el **información de la columna** campo, los cambios que realice en el diseño de la tabla se guardará automáticamente para que podamos y restauración la próxima vez que la aplicación se ejecuta.

Guarde los cambios y vuelva a Visual Studio para Mac para la sincronización con Xcode.

Ahora vamos a editar el `ProductOutlineDelegate.cs` de archivos y agregue el método siguiente:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

El `ShouldReorder` método debe devolver `true` para cualquier columna que va a permitir que arrastre reordenan en la `newColumnIndex`, else return `false`;

Si se ejecuta la aplicación, podemos arrastrar alrededor de los encabezados de columna para reordenar las columnas:

[ ![](outline-view-images/reorder02.png "Ejemplo de reordenación de columnas")](outline-view-images/reorder02.png)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Editar las celdas

Si desea permitir al usuario editar los valores de una celda determinada, edite la `ProductOutlineDelegate.cs` y cambie el `GetViewForItem` método tal como se indica a continuación:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

Ahora si se ejecuta la aplicación, el usuario puede editar las celdas de la vista de tabla:

[ ![](outline-view-images/editing01.png "Un ejemplo de la edición de las celdas")](outline-view-images/editing01.png)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Uso de imágenes en las vistas de esquema

Para incluir una imagen como parte de la celda en una `NSOutlineView`, deberá cambiar cómo se devuelven los datos de la vista de esquema `NSTableViewDelegate's` `GetView` método que se usa un `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
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
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Para obtener más información, consulte el [utilizando imágenes con vistas de esquema](~/mac/app-fundamentals/image.md) sección de nuestro [trabajar con imágenes](~/mac/app-fundamentals/image.md) documentación.

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Vistas de esquema de enlace de datos

Mediante el uso de técnicas de codificación de clave-valor y enlace de datos en la aplicación Xamarin.Mac, puede reducir considerablemente la cantidad de código que se debe escribir y mantener para rellenar y trabajar con elementos de interfaz de usuario. También tiene la ventaja de separar aún más los datos de copia de seguridad (_modelo de datos_) de la parte delantera terminar la interfaz de usuario (_Model-View-Controller_), provocando fáciles de mantener, aplicación más flexible diseño.

Codificación de clave y valor (KVC) es un mecanismo para tener acceso a propiedades de un objeto indirectamente, mediante claves (cadenas con formato especial) para identificar las propiedades en lugar de obtener acceso a ellos a través de las variables de instancia o métodos de descriptor de acceso (`get/set`). Mediante la implementación de clave y valor de codificación compatible con descriptores de acceso en la aplicación Xamarin.Mac, obtendrá acceso a las características de otras macOS como observar de clave y valor (KVO), enlace de datos, datos básicos, enlaces de cacao y scriptability.

Para obtener más información, consulte el [enlace de datos de la vista de esquema](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) sección de nuestro [enlace de datos y valores de clave de codificación](~/mac/app-fundamentals/databinding.md) documentación.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con vistas de esquema en una aplicación Xamarin.Mac. Se vio los distintos tipos y los usos de las vistas de esquema, cómo crear y mantener las vistas de esquema en el generador de interfaz de Xcode y cómo trabajar con vistas de esquema en el código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacOutlines (ejemplo)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages (ejemplo)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Listas de origen](~/mac/user-interface/source-list.md)
- [Enlace de datos y el valor de clave de codificación](~/mac/app-fundamentals/databinding.md)
- [Directrices de interfaz de sistema operativo X humanos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introducción a las vistas de esquema](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
