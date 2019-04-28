---
title: Trabajo con tablas en iOS Designer
description: En las secciones anteriores, analizamos el desarrollo mediante las tablas. En este caso, la quinta y última sección, se le agregar lo que hemos aprendido hasta ahora y cree una aplicación de lista de tareas básica mediante un guión gráfico.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 303c96ae6cdbc9f5b327c971f962d6eac75a6fa1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227618"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Trabajo con tablas en iOS Designer

Los guiones gráficos son una forma WYSIWYG para crear aplicaciones de iOS y se admiten dentro de Visual Studio en Mac y Windows. Para obtener más información sobre los guiones gráficos, consulte el [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md) documento. Guiones gráficos también le permiten modificar los diseños de celda *en* la tabla, que simplifica el desarrollo con tablas y vistas

Al configurar las propiedades de una vista de tabla en el Diseñador de iOS, hay dos tipos de contenido de la celda que puede elegir entre: **Dinámica** o **estático** contenido del prototipo.

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Contenido dinámico de prototipo

Un `UITableView` sin prototipo contenido normalmente está diseñado para mostrar una lista de datos donde la celda prototipo (o las celdas, como pueden definir más de uno) se vuelven a usar para cada elemento en la lista. Las celdas no es necesario crear una instancia, sino que se obtienen en el `GetView` método mediante una llamada a la `DequeueReusableCell` método de su `UITableViewSource`.

 <a name="Static_Content" />


## <a name="static-content"></a>Contenido estático

`UITableView`s con contenido estático permite diseñar directamente en la superficie de diseño de tablas. Pueden arrastrar en la tabla de celdas y personalizarlo cambiando las propiedades y agregando controles.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Creación de una aplicación controlada por el guión gráfico

