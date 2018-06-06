---
title: Arrastrar y colocar en Xamarin.iOS
description: Este documento describe cómo implementar arrastrar y colocar en las aplicaciones de Xamarin.iOS con las API que se introdujo en iOS 11. En concreto, se describe lo que permite arrastrar y colocar en UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/05/2016
ms.openlocfilehash: 7c41f96dae88047e64ec1e74838e3efab55958cc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786969"
---
# <a name="drag-and-drop-in-xamarinios"></a>Arrastrar y colocar en Xamarin.iOS

_Implementación de arrastrar y colocar para iOS 11_

iOS 11 incluye arrastrar y soltar para copiar datos entre las aplicaciones en el iPad. Los usuarios pueden seleccionar y arrastrar todos los tipos de contenido de aplicaciones situadas en paralelo, o bien arrastrando sobre un icono de aplicación que se activará la aplicación para abrir y para permitir que los datos que se va a quitar:

![Ejemplo de arrastrar y colocar desde una aplicación personalizada en notas de la aplicación](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Arrastrar y colocar sólo está disponible dentro de la misma aplicación en iPhone.

Considere la posibilidad de compatibilidad con arrastrar y colocar datos en cualquier parte se puede crear o editar contenido:

- Controles de texto admiten arrastrar y colocar para todas las aplicaciones compiladas con iOS 11, sin ningún trabajo adicional.
- Vistas de tablas y vistas de colección incluyen mejoras en iOS 11 que simplificar la adición de arrastrar y soltar.
- Cualquier otra vista puede realizarse para admitir arrastrar y colocar con personalización adicional.

Cuando se admiten la adición de arrastrar y colocar a las aplicaciones, puede proporcionar distintos niveles de fidelidad contenido; Por ejemplo, puede proporcionar un texto con formato y la versión de texto sin formato de los datos para que la aplicación receptora puede elegir que adapta mejor al destino del arrastre. También es posible personalizar la visualización de arrastre y también para permitir arrastrar varios elementos a la vez.

## <a name="drag-and-drop-with-text-controls"></a>Arrastrar y colocar con controles de texto

`UITextView` y `UITextField` admiten automáticamente el texto seleccionado out de arrastrar y colocar texto contenido en.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Arrastrar y colocar con UITableView

`UITableView` tiene control integrado para arrastrar y colocar las interacciones con filas de tabla, que requiere solo unos cuantos métodos para habilitar el comportamiento predeterminado.

Hay dos interfaces:

- `IUITableViewDragDelegate` : Información de paquetes cuando se inicia un arrastre en la vista de tabla.
- `IUITableViewDropDelegate` – Procesa la información cuando se quita un intentan y se completó.

En el [DragAndDropTableView ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/) estas dos interfaces se implementan en el `UITableViewController` (clase), junto con el origen de datos y el delegado. Le asigna en el `ViewDidLoad` método:

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

A continuación se explica el código mínimo necesario para estas dos interfaces.

### <a name="table-view-drag-delegate"></a>Delegado de arrastre de la vista de tabla

El único método _requiere_ para ofrecer compatibilidad con arrastrar una fila de una vista de tabla `GetItemsForBeginningDragSession`. Si el usuario comienza a arrastrar una fila, se llamará a este método.

A continuación se muestra una implementación. Recupera los datos asociados a la fila arrastrada, lo codifica y configura un `NSItemProvider` que determina cómo controlará las aplicaciones a la parte "drop" de la operación (por ejemplo, si puede controlar el tipo de datos, `PlainText`, en el ejemplo):

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

Existen muchos métodos opcionales en el delegado de arrastrar que se puede implementar para personalizar el comportamiento de arrastrar, como proporcionar varias representaciones de datos que pueden aprovecharse en aplicaciones de destino (como texto con formato como así como texto sin formato o un vector y mapa de bits en versiones de un dibujo). También puede proporcionar representaciones de datos personalizados que se usarán al arrastrar y colocar dentro de la misma aplicación.

### <a name="table-view-drop-delegate"></a>Delegado de colocación de la vista de tabla

Se llama a los métodos en el delegado de eliminación cuando una operación de arrastre se produce a través de una vista de tabla, o se completa por encima de él. Los métodos necesarios determinan si los datos se pueden quitar, y las acciones que se realizan si se ha completado la operación de colocar:

- `CanHandleDropSession` – Mientras un arrastre está en curso y, potencialmente, que se colocan en la aplicación, este método determina si se permiten quitar los datos que se están arrastrando.
- `DropSessionDidUpdate` – Mientras la operación de arrastre está en curso, se llama a este método para determinar qué acción está destinada. Información de la vista de tabla que se está arrastrando sobre la sesión de arrastre y la ruta de acceso de índice posibles puede utilizarse para determinar el comportamiento y la información visual al usuario.
- `PerformDrop` : Cuando el usuario completa la operación de colocar (levantar el dedo), este método extrae los datos que se están arrastrando y modifica la vista de tabla para agregar los datos en una nueva fila (o filas).

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` indica si la vista de tabla puede aceptar los datos que se están arrastrando. En este fragmento de código, `CanLoadObjects` se utiliza para confirmar que esta vista de tabla puede aceptar los datos de cadena.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

El `DropSessionDidUpdate` se llama repetidamente al método mientras la operación de arrastre está en curso, para proporcionar indicaciones visuales para el usuario.

En el código siguiente, `HasActiveDrag` se usa para determinar si la operación se haya originado en la vista de tabla actual. Si es así, solo las filas individuales pueden moverse.
Si la operación de arrastre es de otro origen, se indicará una operación de copia:

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

La intención de colocar puede insertar una fila nueva o agregar/anexar datos a una fila existente.

#### <a name="performdrop"></a>PerformDrop

El `PerformDrop` método se llama cuando el usuario completa la operación y modifica el origen de datos y la vista de tabla para reflejar los datos perdidos.

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

Puede agregarse código adicional para cargar los objetos de datos de gran tamaño de forma asincrónica.

### <a name="testing-drag-and-drop"></a>Pruebas de arrastrar y colocar

Se debe utilizar un iPad para probar el [ejemplo](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/).
Abra el ejemplo junto con otra aplicación (por ejemplo, notas) y arrastre filas y el texto entre ellos:

![captura de pantalla de la operación de arrastre en curso](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>Vínculos relacionados

- [Directrices de interfaz humana de arrastrar y colocar (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Arrastre y coloque el ejemplo de vista de tabla](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [Arrastre y coloque el ejemplo de vista de colección](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [Presentación de arrastrar y colocar (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Arrastrar y colocar con la recopilación y la vista de tabla (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/223/)
