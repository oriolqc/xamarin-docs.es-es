---
title: Vistas de esquema en Xamarin.Mac
description: En este artículo se explica cómo trabajar con vistas de esquema en una aplicación de Xamarin.Mac. Describe cómo crear y mantener vistas de esquema en Xcode e Interface Builder y trabajar con ellos mediante programación.
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 7228a22eeae3dfdb354ba3693c277251fd2cd821
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2018
ms.locfileid: "40250977"
---
# <a name="outline-views-in-xamarinmac"></a>Vistas de esquema en Xamarin.Mac

_En este artículo se explica cómo trabajar con vistas de esquema en una aplicación de Xamarin.Mac. Describe cómo crear y mantener vistas de esquema en Xcode e Interface Builder y trabajar con ellos mediante programación._

Cuando se trabaja con C# y .NET en una aplicación de Xamarin.Mac, se tiene acceso al mismo esquema que ve un desarrollador que trabaje en *Objective-C* y *Xcode* does. Ya que Xamarin.Mac se integra directamente con Xcode, puede usar Xcode _Interface Builder_ para crear y mantener las vistas de esquema (o bien, opcionalmente, crearlos directamente en código de C#).

Una vista de esquema es un tipo de tabla que permite al usuario expandir o contraer las filas de datos jerárquicos. Al igual que una vista de tabla, una vista de esquema muestra datos de un conjunto de elementos relacionados, con filas que representan los elementos individuales y las columnas que representan los atributos de esos elementos. A diferencia de una vista de tabla, los elementos de una vista de esquema no están en una lista plana, están organizados en una jerarquía, como archivos y carpetas en un disco duro.

