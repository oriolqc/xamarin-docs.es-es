---
title: Trabajar con tablas en el Diseñador de iOS
description: En las secciones anteriores se han examinado desarrollar con tablas. En este caso, la quinta y última sección, se le agregar lo que hemos hemos visto hasta ahora y crear una aplicación básica de lista de tareas con un guión gráfico.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 869719b1d3514fee4b45f61dcdb34a0bcf54f7b2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-tables-in-the-ios-designer"></a>Trabajar con tablas en el Diseñador de iOS

Guiones gráficos son una forma WYSIWYG para crear aplicaciones de iOS y se admiten dentro de Visual Studio en Mac y Windows. Para obtener más información sobre los guiones gráficos, consulte la [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md) documento. Guiones gráficos también le permiten modificar los diseños de celda *en* la tabla, lo que simplifica el desarrollo con tablas y las celdas

Al configurar las propiedades de una vista de tabla en el Diseñador de iOS, hay dos tipos de contenido de la celda puede elegir entre: **dinámica** o **estático** contenido de prototipo.

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Contenido dinámico prototipo

Un `UITableView` con prototipo contenido normalmente sirve para mostrar una lista de datos donde la celda prototipo (o las celdas, como pueden definir más de uno) se vuelven a usar para cada elemento de la lista. Las celdas no es necesario crear una instancia, sino que se obtienen en los `GetView` método mediante una llamada a la `DequeueReusableCell` método de su `UITableViewSource`.

 <a name="Static_Content" />


## <a name="static-content"></a>Contenido estático

`UITableView`s con contenido estático permite tablas a diseñarse directamente en la superficie de diseño. Las celdas se pueden arrastrar en la tabla y personalizar cambiando propiedades y agregar controles.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Crear una aplicación controlada por el guión gráfico

