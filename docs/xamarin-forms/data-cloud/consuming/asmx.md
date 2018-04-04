---
title: Consumir un servicio Web ASP.NET (ASMX)
description: ASMX proporciona la capacidad para crear servicios web que envían mensajes mediante el protocolo Simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma e independientes del lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación utilizado para implementar el servicio. Solo necesitan saber cómo enviar y recibir mensajes SOAP. Este artículo demuestra cómo consumir un servicio de ASMX SOAP desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: c45f0de039abc3f98b7c269f183e2883a495910b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>Consumir un servicio Web ASP.NET (ASMX)

_ASMX proporciona la capacidad para crear servicios web que envían mensajes mediante el protocolo Simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma e independientes del lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación utilizado para implementar el servicio. Solo necesitan saber cómo enviar y recibir mensajes SOAP. Este artículo demuestra cómo consumir un servicio de ASMX SOAP desde una aplicación de Xamarin.Forms._

Un mensaje SOAP es un documento XML que contiene los elementos siguientes:

- Un elemento raíz denominado *sobres* que identifica el documento XML como un mensaje SOAP.
- Opcional *encabezado* elemento que contiene información específica de la aplicación, como datos de autenticación. Si el *encabezado* elemento está presente debe ser el primer elemento secundario de la *sobres* elemento.
- Obligatoria *cuerpo* elemento que contiene el mensaje SOAP, diseñado para el destinatario.
- Opcional *error* elemento que se usa para indicar los mensajes de error. Si el *error* elemento está presente, debe ser un elemento secundario de la *cuerpo* elemento.

SOAP puede operar en muchos de los protocolos de transporte, como HTTP, SMTP, TCP y UDP. Sin embargo, un servicio de ASMX solo puede funcionar a través de HTTP. La plataforma Xamarin es compatible con las implementaciones estándar de SOAP 1.1 a través de HTTP, y esto incluye la compatibilidad para muchas de las configuraciones estándar de servicio ASMX.

Las instrucciones sobre cómo configurar el servicio ASMX se pueden encontrar en el archivo Léame que acompaña a la aplicación de ejemplo. Sin embargo, cuando se ejecuta la aplicación de ejemplo, se conectará a un servicio hospedado en Xamarin ASMX que proporciona acceso de solo lectura a los datos, como se muestra en la captura de pantalla siguiente:

