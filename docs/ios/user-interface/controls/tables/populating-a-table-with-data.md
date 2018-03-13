---
title: Rellenar una tabla con datos
ms.topic: article
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: fe62b917946dda4cf669f5b15c91a5e3b596a0fc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="populating-a-table-with-data"></a>Rellenar una tabla con datos

Para agregar filas a una `UITableView` debe implementar un `UITableViewSource` subclase e invalide los métodos que permite ver la tabla llama se rellene automáticamente.

Esta guía se incluyen:

- Creación de subclases de un UITableViewSource
- Reutilización de celda
- Agregar un índice
- Agregar encabezados y pies de página


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>Creación de subclases UITableViewSource

A `UITableViewSource` subclase se asigna a cada `UITableView`. La vista de tabla, consulta la clase de origen para determinar cómo representarse a sí misma (por ejemplo, el número de filas es necesario y el alto de cada fila si es diferente del valor predeterminado). Lo más importante, el origen proporciona cada vista de celda que se rellena con datos.

Hay solo dos métodos obligatorios necesarios para que se muestren los datos de una tabla:

-   **RowsInSection** : devuelto una [ `nint` ](http://developer.xamarin.com/guides/cross-platform/macios/nativetypes/) recuento del número total de filas de datos que se debe mostrar en la tabla.
-   **GetCell** : devuelto una `UITableCellView` llena con datos para el índice de fila correspondiente pasado al método.


El archivo de ejemplo BasicTable **TableSource.cs** incluye la implementación más sencilla posible de `UITableViewSource`. Puede ver en el siguiente fragmento de código que acepta una matriz de cadenas que se muestran en la tabla y devuelve un estilo de celda predeterminado que contiene todas las cadenas:

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //---- if there are no cells to reuse, create a new one
            if (cell == null)
            { cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

Un `UITableViewSource` puede usar cualquier estructura de datos de una matriz de cadenas simple (como se muestra en este ejemplo) para un <> de lista o en otra colección. La implementación de `UITableViewSource` métodos aísla la tabla de la estructura de datos subyacente.

Para usar esta subclase, cree una matriz de cadenas para construir el origen, a continuación, asignarlo a una instancia de `UITableView`:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

La tabla resultante tiene el siguiente aspecto:

 [![](populating-a-table-with-data-images/image3.png "Tabla de ejemplo que se ejecuta")](populating-a-table-with-data-images/image3.png#lightbox)

Mayoría de las tablas permite al usuario tener acceso a una fila para seleccionarlo y realizar alguna otra acción (como reproducir una canción, o llamar a un contacto o mostrar otra pantalla). Para lograr esto, hay algunas cosas que debemos hacer. En primer lugar, vamos a crear un AlertController para mostrar un mensaje cuando el usuario haga clic en una fila mediante la adición de las siguientes acciones para el `RowSelected` método:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

A continuación, cree una instancia de nuestro controlador de vista:

```csharp
HomeScreen owner;
```
Agregue un constructor a la clase UITableViewSource que toma un controlador de vista como un parámetro y lo guarda en un campo:

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```
Modifique el método ViewDidLoad donde se crea la clase UITableViewSource para pasar el `this` referencia:

```csharp
table.Source = new TableSource(tableItems, this);
```
Por último, de nuevo en su `RowSelected` /método siguiente, llamada `PresentViewController` en el campo en caché:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


Ahora el usuario puede tocar una fila y se mostrará una alerta:



 [![](populating-a-table-with-data-images/image4.png "La alerta seleccionada de la fila")](populating-a-table-with-data-images/image4.png#lightbox)


## <a name="cell-reuse"></a>Reutilización de celda

En este ejemplo hay sólo seis elementos, así que no hay ningún reutilización de celda necesario. Al mostrar cientos o miles de filas, sin embargo, sería una pérdida de memoria para crear cientos o miles de `UITableViewCell` objetos cuando solo unas pocas encaje en la pantalla a la vez.

Para evitar esta situación, cuando una celda desaparece de la pantalla de que la vista se coloca en una cola para su reutilización. Cuando el usuario se desplaza, llama a la tabla `GetCell` para solicitar nuevas vistas para mostrar: reutilizar una celda existente (que no se está mostrando) basta con llamar a la `DequeueReusableCell` método. Si una celda está disponible para su reutilización se devolverán, en caso contrario, se devuelve un valor null y el código debe crear una nueva instancia de la celda.

Este fragmento de código del ejemplo muestra el patrón:

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

La `cellIdentifier` eficazmente crea colas independientes para distintos tipos de celda. En este ejemplo todas las celdas buscar el mismo codificado por lo que solo un identificador se usa. Si hay diferentes tipos de celda debe cada uno de ellos tienen una cadena de identificador diferente, cuando se crea una instancia y cuando se solicitan desde la cola de reutilización.

### <a name="cell-reuse-in-ios-6"></a>Reutilización de celda en iOS 6 +

iOS 6 agrega un patrón de reutilización de celda similar a la introducción de uno con las vistas de colección. Aunque todavía se admite el patrón existente de reutilización mostrado anteriormente para con versiones anteriores es preferible la compatibilidad, este nuevo patrón tal y como elimina la necesidad de la comprobación de valores null en la celda.

Con el nuevo modelo de una aplicación registra la clase de celda o xib que va a usar una llamada a `RegisterClassForCellReuse` o `RegisterNibForCellReuse` en el constructor del controlador. A continuación, cuando la celda de vaciado en el `GetCell` método, simplemente call `DequeueReusableCell` pasar el identificador que se registró para la clase de celda o xib y la ruta de acceso del índice.

Por ejemplo, el código siguiente registra una clase de celda personalizada en un UITableViewController:

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

Con la clase MyCell registrada, la celda puede ser quitado de la cola en el `GetCell` método de la `UITableViewSource` sin necesidad de la comprobación de null adicional, como se muestra a continuación:

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

Tenga en cuenta, al utilizar el patrón de reutilización de nuevo con una clase de celda personalizada, debe implementar el constructor que toma un `IntPtr`, tal y como se muestra en el fragmento de código siguiente, en caso contrario Objective-C no podrá construir una instancia de la clase de celda:

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

Puede ver ejemplos de los temas que se ha explicado anteriormente en el **BasicTable** ejemplo vinculados a este artículo.

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>Agregar un índice

Un índice ayuda al usuario a desplazarse a través de listas largas, normalmente ordenadas alfabéticamente aunque se puede indizar por cualquier criterio desea. El **BasicTableIndex** ejemplo carga una lista mucho más completa de elementos de un archivo para mostrar el índice. Cada elemento en el índice corresponde a una 'sección' de la tabla.

 [![](populating-a-table-with-data-images/image5.png "La presentación de índice")](populating-a-table-with-data-images/image5.png#lightbox)

Para admitir 'sections' necesita los datos detrás de la tabla que se desea agrupar, por lo que el ejemplo BasicTableIndex se crea un `Dictionary<>` de la matriz de cadenas con la primera letra de cada elemento como la clave de diccionario:

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

El `UITableViewSource` subclase, a continuación, necesita los métodos siguientes agregan o modifican para utilizar el `Dictionary<>` :

-   **NumberOfSections** : este método es opcional, de forma predeterminada en la tabla, se da por supuesto una sección. Cuando se muestra un índice de este método debe devolver el número de elementos en el índice (por ejemplo, 26 si el índice contiene todas las letras del alfabeto inglés).
-   **RowsInSection** : devuelve el número de filas en una sección determinada.
-   **SectionIndexTitles** : devuelve la matriz de cadenas que se usará para mostrar el índice. El código de ejemplo devuelve una matriz de letras.


Los métodos actualizados en el archivo de ejemplo **BasicTableIndex/TableSource.cs** este aspecto:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

Índices generalmente solo se usa con el estilo de tabla sin formato.


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>Agregar encabezados y pies de página

Encabezados y pies de página pueden utilizarse para agrupar visualmente filas de una tabla. La estructura de datos necesaria es muy similar a la adición de un índice: `Dictionary<>` funciona muy bien. En lugar de usar el alfabeto para agrupar las celdas, en este ejemplo se agrupan las verduras por tipo botánico.
La salida es similar a la siguiente:

 [![](populating-a-table-with-data-images/image6.png "Muestra los encabezados y pies de página")](populating-a-table-with-data-images/image6.png#lightbox)

Para mostrar encabezados y pies de página el `UITableViewSource` subclase requiere estos métodos adicionales:

-   **TitleForHeader** : devuelve el texto que se usará como el encabezado
-   **TitleForFooter** : devuelve el texto que se usará como el pie de página.


Los métodos actualizados en el archivo de ejemplo **BasicTableHeaderFooter/Code/TableSource.cs** este aspecto:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

Puede personalizar aún más la apariencia del encabezado y pie de página con una vista del objeto utilizando el `GetViewForHeader` y `GetViewForFooter` reemplazos de método en `UITableViewSource`.


## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
