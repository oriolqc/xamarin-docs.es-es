---
title: Creación de una aplicación Xamarin.iOS mediante la API de elementos
description: En este artículo se basa en la información presentada en la introducción de cuadro de diálogo MonoTouch artículo. Presenta un tutorial que muestra cómo usar MonoTouch.Dialog (destino maestro. (D) elementos API que se va a empezar rápidamente a generar una aplicación con destino maestro. D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 14711f9cc2c34d72765e28db158379bc2a26849b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670299"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Creación de una aplicación Xamarin.iOS mediante la API de elementos

_En este artículo se basa en la información presentada en la introducción de cuadro de diálogo MonoTouch artículo. Presenta un tutorial que muestra cómo usar MonoTouch.Dialog (destino maestro. (D) elementos API que se va a empezar rápidamente a generar una aplicación con destino maestro. D._

En este tutorial, vamos a usar el destino maestro. D elementos API para crear un estilo de maestro y detalles de la aplicación que muestra una lista de tareas. Cuando el usuario selecciona el <span class="ui"> + </span> botón en la barra de navegación, se agregará una nueva fila a la tabla para la tarea. Si selecciona la fila, irá a la pantalla de detalle que nos permite actualizar la descripción de la tarea y la fecha de vencimiento, tal como se muestra a continuación:

 [![](elements-api-walkthrough-images/01-task-list-app.png "Selección de la fila se le remitirá a la pantalla de detalle que nos permite actualizar la descripción de la tarea y la fecha de vencimiento")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 ## <a name="setting-up-mtd"></a>Configuración de destino maestro. D.

DESTINO MAESTRO. D. se distribuye con Xamarin.iOS. Para ello, haga doble clic en el **referencias** nodo de un Xamarin.iOS del proyecto en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia a la **MonoTouch.Dialog 1** ensamblado. A continuación, agregue `using MonoTouch.Dialog` instrucciones en el origen de código según sea necesario.

## <a name="elements-api-walkthrough"></a>Tutorial de API de elementos

En el [Introducción al cuadro de diálogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) artículo, hemos obtenido un conocimiento sólido de las distintas partes del destino maestro. D. Vamos a usar la API de elementos para colóquelos todos juntos en una aplicación.

## <a name="setting-up-the-multi-screen-application"></a>Configuración de la aplicación de varias pantalla

Para iniciar el proceso de creación de la pantalla, MonoTouch.Dialog crea un `DialogViewController`y, a continuación, agrega un `RootElement`.

Para crear una aplicación de varias pantalla con MonoTouch.Dialog, es necesario:

1.  Cree un control `UINavigationController.`
1.  Cree un control `DialogViewController.`
1.  Agregar el `DialogViewController` como la raíz de la  `UINavigationController.` 
1.  Agregar un `RootElement` a la  `DialogViewController.`
1.  Agregar `Sections` y `Elements` a la  `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Uso de un UINavigationController

Para crear una aplicación de estilo de navegación, debemos crear un `UINavigationController`y, a continuación, agréguelo como el `RootViewController` en el `FinishedLaunching` método de la `AppDelegate`. Para realizar la `UINavigationController` trabajar con MonoTouch.Dialog, agregamos un `DialogViewController` a la `UINavigationController` tal como se muestra a continuación:

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

El código anterior crea una instancia de un `RootElement` y lo pasa a la `DialogViewController`. El `DialogViewController` siempre tiene un `RootElement` en la parte superior de su jerarquía. En este ejemplo, el `RootElement` se crea con la cadena "Lista de tareas pendientes," que actúa como título en la barra de navegación del controlador de navegación. En este momento, ejecute la aplicación podría suponer la pantalla que se muestra a continuación:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Ejecutar la aplicación presentará la pantalla que se muestra aquí")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Veamos cómo usar la estructura jerárquica de MonoTouch.Dialog de `Sections` y `Elements` para agregar más pantallas.

### <a name="creating-the-dialog-screens"></a>Crear las pantallas de cuadro de diálogo

Un `DialogViewController` es un `UITableViewController` subclase que MonoTouch.Dialog se usa para agregar pantallas. MonoTouch.Dialog crea las pantallas mediante la adición de un `RootElement` a un `DialogViewController`, como hemos visto anteriormente. El `RootElement` puede tener `Section` instancias que representan las secciones de una tabla.
Las secciones están formadas por elementos, otras secciones, o incluso otro `RootElements`. Anidando `RootElements`, MonoTouch.Dialog crea automáticamente una aplicación de estilo de navegación, como veremos a continuación.

### <a name="using-dialogviewcontroller"></a>Uso de DialogViewController

El `DialogViewController`, que se va a un `UITableViewController` subclase, tiene un `UITableView` como su vista. En este ejemplo, queremos agregar elementos a la tabla cada vez que el <span class="ui"> + </span> se pulsa el botón. Puesto que la `DialogViewController` se ha agregado a un `UINavigationController`, podemos usar el `NavigationItem`del `RightBarButton` propiedad va a agregar el <span class="ui"> + </span> situado, tal como se muestra a continuación:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Cuando se crea el `RootElement` anteriormente, se lo ha pasado una sola `Section` instancia para que podamos agregar elementos como el <span class="ui"> + </span> se pulsa el botón por el usuario. Podemos usar el código siguiente para realizar este en el evento controlador del botón:

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

Este código crea un nuevo `Task` objeto cada vez que se pulsa el botón. La siguiente muestra la implementación simple de la `Task` clase:

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

La tarea `Name` propiedad se utiliza para crear el `RootElement`del título, junto con una variable de contador denominada `n` que se incrementa para cada nueva tarea. MonoTouch.Dialog convierte los elementos en las filas que se agregan a la `TableView` cuando cada `taskElement` se agrega.

## <a name="presenting-and-managing-dialog-screens"></a>Presentar y administrar las pantallas de cuadro de diálogo

Hemos usado un `RootElement` poder MonoTouch.Dialog automáticamente podría crear una nueva pantalla de detalles de la tarea y navegar a ella cuando se selecciona una fila.

La propia pantalla de detalles de tarea se compone de dos secciones; cada una de estas secciones contiene un único elemento. El primer elemento se crea a partir un `EntryElement` para proporcionar una fila editable de la tarea `Description` propiedad. Cuando se selecciona el elemento, se presenta un teclado para la edición de texto, tal como se muestra a continuación:

 [![](elements-api-walkthrough-images/03-create-task.png "Cuando se selecciona el elemento, un teclado para la edición de texto se presenta como se muestra")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La segunda sección contiene un `DateElement` que nos permite administrar la tarea `DueDate` propiedad. Si selecciona la fecha automáticamente, carga un selector de fecha como se muestra:

 [![](elements-api-walkthrough-images/04-date-picker.png "Si selecciona la fecha automáticamente carga como un selector de fecha")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

Tanto en el `EntryElement` y `DateElement` casos (o de cualquier elemento de entrada de datos de MonoTouch.Dialog), los cambios realizados en los valores se conservan automáticamente. Podemos demostrar esto, edite la fecha y, a continuación, navegar hacia delante y hacia atrás entre la pantalla de raíz y los diversos detalles de tarea, donde se conservan los valores de las pantallas de detalles.

## <a name="summary"></a>Resumen

En este artículo se presenta un tutorial que se ha explicado cómo usar la API de elementos de MonoTouch.Dialog. Tratan los pasos básicos para crear una aplicación de varias pantalla con destino maestro. D., incluida la forma de usar un `DialogViewController` y cómo agregar elementos y secciones para crear pantallas. Además, se ha mostrado cómo usar el destino maestro. D. junto con un `UINavigationController`.

## <a name="related-links"></a>Vínculos relacionados

- [MTDWalkthrough (ejemplo)](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [Presentación en pantalla: Miguel de Icaza crea una pantalla de inicio de sesión de iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Presentación en pantalla - crear fácilmente interfaces de usuario de iOS con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introducción a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial sobre la API de reflexión](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Tutorial del elemento JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Cuadro de diálogo de MonoTouch en Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de clase UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de clase UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