El ejemplo StoryboardTable contiene una aplicación de pantalla principal-detallada simple que usa ambos tipos de UITableView en un guión gráfico. El resto de esta sección describe cómo crear un ejemplo de lista de tareas pendientes pequeño que tendrá un aspecto similar al siguiente cuando haya terminado:

 [![Pantallas de ejemplo](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

La interfaz de usuario se compilará con un guión gráfico, y ambas pantallas usará un UITableView. La pantalla principal usa *contenido prototipo* al diseño de la fila y el detalle usa pantalla *contenido estático* para crear un formulario de entrada de datos mediante los diseños de celda personalizada.

## <a name="walkthrough"></a>Tutorial

Crear una nueva solución en Visual Studio mediante **(crear) un proyecto nuevo... > aplicación de vista única (C#)** y llámelo _StoryboardTables_.

 [![Crear un cuadro de diálogo nuevo proyecto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

Se abrirá la solución con algunos C# archivos y un `Main.storyboard` ya ha creado el archivo. Haga doble clic en el `Main.storyboard` archivo para abrirlo en el Diseñador de iOS.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Modificar el guión gráfico

El guión gráfico se editarán en tres pasos:

-  Primero, diseño de los controladores de ver y establecer sus propiedades.
-  En segundo lugar, cree la interfaz de usuario arrastrando y colocando objetos en la vista
-  Por último, agregue la clase UIKit necesaria a cada vista y asigne un nombre de varios controles de modo que pueden hacer referencia a en el código.


Una vez completado el guión gráfico, se puede agregar código para hacer que todo funcione.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Los controladores de vista de diseño

El primer cambio en el guión gráfico es eliminar la vista de detalle existente y reemplazarla por una UITableViewController. Siga estos pasos:

1.  Seleccione la barra en la parte inferior del controlador de vista y elimínelo.
2.  Arrastre un **controlador de navegación** y un **controlador de vista de tabla** en el guion gráfico en el cuadro de herramientas. 
3.  Cree un segue desde el controlador de vista raíz en el segundo controlador de vista de tabla que se acaba de agregar. Para crear el objeto segue, Control + arrastrar *desde la celda de detalle* a la UITableViewController recién agregado. Elija la opción **mostrar** en **Segue selección**. 
4.  Seleccione el nuevo segue que ha creado y asígnele un identificador para este origen en el código de referencia. Haga clic en el objeto segue y escriba `TaskSegue` para el **identificador** en el **panel de propiedades**, similar al siguiente:    
  [![Segue de nomenclatura en el panel de propiedades](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. A continuación, configure las dos vistas de tabla seleccionándolos y usando el panel de propiedades. Asegúrese de seleccionar la vista y el controlador de vista no: se puede usar el esquema del documento para ayudar con la selección.

6.  Cambiar el controlador de vista raíz para que sea **contenido: Prototipos dinámicos** (la vista en la superficie de diseño se etiquetarse **prototipo contenido** ):

    [![Establecer la propiedad de contenido en prototipos dinámicos](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  Cambiar el nuevo **UITableViewController** sea **contenido: Las celdas estáticas**. 


8. El nuevo UITableViewController debe tener su nombre de clase y el identificador de conjunto. Seleccione el tipo y el controlador de vista _TaskDetailViewController_ para el **clase** en el **panel de propiedades** : Esto creará un nuevo `TaskDetailViewController.cs` archivo de la solución Panel. Escriba el **StoryboardID** como _detalle_, como se muestra en el ejemplo siguiente. Esto se usará más adelante para cargar esta vista en C# código:  

    [![Establecer el identificador de guión gráfico](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. La superficie de diseño de guión gráfico debe tener el siguiente aspecto (título del elemento de navegación del controlador de vista raíz se ha cambiado a "Panel de tareas"):

    [![Superficie de diseño](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Crear la UI

Ahora que las vistas y los objetos Segue están configurados, deben agregarse los elementos de interfaz de usuario.

#### <a name="root-view-controller"></a>Controlador de vista raíz

En primer lugar, seleccione la celda de prototipo en el controlador de vista maestra y establezca el **identificador** como _taskcell_, tal y como se muestra a continuación. Esto se usará más adelante en el código para recuperar una instancia de este UITableViewCell:

 [![establecimiento del identificador de celda](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

A continuación, deberá crear un botón que agregará nuevas tareas, como se muestra a continuación:

[![elemento de botón en la barra de navegación de barra](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Haga lo siguiente: 

-  Arrastre un **elemento de botón de barra** desde el cuadro de herramientas hasta la _lado derecho de la barra de navegación_.
-  En el **panel de propiedades**, en **elemento de botón de barra** seleccione **identificador: Agregar** (para que sea un *+* además de botón). 
-  Asígnele un nombre para que puede identificarse en el código en una etapa posterior. Tenga en cuenta que deberá proporcionar el controlador de vista raíz de un nombre de clase (por ejemplo **ItemViewController**) para que pueda establecer el nombre del elemento de botón de barra.


#### <a name="taskdetail-view-controller"></a>Controlador de vista TaskDetail

La vista de detalles requiere mucho más trabajo. Las celdas de la vista de tabla deben arrastrar a la vista y, a continuación, se rellena con las etiquetas, las vistas de texto y botones. La captura de pantalla siguiente muestra la interfaz de usuario finalizada con dos secciones. Una sección tiene tres celdas, tres etiquetas, cambie uno y dos campos de texto, mientras que la segunda sección muestra una celda con dos botones:

 [![diseño de la vista de detalle](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

Los pasos para crear el diseño completo son:

Seleccione la vista de tabla y abra el **panel propiedad**. Actualizar las propiedades siguientes:

-  **Las secciones**: _2_ 
-  **Estilo**: _Agrupados_
-  **Separador**: _Ninguno_
-  **Selección**: _No hay nada seleccionado_

Seleccione la sección superior y, en **Propiedades > sección de vista de tabla** cambiar **filas** a _3_, tal y como se muestra a continuación:


 [![configuración de la sección superior a tres filas](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Para cada celda abierto el **panel de propiedades** y establezca:

-  **Estilo**:  _Custom_
-  **Identificador**: Elija un identificador único para cada celda (p ej. "_título_","_notas_","_realiza_").
-  Arrastre los controles necesarios para generar el diseño se muestra en la captura de pantalla (colocar **UILabel**, **interfaz de usuario** y **UISwitch** en las celdas correctas y establezca las etiquetas de forma adecuada, Internet Explorer. Título, notas y listo).


En la segunda sección, establezca **filas** a _1_ y agarre el controlador de cambio de tamaño inferior de la celda para hacerla más alta.

-  **Establecer el identificador de**: a un valor único (p ej. "Guardar"). 
-  **Establecer el fondo**:  _Borrar el Color_ .
-  Arrastre dos botones a la celda y establezca sus títulos correctamente (es decir, _guardar_ y _eliminar_), tal y como se muestra a continuación:

   [![dos botones de opción en la sección inferior](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

En este momento también puede establecer restricciones en sus celdas y controles para garantizar un diseño adaptable.

### <a name="adding-uikit-class-and-naming-controls"></a>Agregar clase UIKit y controles de nomenclatura

Hay unos cuantos pasos finales en la creación de nuestra guión gráfico. Primero debemos dar a cada uno de nuestros controles un nombre en **identidad > nombre** por lo que pueden utilizarse en el código más adelante. Nombre como sigue:

-  **Título de la interfaz de usuario** : _TitleText_
-  **Notas de la interfaz de usuario** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **Eliminar UIButton** : _DeleteButton_
-  **Guardar UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>Adición de código

Se hará el resto del trabajo en Visual Studio en Mac o Windows con C#. Tenga en cuenta que los nombres de propiedad utilizados en el código reflejan las que se establecen en el tutorial anterior.

Primero va a crear un `Chores` (clase), que le proporcionarán una manera de obtener y establecer el valor de ID, Name, notas y el hecho booleano, por lo que podemos usar esos valores a lo largo de la aplicación.

En su `Chores` clase agregue el código siguiente:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

A continuación, cree un `RootTableSource` clase que hereda de `UITableViewSource`. 

La diferencia entre esto y una vista de tabla sin guiones gráficos que es el `GetView` método no tiene que crear una instancia de ninguna de las celdas – `theDequeueReusableCell` método siempre devolverá una instancia de la celda prototipo (con el identificador coincidente).

El código siguiente es desde el `RootTableSource.cs` archivo:

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

Para usar el `RootTableSource` de clases, cree una nueva colección en el `ItemViewController`del constructor:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

En `ViewWillAppear` pasar la colección en el origen y asignar a la vista de tabla:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Si ejecuta la aplicación ahora, la pantalla principal ahora cargará y mostrar una lista de las dos tareas. Cuando una tarea se toca el segue definido por el guión gráfico hará que aparezca la pantalla de detalle, pero no mostrará ningún dato en este momento.

Para "enviar un parámetro' en un segue, reemplazar el `PrepareForSegue` método y establecer las propiedades de la `DestinationViewController` (el `TaskDetailViewController` en este ejemplo). Habrá ha creado una instancia de la clase de controlador de vista de destino pero no está aún muestra al usuario: Esto significa que se puede establecer las propiedades de la clase pero no modificar todos los controles de interfaz de usuario:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

En `TaskDetailViewController` el `SetTask` método asigna sus parámetros a propiedades, por lo que pueden que se hace referencia en ViewWillAppear. No se puede modificar las propiedades del control en `SetTask` porque puede que no exista cuando `PrepareForSegue` se denomina:

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

El segue ahora abrirá la pantalla de detalles y mostrar la información de la tarea seleccionada. Lamentablemente, no hay ninguna implementación para el **guardar** y **eliminar** botones. Antes de implementar los botones, agregue estos métodos a **ItemViewController.cs** para actualizar los datos subyacentes y cierre la pantalla de detalle:

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

A continuación, deberá agregar el botón `TouchUpInside` controlador de eventos para el `ViewDidLoad` método **TaskDetailViewController.cs**. El `Delegate` referencia de propiedad a la `ItemViewController` creada específicamente por lo que podemos llamar a `SaveTask` y `DeleteTask`, que cierre esta vista como parte de su funcionamiento:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

La última parte restante de la funcionalidad para compilar es la creación de tareas nuevas. En **ItemViewController.cs** agregar un método que crea nuevas tareas y abre la vista de detalle. Para crear instancias de una vista de un guión gráfico, use el `InstantiateViewController` método con el `Identifier` de esa vista - en este ejemplo, será 'detail':

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

Por último, en la barra de navegación en el botón Conectar **ItemViewController.cs**del `ViewDidLoad` método llamarlo:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Complete el ejemplo de guion gráfico: la aplicación finalizada se ve así:

[![Aplicación finalizada](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

El ejemplo se muestra:

-  Creación de una tabla con contenido de prototipo donde se definen las celdas para que volver a usar mostrar listas de datos. 
-  Creación de una tabla con contenido estático para crear un formulario de entrada. Esto incluye cambiar el estilo de tabla y agregar secciones, las celdas y controles de interfaz de usuario. 
-  Cómo crear un segue e invalidar la `PrepareForSegue` método para notificar a la vista de destino de los parámetros requiere. 
-  Cargando las vistas de guión gráfico directamente con el `Storyboard.InstantiateViewController` método.



## <a name="related-links"></a>Vínculos relacionados

- [StoryboardTable (ejemplo)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