El ejemplo StoryboardTable contiene una aplicación principal-detalle sencilla que utiliza dos tipos de UITableView de un guión gráfico. El resto de esta sección describe cómo compilar un ejemplo de lista de tareas pequeño que tendrá un aspecto similar al siguiente cuando haya terminado:

 [![Pantallas de ejemplo](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

La interfaz de usuario se generarán con un guión gráfico, y ambas pantallas usará un UITableView. La pantalla principal utiliza *contenido prototipo* diseño de la fila y el detalle usa pantalla *contenido estático* para crear un formulario de entrada de datos mediante los diseños de celda personalizado.

## <a name="walkthrough"></a>Tutorial

Cree una nueva solución en Visual Studio mediante **(crear) un proyecto nuevo... > único App(C#) vista**y llámelo _StoryboardTables_.

 [![Crear un cuadro de diálogo nuevo proyecto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

Se abrirá la solución con algunos archivos de C# y un `Main.storyboard` archivo ya se ha creado. Haga doble clic en el `Main.storyboard` archivo para abrirlo en el Diseñador de iOS.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Modificar el guión gráfico

El guión gráfico se editarán en tres pasos:

-  En primer lugar, diseño de los controladores de ver y establecer sus propiedades.
-  En segundo lugar, cree la interfaz de usuario arrastrando y colocando objetos en la vista
-  Por último, agregue la clase UIKit necesaria a cada vista y asigne un nombre de varios controles por lo que pueden hacer referencia en código.


Una vez completado el guión gráfico, se puede agregar código para que todo funcione.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Los controladores de la vista de diseño

El primer cambio en el guión gráfico es eliminar la vista de detalle existente y reemplazarlo con un UITableViewController. Siga estos pasos:

1.  Seleccione la barra en la parte inferior del controlador de vista y elimínelo.
2.  Arrastre un **controlador navegación** y un **controlador de vista de tabla** en el guión gráfico en el cuadro de herramientas. 
3.  Crear un segue desde el controlador de vista de raíz para el segundo controlador de vista de tabla que se acaba de agregar. Para crear el segue, Control + arrastrar *de la celda de detalle* a la UITableViewController recién agregado. Elija la opción **mostrar*** en **desplazará tranquilamente selección**. 
4.  Seleccione el nuevo desplazará tranquilamente que ha creado y asígnele un identificador de referencia se desplazará tranquilamente en código. Haga clic en el segue y escriba `TaskSegue` para el **identificador** en el **panel de propiedades**, similar a la siguiente:    
  [![Nomenclatura desplazará tranquilamente en el panel de propiedades](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. A continuación, configure las dos vistas de tabla seleccionándolos y usando el panel de propiedades. Asegúrese de seleccionar la vista y el controlador de vista no: se puede usar el esquema del documento para ayudar con la selección.

6.  Cambiar el controlador de vista de raíz para que sea **contenido: prototipos dinámica** (la vista en la superficie de diseño se etiquetarse **prototipo contenido** ):

    [![Si se establece la propiedad de contenido en prototipos dinámicos](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  Cambiar la nueva **UITableViewController** como **contenido: estático celdas**. 


8. El nuevo UITableViewController debe tener su nombre de clase y el identificador de conjunto. Seleccione el controlador de vista y el tipo _TaskDetailViewController_ para el **clase** en el **panel de propiedades** : Esto creará un nuevo `TaskDetailViewController.cs` archivo de la solución Panel. Escriba el **StoryboardID** como _detalle_, como se muestra en el ejemplo siguiente. Se usará más adelante para cargar esta vista en código C#:  

    [![Establecer el identificador de guión gráfico](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. La superficie de diseño de guión gráfico debe tener el siguiente aspecto (título del elemento de navegación del controlador de vista raíz ha cambiado a "Panel de tarea ardua"):

    [![Superficie de diseño](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Crear la UI

Ahora que las vistas y segues están configurados, los elementos de la interfaz de usuario deben agregarse.

#### <a name="root-view-controller"></a>Controlador de vista raíz

En primer lugar, seleccione la celda de prototipo en el controlador de vista maestra y establezca el **identificador** como _taskcell_, como se muestra a continuación. Esto se utilizará más adelante en el código para recuperar una instancia de este UITableViewCell:

 [![establecer el identificador de celda](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

A continuación, deberá crear un botón que agregará nuevas tareas, como se muestra a continuación:

[![elemento de botón en la barra de navegación de barra](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Haga lo siguiente: 

-  Arrastre un **elemento de botón de barra de** desde el cuadro de herramientas hasta la _lado derecho de la barra de navegación_.
-  En el **panel de propiedades**, en **elemento de botón de barra de** seleccione **identificador: agregar** (para que sea un *+* además de botón). 
-  Asígnele un nombre para que se puedan identificar en el código en una fase posterior. Tenga en cuenta que debe proporcionar el controlador de vista de la raíz de un nombre de clase (por ejemplo **ItemViewController**) para que pueda establecer el nombre del elemento de botón de barra.


#### <a name="taskdetail-view-controller"></a>Controlador de vista de TaskDetail

La vista de detalles requiere mucho más trabajo. Las celdas de la vista de tabla deben arrastrar hasta la vista y, a continuación, se rellena con las etiquetas, vistas de texto y botones. La captura de pantalla siguiente muestra la interfaz de usuario terminado con dos secciones. Una sección tiene tres celdas, tres etiquetas, cambiar uno y dos campos de texto, mientras que la segunda sección muestra una celda con dos botones:

 [![diseño de la vista de detalle](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

Los pasos para crear el diseño completando son:

Seleccione la vista de tabla y abra el **controlador de propiedad**. Actualice las siguientes propiedades:

-  **Secciones**: _2_ 
-  **Estilo**: _agrupados_
-  **Separador de**: _ninguno_
-  **Selección**: _no hay nada seleccionado_

Seleccione la sección superior y en **Propiedades > sección de vista de tabla** cambiar **filas** a _3_, como se muestra a continuación:


 [![configuración de la sección superior a tres filas](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Para cada celda abierta la **panel de propiedades** y establezca:

-  **Estilo**: _personalizado_
-  **Identificador**: elija un identificador único para cada celda (p. ej. "_título_","_notas_","_realiza_").
-  Arrastre los controles necesarios para generar el diseño se muestra en la captura de pantalla (colocar **UILabel**, **UITextField** y **UISwitch** en las celdas adecuadas y establezca las etiquetas de forma adecuada, Internet Explorer. Título, notas y realizar).


En la segunda sección, establezca **filas** a _1_ y agarre el controlador de cambio de tamaño inferior de la celda para que sea más alto.

-  **Establecer el identificador de**: a un valor único (p. ej. "Guardar"). 
-  **Establecer el fondo**: _borrar el Color_ .
-  Arrastre dos botones a la celda y establezca sus títulos correctamente (es decir, _guardar_ y _eliminar_), como se muestra a continuación:

   [![dos botones de opción en la sección inferior](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

En este momento también puede establecer restricciones en las celdas y los controles para asegurar un diseño adaptable.

### <a name="adding-uikit-class-and-naming-controls"></a>Agregar clase de UIKit y controles de nomenclatura

Hay algunos pasos finales en la creación de nuestro guión gráfico. Primero debemos dar a cada uno de nuestros controles un nombre en **identidad > nombre** por lo que pueden usarse en el código más adelante. Nombre como se indica a continuación:

-  **Título UITextField** : _TitleText_
-  **Notas de la UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **Eliminar UIButton** : _DeleteButton_
-  **Guardar UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>Agregar código

El resto del trabajo se hará en Visual Studio en Mac o Windows con C#. Tenga en cuenta que los nombres de propiedad que se utiliza en código reflejan las que se establecen en el tutorial anterior.

Primero va a crear un `Chores` (clase), que proporcionará una manera para obtener y establecer el valor de Id., nombre, notas y realiza Boolean, por lo que podemos usar esos valores a lo largo de la aplicación.

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

La diferencia entre esto y una vista de tabla sin guiones gráficos es que la `GetView` método no es necesario crear una instancia de ninguna de las celdas: `theDequeueReusableCell` método siempre devolverá una instancia de la celda prototipo (con el identificador de búsqueda de coincidencias).

El código siguiente procede el `RootTableSource.cs` archivo:

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

Para usar el `RootTableSource` clase, cree una nueva colección en el `ItemViewController`del constructor:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

En `ViewWillAppear` pasas la colección en el origen y asignar a la vista de tabla:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Si se ejecuta la aplicación ahora, la pantalla principal ahora cargará y mostrar una lista de dos tareas. Cuando una tarea se toca la segue definido por el guión gráfico hará que aparezca la pantalla de detalles, pero no mostrará ningún dato en este momento.

Para 'enviar un parámetro de' en un segue, invalidar la `PrepareForSegue` método y establecer las propiedades de la `DestinationViewController` (el `TaskDetailViewController` en este ejemplo). La clase de controlador de vista de destino se habrá creado una instancia pero no es aún muestra al usuario: Esto significa que puede establecer propiedades en la clase pero no modificar los controles de interfaz de usuario:

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

En `TaskDetailViewController` el `SetTask` método asigna sus parámetros a las propiedades, por lo que pueden hacer referencia en ViewWillAppear. No se puede modificar las propiedades del control en `SetTask` porque puede que no exista cuando `PrepareForSegue` se llama:

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

El segue ahora se abrirá la pantalla de detalles y mostrar la información de la tarea seleccionada. Lamentablemente, no hay ninguna implementación para el **guardar** y **eliminar** botones. Antes de implementar los botones, agregue estos métodos para **ItemViewController.cs** para actualizar los datos subyacentes y cerrar la pantalla de detalles:

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

A continuación, deberá agregar el botón `TouchUpInside` controlador de eventos para el `ViewDidLoad` método **TaskDetailViewController.cs**. El `Delegate` referencia de propiedad a la `ItemViewController` creada específicamente para, podemos llamar a `SaveTask` y `DeleteTask`, que cerrar esta vista como parte de su funcionamiento:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

La última parte restante de la funcionalidad para compilar es la creación de nuevas tareas. En **ItemViewController.cs** agregar un método que crea nuevas tareas y abre la vista de detalle. Para crear instancias de una vista de un guión gráfico, use la `InstantiateViewController` método con el `Identifier` para esa vista - en este ejemplo, será 'detail':

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

Por último, conecte el botón en la barra de navegación en **ItemViewController.cs**del `ViewDidLoad` método para que se llame:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Esto completa el ejemplo de guión gráfico: la aplicación finalizada se ve así:

[![Aplicación finalizada](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

El ejemplo muestra:

-  Crear una tabla con contenido de prototipo donde se definen las celdas para que volver a usar mostrar listas de datos. 
-  Crear una tabla con contenido estático para crear un formulario de entrada. Esto incluye cambiar el estilo de tabla y agregar secciones, las celdas y controles de interfaz de usuario. 
-  Cómo crear un segue e invalide el `PrepareForSegue` método para informar a la vista de destino de los parámetros requiere. 
-  Cargar vistas de guión gráfico directamente con el `Storyboard.InstantiateViewController` método.



## <a name="related-links"></a>Vínculos relacionados

- [StoryboardTable (ejemplo)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introducción a guiones gráficos](~/ios/user-interface/storyboards/index.md)
