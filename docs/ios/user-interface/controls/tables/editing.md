---
title: Edición de tablas con Xamarin.iOS
description: Este documento describe cómo modificar las tablas de Xamarin.iOS. Describe pasar el dedo para eliminar, modificar el modo y la inserción de fila.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 1267de341a88130c18254f414d2fbb1c42595a0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61195919"
---
# <a name="editing-tables-with-xamarinios"></a>Edición de tablas con Xamarin.iOS

Características de edición de la tabla están habilitadas invalidando los métodos en un `UITableViewSource` subclase. El comportamiento de edición más sencillo es el gesto de deslizar rápidamente para eliminar que puede implementarse con una invalidación de método único.
Edición más complejas (incluidas muevan las filas) se puede hacer con la tabla en modo de edición.

## <a name="swipe-to-delete"></a>Deslice el dedo para Delete

El dedo para eliminar la función es un gesto natural de iOS que los usuarios esperan. 

 [![](editing-images/image10.png "Ejemplo de pasar el dedo para Delete")](editing-images/image10.png#lightbox)

Hay tres reemplazos de método que afectan el gesto de deslizar rápidamente para mostrar un **eliminar** botón en una celda:

-   **CommitEditingStyle** : el origen de tabla detecta si este método se invalida y habilita automáticamente el gesto de deslizar rápidamente para eliminar. Debe llamar la implementación del método `DeleteRows` en el `UITableView` para hacer que las celdas a desaparecer y también quitar los datos subyacentes del modelo (por ejemplo, una matriz, diccionario o base de datos). 
-   **CanEditRow** – CommitEditingStyle si se reemplaza, se supone que todas las filas sean editables. Si este método se implementa y se devuelve false (algunas filas específicas o para todas las filas), a continuación, el gesto de deslizar rápidamente para eliminar no estará disponible en esa celda. 
-   **TitleForDeleteConfirmation** : opcionalmente, especifica el texto de la **eliminar** botón. Si este método no está implementado el texto del botón será "Eliminar". 


Estos métodos se implementan en el `TableSource` clase se indica a continuación:

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

En este ejemplo el `UITableViewSource` se ha actualizado para usar un `List<TableItem>` (en lugar de una matriz de cadenas) como el origen de datos porque admite la adición y eliminación de elementos de la colección.


## <a name="edit-mode"></a>Modo de edición

Cuando una tabla está en modo de edición el usuario ve un widget rojo 'stop' en cada fila, que revela un botón de eliminación al tocarla. La tabla también muestra un icono de 'identificador' para indicar que la fila se puede arrastrar para cambiar el orden.
El **TableEditMode** ejemplo implementa estas características, como se muestra.

 [![](editing-images/image11.png "El ejemplo de TableEditMode implementa estas características, como se muestra")](editing-images/image11.png#lightbox)

Hay una serie de métodos diferentes en `UITableViewSource` que afectan al comportamiento del modo de edición de una tabla:

-   **CanEditRow** : indica si se puede editar cada fila. Devolver false para evitar pasar el dedo para eliminar y eliminación en el modo de edición. 
-   **CanMoveRow** : el valor devuelto de true para habilitar el movimiento 'identificador' o en false para evitar mover. 
-   **EditingStyleForRow** : cuando la tabla está en modo de edición, el valor devuelto de este método determina si la celda muestra el icono de eliminación de color rojo o verde agregar icono. Devolver `UITableViewCellEditingStyle.None` si la fila no debe ser editable. 
-   **MoveRow** : se le llama cuando se mueve una fila hasta que se puede modificar la estructura de datos subyacente para que coincida con los datos tal y como se muestra en la tabla. 


La implementación para los tres primeros métodos es relativamente sencillo: a menos que desee usar el `indexPath` para cambiar el comportamiento de determinadas filas, simplemente codificar los valores devueltos para toda la tabla.

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

El `MoveRow` implementación es un poco más complicado porque se debe modificar la estructura de datos subyacente para que coincida con el nuevo orden. Dado que los datos se implementan como un `List` el código siguiente elimina el elemento de datos en su ubicación anterior y lo inserta en la nueva ubicación. Si los datos se almacenan en una tabla de base de datos de SQLite con una columna 'order' (por ejemplo), este método en su lugar, deberá realizar algunas operaciones de SQL para volver a ordenar los números de esa columna.

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

Por último, para obtener la tabla en modo de edición, el **editar** botón necesita llamar a `SetEditing` similar a éste

```csharp
table.SetEditing (true, true);
```

y cuando el usuario ha terminado de edición, el **realiza** botón debe desactivar el modo de edición:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>Estilo de edición de inserción de fila

Inserción de filas desde dentro de la tabla es una interfaz de usuario habitual: el ejemplo principal en las aplicaciones de iOS estándar es la **Edit Contact** pantalla. Esta captura de pantalla muestra cómo funciona la funcionalidad de inserción de fila: en edición modo hay más de fila que (al hacer clic en) inserta filas adicionales en los datos. Cuando la edición se completa, temporal **(Agregar nuevo)** se quita la fila.

 [![](editing-images/image12.png "Cuando se completa la edición, temporal agregar nueva fila se quita.")](editing-images/image12.png#lightbox)

Hay una serie de métodos diferentes en `UITableViewSource` que afectan al comportamiento del modo de edición de una tabla. Estos métodos se han implementado como se indica a continuación en el código de ejemplo:

-   **EditingStyleForRow** – devuelve `UITableViewCellEditingStyle.Delete` para las filas que contienen datos y devuelve `UITableViewCellEditingStyle.Insert` para la última fila (que se agregarán específicamente para comportarse como un botón de inserción). 
-   **CustomizeMoveTarget** : mientras que el usuario está moviendo una celda, el valor devuelto de este método opcional puede invalidar su elección de ubicación. Esto significa que puede impedirles 'Quitar' la celda en ciertas posiciones, como en este ejemplo que impide que cualquier fila que se va a mover después los **(Agregar nuevo)** fila. 
-   **CanMoveRow** : el valor devuelto de true para habilitar el movimiento 'identificador' o en false para evitar mover. En el ejemplo, la última fila tiene el "controlador de movimiento' ocultado porque está diseñado como un botón de inserción solo al servidor. 


También agregamos dos métodos personalizados para agregar la fila de 'insert' y eliminarla nuevamente cuando ya no es necesario. Se les llama desde el **editar** y **realiza** botones:

-   **WillBeginTableEditing** : cuando el **editar** botón está modificada se llama a `SetEditing` para colocar la tabla en modo de edición. Esto desencadena el método WillBeginTableEditing donde se muestre el **(Agregar nuevo)** fila al final de la tabla para que actúe como un botón de' insert'. 
-   **DidFinishTableEditing** : cuando se toca el botón Listo `SetEditing` se llama de nuevo para desactivar el modo de edición. El código de ejemplo se quita el **(Agregar nuevo)** fila de la tabla al editar ya no es necesaria. 


Estas invalidaciones de método se implementan en el archivo de ejemplo **TableEditModeAdd/Code/TableSource.cs**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

Estos dos métodos personalizados se usan para agregar y quitar el **(Agregar nuevo)** fila al modo de edición de la tabla está habilitado o deshabilitado:

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

Por último, este código crea instancias el **editar** y **realiza** botones con expresiones lambda que habilitan o deshabilitan el modo de edición cuando están modificadas:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

Este patrón de interfaz de usuario de inserción de fila no se usa muy a menudo, sin embargo, también puede usar el `UITableView.BeginUpdates` y `EndUpdates` métodos para animar la inserción o eliminación de las celdas de cualquier tabla. La regla para el uso de estos métodos es que la diferencia en el valor devuelto por `RowsInSection` entre el `BeginUpdates` y `EndUpdates` llamadas deben coincidir con el número de celdas agregado/eliminado con net el `InsertRows` y `DeleteRows` métodos. Si no se cambia el origen de datos subyacente para que coincida con las inserciones y eliminaciones en la vista de tabla, se producirá un error.


## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
