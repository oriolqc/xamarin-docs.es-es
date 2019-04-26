---
title: Alto de fila de tamaño automático en Xamarin.iOS
description: Este documento describe cómo agregar a aplicaciones Xamarin.iOS filas de la vista de tabla cuyo alto varía según el contenido. Describe el diseño de la celda en el Diseñador de iOS y alto de lo que permite el cambio de tamaño automático.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e4446abc73817eb0672cd10a69ff6f738de0c1e1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029119"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Alto de fila de tamaño automático en Xamarin.iOS

A partir de iOS 8, Apple ha agregado la capacidad de crear una vista de tabla (`UITableView`) que puede aumentar y reducir el alto de una fila determinada en función del tamaño de su contenido con diseño automático, las clases de tamaño y las restricciones automáticamente.

iOS 11 agregó la posibilidad de que las filas se expanda automáticamente. Celdas, encabezados y pies de página pueden ahora ajustarse automáticamente basándose en su contenido. Sin embargo, si se crea la tabla en el Diseñador de iOS, el generador de interfaz, o si corrigió el alto de las filas debe habilitar manualmente self ajustar el tamaño de las celdas, como se describe en esta guía.

## <a name="cell-layout-in-the-ios-designer"></a>Diseño de la celda en el Diseñador de iOS

Abra el guión gráfico de la vista de tabla que desea tener auto-resize la fila para en iOS Designer, seleccione la celda *prototipo* y definir el diseño de la celda. Por ejemplo:

[![](autosizing-row-height-images/table01.png "Diseño de prototipo de la celda")](autosizing-row-height-images/table01.png#lightbox)

Para cada elemento en el prototipo, agregue restricciones para mantener los elementos en la posición correcta de cambiar el tamaño de la vista de tabla para la rotación o iOS diferentes tamaños de pantalla del dispositivo. Por ejemplo, fijar el `Title` a la parte superior, izquierda y derecha de la celda *vista contenido*:

[![](autosizing-row-height-images/table02.png "Anclar el título a la parte superior, izquierda y derecha de la vista de contenido de celdas")](autosizing-row-height-images/table02.png#lightbox)

En el caso de nuestra tabla de ejemplo, la pequeña `Label` (bajo la `Title`) es el campo que se puede reducir y crecer a aumentar o disminuir el alto de fila. Para lograr este efecto, agregue las siguientes restricciones para anclar la izquierda, derecha, superior e inferior de la etiqueta:

[![](autosizing-row-height-images/table03.png "Estas restricciones para anclar la izquierda, derecha, superior e inferior de la etiqueta")](autosizing-row-height-images/table03.png#lightbox)

Ahora que nos hemos restringido completamente los elementos de la celda, es necesario aclarar qué elemento se debe ajustar. Para ello, establezca el **prioridad de una disposición a nivel de contenido** y **prioridad de resistencia de compresión de contenido** según sea necesario en el **diseño** sección del panel de propiedades:

[![](autosizing-row-height-images/table03a.png "La sección de diseño del panel de propiedades")](autosizing-row-height-images/table03a.png#lightbox)

Establezca el elemento que desea expandir para que tenga un **inferior** el valor de prioridad de una disposición a nivel y un **inferior** valor de prioridad de la resistencia de compresión.

A continuación, necesitamos seleccionar el prototipo de la celda y asígnele un único **identificador**:

[![](autosizing-row-height-images/table04.png "Lo que proporciona el prototipo de la celda de un identificador único")](autosizing-row-height-images/table04.png#lightbox)

En el caso de nuestro ejemplo, `GrowCell`. Vamos a usar este valor más adelante cuando se rellene la tabla.

> [!IMPORTANT]
> Si la tabla contiene más de un tipo de celda (**prototipo**), debe asegurarse de que cada tipo tiene su propia `Identifier` para cambiar el tamaño de fila de Auto para que funcione.

Para cada elemento de prototipo de la celda, asignar un **nombre** exponerlo a C# código. Por ejemplo:

[![](autosizing-row-height-images/table05.png "Asigne un nombre para exponerlo a C# código")](autosizing-row-height-images/table05.png#lightbox)

A continuación, agregue una clase personalizada para el `UITableViewController`, `UITableView` y `UITableCell` (Prototype). Por ejemplo: 

[![](autosizing-row-height-images/table06.png "Adición de una clase personalizada para el UITableViewController, el UITableView y el UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Por último, para asegurarse de que todos los esperados contenido se muestra en la etiqueta, establezca el **líneas** propiedad `0`:

[![](autosizing-row-height-images/table06.png "La propiedad de líneas que se establece en 0")](autosizing-row-height-images/table06a.png#lightbox)

Con la interfaz de usuario definida, vamos a agregar el código para permitir el cambio de tamaño de alto de fila automáticamente.

## <a name="enabling-auto-resizing-height"></a>Habilitar el cambio de tamaño automático alto

En cualquiera origen de datos del nuestra tabla vista (`UITableViewDatasource`) o un origen (`UITableViewSource`), cuando se quitan de la cola una celda que debemos usar el `Identifier` que hemos definido en el diseñador. Por ejemplo:

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

De forma predeterminada, la vista de tabla se establecerá para el cambio de tamaño automáticamente el alto de fila. Para asegurarse de esto, el `RowHeight` propiedad debe establecerse en `UITableView.AutomaticDimension`. También es necesario establecer la `EstimatedRowHeight` propiedad en nuestra `UITableViewController`. Por ejemplo:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

Esta estimación no tiene que ser exactos, solo una estimación aproximada del promedio alto de cada fila de la vista de tabla.

Con este código en su lugar, cuando se ejecuta la aplicación, cada fila se reducir y crecer en función del alto de la última etiqueta en el prototipo de la celda. Por ejemplo:

[![](autosizing-row-height-images/table07.png "Una tabla de ejemplo que se ejecute")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>Vínculos relacionados

- [GrowRowTable (sample)](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
