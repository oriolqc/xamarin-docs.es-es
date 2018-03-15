---
title: "Descripción del ejemplo"
description: "En este tema se ofrece un tutorial de la aplicación de ejemplo de Xamarin.Forms que muestra cómo comunicar con los servicios web diferentes. Mientras que cada servicio web utiliza una aplicación de ejemplo independiente, son funciones similares y compartir clases comunes."
ms.topic: article
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: ab44cb7a065164bb5b7501ac63bd0321b612b7ca
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/15/2018
---
# <a name="understanding-the-sample"></a>Descripción del ejemplo

_En este tema se ofrece un tutorial de la aplicación de ejemplo de Xamarin.Forms que muestra cómo comunicar con los servicios web diferentes. Mientras que cada servicio web utiliza una aplicación de ejemplo independiente, son funciones similares y compartir clases comunes._

La aplicación de lista de tareas pendientes de ejemplo que se describe a continuación se utiliza para mostrar cómo obtener acceso a diferentes tipos de servidores backend de servicio web con Xamarin.Forms. Proporciona funcionalidad para:

- Ver una lista de tareas.
- Agregar, editar y eliminar tareas.
- Establece el estado de una tarea a 'done'.
- Hablar campos de nombre y notas de la tarea.

En todos los casos, las tareas se almacenan en un back-end que se accede a través de un servicio web.

Cuando se inicia la aplicación, se muestra una página que muestra las tareas que se recupera del servicio web y permite al usuario crear una nueva tarea. Al hacer clic en una tarea desplaza a la aplicación a otra página donde la tarea puede editar, guardar, eliminar y se habla. A continuación se muestra la aplicación final:

![](walkthrough-images/app-example-1.png "Aplicación de lista de tareas, la primera página")
![](walkthrough-images/app-example-2.png "aplicación de lista de tareas - segunda página")

Todos los temas de esta guía proporcionan un vínculo de descarga a un *diferentes* versión de la aplicación que muestra un tipo específico de servicio de web back-end. Descargar el código de ejemplo correspondiente en la página relativa al estilo de cada servicio web.

## <a name="understanding-the-application-anatomy"></a>Descripción de la Anatomía de aplicación

El proyecto PCL para cada aplicación de ejemplo consta de tres carpetas principales:

|Carpeta|Propósito|
|--- |--- |
|Datos|Contiene las clases e interfaces que se usan para administrar elementos de datos y comunicarse con el servicio web. Como mínimo, esto incluye la `TodoItemManager` (clase), que se expone a través de una propiedad en la `App` clase para llamar a operaciones del servicio web.|
|Modelos|Contiene las clases del modelo de datos para la aplicación. Como mínimo, esto incluye el `TodoItem` (clase), que modela un único elemento de datos que usa la aplicación. La carpeta también puede incluir cualquier clases adicionales que se usa para modelar los datos de usuario.|
|Vistas|Contiene las páginas de la aplicación. Esto suele estar compuesto por el `TodoListPage` y `TodoItemPage` las clases y las clases adicionales que se utiliza con fines de autenticación.|

El proyecto PCL para cada aplicación también consta de un número de archivos importantes:

|Archivo|Propósito|
|--- |--- |
|Constants.cs|El `Constants` (clase), que especifica las constantes utilizadas por la aplicación para comunicarse con el servicio web. Estas constantes requieren actualizar para tener acceso a su servicio de backend personal creado en un proveedor.|
|ITextToSpeech.cs|El `ITextToSpeech` interfaz, que especifica que la `Speak` método debe ser proporcionado por las clases implementadoras.|
|Todo.cs|El `App` clase que es responsable de crear instancias de ambos la primera página que se mostrará de la aplicación en cada plataforma, y la `TodoItemManager` clase que se utiliza para invocar operaciones del servicio web.|

### <a name="viewing-pages"></a>Ver las páginas

La mayoría de las aplicaciones de ejemplo contienen al menos dos páginas:

