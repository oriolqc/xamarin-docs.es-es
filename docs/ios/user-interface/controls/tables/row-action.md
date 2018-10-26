---
title: Trabajar con acciones de fila en Xamarin.iOS
description: Esta guía muestra cómo crear acciones de pasar el dedo personalizado para las filas de tabla con UISwipeActionsConfiguration o UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6d41f37d4a63db710bb04e35e6e1a4be0dd4f7a4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105914"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Trabajar con acciones de fila en Xamarin.iOS

_Esta guía muestra cómo crear acciones de pasar el dedo personalizado para las filas de tabla con UISwipeActionsConfiguration o UITableViewRowAction_

![Muestra que demuestran las acciones de pasar el dedo en filas](row-action-images/action02.png)

iOS proporciona dos maneras de realizar acciones en una tabla: `UISwipeActionsConfiguration` y `UITableViewRowAction`.

`UISwipeActionsConfiguration` se introdujo en iOS 11 y se utiliza para definir un conjunto de acciones que deban tener lugar cuando los deslizamientos usuario _en cualquier dirección_ en una fila de una vista de tabla. Este comportamiento es similar de la aplicación nativa Mail.app 

La `UITableViewRowAction` clase se utiliza para definir una acción que se llevará a cabo cuando los deslizamientos usuario restante horizontalmente en una fila en una vista de tabla.
Por ejemplo, al editar una tabla, deslice el dedo hacia la izquierda en una fila muestra un **eliminar** botón de forma predeterminada. Mediante la asociación de varias instancias de la `UITableViewRowAction` clase a un `UITableView`, se pueden definir múltiples acciones personalizadas, cada uno con su propio texto, formato y el comportamiento.


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Hay un tres pasos necesarios para implementar las acciones de pasar el dedo con `UISwipeActionsConfiguration`:

1. Invalidar `GetLeadingSwipeActionsConfiguration` o `GetTrailingSwipeActionsConfiguration` métodos. Estos métodos devuelven un `UISwipeActionsConfiguration`. 
2. Crear una instancia de la `UISwipeActionsConfiguration` va a devolver. Esta clase toma una matriz de `UIContextualAction`.
3. Creará un control `UIContextualAction`.

Estos se explican con más detalle en las secciones siguientes.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. Implementación de los métodos SwipeActionsConfigurations

`UITableViewController` (y también `UITableViewSource` y `UITableViewDelegate`) contiene dos métodos: `GetLeadingSwipeActionsConfiguration` y `GetTrailingSwipeActionsConfiguration`, que se usan para implementar un conjunto de acciones de pasar el dedo en una fila de la vista de tabla. La acción de deslizar rápidamente iniciales se refiere al pasar el dedo desde el lado izquierdo de la pantalla en un idioma de izquierda a derecha y desde el lado derecho de la pantalla en un idioma de derecha a izquierda. 

El ejemplo siguiente (desde el [TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions) ejemplo) muestra cómo implementar la configuración inicial de deslizar rápidamente. Se crean dos acciones de las acciones contextuales, que se explican [debajo](#create-uicontextualaction). Estas acciones, a continuación, se pasan a una recién inicializado [ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations), que se usa como el valor devuelto.


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. Crear una instancia de un `UISwipeActionsConfiguration`

Crear una instancia de un `UISwipeActionsConfiguration` utilizando el `FromActions` método para agregar una nueva matriz de `UIContextualAction`s, como se muestra en el siguiente fragmento de código:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

Es importante tener en cuenta que el orden en el que se muestran sus acciones depende de cómo se pasan en la matriz. Por ejemplo, el código anterior para deslizamientos iniciales muestra así como las acciones:

![iniciales de las acciones de pasar el dedo se muestran en una fila de tabla](row-action-images/action03.png)

Para finales deslizamientos, las acciones se mostrará como se muestra en la siguiente imagen:

![al final de las acciones de pasar el dedo muestra en una fila de tabla](row-action-images/action04.png)

Este fragmento de código también hace uso de la nueva `PerformsFirstActionWithFullSwipe` propiedad. De forma predeterminada, esta propiedad se establece en true, lo que significa que la primera acción de la matriz se producirá cuando un usuario pase el dedo por completo en una fila. Si tiene una acción que no es destructiva (por ejemplo "Delete", esto podría no ser comportamiento ideal y, por tanto, debe establecer en `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Cree un control `UIContextualAction`

La acción contextual es donde se crea realmente la acción que se mostrará cuando el usuario pase el dedo una fila de tabla.

Para inicializar una acción que debe proporcionar un `UIContextualActionStyle`, un título y un `UIContextualActionHandler`. El `UIContextualActionHandler` toma tres parámetros: una acción, la vista mostrada en la acción y un controlador de finalización:

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

Se pueden editar diversas propiedades visuales, como el color de fondo o imagen de la acción. El fragmento de código anterior muestra cómo agregar una imagen a la acción y establecer su color de fondo en azul.

Una vez que se han creado las acciones contextuales, pueden usar para inicializar el `UISwipeActionsConfiguration` en el `GetLeadingSwipeActionsConfiguration` método.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Para definir una o varias acciones de fila personalizado para un `UITableView`, deberá crear una instancia de la `UITableViewDelegate` clase e invalidar el `EditActionsForRow` método. Por ejemplo:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

Estático `UITableViewRowAction.Create` método se utiliza para crear un nuevo `UITableViewRowAction` que mostrará un **Hi** cuando los deslizamientos usuario restante horizontalmente en una fila en la tabla. Más adelante una nueva instancia de la `TableDelegate` creado y conectado a la `UITableView`. Por ejemplo:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Cuando se ejecuta el código anterior y dejan los deslizamientos de usuario en una fila de tabla, el **Hi** se mostrará un botón en lugar de la **eliminar** botón que se muestra de forma predeterminada:

[![](row-action-images/action01.png "El botón de alta que se muestra en lugar del botón de eliminación")](row-action-images/action01.png#lightbox)

Si el usuario pulsa el **Hi** botón, `Hello World!` se escribirá en la consola en Visual Studio para Mac o Visual Studio cuando la aplicación se ejecuta en el modo de depuración.



## <a name="related-links"></a>Vínculos relacionados

- [TableSwipeActions (ejemplo)](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
