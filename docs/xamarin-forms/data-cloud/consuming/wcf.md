---
title: Consumir un servicio Web de Windows Communication Foundation (WCF)
description: En este artículo se muestra cómo consumir un servicio de Protocolo Simple de acceso de objetos (SOAP) de WCF desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 7e8acc6e8aaf8b8e0e8cec7d5d0f3e28cf60073a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055600"
---
# <a name="consuming-a-windows-communication-foundation-wcf-web-service"></a>Consumir un servicio Web de Windows Communication Foundation (WCF)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)

_WCF es el marco unificado de Microsoft para crear aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transacciones e interoperables. En este artículo se muestra cómo consumir un servicio de Protocolo Simple de acceso de objetos (SOAP) de WCF desde una aplicación de Xamarin.Forms._

WCF describe un servicio con una variedad de contratos diferentes que se incluyen los siguientes:

- **Los contratos de datos** : definir las estructuras de datos que forman la base para el contenido dentro de un mensaje.
- **Contratos de mensaje** – redactar mensajes de los contratos de datos existente.
- **Contratos de error** : permitir que los errores de SOAP personalizados que se especifique.
- **Contratos de servicio** : especificar las operaciones que admiten servicios y los mensajes necesarios para interactuar con cada operación. También especifique ningún comportamiento de error personalizado que se puede asociar con las operaciones en cada servicio.

