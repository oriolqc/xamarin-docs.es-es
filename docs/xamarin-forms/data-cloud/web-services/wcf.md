---
title: Consumir un servicio Web de Windows Communication Foundation (WCF)
description: En este artículo se muestra cómo consumir un servicio de Protocolo Simple de acceso de objetos (SOAP) de WCF desde una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: c79dd6430d387d75acfa010e7f5ad01829f8a6f0
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658942"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Consumir un servicio Web de Windows Communication Foundation (WCF)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)

_WCF es el marco unificado de Microsoft para crear aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transacciones e interoperables. En este artículo se muestra cómo consumir un servicio de Protocolo Simple de acceso de objetos (SOAP) de WCF desde una aplicación de Xamarin.Forms._

WCF describe un servicio con una variedad de contratos diferentes incluidas:

- **Los contratos de datos** : definir las estructuras de datos que forman la base para el contenido dentro de un mensaje.
- **Contratos de mensaje** – redactar mensajes de los contratos de datos existente.
- **Contratos de error** : permitir que los errores de SOAP personalizados que se especifique.
- **Contratos de servicio** : especificar las operaciones que admiten servicios y los mensajes necesarios para interactuar con cada operación. También especifique ningún comportamiento de error personalizado que se puede asociar con las operaciones en cada servicio.

Existen diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero WCF admite las mismas funcionalidades que proporciona ASMX: los mensajes SOAP a través de HTTP. Para obtener más información sobre cómo usar un servicio de ASMX, consulte [consumir servicios de Web de ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md).

> [!IMPORTANT]
> La compatibilidad con la plataforma de Xamarin para WCF se limita a los mensajes codificados en texto SOAP a través de HTTP/HTTPS mediante la `BasicHttpBinding` clase.
>
> Compatibilidad con WCF requiere el uso de herramientas solo está disponibles en un entorno de Windows para generar al proxy y hospedar el TodoWCFService. Compilar y probar la aplicación de iOS requiere implementar el TodoWCFService en un equipo Windows o como un servicio web de Azure.
>
> Aplicaciones nativas de Xamarin Forms suelen compartan el código con una biblioteca de clases .NET Standard. Sin embargo, .NET Core no admite actualmente WCF para que el proyecto compartido debe ser una biblioteca de clases Portable heredada. Para obtener información sobre la compatibilidad WCF en .NET Core, vea [selección entre .NET Core y .NET Framework para aplicaciones de servidor](/dotnet/standard/choosing-core-framework-server).

La solución de aplicación de ejemplo incluye un servicio WCF que se puede ejecutar localmente y se muestra en la captura de pantalla siguiente:

![](wcf-images/portal.png "Aplicación de ejemplo")

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
>
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumir el servicio web

El servicio WCF proporciona las siguientes operaciones:

|Operación|Descripción|Parámetros|
|--- |--- |--- |
|GetTodoItems|Obtener una lista de tareas pendientes|
|CreateTodoItem|Crear una nueva tarea pendiente|Un documento XML serializado TodoItem|
|EditTodoItem|Actualizar una tarea pendiente|Un documento XML serializado TodoItem|
|DeleteTodoItem|Eliminar una tarea pendiente|Un documento XML serializado TodoItem|

Para obtener más información sobre el modelo de datos utilizado en la aplicación, consulte [los datos de modelado](~/xamarin-forms/data-cloud/web-services/introduction.md).

