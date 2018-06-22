---
title: Trabajar con acciones de la fila en Xamarin.iOS
description: Esta guía muestra cómo crear acciones personalizadas deslice el dedo para filas de tabla con UISwipeActionsConfiguration o UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2017
ms.openlocfilehash: 4be8b6dc66c9c047e6662067e7e3ecf81ab22893
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789946"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Trabajar con acciones de la fila en Xamarin.iOS

_Esta guía muestra cómo crear acciones personalizadas deslice el dedo para filas de tabla con UISwipeActionsConfiguration o UITableViewRowAction_

![Que muestra las acciones de deslizar rápidamente en filas](row-action-images/action02.png)

iOS proporciona dos mecanismos para realizar acciones en una tabla: `UISwipeActionsConfiguration` y `UITableViewRowAction`.

`UISwipeActionsConfiguration` se introdujo en iOS 11 y se utiliza para definir un conjunto de acciones que deban tener lugar cuando los lectores de crédito del usuario _en cualquier dirección_ en una fila de una vista de tabla. Este comportamiento es similar de la aplicación nativa Mail.app 

La `UITableViewRowAction` clase se utiliza para definir una acción que se llevará a cabo cuando los lectores de crédito del usuario que queda horizontalmente en una fila en una vista de tabla.
Por ejemplo, al editar una tabla, Deslizar rápidamente izquierda en una fila muestra un **eliminar** botón de forma predeterminada. Adjuntando varias instancias de la `UITableViewRowAction` clase a un `UITableView`, se pueden definir varias acciones personalizadas, cada uno con su propio texto, formato y comportamiento.


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Hay un tres pasos necesarios para implementar las acciones de deslizar rápidamente con `UISwipeActionsConfiguration`:

1. Invalidar `GetLeadingSwipeActionsConfiguration` o `GetTrailingSwipeActionsConfiguration` métodos. Estos métodos devuelven un `UISwipeActionsConfiguration`. 
2. Crear una instancia de la `UISwipeActionsConfiguration` va a devolver. Esta clase toma una matriz de `UIContextualAction`.
3. Creará un control `UIContextualAction`.

Estos se explican con más detalle en las secciones siguientes.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. Implementar los métodos de SwipeActionsConfigurations

`UITableViewController` (y también `UITableViewSource` y `UITableViewDelegate`) contiene dos métodos: `GetLeadingSwipeActionsConfiguration` y `GetTrailingSwipeActionsConfiguration`, que se usan para implementar un conjunto de acciones de deslizar rápidamente en una fila de la vista de tabla. La acción de deslizar rápidamente inicial hace referencia a un deslice el dedo desde el lado izquierdo de la pantalla en un idioma de izquierda a derecha y desde el lado derecho de la pantalla en un idioma de derecha a izquierda. 

En el siguiente ejemplo (desde el [TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions) ejemplo) muestra cómo implementar la configuración inicial de deslizar rápidamente. Se crean dos acciones de las acciones contextuales, que se explican [a continuación](#create-uicontextualaction). Estas acciones, a continuación, se pasan a un recién inicializado [ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations), que se utiliza como el valor devuelto.


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

Crear una instancia de un `UISwipeActionsConfiguration` mediante el uso de la `FromActions` método para agregar una nueva matriz del `UIContextualAction`s, tal como se muestra en el fragmento de código siguiente:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

Es importante tener en cuenta que el orden en el que se muestran sus acciones depende de cómo se pasan en la matriz. Por ejemplo, el código anterior para lectores de crédito iniciales muestra así como las acciones:

![Acciones de deslizar rápidamente a la izquierda muestra en una fila de tabla](row-action-images/action03.png)

Para los finales lectores de crédito, las acciones se mostrará como se muestra en la siguiente imagen:

![finales deslice el dedo acciones muestran en una fila de tabla](row-action-images/action04.png)

Este fragmento de código también hace uso de la nueva `PerformsFirstActionWithFullSwipe` propiedad. De forma predeterminada, esta propiedad se establece en true, lo que significa que la primera acción de la matriz se realizará cuando un usuario lectores crédito totalmente en una fila. Si tiene una acción que no es destructiva (por ejemplo "Delete", esto puede no ser un comportamiento ideal y, por tanto, debe establecer en `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Cree un control `UIContextualAction`

La acción contextual es donde se crea realmente la acción que se mostrará cuando el usuario swipes una fila de tabla.

Para inicializar una acción que se debe proporcionar un `UIContextualActionStyle`, un título y un `UIContextualActionHandler`. El `UIContextualActionHandler` toma tres parámetros: una acción, la vista mostrada en la acción y un controlador de finalización:

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

Varias propiedades visuales, como el color de fondo o la imagen de la acción se pueden editar. El fragmento de código anterior muestra cómo agregar una imagen a la acción y establecer su color de fondo en azul.

Una vez que se han creado las acciones contextuales, puede usar para inicializar la `UISwipeActionsConfiguration` en el `GetLeadingSwipeActionsConfiguration` método.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Para definir una o varias acciones de fila personalizado para un `UITableView`, debe crear una instancia de la `UITableViewDelegate` clase e invalidar el `EditActionsForRow` método. Por ejemplo:

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

El método estático `UITableViewRowAction.Create` método se utiliza para crear un nuevo `UITableViewRowAction` que mostrará un **Hi** botón cuando los lectores de crédito del usuario que queda horizontalmente en una fila en la tabla. Más adelante una nueva instancia de la `TableDelegate` se crea y asocia a la `UITableView`. Por ejemplo:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Cuando se ejecuta el código anterior y los lectores de crédito del usuario que queda en una fila de la tabla, el **Hi** botón se mostrarán en lugar de la **eliminar** botón que se muestra de forma predeterminada:

[![](row-action-images/action01.png "El botón de alta que se muestra en lugar del botón Eliminar.")](row-action-images/action01.png#lightbox)

Si el usuario puntea el **Hi** botón, `Hello World!` se escribirá en la consola en Visual Studio para Mac o en Visual Studio cuando la aplicación se ejecuta en el modo de depuración.



## <a name="related-links"></a>Vínculos relacionados

- [TableSwipeActions (ejemplo)](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables (ejemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
