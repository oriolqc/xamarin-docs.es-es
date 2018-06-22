---
title: Editar tablas con Xamarin.iOS
description: Este documento describe cómo editar tablas de Xamarin.iOS. Se trata de deslice el dedo para eliminar, editar el modo y la inserción de fila.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 28ebf1157a1bfc9f7bd910fd11365b29cecb9529
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789995"
---
# <a name="editing-tables-with-xamarinios"></a>Editar tablas con Xamarin.iOS

Características de edición de la tabla están habilitadas al invalidar los métodos en un `UITableViewSource` subclase. El comportamiento de edición más sencillo es el gesto de deslizar rápidamente para eliminar que puedan implementarse con una invalidación del método único.
Edición más complejas (incluidas las filas móviles) puede realizarse con la tabla en modo de edición.

## <a name="swipe-to-delete"></a>Deslice el dedo para eliminar

El deslice el dedo para eliminar la función es un gesto natural en operaciones de E/s que los usuarios esperan. 

 [![](editing-images/image10.png "Ejemplo de deslice el dedo para Delete")](editing-images/image10.png#lightbox)

Hay tres valores de reemplazo de método que afectan el gesto de deslizar rápidamente para mostrar un **eliminar** botón en una celda:

-   **CommitEditingStyle** : la tabla de origen detecta si este método se invalida y habilita automáticamente el gesto de deslizar rápidamente para eliminar. Debe llamar la implementación del método `DeleteRows` en el `UITableView` para hacer que las celdas a desaparecer y quitar los datos subyacentes del modelo (por ejemplo, una matriz, diccionario o base de datos). 
-   **CanEditRow** – CommitEditingStyle si se reemplaza, se suponen que todas las filas se pueden modificar. Si este método se implementa y devuelve false (para algunas filas específicas o para todas las filas), a continuación, el gesto de deslizar rápidamente para eliminar no estará disponible en esa celda. 
-   **TitleForDeleteConfirmation** : opcionalmente, especifica el texto de la **eliminar** botón. Si este método no se implementa el texto del botón será "Delete". 


Estos métodos se implementan en la `TableSource` clase se indica a continuación:

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

En este ejemplo el `UITableViewSource` se ha actualizado para usar un `List<TableItem>` (en lugar de una matriz de cadenas) como el origen de datos ya que admite agregar y eliminar elementos de la colección.


## <a name="edit-mode"></a>Modo de edición

Cuando una tabla está en modo de edición el usuario ve un widget rojo 'stop' en cada fila, lo que revela un botón Eliminar cuando tocadas. La tabla también muestra un icono de 'identificador' para indicar que la fila se puede arrastrar para cambiar el orden.
El **TableEditMode** ejemplo implementa estas características, tal y como se muestra.

 [![](editing-images/image11.png "El ejemplo TableEditMode implementa estas características tal y como se muestra")](editing-images/image11.png#lightbox)

Hay una serie de métodos diferentes en `UITableViewSource` que afectan al comportamiento del modo de edición de una tabla:

-   **CanEditRow** : indica si se puede editar cada fila. Devolver false para evitar que se deslice el dedo para eliminar y eliminación mientras esté en modo de edición. 
-   **CanMoveRow** : devuelto true para habilitar el movimiento 'identificador' o en false para evitar mover. 
-   **EditingStyleForRow** : cuando la tabla está en modo de edición, el valor devuelto de este método determina si la celda muestra el icono de eliminación de color rojo o verde agregar icono. Devolver `UITableViewCellEditingStyle.None` si la fila no debería ser editable. 
-   **MoveRow** – debe llamar cuando se mueve una fila para que se pueda modificar la estructura de datos subyacente para que coincida con los datos tal y como se muestra en la tabla. 


La implementación para los tres primeros métodos es relativamente sencillo: a menos que se va a usar el `indexPath` para cambiar el comportamiento de determinadas filas, simplemente codificar los valores devueltos para toda la tabla.

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

El `MoveRow` implementación es un poco más complicada, porque es necesario modificar la estructura de datos subyacente para que coincida con el nuevo orden. Dado que los datos se implementan como un `List` el código siguiente elimina el elemento de datos en su ubicación anterior y lo inserta en la nueva ubicación. Si los datos se almacenan en una tabla de base de datos de SQLite con una columna 'pedido' (por ejemplo), este método en su lugar, tendría que realizar algunas operaciones de SQL para volver a ordenar los números de esa columna.

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

Inserción de filas desde dentro de la tabla es una interfaz de usuario poco comunes: el ejemplo principal en las aplicaciones de iOS estándar es el **Edit Contact** pantalla. Esta captura de pantalla muestra el funcionamiento de la funcionalidad de inserción de fila: en edición modo hay más de fila que (al hacer clic en) inserta filas adicionales en los datos. Cuando la edición se completa, la contraseña **(agregar nuevos)** se quita la fila.

 [![](editing-images/image12.png "Cuando se completa la edición, la contraseña Agregar nueva fila se quita")](editing-images/image12.png#lightbox)

Hay una serie de métodos diferentes en `UITableViewSource` que afectan al comportamiento del modo de edición de una tabla. Estos métodos se han implementado como se indica a continuación en el código de ejemplo:

-   **EditingStyleForRow** – devuelve `UITableViewCellEditingStyle.Delete` para las filas que contienen datos y devuelve `UITableViewCellEditingStyle.Insert` para la última fila (que se agregarán específicamente se comporte como un botón de inserción). 
-   **CustomizeMoveTarget** : mientras que el usuario está moviendo de una celda, el valor devuelto de este método opcional puede invalidar su elección de ubicación. Esto significa que puede impedir que ellos 'Quitar' la celda en ciertas posiciones, como en este ejemplo que impide que cualquier fila que se va a mover después la **(agregar nuevos)** fila. 
-   **CanMoveRow** : devuelto true para habilitar el movimiento 'identificador' o en false para evitar mover. En el ejemplo, la última fila tiene el movimiento 'identificador' ocultado porque está destinada al servidor como un botón de inserción únicamente. 


También agregamos dos métodos personalizados para agregar la fila de 'insert' y, a continuación, quitarlo de nuevo cuando ya no es necesario. Se les llama desde la **editar** y **realiza** botones:

-   **WillBeginTableEditing** : cuando el **editar** botón está tocadas llamadas `SetEditing` para colocar la tabla en modo de edición. Esto desencadena el método WillBeginTableEditing donde se muestra la **(agregar nuevos)** fila al final de la tabla para que actúe como un botón de' insert'. 
-   **DidFinishTableEditing** : cuando se toca el botón Listo `SetEditing` se llama de nuevo para desactivar el modo de edición. El código de ejemplo se quita el **(agregar nuevos)** la fila de la tabla al editar ya no es necesaria. 


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

Estos dos métodos personalizados se utilizan para agregar y quitar el **(agregar nuevos)** fila al modo de edición de la tabla está habilitada o deshabilitada:

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

Por último, este código crea instancias el **editar** y **realiza** botones, con las expresiones lambda que habilitar o deshabilitar el modo de edición cuando están modificadas:

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

Este patrón de interfaz de usuario de inserción de filas no se utiliza con frecuencia, pero también puede usar el `UITableView.BeginUpdates` y `EndUpdates` métodos para animar la inserción o eliminación de las celdas de una tabla. La regla para el uso de estos métodos es que la diferencia en el valor devuelto por `RowsInSection` entre el `BeginUpdates` y `EndUpdates` llamadas deben coincidir con el número de red de celdas agregado/eliminado con la `InsertRows` y `DeleteRows` métodos. Si el origen de datos subyacente no se cambia para que coincida con las inserciones y eliminaciones en la vista de tabla, que se producirá un error.


## <a name="related-links"></a>Vínculos relacionados

- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