- **TodoListPage** : esta página muestra una lista de `TodoItem` instancias y un icono de marca si la `TodoItem.Done` propiedad es `true`. Al hacer clic en un elemento se desplaza a la `TodoItemPage`. Además, se pueden crear elementos nuevos, haga clic en el  *+*  símbolos.
- **TodoItemPage** : esta página muestra los detalles para seleccionado `TodoItem`y le permite editar, guardar, eliminar y hablar.

Además, algunas aplicaciones de ejemplo contienen páginas adicionales que se usan para administrar el proceso de autenticación de usuario.

### <a name="modeling-the-data"></a>Modelar los datos

Cada aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos que se muestran y se envía al servicio web para su almacenamiento. En el ejemplo de código siguiente se muestra la clase `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

El `ID` propiedad se utiliza para identificar de forma única cada `TodoItem` una instancia y se utiliza por cada servicio web para identificar los datos que se va a actualizar o eliminar.

### <a name="invoking-web-service-operations"></a>Invocar operaciones del servicio Web

Se tiene acceso a las operaciones del servicio Web a través de la `TodoItemManager` clase y una instancia de la clase pueden tener acceso mediante el `App.TodoManager` propiedad. La `TodoItemManager` clase proporciona los métodos siguientes para invocar operaciones del servicio web:

- **GetTasksAsync** : este método se usa para rellenar el `ListView` en controlar la `TodoListPage` con el `TodoItem` instancias de recuperaron del servicio web.
- **SaveTaskAsync** : este método se utiliza para crear o actualizar un `TodoItem` instancia del servicio web.
- **DeleteTaskAsync** : este método se usa para eliminar un `TodoItem` instancia del servicio web.

Además, algunas aplicaciones de ejemplo contienen métodos adicionales en el `TodoItemManager` (clase), que se usan para administrar el proceso de autenticación de usuario.

En lugar de invocar las operaciones del servicio web directamente, la `TodoItemManager` métodos invocan métodos en una clase dependiente que se aplica en el `TodoItemManager` constructor. Por ejemplo, una aplicación de ejemplo inserta la `SimpleDBStorage` clase en el `TodoItemManager` constructor para proporcionar la implementación que invoca las operaciones contra el servicio de SimpleDB de Amazon.

### <a name="translating-text-to-speech"></a>Convertir texto a voz

La mayoría de las aplicaciones de ejemplo contienen una funcionalidad de texto a voz (TTS) para hablar de los valores de la `TodoItem.Name` y `TodoItem.Notes` propiedades. Esto se logra mediante la `OnSpeakActivated` controlador de eventos en el `TodoItemPage` de la clase, como se muestra en el ejemplo de código siguiente:

```csharp
void OnSpeakActivated (object sender, EventArgs e)
{
    var todoItem = (TodoItem)BindingContext;
    App.Speech.Speak(todoItem.Name + " " + todoItem.Notes);
}
```

Este método invoca simplemente la `Speak` método que se implementa mediante un específico de la plataforma `Speech` clase. Cada `Speech` la clase implementa la `ITextToSpeech` interfaz, y el código de inicio específico de la plataforma crea una instancia de la `Speech` clase que se puede tener acceso a través de la `App.Speech` propiedad.

## <a name="summary"></a>Resumen

En este tema se proporciona un tutorial de la aplicación de ejemplo de Xamarin.Forms que se utiliza para mostrar cómo comunicarse con servicios web diferentes. Mientras que cada servicio web utiliza una aplicación de ejemplo independiente, están basadas en la misma interfaz de usuario y la lógica empresarial como se describió anteriormente: solo el mecanismo de almacenamiento de los datos de web service es diferente.


## <a name="related-links"></a>Vínculos relacionados

- [Versión ASMX (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX)
- [Versión WCF (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF)
- [Versión REST (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST)
- [Versión de Azure (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure)
- [Versión de Amazon Web Services (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS)