[![](outline-view-images/populate03.png "Ejecutar una aplicación de ejemplo")](outline-view-images/populate03.png#lightbox)

En este artículo, trataremos los aspectos básicos de trabajar con vistas de esquema en una aplicación de Xamarin.Mac. Se recomienda que trabaje en el [Hello, Mac](~/mac/get-started/hello-mac.md) artículo en primer lugar, específicamente el [Introducción a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) secciones, tal y como se tratan los conceptos clave y las técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la [clases de C# exponer / métodos a Objective-C](~/mac/internals/how-it-works.md) sección de la [funcionamiento interno de Xamarin.Mac](~/mac/internals/how-it-works.md) documentar, también explica la `Register` y `Export` comandos se usa para conexión de seguridad de las clases de C# para objetos de Objective-C y elementos de interfaz de usuario.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Introducción a las vistas de esquema

Una vista de esquema es un tipo de tabla que permite al usuario expandir o contraer las filas de datos jerárquicos. Al igual que una vista de tabla, una vista de esquema muestra datos de un conjunto de elementos relacionados, con filas que representan los elementos individuales y las columnas que representan los atributos de esos elementos. A diferencia de una vista de tabla, los elementos de una vista de esquema no están en una lista plana, están organizados en una jerarquía, como archivos y carpetas en un disco duro.

Si un elemento en una vista de esquema contiene otros elementos, se puede expandir o contraer por el usuario. Un elemento expandible muestra un triángulo que apunta a la derecha cuando el elemento está contraído y apunta hacia abajo cuando se expande el elemento. Al hacer clic en el triángulo hace que el elemento expandir o contraer.

La vista de esquema (`NSOutlineView`) es una subclase de la vista de tabla (`NSTableView`) y por lo tanto, hereda la mayor parte de su comportamiento de su clase primaria. Como resultado, muchas operaciones admitidas por una vista de tabla, como la selección de filas o columnas, cambiar la posición de las columnas arrastrando los encabezados de columna, etc., también son compatibles con una vista de esquema. Una aplicación de Xamarin.Mac tiene el control de estas características y puede configurar los parámetros de la vista de esquema (ya sea en el código o Interface Builder) para activar o desactivar determinadas operaciones.

Una vista de esquema no almacena sus propios datos, en su lugar se basa en un origen de datos (`NSOutlineViewDataSource`) para proporcionar tanto las filas y columnas requeridas, según sea necesario.

Se puede personalizar el comportamiento de la vista de esquema proporcionando una subclase del delegado de la vista de esquema (`NSOutlineViewDelegate`) para admitir la administración de la columna de esquema, escriba para seleccionar la funcionalidad, selección de fila y de edición, seguimiento personalizado y vistas personalizadas individuales las columnas y filas.

Dado que una vista de esquema comparte gran parte de su comportamiento y funcionalidad con una vista de tabla, es posible que desea ir a través de nuestro [vistas de tabla](~/mac/user-interface/table-view.md) documentación antes de continuar con este artículo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Crear y mantener vistas de esquema en Xcode

Cuando se crea una nueva aplicación de Xamarin.Mac Cocoa, obtendrá una ventana en blanco, estándar de forma predeterminada. Este windows se define en un `.storyboard` archivo incluido automáticamente en el proyecto. Para editar el diseño de windows, en el **el Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo:

[![](outline-view-images/edit01.png "Seleccione el guion gráfico principal")](outline-view-images/edit01.png#lightbox)

Se abrirá el diseño de ventana en Interface Builder de Xcode:

[![](outline-view-images/edit02.png "Edición de la interfaz de usuario en Xcode")](outline-view-images/edit02.png#lightbox)

Tipo `outline` en el **Inspector de biblioteca** cuadro de búsqueda para que sea más fácil encontrar los controles de vista de esquema:

[![](outline-view-images/edit03.png "Seleccionar una vista de esquema de la biblioteca")](outline-view-images/edit03.png#lightbox)

Arrastre una vista de esquema en el controlador de vista el **Editor de la interfaz**, asegúrese de rellenar el área de contenido del controlador de vista y establézcalo en donde disminuye y crece con la ventana en la **Editor de restricciones**:

[![](outline-view-images/edit04.png "Las restricciones de edición")](outline-view-images/edit04.png#lightbox)

Seleccione la vista de esquema en el **jerarquía de la interfaz** y las propiedades siguientes están disponibles en el **Inspector de atributos**:

[![](outline-view-images/edit05.png "El Inspector de atributos")](outline-view-images/edit05.png#lightbox)

- **Columna de esquema** -la columna de tabla en la que se muestran los datos jerárquicos.
- **Columna de esquema de autoguardado** : si `true`, se guardará automáticamente la columna de esquema y restaurarse entre ejecuciones de la aplicación.
- **Sangría** -que se va a aplicar sangría a las columnas en un elemento expandido.
- **Las celdas de sangría sigue** : si `true`, se aplicará sangría a la marca de sangría junto con las celdas.
- **Autoguardar Expandir elementos** : si `true`, se guardará automáticamente el estado contraído/expandido de los elementos y restaurarse entre ejecuciones de la aplicación.
- **Modo de contenido** -le permite usar cualquiera de las vistas (`NSView`) o las celdas (`NSCell`) para mostrar los datos en las filas y columnas. 10.7 macOS, se deben utilizar las vistas.
- **Flote hasta llegar al grupo de filas** : si `true`, la vista de tabla dibujará celdas agrupadas como si son flotantes.
- **Columnas** -define el número de columnas mostradas.
- **Encabezados** : si `true`, las columnas tendrán los encabezados.
- **Reordenación** : si `true`, el usuario podrá arrastrar reordenar las columnas de la tabla.
- **El cambio de tamaño** : si `true`, el usuario podrá arrastrar encabezados de columna para cambiar el tamaño de columnas.
- **Tamaño de columna** -controla cómo la tabla automáticamente las columnas de tamaño.
- **Resaltar** -controla el tipo de resaltado de la tabla que se utiliza cuando se selecciona una celda.
- **Alternar filas** : si `true`, nunca otra fila tendrá un color de fondo diferente.
- **Cuadrícula horizontal** -selecciona el tipo de borde que se dibujan entre las celdas horizontalmente.
- **Cuadrícula vertical** -selecciona el tipo de borde que se dibujan entre las celdas verticalmente.
- **Color de la cuadrícula** -establece el color de borde de celda.
- **En segundo plano** -establece el color de fondo de celda.
- **Selección** -le permiten controlar cómo el usuario puede seleccionar las celdas de la tabla como:
    - **Varios** : si `true`, el usuario puede seleccionar varias filas y columnas.
    - **Columna** : si `true`, el usuario puede seleccionar las columnas.
    - **Escriba Select** : si `true`, el usuario puede escribir un carácter para seleccionar una fila.
    - **Vacía** : si `true`, el usuario no es necesario seleccionar una fila o columna, la tabla permite ninguna selección en absoluto.
- **Autoguardar** -guardar el nombre que el formato de las tablas automáticamente es bajo.
- **Información de la columna** : si `true`, el orden y el ancho de las columnas se guardarán automáticamente.
- **Los saltos de línea** : seleccione cómo la celda controla los saltos de línea.
- **Trunca la última línea Visible** : si `true`, la celda se truncará en el puede datos no caben dentro de sus límites.

> [!IMPORTANT]
> A menos que se está realizando el mantenimiento de una aplicación de Xamarin.Mac heredada, `NSView` en función de las vistas de esquema debe usarse a través de `NSCell` en función de las vistas de tabla. `NSCell` se considera heredado y no admitirse en el futuro.

Seleccione una columna de tabla en la **jerarquía de la interfaz** y las propiedades siguientes están disponibles en el **Inspector de atributos**:

[![](outline-view-images/edit06.png "El Inspector de atributos")](outline-view-images/edit06.png#lightbox)

- **Título** -establece el título de la columna.
- **Alineación** -establecer la alineación del texto dentro de las celdas.
- **Fuente del título** -selecciona la fuente para el texto de encabezado de la celda.
- **Clave de ordenación** -es la clave utilizada para ordenar los datos en la columna. Deje en blanco si el usuario no puede ordenar esta columna.
- **Selector de** -es el **acción** utilizado para realizar la ordenación. Deje en blanco si el usuario no puede ordenar esta columna.
- **Orden** -es el criterio de ordenación para los datos de columnas.
- **El cambio de tamaño** -selecciona el tipo de cambio de tamaño de la columna.
- **Puede editar** : si `true`, el usuario puede editar las celdas de una tabla en función de celda.
- **Oculto** : si `true`, la columna está oculta.

También puede cambiar el tamaño de la columna arrastrando el identificador (centrado verticalmente en el lado derecho de la columna) de izquierda o derecha de TI.

Vamos a seleccionar la cada columna en la vista de tabla y asignar a la primera columna un **título** de `Product` y el segundo `Details`.

Seleccione una vista de la celda de tabla (`NSTableViewCell`) en el **jerarquía de la interfaz** y las propiedades siguientes están disponibles en el **Inspector de atributos**:

[![](outline-view-images/edit07.png "El Inspector de atributos")](outline-view-images/edit07.png#lightbox)

Estas son todas las propiedades de una vista estándar. También tiene la opción de cambiar el tamaño de las filas de esta columna aquí.

Seleccione una celda de vista de tabla (de forma predeterminada, se trata de un `NSTextField`) en el **jerarquía de la interfaz** y las propiedades siguientes están disponibles en el **Inspector de atributos**:

[![](outline-view-images/edit08.png "El Inspector de atributos")](outline-view-images/edit08.png#lightbox)

Tendrá todas las propiedades de un campo de texto estándar para establecer aquí. De forma predeterminada, un campo de texto estándar se utiliza para mostrar datos de una celda en una columna.

Seleccione una vista de la celda de tabla (`NSTableFieldCell`) en el **jerarquía de la interfaz** y las propiedades siguientes están disponibles en el **Inspector de atributos**:

[![](outline-view-images/edit09.png "El Inspector de atributos")](outline-view-images/edit09.png#lightbox)

Estas opciones más importantes son:

- **Diseño** : seleccione cómo se distribuyen las celdas de esta columna.
- **Usa el modo de línea única** : si `true`, la celda se limita a una sola línea.
- **Ancho del diseño en tiempo de ejecución primera** : si `true`, la celda preferirán el ancho establecido para él (manual o automáticamente) cuando se muestra la primera vez que se ejecuta la aplicación.
- **Acción** -controla cuándo la edición **acción** se envía para la celda.
- **Comportamiento** -define si una celda editable o seleccionable.
- **Rich texto** : si `true`, la celda puede mostrar texto con formato y estilo.
- **Deshacer** : si `true`, la celda asume la responsabilidad para es deshacer el comportamiento.

Seleccione la vista de la celda de tabla (`NSTableFieldCell`) en la parte inferior de una columna de tabla en la **jerarquía de la interfaz**:

[![](outline-view-images/edit11.png "Seleccionar la vista de la celda de tabla")](outline-view-images/edit10.png#lightbox)

Esto le permite editar la vista de la celda de tabla utilizado como base _patrón_ para todas las celdas que se creó para la columna especificada.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Adición de acciones y salidas

Simplemente como cualquier otro control de interfaz de usuario de Cocoa, es necesario exponer la vista de esquema y se las columnas y las celdas para código de C# mediante **acciones** y **salidas** (según la funcionalidad necesaria).

El proceso es el mismo para cualquier elemento de vista de esquema que se va a exponer:

1. Cambie a la **Editor del asistente** y asegúrese de que el `ViewController.h` archivo seleccionado: 

    [![](outline-view-images/edit11.png "Seleccionar el archivo .h correcto")](outline-view-images/edit11.png#lightbox)
2. Seleccione la vista de esquema desde el **jerarquía de la interfaz**, control y haga clic y arrastre hasta el `ViewController.h` archivo.
3. Crear un **toma** para la vista de esquema denominado `ProductOutline`: 

    [![](outline-view-images/edit13.png "Configurar una salida")](outline-view-images/edit13.png#lightbox)
4. Crear **salidas** para las columnas de tablas también denominada `ProductColumn` y `DetailsColumn`: 

    [![](outline-view-images/edit14.png "Configurar una salida")](outline-view-images/edit14.png#lightbox)
5. Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

A continuación, escribiremos la visualización del código algunos datos para el esquema cuando se ejecuta la aplicación.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Rellenar la vista de esquema

Con nuestra vista Esquema diseñados en Interface Builder y expone a través de un **toma**, a continuación, debe crear el código de C# para rellenarlo.

En primer lugar, vamos a crear un nuevo `Product` clase para contener la información de las filas individuales y grupos de productos de sub. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `Product` para el **nombre** y haga clic en el **New** botón:

[![](outline-view-images/populate01.png "Creación de una clase vacía")](outline-view-images/populate01.png#lightbox)

Realizar el `Product.cs` archivo aspecto parecido al siguiente:

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

A continuación, se debe crear una subclase de `NSOutlineDataSource` para proporcionar los datos de nuestro esquema conforme se solicitan. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `ProductOutlineDataSource` para el **nombre** y haga clic en el **New** botón.

Editar el `ProductTableDataSource.cs` de archivo y dele un aspecto similar al siguiente:

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

Esta clase tiene el almacenamiento para los elementos de la vista de nuestro esquema e invalida el `GetChildrenCount` para devolver el número de filas en la tabla. El `GetChild` devuelve un elemento primario o secundario específico (como se solicitó la vista de esquema) y el `ItemExpandable` define el elemento especificado como un elemento primario o secundario.

Por último, necesitamos crear una subclase de `NSOutlineDelegate` para proporcionar el comportamiento de nuestro esquema. En el **el Explorador de soluciones**, haga clic en el proyecto y seleccione **agregar** > **nuevo archivo...** Seleccione **General** > **clase vacía**, escriba `ProductOutlineDelegate` para el **nombre** y haga clic en el **New** botón.

Editar el `ProductOutlineDelegate.cs` de archivo y dele un aspecto similar al siguiente:

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

Cuando se crea una instancia de la `ProductOutlineDelegate`, también se pasa en una instancia de la `ProductOutlineDataSource` que proporciona los datos para el esquema. El `GetView` método es responsable de devolver una vista (datos) para mostrar la celda para una determinada columna y fila. Si es posible, se volverá a una vista existente para mostrar la celda, si no se debe crear una nueva vista.

Para rellenar el esquema, vamos a editar el `MainWindow.cs` y realice la `AwakeFromNib` método aspecto parecido al siguiente:

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

[![](outline-view-images/populate02.png "La vista contraída")](outline-view-images/populate02.png#lightbox)

Si se expande un nodo en la vista de esquema, lo tendrá un aspecto similar al siguiente:

[![](outline-view-images/populate03.png "La vista ampliada")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Ordenar por columna

Vamos a permitir al usuario ordenar los datos en el esquema haciendo clic en un encabezado de columna. En primer lugar, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione el `Product` columna, escriba `Title` para el **criterio de ordenación**, `compare:` para el **Selector** y seleccione `Ascending` para el **orden**:

[![](outline-view-images/sort01.png "Establecer la clave de ordenación")](outline-view-images/sort01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Ahora vamos a editar el `ProductOutlineDataSource.cs` archivo y agregue los métodos siguientes:

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

El `Sort` método nos permiten ordenar los datos del origen de datos según un determinado `Product` campo de clase en orden ascendente o descendente. Invalidado `SortDescriptorsChanged` método se llamará cada vez que el uso que hace clic en un encabezado de columna. Se pasará el **clave** valor que se estableció en Interface Builder y el criterio de ordenación para esa columna.

Si se ejecuta la aplicación y haga clic en los encabezados de columna, las filas se ordenará por esa columna:

[![](outline-view-images/sort02.png "Ejemplo de salida ordenada")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selección de filas

Si desea permitir al usuario seleccionar una sola fila, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de esquema en el **jerarquía de la interfaz** y desactive el **varios** casilla de verificación en la **Inspector de atributos**:

[![](outline-view-images/select01.png "El Inspector de atributos")](outline-view-images/select01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.


A continuación, edite el `ProductOutlineDelegate.cs` archivo y agregue el siguiente método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Esto permitirá al usuario seleccionar una sola fila en la vista de esquema. Devolver `false` para el `ShouldSelectItem` para cualquiera de los elementos que no desea que el usuario para poder seleccionar o `false` para todos los elementos si no desea que el usuario para poder seleccionar los elementos.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selección múltiple de filas

Si desea permitir al usuario seleccionar una de varias filas, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de esquema en el **jerarquía de la interfaz** y compruebe el **varios** casilla de verificación en la **Inspector de atributos**:

[![](outline-view-images/select02.png "El Inspector de atributos")](outline-view-images/select02.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.


A continuación, edite el `ProductOutlineDelegate.cs` archivo y agregue el siguiente método:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Esto permitirá al usuario seleccionar una sola fila en la vista de esquema. Devolver `false` para el `ShouldSelectRow` para cualquiera de los elementos que no desea que el usuario para poder seleccionar o `false` para todos los elementos si no desea que el usuario para poder seleccionar los elementos.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo para seleccionar la fila

Si desea permitir que el usuario escribe un carácter con la vista de esquema seleccionado y seleccione la primera fila que tiene ese carácter, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de esquema en el **jerarquía de la interfaz** y compruebe el **Seleccione tipo** casilla de verificación en la **Inspector de atributos**:

[![](outline-view-images/type01.png "Editar el tipo de fila")](outline-view-images/type01.png#lightbox)

Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Ahora vamos a editar el `ProductOutlineDelegate.cs` archivo y agregue el siguiente método:

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

Si desea permitir que el usuario arrastre reordenar las columnas en la vista de esquema, haga doble clic en el `Main.storyboard` archivo para abrirlo y editarlo en el generador de interfaz. Seleccione la vista de esquema en el **jerarquía de la interfaz** y compruebe el **reordenación** casilla de verificación en la **Inspector de atributos**:

[![](outline-view-images/reorder01.png "El Inspector de atributos")](outline-view-images/reorder01.png#lightbox)

Si se proporciona un valor para el **Autosave** propiedad y compruebe el **información de la columna** campo, los cambios se realicen en el diseño de la tabla se guardará automáticamente para nosotros y restauración la próxima vez que la aplicación se ejecuta.

Guarde los cambios y vuelva a Visual Studio para Mac sincronizar con Xcode.

Ahora vamos a editar el `ProductOutlineDelegate.cs` archivo y agregue el siguiente método:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

El `ShouldReorder` método debe devolver `true` para cualquier columna que va a permitir que los arrastre reordena en el `newColumnIndex`, else return `false`;

Si se ejecuta la aplicación, podemos arrastrar alrededor de los encabezados de columna para reordenar las columnas:

[![](outline-view-images/reorder02.png "Ejemplo de reordenación de columnas")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Edición de celdas

Si desea permitir que el usuario edite los valores de una celda determinada, edite el `ProductOutlineDelegate.cs` y cambie el `GetViewForItem` método como sigue:

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

[![](outline-view-images/editing01.png "Un ejemplo de la edición de celdas")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Uso de imágenes en las vistas de esquema

Para incluir una imagen como parte de la celda de un `NSOutlineView`, deberá cambiar cómo se devuelven los datos mediante la vista de esquema `NSTableViewDelegate's` `GetView` método que se usará un `NSTableCellView` en lugar de la típica `NSTextField`. Por ejemplo:

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

Para obtener más información, consulte el [utilizando imágenes con vistas de esquema](~/mac/app-fundamentals/image.md) sección de nuestra [trabajar con imágenes](~/mac/app-fundamentals/image.md) documentación.

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Vistas de esquema de enlace de datos

Mediante el uso de técnicas de codificación de clave-valor y enlace de datos en la aplicación de Xamarin.Mac, puede reducir significativamente la cantidad de código que debe escribir y mantener para rellenar y trabajar con elementos de interfaz de usuario. También tiene la ventaja de separar aún más los datos de respaldo (_modelo de datos_) de la parte delantera terminar la interfaz de usuario (_Model-View-Controller_), provocando fáciles de mantener, aplicaciones más flexibles diseño.

Codificación de clave-valor (KVC) es un mecanismo para tener acceso a propiedades de un objeto indirectamente, con claves (cadenas con formato especial) para identificar las propiedades en lugar de obtener acceso a ellos a través de las variables de instancia o métodos de descriptor de acceso (`get/set`). Mediante la implementación de pares clave-valor de codificación compatible con descriptores de acceso en la aplicación de Xamarin.Mac, obtendrá acceso a otras características de macOS como pares clave-valor observa (KVO), enlace de datos, Core Data, enlaces de cacao y scriptability.

Para obtener más información, consulte el [enlace de datos de la vista de esquema](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) sección de nuestra [enlace de datos y codificación de pares clave-valor](~/mac/app-fundamentals/databinding.md) documentación.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo ha tomado una visión detallada de trabajar con vistas de esquema en una aplicación de Xamarin.Mac. Se ha visto los distintos tipos y usos de las vistas de esquema, cómo crear y mantener las vistas de esquema en Interface Builder de Xcode y cómo trabajar con vistas de esquema en el código C#.

## <a name="related-links"></a>Vínculos relacionados

- [MacOutlines (ejemplo)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages (ejemplo)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vistas de tabla](~/mac/user-interface/table-view.md)
- [Listas de origen](~/mac/user-interface/source-list.md)
- [Enlace de datos y codificación de clave-valor](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a las vistas de esquema](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
