---
title: Consumir un servicio Web ASP.NET (ASMX)
description: En este artículo se muestra cómo consumir un servicio de ASMX SOAP desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1fa2fee36619a2a443d84b861b2473a1f616ed0e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055146"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>Consumir un servicio Web ASP.NET (ASMX)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX proporciona la capacidad de crear servicios web que envían mensajes mediante el protocolo Simple de acceso de objetos (SOAP). SOAP es un protocolo independiente de la plataforma y lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio de ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Sólo necesitan entender cómo enviar y recibir mensajes SOAP. En este artículo se muestra cómo consumir un servicio de ASMX SOAP desde una aplicación de Xamarin.Forms._

Un mensaje SOAP es un documento XML que contiene los siguientes elementos:

- Un elemento raíz denominado *sobres* que identifica el documento XML como un mensaje SOAP.
- Opcional *encabezado* elemento que contiene información específica de la aplicación, como datos de autenticación. Si el *encabezado* elemento está presente debe ser el primer elemento secundario de la *sobres* elemento.
- Obligatoria *cuerpo* elemento que contiene el mensaje SOAP, diseñado para el destinatario.
- Opcional *error* elemento que se usa para indicar los mensajes de error. Si el *error* elemento está presente, debe ser un elemento secundario de la *cuerpo* elemento.

SOAP puede operar en muchos protocolos de transporte, incluidos HTTP, SMTP, TCP y UDP. Sin embargo, un servicio de ASMX solo puede funcionar a través de HTTP. La plataforma Xamarin es compatible con las implementaciones estándar de SOAP 1.1 a través de HTTP, y esto incluye compatibilidad con muchas de las configuraciones estándar de servicio ASMX.

Puede encontrar instrucciones sobre cómo configurar el servicio ASMX en el archivo Léame que acompaña a la aplicación de ejemplo. Sin embargo, cuando se ejecuta la aplicación de ejemplo, se conectará a un servicio ASMX alojado en Xamarin que proporciona acceso de solo lectura a los datos, como se muestra en la captura de pantalla siguiente:

![](asmx-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumo del servicio Web

El servicio ASMX proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtener una lista de tareas pendientes|
|CreateTodoItem|Crear una nueva tarea pendiente|Un documento XML serializado TodoItem|
|EditTodoItem|Actualizar una tarea pendiente|Un documento XML serializado TodoItem|
|DeleteTodoItem|Eliminar una tarea pendiente|Un documento XML serializado TodoItem|

Para obtener más información sobre el modelo de datos utilizado en la aplicación, consulte [los datos de modelado](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> La aplicación de ejemplo consume el servicio ASMX alojado en Xamarin que proporciona acceso de solo lectura al servicio web. Por lo tanto, las operaciones que creación, actualización y eliminarán los datos no afectará a los datos consumidos en la aplicación. Sin embargo, está disponible en una versión del servicio ASMX hospedable el **TodoASMXService** carpeta en la aplicación de ejemplo que lo acompaña. Esta versión de los permisos de servicio ASMX completas hospedable crear, actualizar, leer y eliminar el acceso a los datos.

Un *proxy* debe generarse para consumir el servicio ASMX, que permite que la aplicación para conectarse al servicio. El proxy se construye por consumir los metadatos del servicio que define los métodos y la configuración del servicio asociado. Estos metadatos se muestran en forma de un documento de lenguaje de descripción de servicios Web (WSDL) que se genera el servicio web. El proxy se genera al agregar una referencia web para el servicio web a los proyectos específicos de la plataforma.

Las clases de proxy generadas proporcionan métodos para consumir el servicio web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este patrón de una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso de llamada mientras lleva a la operación asincrónica en un subproceso ThreadPool.

Para cada llamada a *BeginOperationName*, también debe llamar la aplicación *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio web sincrónico. Por ejemplo, el `EndGetTodoItems` método devuelve una colección de `TodoItem` instancias. El *EndOperationName* método también incluye un `IAsyncResult` parámetro que se debe establecer en la instancia devuelta por la llamada correspondiente a la *BeginOperationName* método.

Task Parallel Library (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM al encapsular las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporciona varias sobrecargas de los `TaskFactory.FromAsync` método.

Para obtener más información acerca de APM vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y la programación tradicional de .NET Framework asincrónicas](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

### <a name="creating-the-todoservice-object"></a>Crear el objeto TodoService

La clase de proxy generado proporciona el `TodoService` (clase), que se usa para comunicarse con el servicio ASMX a través de HTTP. Proporciona funcionalidad para invocar métodos de servicio web como operaciones asincrónicas de un URI identifican la instancia de servicio. Para obtener más información acerca de las operaciones asincrónicas, vea [información general de soporte técnico de Async](~/cross-platform/platform/async.md).

El `TodoService` instancia se declara en el nivel de clase para que se encuentra el objeto para siempre y cuando la aplicación necesita consumir el servicio ASMX, tal como se muestra en el ejemplo de código siguiente:

```csharp
public class SoapService : ISoapService
{
  ASMXService.TodoService asmxService;
  ...

  public SoapService ()
  {
    asmxService = new ASMXService.TodoService (Constants.SoapUrl);
  }
  ...
}
```

El `TodoService` constructor toma un parámetro de cadena opcional que especifica la dirección URL de la instancia de servicio ASMX. Esto permite que la aplicación para conectarse a distintas instancias del servicio ASMX, siempre que hay varias instancias publicadas.

### <a name="creating-data-transfer-objects"></a>Creación de objetos de transferencia de datos

La aplicación de ejemplo usa el `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` elemento en el servicio web debe convertirse primero en el proxy generado `TodoItem` tipo. Esto se consigue mediante la `ToASMXServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
  return new ASMXService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Este método simplemente crea un nuevo `ASMService.TodoItem` de instancia y establece cada propiedad a la propiedad idéntica desde el `TodoItem` instancia.

De forma similar, cuando se recuperan datos desde el servicio web, se debe convertir desde el proxy generado `TodoItem` tipo a un `TodoItem` instancia. Esto se consigue con la `FromASMXServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
{
  return new TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Este método simplemente recupera los datos desde el proxy generado `TodoItem` escriba y lo establece en recién creado `TodoItem` instancia.

### <a name="retrieving-data"></a>Recuperar datos

El `TodoService.BeginGetTodoItems` y `TodoService.EndGetTodoItems` métodos se usan para llamar a la `GetTodoItems` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromASMXServiceTodoItem (item));
  }
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoService.EndGetTodoItems` método una vez el `TodoService.BeginGetTodoItems` método se completa, con el `null` parámetro que indica que no hay datos se pasan a la `BeginGetTodoItems` delegar. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El `TodoService.EndGetTodoItems` método devuelve una matriz de `ASMXService.TodoItem` instancias, que, a continuación, se convierte en un `List` de `TodoItem` instancias para su presentación.

### <a name="creating-data"></a>Creación de datos

El `TodoService.BeginCreateTodoItem` y `TodoService.EndCreateTodoItem` métodos se usan para llamar a la `CreateTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoService.EndCreateTodoItem` método una vez el `TodoService.BeginCreateTodoItem` método se completa, con el `todoItem` parámetro que se va a los datos que se pasan a la `BeginCreateTodoItem` delegado para especificar el `TodoItem` que va a crear el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `SoapException` si se produce un error al crear el `TodoItem`, que se controla mediante la aplicación.

### <a name="updating-data"></a>Actualizar datos

El `TodoService.BeginEditTodoItem` y `TodoService.EndEditTodoItem` métodos se usan para llamar a la `EditTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoService.EndEditTodoItem` método una vez el `TodoService.BeginCreateTodoItem` método se completa, con el `todoItem` parámetro que se va a los datos que se pasan a la `BeginEditTodoItem` delegado para especificar el `TodoItem` actualizarse por el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `SoapException` si se produce un error al buscar o actualizar el `TodoItem`, que se controla mediante la aplicación.

### <a name="deleting-data"></a>Eliminar datos

El `TodoService.BeginDeleteTodoItem` y `TodoService.EndDeleteTodoItem` métodos se usan para llamar a la `DeleteTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoService.EndDeleteTodoItem` método una vez el `TodoService.BeginDeleteTodoItem` método se completa, con el `id` parámetro que se va a los datos que se pasan a la `BeginDeleteTodoItem` delegado para especificar el `TodoItem` va a eliminar el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `SoapException` si se produce un error al buscar o eliminar el `TodoItem`, que se controla mediante la aplicación.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo consumir un servicio web ASMX desde una aplicación de Xamarin.Forms. ASMX proporciona la capacidad de crear servicios web que envían mensajes a través de HTTP utilizando SOAP. Los consumidores de un servicio de ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación usado para implementar el servicio. Sólo necesitan entender cómo enviar y recibir mensajes SOAP.


## <a name="related-links"></a>Vínculos relacionados

- [TodoASMX (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
