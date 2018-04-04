---
title: 'Tutorial: crear una aplicación mediante la API de elementos'
description: En este artículo se basa en la información presentada en la introducción de cuadro de diálogo MonoTouch artículo. Presenta un tutorial que muestra cómo utilizar el MonoTouch.Dialog (MT D) elementos API para empezar rápidamente a generar una aplicación con MT D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: e4fbf744c6f967d09e0033212024c2e2398fb768
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---creating-an-application-using-the-elements-api"></a>Tutorial: crear una aplicación mediante la API de elementos

_En este artículo se basa en la información presentada en la introducción de cuadro de diálogo MonoTouch artículo. Presenta un tutorial que muestra cómo utilizar el MonoTouch.Dialog (MT D) elementos API para empezar rápidamente a generar una aplicación con MT D._

En este tutorial, usaremos el Monte D elementos API para crear un estilo de principal-detalle de aplicación que se muestra una lista de tareas. Cuando el usuario selecciona el <span class="ui"> + </span> botón en la barra de navegación, se agregará una nueva fila a la tabla para la tarea. Selección de la fila se le remitirá a la pantalla de detalles que nos permite actualizar la descripción de la tarea y la fecha de vencimiento, como se muestra a continuación:

 [![](elements-api-walkthrough-images/01-task-list-app.png "Selección de la fila se le remitirá a la pantalla de detalles que nos permite actualizar la descripción de la tarea y la fecha de vencimiento")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 <a name="Elements_API_Walkthrough" />


## <a name="elements-api-walkthrough"></a>Tutorial de API de elementos

En el [Introducción al cuadro de diálogo de MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) artículo, hemos obtenido un conocimiento sólido de las distintas partes del MT D. Vamos a usar la API de elementos para colocarlos todo junto en una aplicación.

 <a name="Setting_up_the_Multi-Screen_Application" />


## <a name="setting-up-the-multi-screen-application"></a>Cómo configurar la aplicación de la pantalla

Para iniciar el proceso de creación de la pantalla, se crea MonoTouch.Dialog una `DialogViewController`y, a continuación, agrega un `RootElement`.

Para crear una aplicación de la pantalla con MonoTouch.Dialog, se necesita:

1.  Crear un  `UINavigationController.`
1.  Crear un  `DialogViewController.`
1.  Agregar el `DialogViewController` como la raíz de la  `UINavigationController.` 
1.  Agregar un `RootElement` a la  `DialogViewController.`
1.  Agregar `Sections` y `Elements` a la  `RootElement.` 


 <a name="Using_A_UINavigationController" />


### <a name="using-a-uinavigationcontroller"></a>Uso de un UINavigationController

Para crear una aplicación de estilo de navegación, es necesario crear un `UINavigationController`y, a continuación, agréguela como la `RootViewController` en el `FinishedLaunching` método de la `AppDelegate`. Para realizar la `UINavigationController` trabajar con MonoTouch.Dialog, agregamos una `DialogViewController` a la `UINavigationController` tal y como se muestra a continuación:

```csharp
public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
{
        _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
        _rootElement = new RootElement ("To Do List"){new Section ()};

        // code to create screens with MT.D will go here …

        _rootVC = new DialogViewController (_rootElement);
        _nav = new UINavigationController (_rootVC);
        _window.RootViewController = _nav;
        _window.MakeKeyAndVisible ();
            
        return true;
}
```

El código anterior crea una instancia de un `RootElement` y lo pasa en el `DialogViewController`. El `DialogViewController` siempre tiene un `RootElement` en la parte superior de su jerarquía. En este ejemplo, el `RootElement` se crea con la cadena "Lista de tareas pendientes," que actúa como el título en la barra de navegación del controlador de navegación. En este momento, ejecutar la aplicación podría presentar la pantalla que se muestra a continuación:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Ejecutar la aplicación presentará la pantalla que se muestra aquí")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Vamos a ver cómo se utiliza la estructura jerárquica del MonoTouch.Dialog de `Sections` y `Elements` para agregar más pantallas.

 <a name="Creating_the_Dialog_Screens" />


### <a name="creating-the-dialog-screens"></a>Crear las pantallas de cuadro de diálogo

A `DialogViewController` es un `UITableViewController` subclase que MonoTouch.Dialog se usa para agregar pantallas. MonoTouch.Dialog crea pantallas mediante la adición de un `RootElement` a un `DialogViewController`, tal y como se ha explicado anteriormente. El `RootElement` puede tener `Section` instancias que representan las secciones de una tabla.
Las secciones se componen de elementos, secciones o incluso otros `RootElements`. Anidando `RootElements`, MonoTouch.Dialog crea automáticamente una aplicación de estilo de navegación, como verá a continuación.

 <a name="Using_DialogViewController" />


### <a name="using-dialogviewcontroller"></a>Usar DialogViewController

El `DialogViewController`, que se va a un `UITableViewController` subclase, tiene un `UITableView` como su vista. En este ejemplo, queremos agregar elementos a la tabla cada vez que la <span class="ui"> + </span> botón que se derivan. Puesto que la `DialogViewController` se ha agregado a un `UINavigationController`, podemos usar la `NavigationItem`del `RightBarButton` propiedad que se va a agregar el <span class="ui"> + </span> botón, tal y como se muestra a continuación:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Cuando se crea el `RootElement` anteriormente, se lo ha pasado una sola `Section` instancia para que podamos agregar elementos como la <span class="ui"> + </span> botón derivado por el usuario. Podemos utilizar el siguiente código para llevar a cabo este en el evento controlador para el botón:

```csharp
_addButton.Clicked += (sender, e) => {
                
        ++n;
                
        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
        var taskElement = new RootElement (task.Name){
                new Section () {
                        new EntryElement (task.Name, 
                                "Enter task description", task.Description)
                },
                new Section () {
                        new DateElement ("Due Date", task.DueDate)
                }
        };
        _rootElement [0].Add (taskElement);
};
```

Este código crea un nuevo `Task` objeto cada vez que se derivan del botón. La siguiente muestra la implementación simple de la `Task` clase:

```csharp
public class Task
{   
        public Task ()
        {
        }
        
        public string Name { get; set; }
        
        public string Description { get; set; }

        public DateTime DueDate { get; set; }
}
```

 []()

La tarea `Name` propiedad se utiliza para crear la `RootElement`del título junto con una variable de contador denominada `n` que se incrementa para cada nueva tarea. MonoTouch.Dialog convierte los elementos en las filas que se agregan a la `TableView` cuando cada `taskElement` se agrega.

 <a name="Presenting_and_Managing_Dialog_Screens" />


## <a name="presenting-and-managing-dialog-screens"></a>Presentar y administrar filtros de cuadro de diálogo

Se usa un `RootElement` para que MonoTouch.Dialog crearía automáticamente una nueva pantalla para obtener detalles de cada tarea y navegar hasta él cuando se selecciona una fila.

La propia pantalla de detalles de tarea se compone de dos secciones; cada una de estas secciones contiene un único elemento. El primer elemento se crea desde una `EntryElement` para proporcionar una fila editable de la tarea `Description` propiedad. Cuando se selecciona el elemento, se presenta un teclado para editar texto tal y como se muestra a continuación:

 [![](elements-api-walkthrough-images/03-create-task.png "Cuando el elemento está seleccionado, se presenta un teclado para editar texto tal como se muestra")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La segunda sección contiene un `DateElement` que nos permite administrar la tarea `DueDate` propiedad. Selección de la fecha de carga automáticamente un selector de fecha como se muestra:

 [![](elements-api-walkthrough-images/04-date-picker.png "Selección de la fecha de carga automáticamente como un selector de fecha")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

Tanto en el `EntryElement` y `DateElement` casos (o de cualquier elemento de entrada de datos en MonoTouch.Dialog), los cambios realizados en los valores se conservan automáticamente. Podemos mostrar esto modificando la fecha y, a continuación, navegar y hacia atrás entre la pantalla de raíz y varios detalles de la tarea, donde se conservan los valores en las pantallas de detalles.

 <a name="Summary" />


## <a name="summary"></a>Resumen

Este artículo presenta un tutorial en el que se ha explicado cómo usar la API de elementos MonoTouch.Dialog. Tratan los pasos básicos para crear una aplicación de varias pantalla con MT D., incluida la forma de utilizar un `DialogViewController` y cómo agregar elementos y secciones para crear pantallas. Además, ha explicado cómo usar MT D. junto con un `UINavigationController`.


## <a name="related-links"></a>Vínculos relacionados

- [MTDWalkthrough (ejemplo)](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [Presentación en pantalla - Miguel de Icaza crea una pantalla de inicio de sesión de iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Presentación en pantalla - crear fácilmente interfaces de usuario de iOS con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introducción a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial de API de reflexión](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Tutorial de elemento JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Cuadro de diálogo de MonoTouch en Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de clase UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de clase UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