Un *proxy* debe generarse para consumir un servicio WCF, que permite que la aplicación para conectarse al servicio. El proxy se construye por consumir los metadatos del servicio que definen los métodos y la configuración del servicio asociado. Estos metadatos se muestran en forma de un documento de lenguaje de descripción de servicios Web (WSDL) que se genera el servicio web. El proxy puede compilarse mediante el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 para agregar una referencia de servicio para el servicio web en una biblioteca de .NET Standard. Una alternativa para crear al proxy con el Microsoft WCF Web Service Reference Provider en Visual Studio 2017 consiste en utilizar ServiceModel Metadata Utility Tool (svcutil.exe). Para obtener más información, consulte [ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

Las clases de proxy generadas proporcionan métodos para consumir los servicios web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este patrón, una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que comienzan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso de llamada mientras lleva a la operación asincrónica en un subproceso ThreadPool.

Para cada llamada a *BeginOperationName*, también debe llamar la aplicación *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es el mismo tipo devuelto por el método de servicio web sincrónico. Por ejemplo, el `EndGetTodoItems` método devuelve una colección de `TodoItem` instancias. El *EndOperationName* método también incluye un `IAsyncResult` parámetro que se debe establecer en la instancia devuelta por la llamada correspondiente a la *BeginOperationName* método.

Task Parallel Library (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM al encapsular las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporciona varias sobrecargas de los `TaskFactory.FromAsync` método.

Para obtener más información acerca de APM vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y la programación tradicional de .NET Framework asincrónicas](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

### <a name="create-the-todoserviceclient-object"></a>Crear el objeto TodoServiceClient

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

### <a name="create-data-transfer-objects"></a>Crear objetos de transferencia de datos

La aplicación de ejemplo usa el `TodoItem` clase para modelar los datos. Para almacenar un `TodoItem` elemento en el servicio web debe convertirse primero en el proxy generado `TodoItem` tipo. Esto se consigue mediante la `ToWCFServiceTodoItem` método, como se muestra en el ejemplo de código siguiente:

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem
  {
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
  return new TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

Este método simplemente recupera los datos desde el proxy generado `TodoItem` escriba y lo establece en recién creado `TodoItem` instancia.

### <a name="retrieve-data"></a>Recuperar datos

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

  foreach (var item in todoItems)
  {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

El `Task.Factory.FromAsync` método crea un `Task` que se ejecuta el `TodoServiceClient.EndGetTodoItems` método una vez el `TodoServiceClient.BeginGetTodoItems` método se completa, con el `null` parámetro que indica que no hay datos se pasan a la `BeginGetTodoItems` delegar. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

El `TodoServiceClient.EndGetTodoItems` método devuelve un `ObservableCollection` de `TodoWCFService.TodoItem` instancias, que, a continuación, se convierte en un `List` de `TodoItem` instancias para su presentación.

### <a name="create-data"></a>Crear datos

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

### <a name="update-data"></a>actualizar datos

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

### <a name="delete-data"></a>Eliminar datos

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

## <a name="configure-remote-access-to-iis-express"></a>Configurar el acceso remoto a IIS Express
En Visual Studio 2017 o Visual Studio de 2019, debería poder probar la aplicación para UWP en un equipo sin ninguna configuración adicional. Probar los clientes iOS y Android puede requerir que los pasos adicionales en esta sección. Consulte [conéctese a servicios Web locales de los emuladores de Android y simuladores de iOS](~/cross-platform/deploy-test/connect-to-local-web-services.md) para obtener más información.

De forma predeterminada, IIS Express solo responderá a las solicitudes para `localhost`. Los dispositivos remotos (por ejemplo, un dispositivo Android, un iPhone o incluso un simulador) no tendrán acceso a un servicio WCF local. Necesitará saber la dirección IP de estación de trabajo de Windows 10 en la red local. Para este ejemplo, suponga que la estación de trabajo tiene la dirección IP `192.168.1.143`. Los pasos siguientes explican cómo configurar Windows 10 e IIS Express para que acepte conexiones remotas y conectarse al servicio desde un dispositivo físico o virtual:

1. **Agregar una excepción al Firewall de Windows**. Debe abrir un puerto a través de Firewall de Windows que las aplicaciones de la subred pueden usar para comunicarse con el servicio WCF. Cree una regla de entrada abrir puerto 49393 en el firewall. Desde un símbolo del sistema administrativo, ejecute este comando:
    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configurar IIS Express para las conexiones remotas acepte**. Puede configurar IIS Express editando el archivo de configuración de IIS Express en **[directorio de la solución]\.vs\config\applicationhost.config**. Buscar el `site` elemento con el nombre `TodoWCFService`. Debería ser similar al siguiente XML:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
        </bindings>
    </site>
    ```

    Deberá agregar dos `binding` elementos para abrir el puerto 49393 para el tráfico externo y el emulador de Android. El enlace utiliza un `[IP address]:[port]:[hostname]` formato que especifica cómo IIS Express responderá a las solicitudes. Las solicitudes externas tendrán nombres de host que se deben especificar como un `binding`. Agregue el siguiente código XML para el `bindings` elemento, reemplazando la dirección IP con su propia dirección IP:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Después de los cambios del `bindings` elemento debe ser similar al siguiente:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
            <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
            <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
        </bindings>
    </site>
    ```

    >[!IMPORTANT]
    >De forma predeterminada, IIS Express no aceptará las conexiones de orígenes externos por motivos de seguridad. Para habilitar las conexiones procedentes de dispositivos remotos deben ejecutar IIS Express con permisos administrativos. La manera más fácil de hacerlo es ejecutar Visual Studio 2017 con permisos administrativos. Esto iniciará IIS Express con permisos administrativos al ejecutar el TodoWCFService.

    Con estos pasos completados, debe ser capaz de ejecutar el TodoWCFService y conectarse desde otros dispositivos de la subred. Puede probar esto mediante la ejecución de la aplicación y visitar `http://localhost:49393/TodoService.svc`. Si se produce un **solicitud incorrecta** error al visitar esa dirección URL, su `bindings` puede ser incorrecto en la configuración de IIS Express (la solicitud está llegando a IIS Express, pero se ha rechazado). Si se produce un error diferente, es posible que no se está ejecutando la aplicación o el firewall está configurado incorrectamente.

    Para permitir que IIS Express mantener la ejecución y ofrecer el servicio, desactive la **editar y continuar** opción **las propiedades del proyecto > Web > depuradores**.

1. **Personalizar el punto de conexión de dispositivos usan para tener acceso al servicio**. Este paso implica la configuración de la aplicación cliente, que se ejecuta en un dispositivo físico o emulado, para acceder al servicio WCF.

    El emulador de Android usa un proxy interno que impide que el emulador de obtener acceso directamente a la máquina host `localhost` dirección. En su lugar, la dirección `10.0.2.2` en el emulador se enruta a `localhost` en el equipo host a través de un proxy interno. Estas solicitudes procesadas por el proxy tendrá `127.0.0.1` como nombre de host en el encabezado de solicitud, que es la razón por la que creó el enlace de IIS Express para este nombre de host en los pasos anteriores.

    IOS Simulator se ejecuta en un equipo Mac compilar host, incluso si usas el [remoto de iOS Simulator para Windows](~/tools/ios-simulator/index.md). Las solicitudes de red desde el simulador tendrá su dirección IP de la estación de trabajo en la red local como el nombre de host (en este ejemplo tiene `192.168.1.143`, pero la dirección IP real probablemente serán diferente). Se trata de por qué creó el enlace de IIS Express para este nombre de host en los pasos anteriores.

    Asegúrese del `SoapUrl` propiedad en el **Constants.cs** archivo en el proyecto TodoWCF (Portable) tienen valores que son correctos para la red:

    ```csharp
    public static string SoapUrl
    {
        get
        {
            var defaultUrl = "http://localhost:49393/TodoService.svc";

            if (Device.RuntimePlatform == Device.Android)
            {
                defaultUrl = "http://10.0.2.2:49393/TodoService.svc";
            }
            else if (Device.RuntimePlatform == Device.iOS)
            {
                defaultUrl = "http://192.168.1.143:49393/TodoService.svc";
            }

            return defaultUrl;
        }
    }
    ```

    Una vez que haya configurado el **Constants.cs** con los puntos de conexión adecuadas, debe ser capaz de conectarse a la TodoWCFService que se ejecuta en la estación de trabajo de Windows 10 desde dispositivos físicos o virtuales.

## <a name="related-links"></a>Vínculos relacionados

- [TodoWCF (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [Cómo: Crear a un cliente de Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
