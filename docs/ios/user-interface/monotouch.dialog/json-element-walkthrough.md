---
title: Uso de JSON para crear una interfaz de usuario en Xamarin.iOS
description: MonoTouch.Dialog (destino maestro. (D) incluye compatibilidad para la generación dinámica de la interfaz de usuario a través de los datos JSON. En este tutorial, le guiaremos a través de cómo usar un JSONElement para crear una interfaz de usuario de JSON que se incluyen con una aplicación, o cargado desde una dirección Url remota.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 07bcbfe046fd689e08eed4e64495b56d083ceeb8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304468"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Uso de JSON para crear una interfaz de usuario en Xamarin.iOS

_MonoTouch.Dialog (destino maestro. (D) incluye compatibilidad para la generación dinámica de la interfaz de usuario a través de los datos JSON. En este tutorial, le guiaremos a través de cómo usar un JSONElement para crear una interfaz de usuario de JSON que se incluyen con una aplicación, o cargado desde una dirección Url remota._

DESTINO MAESTRO. D admite crear interfaces de usuario declaradas en JSON. Cuando los elementos se declaran mediante JSON, MT. D. crear automáticamente los elementos asociados para usted. El archivo JSON puede cargarse desde un archivo local, un analizada `JsonObject` instancia, o incluso una dirección Url remota.

DESTINO MAESTRO. D admite la gama completa de características que están disponibles en la API de elementos cuando se usa JSON. Por ejemplo, la aplicación en la captura de pantalla siguiente se declara por completo mediante JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "Por ejemplo, la aplicación en esta captura de pantalla se declara por completo mediante JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox) [![](json-element-walkthrough-images/01-load-from-file.png "por ejemplo, la aplicación en esta captura de pantalla se declara por completo mediante JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Retomemos el ejemplo desde el [tutorial sobre la API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) tutorial, que muestra cómo agregar una pantalla de detalles de tarea mediante JSON.

## <a name="setting-up-mtd"></a>Configuración de destino maestro. D.

DESTINO MAESTRO. D. se distribuye con Xamarin.iOS. Para ello, haga doble clic en el **referencias** nodo de un Xamarin.iOS del proyecto en Visual Studio 2017 o Visual Studio para Mac y agregue una referencia a la **MonoTouch.Dialog 1** ensamblado. A continuación, agregue `using MonoTouch.Dialog` instrucciones en el origen de código según sea necesario.

## <a name="json-walkthrough"></a>Tutorial JSON

El ejemplo de este tutorial permite crear tareas. Cuando se selecciona una tarea en la primera pantalla, se presenta una pantalla de detalles como se muestra:

 [![](json-element-walkthrough-images/03-task-list.png "Cuando se selecciona una tarea en la primera pantalla, se presenta una pantalla de detalles como se muestra")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Crear el archivo JSON

En este ejemplo, vamos a cargar el archivo JSON desde un archivo en el proyecto denominado `task.json`. DESTINO MAESTRO. D. espera el JSON que se ajuste a una sintaxis que refleja la API de elementos. Al igual que con la API de elementos de código, cuando se usa JSON, declaramos las secciones y dentro de esas secciones se agregue elementos. Para declarar las secciones y los elementos de JSON, usamos las cadenas "secciones" y "elementos" respectivamente como las claves. Para cada elemento, se establece el tipo del elemento asociado con el `type` clave. Cada propiedad de otros elementos se establece con el nombre de propiedad como clave.

Por ejemplo, el siguiente código JSON describe las secciones y los elementos de los detalles de tarea:

```csharp
{
    "title": "Task",
    "sections": [
        {
          "elements" : [
            {
                "id" : "task-description",
                "type": "entry",
                "placeholder": "Enter task description"
            },
            {
                "id" : "task-duedate",
                "type": "date",
                "caption": "Due Date",
                "value": "00:00"
            }
         ]
        }
    ]
  }
```

Tenga en cuenta el código JSON anterior incluye un identificador para cada elemento. Cualquier elemento puede incluir un identificador para hacer referencia a él en tiempo de ejecución. Veremos cómo se utiliza en un momento cuando se muestra cómo cargar el archivo JSON en el código.

## <a name="loading-the-json-in-code"></a>Cargando el archivo JSON en el código

Una vez que se ha definido el JSON, es necesario cargarlos en el destino maestro. D. utilizar el `JsonElement` clase. Suponiendo que un archivo con el código JSON que hemos creado anteriormente se ha agregado al proyecto con el nombre de sample.json y tiene una acción de compilación de contenido, cargar el `JsonElement` es tan sencillo como llamar a la siguiente línea de código:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Puesto que estamos agregando a petición cada vez que se crea una tarea, podemos modificamos el botón que se hace clic en el ejemplo anterior de la API de elementos como sigue:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>Acceso a los elementos en tiempo de ejecución

Recuerde que agregamos un identificador a ambos elementos cuando se ha declarado en el archivo JSON. Podemos usar la propiedad id para tener acceso a cada elemento en tiempo de ejecución para modificar sus propiedades en el código. Por ejemplo, el código siguiente hace referencia a los elementos de entrada y la fecha para establecer los valores desde el objeto de tarea:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        taskElement.Caption = task.Name;

        var description = taskElement ["task-description"] as EntryElement;

        if (description != null) {
                description.Caption = task.Name;
                description.Value = task.Description;       
        }

        var duedate = taskElement ["task-duedate"] as DateElement;

        if (duedate != null) {                
                duedate.DateValue = task.DueDate;
        }
        _rootElement [0].Add (taskElement);
};
```

## <a name="loading-json-from-a-url"></a>Cargar JSON de una dirección url

DESTINO MAESTRO. D también admite la carga dinámica de JSON de una dirección Url externa pasando simplemente la dirección Url al constructor de la `JsonElement`. DESTINO MAESTRO. D. expandirá la jerarquía que se declara en JSON a petición a medida que navega entre pantallas. Por ejemplo, considere la posibilidad de un archivo JSON, como la siguiente en la raíz del servidor web local:

```csharp
{
    "type": "root",
    "title": "home",
    "sections": [
       {
         "header": "Nested view!",
         "elements": [
           {
             "type": "boolean",
             "caption": "Just a boolean",
             "id": "first-boolean",
             "value": false
           },
           {
             "type": "string",
             "caption": "Welcome to the nested controller"
           }
         ]
       }
     ]
}
```

Se puede cargar mediante el `JsonElement` como se muestra en el código siguiente:

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

En tiempo de ejecución, el archivo se se recuperan y analizan mediante el destino maestro. D. cuando el usuario navega a la segunda vista, como se muestra en la captura de pantalla siguiente:

 [![](json-element-walkthrough-images/04-json-web-example.png "El archivo se recuperará y analizado por el destino maestro. D. cuando el usuario navega a la segunda vista")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo crear una mediante la interfaz con el destino maestro. D. de JSON. Se ha mostrado cómo cargar JSON incluido en un archivo con la aplicación, así como desde una dirección Url remota. También se ha explicado cómo tener acceso a los elementos descritos en JSON en tiempo de ejecución.

## <a name="related-links"></a>Vínculos relacionados

- [MTDJsonDemo (sample)](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [Presentación en pantalla: Miguel de Icaza crea una pantalla de inicio de sesión de iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Presentación en pantalla - crear fácilmente interfaces de usuario de iOS con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introducción a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial sobre la API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial sobre la API de reflexión](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Cuadro de diálogo de MonoTouch en Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de clase UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de clase UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