![](asmx-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se pueden alta ATS fuera de si no es posible utilizar la `HTTPS` del protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizar el servicio Web

El servicio ASMX proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtener una lista de elementos pendientes|
|CreateTodoItem|Crear un nuevo elemento de tarea|Un documento XML serializado TodoItem|
|EditTodoItem|Actualizar un elemento de tarea|Un documento XML serializado TodoItem|
|DeleteTodoItem|Eliminar un elemento de tarea|Un documento XML serializado TodoItem|

Para obtener más información acerca del modelo de datos usado en la aplicación, consulte [modelar los datos](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> La aplicación de ejemplo consume el servicio hospedado en Xamarin ASMX que proporciona acceso de solo lectura para el servicio web. Por lo tanto, las operaciones que crean, actualización y eliminan datos no modificará los datos que se consume en la aplicación. Sin embargo, está disponible en una versión del servicio ASMX hospedable el **TodoASMXService** carpeta en la aplicación de ejemplo que lo acompaña. Esta versión de los completa que permite el servicio ASMX hospedable crear, actualizar, leer y eliminar el acceso a los datos.

A *proxy* debe generarse para consumir el servicio ASMX, que permite que la aplicación para conectarse al servicio. El proxy se construye por consumo metadatos de servicio que definen los métodos y la configuración del servicio asociado. Estos metadatos se muestran en forma de un documento de lenguaje de descripción de servicios Web (WSDL) que se genera mediante el servicio web. El proxy se genera al agregar una referencia web para el servicio web a los proyectos específicos de la plataforma.

Las clases de proxy generado proporcionan métodos para consumir el servicio web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este patrón de una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que empiezan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso que realiza la llamada, mientras la operación asincrónica tiene lugar en un subproceso del grupo.

Para cada llamada a *BeginOperationName*, la aplicación debe llamar a *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es del mismo tipo devuelto por el método de servicio web sincrónico. Por ejemplo, el `EndGetTodoItems` método devuelve una colección de `TodoItem` instancias. El *EndOperationName* método también incluye una `IAsyncResult` parámetro que se debe establecer en la instancia devuelta por la llamada correspondiente a la *BeginOperationName* método.

La biblioteca (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM encapsulando las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporcionan varias sobrecargas de la `TaskFactory.FromAsync` método.

Para obtener más información acerca de APM, vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y .NET Framework programación asincrónica tradicional de](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

### <a name="creating-the-todoservice-object"></a>Crear el objeto TodoService

La clase de proxy generado proporciona el `TodoService` (clase), que se utiliza para comunicarse con el servicio ASMX a través de HTTP. Proporciona funcionalidad para invocar métodos de servicio web como operaciones asincrónicas de un URI identifican la instancia de servicio. Para obtener más información acerca de las operaciones asincrónicas, vea [Introducción al soporte técnico Async](~/cross-platform/platform/async.md).

El `TodoService` instancia se declara en el nivel de clase para que se encuentra el objeto para siempre y cuando la aplicación debe utilizar el servicio ASMX, tal como se muestra en el ejemplo de código siguiente:

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

El `TodoService` constructor toma un parámetro de cadena opcional que especifica la dirección URL de la instancia de servicio ASMX. Esto permite la aplicación para conectarse a distintas instancias del servicio ASMX, siempre que hay varias instancias publicadas.

### <a name="creating-data-transfer-objects"></a>Crear objetos de transferencia de datos

La aplicación de ejemplo utiliza la `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` elemento en el servicio web debe convertirse primero en el proxy generado `TodoItem` tipo. Esto se logra mediante la `ToASMXServiceTodoItem` método, tal como se muestra en el ejemplo de código siguiente:

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

Este método simplemente crea un nuevo `ASMService.TodoItem` una instancia y establece cada propiedad a la propiedad idéntica desde el `TodoItem` instancia.

De forma similar, cuando se recuperan datos del servicio web, se debe convertir desde el proxy generado `TodoItem` tipo a un `TodoItem` instancia. Esto se consigue con el `FromASMXServiceTodoItem` método, tal como se muestra en el ejemplo de código siguiente:

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

Este método simplemente recupera los datos desde el servidor proxy generado `TodoItem` escriba y establece recién creado `TodoItem` instancia.

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoService.EndGetTodoItems` una vez al método el `TodoService.BeginGetTodoItems` método se completa, con el `null` parámetro que indica que no hay datos se pasan a la `BeginGetTodoItems` delegar. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El `TodoService.EndGetTodoItems` método devuelve una matriz de `ASMXService.TodoItem` instancias, que, a continuación, se convierte en una `List` de `TodoItem` instancias para su presentación.

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoService.EndCreateTodoItem` una vez al método el `TodoService.BeginCreateTodoItem` método finaliza, con el `todoItem` parámetro que se va a los datos que se pasan a la `BeginCreateTodoItem` delegado para especificar el `TodoItem` para ser creado por el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoService.EndEditTodoItem` una vez al método el `TodoService.BeginCreateTodoItem` método finaliza, con el `todoItem` parámetro que se va a los datos que se pasan a la `BeginEditTodoItem` delegado para especificar el `TodoItem` actualizarse por el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoService.EndDeleteTodoItem` una vez al método el `TodoService.BeginDeleteTodoItem` método finaliza, con el `id` parámetro que se va a los datos que se pasan a la `BeginDeleteTodoItem` delegado para especificar el `TodoItem` eliminarse mediante el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `SoapException` si se produce un error al buscar o eliminar el `TodoItem`, que se controla mediante la aplicación.

## <a name="summary"></a>Resumen

Este artículo muestra cómo consumir un servicio web ASMX desde una aplicación de Xamarin.Forms. ASMX proporciona la capacidad para crear servicios web que envían mensajes a través de HTTP utilizando SOAP. Los consumidores de un servicio ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación utilizado para implementar el servicio. Solo necesitan saber cómo enviar y recibir mensajes SOAP.


## <a name="related-links"></a>Vínculos relacionados

- [TodoASMX (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
