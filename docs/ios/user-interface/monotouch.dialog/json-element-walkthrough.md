---
title: Usar JSON para crear una interfaz de usuario en Xamarin.iOS
description: MonoTouch.Dialog (MT D) incluye compatibilidad para la generación dinámica de interfaz de usuario a través de los datos JSON. En este tutorial, le mostraremos cómo usar un JSONElement para crear una interfaz de usuario de JSON que se incluyen con una aplicación, o cargado desde una dirección Url remota.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f9ba2cce1650260aa889e8282c091012ef8bbddc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790658"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Usar JSON para crear una interfaz de usuario en Xamarin.iOS

_MonoTouch.Dialog (MT D) incluye compatibilidad para la generación dinámica de interfaz de usuario a través de los datos JSON. En este tutorial, le mostraremos cómo usar un JSONElement para crear una interfaz de usuario de JSON que se incluyen con una aplicación, o cargado desde una dirección Url remota._

MONTE D admite crear interfaces de usuario declaradas en JSON. Cuando los elementos se declaran mediante JSON, monte D. crear automáticamente los elementos asociados de. El JSON puede cargarse desde un archivo local, un analizada `JsonObject` instancia o incluso una dirección Url remota.

MONTE D admite toda la gama de características que están disponibles en la API de elementos al usar JSON. Por ejemplo, la aplicación en la captura de pantalla siguiente se declara completamente mediante JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "Por ejemplo, la aplicación en esta captura de pantalla completamente se declara mediante JSON") ](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![ ] (json-element-walkthrough-images/01-load-from-file.png "por ejemplo, la aplicación en esta captura de pantalla se declare por completo mediante JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Vamos a volver a visitar el ejemplo de la [elementos API tutorial](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) tutorial, que muestra cómo agregar una pantalla de detalles de tarea mediante JSON.

## <a name="json-walkthrough"></a>Tutorial JSON

El ejemplo de este tutorial permite crear tareas. Cuando una tarea está seleccionada en la primera pantalla, se presenta una pantalla de detalles, como se muestra:

 [![](json-element-walkthrough-images/03-task-list.png "Cuando una tarea está seleccionada en la primera pantalla, se presenta una pantalla de detalles como se muestra")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Creación de JSON

En este ejemplo, vamos a cargar el JSON desde un archivo en el proyecto denominado `task.json`. MONTE D. espera el JSON para que coincida con una sintaxis que refleja la API de elementos. Al igual que con la API de elementos de código, al usar JSON, se declaran secciones y dentro de estas secciones se agregue elementos. Para declarar las secciones y los elementos en JSON, utilizamos las cadenas "secciones" y "elementos" respectivamente como las claves. Para cada elemento, se establece el tipo de elemento asociado con el `type` clave. Todas las demás propiedades de elementos se establece con el nombre de propiedad como la clave.

Por ejemplo, el siguiente JSON describe las secciones y los elementos para obtener los detalles de tarea:

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

Observe el JSON anterior incluye un identificador para cada elemento. Cualquier elemento puede incluir un identificador para hacer referencia a él en tiempo de ejecución. Veremos cómo se utiliza en un momento cuando se muestra cómo cargar el código JSON en código.

 <a name="Loading_the_JSON_in_Code" />


## <a name="loading-the-json-in-code"></a>Cargar el código JSON en código

Una vez que se ha definido el JSON, es necesario cargarlos en MT D utilizando la `JsonElement` clase. Suponiendo que un archivo con el JSON que hemos creado anteriormente se ha agregado al proyecto con el nombre sample.json y tiene una acción de compilación de contenido, cargar el `JsonElement` es tan sencillo como llamar a la siguiente línea de código:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Puesto que se ha agregado a petición cada vez que se crea una tarea, se podrá modificar el botón que se hace clic en el ejemplo anterior de la API de elementos como se indica a continuación:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

 <a name="Accessing_Elements_at_Runtime" />


## <a name="accessing-elements-at-runtime"></a>Obtener acceso a elementos en tiempo de ejecución

Recuerde que agregamos un identificador a ambos elementos cuando se declaró en el archivo JSON. Podemos usar la propiedad id para tener acceso a cada elemento en tiempo de ejecución para modificar sus propiedades en el código. Por ejemplo, el código siguiente hace referencia a los elementos de entrada y la fecha para establecer los valores del objeto de tarea:

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

 <a name="Loading_JSON_from_a_Url" />


## <a name="loading-json-from-a-url"></a>Carga JSON desde una dirección Url

MONTE D también admite la carga dinámica de JSON de una dirección Url externa pasando simplemente la dirección Url al constructor de la `JsonElement`. MONTE D. expandirá la jerarquía que se declara en JSON a petición al navegar entre pantallas. Por ejemplo, considere la posibilidad de un archivo JSON como siguiente situado en la raíz del servidor web local:

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

Se puede cargar mediante el `JsonElement` como en el código siguiente:

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

En tiempo de ejecución, se recuperará y analizar Monte el archivo D. cuando el usuario navega a la segunda vista, tal y como se muestra en la captura de pantalla siguiente:

 [![](json-element-walkthrough-images/04-json-web-example.png "El archivo se puede recuperar y analizar por MT D. cuando el usuario navega a la segunda vista")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo crear una mediante la interfaz con MT D. de JSON Ha explicado cómo carga JSON que se incluye en un archivo con la aplicación, así como desde una dirección Url remota. También muestra cómo obtener acceso a los elementos que se describen en JSON en tiempo de ejecución.


## <a name="related-links"></a>Vínculos relacionados

- [MTDJsonDemo (ejemplo)](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [Presentación en pantalla - Miguel de Icaza crea una pantalla de inicio de sesión de iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Presentación en pantalla - crear fácilmente interfaces de usuario de iOS con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introducción a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Tutorial de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Tutorial de API de reflexión](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Cuadro de diálogo de MonoTouch en Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicación de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referencia de clase UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referencia de clase UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