Existen diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero es importante comprender que WCF admite las mismas funcionalidades que proporciona ASMX: los mensajes SOAP a través de HTTP. Para obtener más información sobre cómo usar un servicio de ASMX, consulte [consumiendo servicios de Web de ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

En general, la plataforma Xamarin es compatible con el mismo subconjunto de lado cliente de WCF que se incluye con el tiempo de ejecución de Silverlight. Esto incluye las implementaciones más comunes de codificación y protocolo de WCF: texto con codificación de mensajes SOAP a través de HTTP utilizando el protocolo de transporte la `BasicHttpBinding` clase. Además, compatibilidad con WCF requiere el uso de herramientas solo está disponibles en un entorno de Windows para generar al proxy.

Puede encontrar instrucciones sobre cómo configurar el servicio de WCF en el archivo Léame que acompaña a la aplicación de ejemplo. Sin embargo, cuando se ejecuta la aplicación de ejemplo se conectará a un servicio WCF hospedado en Xamarin que proporciona acceso de solo lectura a los datos, como se muestra en la captura de pantalla siguiente:

![](wcf-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumo del servicio Web

El servicio WCF proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtener una lista de tareas pendientes|
|CreateTodoItem|Crear una nueva tarea pendiente|Un documento XML serializado TodoItem|
|EditTodoItem|Actualizar una tarea pendiente|Un documento XML serializado TodoItem|
|DeleteTodoItem|Eliminar una tarea pendiente|Un documento XML serializado TodoItem|

Para obtener más información sobre el modelo de datos utilizado en la aplicación, consulte [los datos de modelado](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> La aplicación de ejemplo consume el servicio WCF hospedado en Xamarin que proporciona acceso de solo lectura al servicio web. Por lo tanto, las operaciones que creación, actualización y eliminarán los datos no afectará a los datos consumidos en la aplicación. Sin embargo, está disponible en una versión del servicio ASMX hospedable el **TodoWCFService** carpeta en la aplicación de ejemplo que lo acompaña. Esta versión de los servicios WCF que permite completas hospedable crear, actualizar, leer y eliminar el acceso a los datos.

Un *proxy* debe generarse para consumir un servicio WCF, que permite que la aplicación para conectarse al servicio. El proxy se construye por consumir los metadatos del servicio que definen los métodos y la configuración del servicio asociado. Estos metadatos se muestran en forma de un documento de lenguaje de descripción de servicios Web (WSDL) que se genera el servicio web. El proxy puede compilarse mediante el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 para agregar una referencia de servicio para el servicio web en una biblioteca de .NET Standard. Una alternativa para crear al proxy con el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 consiste en utilizar ServiceModel Metadata Utility Tool (svcutil.exe). Para obtener más información, consulte [ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Las clases de proxy generadas proporcionan métodos para consumir los servicios web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este patrón, una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso de llamada mientras lleva a la operación asincrónica en un subproceso ThreadPool.

Para cada llamada a *BeginOperationName*, también debe llamar la aplicación *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio web sincrónico. Por ejemplo, el `EndGetTodoItems` método devuelve una colección de `TodoItem` instancias. El *EndOperationName* método también incluye un `IAsyncResult` parámetro que se debe establecer en la instancia devuelta por la llamada correspondiente a la *BeginOperationName* método.

Task Parallel Library (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM al encapsular las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporciona varias sobrecargas de los `TaskFactory.FromAsync` método.

Para obtener más información acerca de APM vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y la programación tradicional de .NET Framework asincrónicas](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

### <a name="creating-the-todoserviceclient-object"></a>Crear el objeto TodoServiceClient

La clase de proxy generado proporciona el `TodoServiceClient` (clase), que se usa para comunicarse con el servicio WCF a través de HTTP. Proporciona funcionalidad para invocar métodos de servicio web como operaciones asincrónicas de un URI identifican la instancia de servicio. Para obtener más información acerca de las operaciones asincrónicas, vea [información general de soporte técnico de Async](~/cross-platform/platform/async.md).

El `TodoServiceClient` instancia se declara en el nivel de clase para que se encuentra el objeto para siempre y cuando la aplicación necesita consumir el servicio WCF, tal como se muestra en el ejemplo de código siguiente:

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

El `TodoServiceClient` instancia está configurada con una dirección de extremo y la información de enlace. Un enlace se utiliza para especificar el transporte, codificación y detalles protocolares requeridos para que las aplicaciones y servicios para comunicarse entre sí. El `BasicHttpBinding` especifica que se enviarán los mensajes codificados en texto SOAP a través del protocolo de transporte HTTP. Especificación de una dirección de punto de conexión permite a la aplicación para conectarse a distintas instancias del servicio WCF, siempre que hay varias instancias publicadas.

Para obtener más información sobre la configuración de la referencia de servicio, consulte [configuración de la referencia de servicio](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="creating-data-transfer-objects"></a>Creación de objetos de transferencia de datos

La aplicación de ejemplo usa el `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` elemento en el servicio web debe convertirse primero en el proxy generado `TodoItem` tipo. Esto se consigue mediante la `ToWCFServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Este método simplemente crea un nuevo `TodoWCFService.TodoItem` de instancia y establece cada propiedad a la propiedad idéntica desde el `TodoItem` instancia.

De forma similar, cuando se recuperan datos desde el servicio web, se debe convertir desde el proxy generado `TodoItem` tipo a un `TodoItem` instancia. Esto se consigue con la `FromWCFServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
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

El `TodoServiceClient.BeginGetTodoItems` y `TodoServiceClient.EndGetTodoItems` métodos se usan para llamar a la `GetTodoItems` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndGetTodoItems` método una vez el `TodoServiceClient.BeginGetTodoItems` método se completa, con el `null` parámetro que indica que no hay datos se pasan a la `BeginGetTodoItems` delegar. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El `TodoServiceClient.EndGetTodoItems` método devuelve un `ObservableCollection` de `TodoWCFService.TodoItem` instancias, que, a continuación, se convierte en un `List` de `TodoItem` instancias para su presentación.

### <a name="creating-data"></a>Creación de datos

El `TodoServiceClient.BeginCreateTodoItem` y `TodoServiceClient.EndCreateTodoItem` métodos se usan para llamar a la `CreateTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndCreateTodoItem` método una vez el `TodoServiceClient.BeginCreateTodoItem` método se completa, con el `todoItem` parámetro que se va a los datos que se pasan a la `BeginCreateTodoItem` delegado para especificar el `TodoItem` que va a crear el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `FaultException` si se produce un error al crear el `TodoItem`, que se controla mediante la aplicación.

### <a name="updating-data"></a>Actualizar datos

El `TodoServiceClient.BeginEditTodoItem` y `TodoServiceClient.EndEditTodoItem` métodos se usan para llamar a la `EditTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndEditTodoItem` método una vez el `TodoServiceClient.BeginCreateTodoItem` método se completa, con el `todoItem` parámetro que se va a los datos que se pasan a la `BeginEditTodoItem` delegado para especificar el `TodoItem` actualizarse por el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `FaultException` si se produce un error al buscar o actualizar el `TodoItem`, que se controla mediante la aplicación.

### <a name="deleting-data"></a>Eliminar datos

El `TodoServiceClient.BeginDeleteTodoItem` y `TodoServiceClient.EndDeleteTodoItem` métodos se usan para llamar a la `DeleteTodoItem` operación proporcionada por el servicio web. Estos métodos asincrónicos se encapsulan en un `Task` de objeto, como se muestra en el ejemplo de código siguiente:

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

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndDeleteTodoItem` método una vez el `TodoServiceClient.BeginDeleteTodoItem` método se completa, con el `id` parámetro que se va a los datos que se pasan a la `BeginDeleteTodoItem` delegado para especificar el `TodoItem` va a eliminar el servicio web. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El servicio web inicia una `FaultException` si se produce un error al buscar o eliminar el `TodoItem`, que se controla mediante la aplicación.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo consumir un servicio SOAP de WCF desde una aplicación de Xamarin.Forms. En general, la plataforma Xamarin es compatible con el mismo subconjunto de lado cliente de WCF que se incluye con el tiempo de ejecución de Silverlight. Esto incluye las implementaciones más comunes de codificación y protocolo de WCF: texto con codificación de mensajes SOAP a través de HTTP utilizando el protocolo de transporte la `BasicHttpBinding` clase. Además, compatibilidad con WCF requiere el uso de herramientas solo está disponibles en un entorno de Windows para generar al proxy.


## <a name="related-links"></a>Vínculos relacionados

- [TodoWCF (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)
