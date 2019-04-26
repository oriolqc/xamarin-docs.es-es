---
title: Arrastre y coloque en Xamarin.iOS
description: Este documento describe cómo implementar arrastrar y colocar en aplicaciones de Xamarin.iOS mediante las API que se introdujo en iOS 11. En concreto, se describe lo que permite arrastrar y colocar en UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/05/2017
ms.openlocfilehash: aa93e015a399e733a2bb52f087a1e482bc23a00a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169716"
---
# <a name="drag-and-drop-in-xamarinios"></a>Arrastre y coloque en Xamarin.iOS

_Implementación de arrastrar y colocar para iOS 11_

iOS 11 incluye arrastrar y soltar para copiar datos entre aplicaciones en el iPad. Los usuarios pueden seleccionar y arrastrar todos los tipos de contenido de aplicaciones colocadas side-by-side o arrastrando sobre un icono de aplicación, lo que desencadenará la aplicación para abrir y permitir que los datos que se va a quitar:

![Ejemplo de arrastrar y colocar desde una aplicación personalizada en las notas de la aplicación](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Arrastrar y colocar solo está disponible en la misma aplicación en iPhone.

Considere la posibilidad de compatibilidad con arrastrar y colocar contenido en cualquier parte se puede crear o editar:

- Los controles de texto admiten arrastrar y colocar para todas las aplicaciones compiladas con iOS 11, sin ningún trabajo adicional.
- Las vistas de tablas y vistas de colección incluyen mejoras en iOS 11 que simplifican la adición de arrastrar y soltar.
- Para la compatibilidad con arrastrar y colocar con personalizaciones adicionales se puede realizar cualquier otra vista.

Al agregar arrastrar y colocar compatibilidad a las aplicaciones, puede proporcionar diferentes niveles de fidelidad contenido; Por ejemplo, podría proporcionar un texto con formato y la versión de texto sin formato de los datos para que la aplicación receptora puede elegir que adapta mejor a en el destino del arrastre. También es posible personalizar la visualización de arrastrar y poder arrastrar varios elementos a la vez.

## <a name="drag-and-drop-with-text-controls"></a>Arrastrar y colocar con los controles de texto

`UITextView` y `UITextField` admiten automáticamente el texto seleccionado out de arrastrar y colocar texto contenido en.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Arrastrar y colocar con UITableView

`UITableView` tiene control integrado para arrastrar y colocar las interacciones con filas de tabla, que requieren sólo algunos métodos para habilitar el comportamiento predeterminado.

Hay dos interfaces:

- `IUITableViewDragDelegate` : Información de paquetes cuando se inicia un arrastre en la vista de tabla.
- `IUITableViewDropDelegate` – Procesa la información cuando una acción de colocar se ha intentado y completado.

En el [DragAndDropTableView ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/) estas dos interfaces se implementan en el `UITableViewController` (clase), junto con el origen de datos y el delegado. Se les asigna en el `ViewDidLoad` método:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

El código mínimo necesario para estas dos interfaces se explica a continuación.

### <a name="table-view-drag-delegate"></a>Delegado de arrastre de la vista de tabla

El único método _requiere_ admitir arrastrando una fila de una vista de tabla es `GetItemsForBeginningDragSession`. Si el usuario comienza a arrastrar una fila, se llamará a este método.

A continuación se muestra una implementación. Recupera los datos asociados con la fila arrastrada, lo codifica y configura un `NSItemProvider` que determina cómo va a controlar las aplicaciones de la parte "drop" de la operación (por ejemplo, si puede controlar el tipo de datos, `PlainText`, en el ejemplo):

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

Hay muchos métodos opcionales en el delegado de arrastre que se puede implementar para personalizar el comportamiento de arrastrar, como proporcionar varias representaciones de datos que pueden aprovecharse de las aplicaciones de destino (como texto con formato como así como texto sin formato o un vector y mapa de bits las versiones de un dibujo). También puede proporcionar las representaciones de datos personalizado que se usará al arrastrar y soltar dentro de la misma aplicación.

### <a name="table-view-drop-delegate"></a>Delegado de colocar la vista de tabla

Los métodos en el delegado de colocar se llaman cuando una operación de arrastre se realiza a través de una vista de tabla o se complete por encima de él. Los métodos necesarios determinan si puede quitarse los datos y las acciones que se realizan si se ha completado la operación de colocar:

- `CanHandleDropSession` – Mientras un arrastre está en curso y, potencialmente, que se coloca en la aplicación, este método determina si los datos que se arrastran puede quitarse.
- `DropSessionDidUpdate` – Mientras la operación de arrastrar está en curso, se llama a este método para determinar qué acción está diseñada. Información de la vista de tabla que se está arrastrando sobre la sesión de arrastre y la ruta de acceso de índice posibles puede utilizarse para determinar el comportamiento y los comentarios visuales proporcionada al usuario.
- `PerformDrop` : Cuando el usuario completa la operación de colocar (levantar el dedo hacia), este método extrae los datos que se arrastran y modifica la vista de tabla para agregar los datos en una nueva fila (o filas).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` indica si la vista de tabla puede aceptar los datos que se está arrastrando. En este fragmento de código `CanLoadObjects` se utiliza para confirmar que esta vista de tabla puede aceptar datos de cadena.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

El `DropSessionDidUpdate` método se llama repetidamente mientras la operación de arrastrar está en curso, para proporcionar indicaciones visuales para el usuario.

En el código siguiente, `HasActiveDrag` se usa para determinar si se ha originado la operación en la vista de tabla actual. Si es así, solo las filas solo se pueden mover.
Si la operación de arrastrar es de otro origen, se indicará una operación de copia:

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

La operación de colocar puede ser uno de `Cancel`, `Move`, o `Copy`.

La intención de colocar se puede insertar una fila nueva o agregar/anexar datos a una fila existente.

#### <a name="performdrop"></a>PerformDrop

El `PerformDrop` se llama al método cuando el usuario completa la operación y modifica el origen de datos y vista de tabla para reflejar los datos colocados.

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

Para cargar asincrónicamente los objetos de datos de gran tamaño, se puede agregar código adicional.

### <a name="testing-drag-and-drop"></a>Pruebas de arrastrar y colocar

Debe usar un iPad para probar la [ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/).
Abra el ejemplo junto con otra aplicación (por ejemplo, las notas) y arrastre las filas y el texto entre ellos:

![captura de pantalla de la operación de arrastre en curso](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>Vínculos relacionados

- [Directrices de interfaz humana de arrastrar y colocar (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Arrastre y coloque el ejemplo de la vista de tabla](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [Arrastre y coloque el ejemplo de la vista de colección](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [Introducción a la operación de arrastrar y colocar (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Arrastrar y colocar con la colección y la tabla de vista (sesión WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/223/)
