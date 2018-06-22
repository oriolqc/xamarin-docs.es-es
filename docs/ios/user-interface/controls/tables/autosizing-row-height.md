---
title: Alto de fila de tamaño automático en Xamarin.iOS
description: Este documento describe cómo agregar a las aplicaciones de Xamarin.iOS filas de la vista de tabla cuyo alto varía según el contenido. Se trata el diseño de la celda en el Diseñador de iOS y alto lo que permite cambiar automáticamente el tamaño.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 3c6beb112947f5423de200fd5c8957ef28dd48f9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789972"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Alto de fila de tamaño automático en Xamarin.iOS

A partir de iOS 8, Apple agrega la capacidad para crear una vista de tabla (`UITableView`) que puede crecer automáticamente y reducir el alto de una fila determinada en función del tamaño de su contenido con diseño automático, clases de tamaño y las restricciones.

iOS 11 agregó la capacidad de filas que se expanden automáticamente. Encabezados y pies de página, las celdas pueden ahora pueden ajustar de forma automática basándose en su contenido. Sin embargo, si la tabla se crea en el archivo iOS diseñador, el generador de interfaz, o si ha corregido alto de las filas debe habilitar manualmente en sí mismo ajustar el tamaño de las celdas, como se describe en esta guía.

## <a name="cell-layout-in-the-ios-designer"></a>Diseño de la celda en el Diseñador de iOS

Abra el guión gráfico de la vista de tabla que desea tener auto-resize la fila para en el diseñador, iOS seleccione la celda *prototipo* y definir el diseño de la celda. Por ejemplo:

[![](autosizing-row-height-images/table01.png "Diseño de prototipo de la celda")](autosizing-row-height-images/table01.png#lightbox)

Para cada elemento en el prototipo, agregar restricciones para mantener los elementos en la posición correcta cuando se cambia el tamaño de la vista de tabla para rotación o iOS diferentes tamaños de pantalla del dispositivo. Por ejemplo, fijar el `Title` a la parte superior, izquierda y derecha de la celda *vista de contenido*:

[![](autosizing-row-height-images/table02.png "Anclar el título a la parte superior, izquierda y derecha de la vista de contenido de las celdas")](autosizing-row-height-images/table02.png#lightbox)

En el caso de la tabla de ejemplo, la pequeña `Label` (bajo la `Title`) es el campo que se puede reducir y crecer para aumentar o disminuir el alto de fila. Para lograr este efecto, agregue las siguientes restricciones para anclar izquierda, derecha, superior e inferior de la etiqueta:

[![](autosizing-row-height-images/table03.png "Estas restricciones para anclar izquierda, derecha, superior e inferior de la etiqueta")](autosizing-row-height-images/table03.png#lightbox)

Ahora que nos hemos totalmente restringido los elementos de la celda, es necesario aclarar qué elemento debe ajustarse. Para ello, establezca la **prioridad de una disposición a nivel de contenido** y **prioridad de resistencia de compresión de contenido** según sea necesario en el **diseño** sección del panel de propiedades:

[![](autosizing-row-height-images/table03a.png "La sección de diseño del panel de propiedades")](autosizing-row-height-images/table03a.png#lightbox)

Establezca el elemento que desea expandir para que tenga un **inferior** valor de prioridad de una disposición a nivel y un **inferior** valor de prioridad de resistencia de compresión.

A continuación, necesitamos seleccionar el prototipo de la celda y asígnele un nombre único **identificador**:

[![](autosizing-row-height-images/table04.png "Lo que proporciona el prototipo de la celda de un identificador único")](autosizing-row-height-images/table04.png#lightbox)

En el caso de nuestro ejemplo, `GrowCell`. Vamos a usar este valor más adelante cuando se rellene la tabla.

> [!IMPORTANT]
> Si la tabla contiene más de un tipo de celda (**prototipo**), debe asegurarse de que cada tipo tiene su propia estructura `Identifier` para cambiar el tamaño de fila de automática para que funcione.

Para cada elemento de nuestro prototipo de celda, asignar un **nombre** para exponerlo a código de C#. Por ejemplo:

[![](autosizing-row-height-images/table05.png "Asigne un nombre para exponerlo a código de C#")](autosizing-row-height-images/table05.png#lightbox)

A continuación, agregue una clase personalizada para la `UITableViewController`, `UITableView` y `UITableCell` (Prototype). Por ejemplo: 

[![](autosizing-row-height-images/table06.png "Agregar una clase personalizada para el UITableViewController, la UITableView y la UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Por último, para asegurarse de que todos los esperados contenido se muestra en la etiqueta, establezca el **líneas** propiedad `0`:

[![](autosizing-row-height-images/table06.png "La propiedad de líneas que se establece en 0")](autosizing-row-height-images/table06a.png#lightbox)

Con la interfaz de usuario definida, vamos a agregar el código para permitir el cambio de tamaño de alto de fila automáticamente.

## <a name="enabling-auto-resizing-height"></a>Habilitar el cambio de tamaño automático alto

En la vista origen de datos de la vista de la tabla (`UITableViewDatasource`) o un origen (`UITableViewSource`), cuando se quitan de la cola una celda que debemos usar el `Identifier` que hemos definido en el diseñador. Por ejemplo:

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

Esta estimación no tiene que ser exactos, simplemente una estimación aproximada del medio alto de cada fila de la vista de tabla.

Con este código en su lugar, cuando se ejecuta la aplicación, cada fila se reducir y crecer en función del alto de la última etiqueta en el prototipo de la celda. Por ejemplo:

[![](autosizing-row-height-images/table07.png "Una tabla de ejemplo que se ejecute")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>Vínculos relacionados

- [GrowRowTable (ejemplo)](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
