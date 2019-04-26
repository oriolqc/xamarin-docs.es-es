---
title: Consumir un servicio Web ASP.NET (ASMX)
description: En este artículo se muestra cómo consumir un servicio de ASMX SOAP desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 71fb2b4742a66a560541febc9dbe87aed503da4c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328650"
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

Este ejemplo incluye las aplicaciones móviles que se ejecutan en dispositivos físicos o emulados y un servicio de ASMX que proporciona métodos para obtener, agregar, editar y eliminar datos. Cuando se ejecutan las aplicaciones móviles, se conectan al servicio ASMX alojado localmente como se muestra en la captura de pantalla siguiente:

![](asmx-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumir el servicio web

El servicio ASMX proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtener una lista de tareas pendientes|
|CreateTodoItem|Crear una nueva tarea pendiente|Un documento XML serializado TodoItem|
|EditTodoItem|Actualizar una tarea pendiente|Un documento XML serializado TodoItem|
|DeleteTodoItem|Eliminar una tarea pendiente|Un documento XML serializado TodoItem|

Para obtener más información sobre el modelo de datos utilizado en la aplicación, consulte [los datos de modelado](~/xamarin-forms/data-cloud/walkthrough.md).

## <a name="create-the-todoservice-proxy"></a>Crear al proxy TodoService

Una clase de proxy, llamada `TodoService`, extiende `SoapHttpClientProtocol` y proporciona métodos para comunicarse con el servicio ASMX a través de HTTP. El proxy se genera mediante la adición de una referencia web a cada proyecto específico de plataforma de 2019 de Visual Studio o Visual Studio 2017. La referencia web genera métodos y eventos para cada acción definida en el documento de lenguaje de descripción de servicios Web (WSDL) del servicio.

Por ejemplo, el `GetTodoItems` acción de servicio da como resultado un `GetTodoItemsAsync` método y un `GetTodoItemsCompleted` eventos en el servidor proxy. El método generado tiene un tipo de valor devuelto void e invoca el `GetTodoItems` acción en el elemento primario `SoapHttpClientProtocol` clase. Cuando el método invocado recibe una respuesta del servicio, se activa el `GetTodoItemsCompleted` eventos y proporciona los datos de respuesta dentro del evento `Result` propiedad.

## <a name="create-the-isoapservice-implementation"></a>Crear la implementación ISoapService

Para habilitar el proyecto compartido y multiplataforma trabajar con el servicio, el ejemplo define el `ISoapService` interfaz, que sigue el [modelo de programación de tareas asincrónica en C# ](/dotnet/csharp/programming-guide/concepts/async/). Cada plataforma implementa el `ISoapService` para exponer el proxy específico de la plataforma. El ejemplo usa `TaskCompletionSource` objetos para exponer el proxy como una interfaz de tarea asincrónica. Detalles sobre el uso de `TaskCompletionSource` se encuentran en las implementaciones de cada tipo de acción en las secciones siguientes.

El ejemplo `SoapService`:

1. Crea una instancia de la `TodoService` como una instancia de nivel de clase
1. Crea una colección denominada `Items` almacenar `TodoItem` objetos
1. Especifica un punto de conexión personalizado para el elemento opcional `Url` propiedad en el `TodoService`

```csharp
public class SoapService : ISoapService
{
    ASMXService.TodoService todoService;
    public List<TodoItem> Items { get; private set; } = new List<TodoItem>();

    public SoapService ()
    {
        todoService = new ASMXService.TodoService ();
        todoService.Url = Constants.SoapUrl;
        ...
    }
}
```

### <a name="create-data-transfer-objects"></a>Crear objetos de transferencia de datos

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

Este método crea un nuevo `ASMService.TodoItem` de instancia y establece cada propiedad a la propiedad idéntica desde el `TodoItem` instancia.

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

Este método recupera los datos desde el proxy generado `TodoItem` escriba y lo establece en recién creado `TodoItem` instancia.

### <a name="retrieve-data"></a>Recuperar datos

El `ISoapService` interfaz espera el `RefreshDataAsync` método devuelva un `Task` con la colección de elementos. Sin embargo, el `TodoService.GetTodoItemsAsync` método devuelve void. Para satisfacer el patrón de interfaz, debe llamar a `GetTodoItemsAsync`, espere a que el `GetTodoItemsCompleted` eventos se activan y rellene la colección. Esto permite devolver una colección válida a la interfaz de usuario.

El ejemplo siguiente crea un nuevo `TaskCompletionSource`, comienza la llamada asincrónica en el `RefreshDataAsync` método y espera el `Task` proporcionada por el `TaskCompletionSource`. Cuando el `TodoService_GetTodoItemsCompleted` se invoca el controlador de eventos rellena el `Items` colección y las actualizaciones de la `TaskCompletionSource`:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> getRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.GetTodoItemsCompleted += TodoService_GetTodoItemsCompleted;
    }

    public async Task<List<TodoItem>> RefreshDataAsync()
    {
        getRequestComplete = new TaskCompletionSource<bool>();
        todoService.GetTodoItemsAsync();
        await getRequestComplete.Task;
        return Items;
    }

    private void TodoService_GetTodoItemsCompleted(object sender, ASMXService.GetTodoItemsCompletedEventArgs e)
    {
        try
        {
            getRequestComplete = getRequestComplete ?? new TaskCompletionSource<bool>();

            Items = new List<TodoItem>();
            foreach (var item in e.Result)
            {
                Items.Add(FromASMXServiceTodoItem(item));
            }
            getRequestComplete?.TrySetResult(true);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(@"\t\tERROR {0}", ex.Message);
        }
    }

    ...
}
```

Para obtener más información, consulte [modelo de programación asincrónica](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) y [TPL y la programación tradicional de .NET Framework asincrónicas](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming).

### <a name="create-or-edit-data"></a>Crear o editar datos

Al crear o editar datos, se debe implementar la `ISoapService.SaveTodoItemAsync` método. Este método detecta si el `TodoItem` es un elemento nuevo o actualizado y llama al método apropiado en el `todoService` objeto. El `CreateTodoItemCompleted` y `EditTodoItemCompleted` también se deberían implementar controladores de eventos para que sepa cuándo el `todoService` ha recibido una respuesta desde el servicio ASMX (estos se pueden combinar en un único controlador puesto que realizan la misma operación). El ejemplo siguiente muestra las implementaciones de controlador de interfaz y eventos, así como el `TaskCompletionSource` objeto usado para funcionar de forma asincrónica:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> saveRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.CreateTodoItemCompleted += TodoService_SaveTodoItemCompleted;
        todoService.EditTodoItemCompleted += TodoService_SaveTodoItemCompleted;
    }

    public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
    {
        try
        {
            var todoItem = ToASMXServiceTodoItem(item);
            saveRequestComplete = new TaskCompletionSource<bool>();
            if (isNewItem)
            {
                todoService.CreateTodoItemAsync(todoItem);
            }
            else
            {
                todoService.EditTodoItemAsync(todoItem);
            }
            await saveRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_SaveTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        saveRequestComplete?.TrySetResult(true);
    }

    ...
}
```

### <a name="delete-data"></a>Eliminar datos

Eliminación de datos requiere una implementación similar. Definir un `TaskCompletionSource`, implemente un controlador de eventos y el `ISoapService.DeleteTodoItemAsync` método:

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> deleteRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.DeleteTodoItemCompleted += TodoService_DeleteTodoItemCompleted;
    }

    public async Task DeleteTodoItemAsync (string id)
    {
        try
        {
            deleteRequestComplete = new TaskCompletionSource<bool>();
            todoService.DeleteTodoItemAsync(id);
            await deleteRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_DeleteTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        deleteRequestComplete?.TrySetResult(true);
    }

    ...
}
```

## <a name="test-the-web-service"></a>Probar el servicio web

Dispositivos físicos o emulados con un servicio hospedado localmente para probar requiere la configuración personalizada de IIS, direcciones de punto de conexión y las reglas de firewall en su lugar. Para obtener más detalles sobre cómo configurar el entorno de prueba, consulte el [configurar el acceso remoto a IIS Express](wcf.md#configure-remote-access-to-iis-express). La única diferencia entre las pruebas de WCF y ASMX es el número de puerto de la TodoService.

## <a name="related-links"></a>Vínculos relacionados

- [TodoASMX (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)
