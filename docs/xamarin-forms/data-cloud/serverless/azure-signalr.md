---
title: Azure SignalR Service con Xamarin.Forms
description: Empezar a trabajar con Azure SignalR Service y Azure Functions con Xamarin.Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: d79ffb844a65c145fd6cb22a5a3e1dfc7a6bfe41
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2019
ms.locfileid: "67418128"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Azure SignalR Service con Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureSignalR)

ASP.NET Core SignalR es un modelo de aplicación que simplifica el proceso de incorporación de comunicación en tiempo real a las aplicaciones. Azure SignalR Service permite el rápido desarrollo e implementación de aplicaciones escalables de SignalR. Las funciones de Azure son los métodos de código sin servidor y de corta duración que se pueden combinar con aplicaciones de forma escalable, controlado por eventos.

Este artículo y el ejemplo muestran cómo combinar Azure SignalR Service y Azure Functions con Xamarin.Forms para entregar mensajes en tiempo real a los clientes conectados.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Crear un servicio Azure SignalR y aplicación de Azure Functions

La aplicación de ejemplo consta de tres componentes principales: un centro de Azure SignalR Service, una instancia de Azure Functions con dos funciones y una aplicación móvil que pueda enviar y recibir mensajes. Estos componentes interactúan como sigue:

1. La aplicación móvil invoca un **Negotiate** función de Azure para obtener información sobre el centro de SignalR.
1. La aplicación móvil usa la información de la negociación para registrarse con el centro de SignalR y forma de una conexión.
1. Después del registro, la aplicación móvil envía mensajes a la **hablar** función de Azure.
1. El **hablar** función pasa el mensaje entrante para el centro de SignalR.
1. El centro de SignalR transmite el mensaje a todas las instancias de aplicación móvil conectada, incluido el remitente original.

> [!NOTE]
> El **Negotiate** y **hablar** funciones en la aplicación de ejemplo se pueden ejecutar localmente mediante Visual Studio de 2019 y las herramientas de Azure en tiempo de ejecución. Sin embargo, no se emula localmente Azure SignalR Service y resulta difícil exponer hospedados localmente Azure Functions a dispositivos físicos o virtuales para las pruebas. Se recomienda implementar las funciones de Azure a una instancia de la aplicación de Azure Functions, ya que esto permite que las pruebas multiplataforma. Para obtener detalles de implementación, consulte [implementar Azure Functions con Visual Studio de 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Crear un servicio de Azure SignalR

Se puede crear un servicio de Azure SignalR eligiendo **crear un recurso** en la esquina superior izquierda de Azure portal y buscar **SignalR**. Azure SignalR Service puede crearse en el nivel gratis. Azure SignalR Service debe estar en **sin servidor** modo de servicio. Si opta por accidente el valor predeterminado o el modo de servicio clásico, puede cambiar más adelante en las propiedades de Azure SignalR Service.

Captura de pantalla siguiente muestra la creación de un nuevo servicio de Azure SignalR:

![Creación de la captura de pantalla de Azure SignalR Service en Azure portal](azure-signalr-images/azure-signalr-create.png "creación de un servicio de Azure SignalR")

Una vez creado, el **claves** sección de Azure SignalR Service contiene un **cadena de conexión**, que se usa para conectar la aplicación de Azure Functions con el centro de SignalR. Captura de pantalla siguiente muestra dónde encontrar la cadena de conexión de Azure SignalR Service:

![Cadena de conexión de la captura de pantalla de Azure SignalR en el portal de Azure](azure-signalr-images/azure-signalr-connection-string.png "cadena de conexión de Azure SignalR")

Esta cadena de conexión se usa para [implementar Azure Functions con Visual Studio de 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-functions-app"></a>Crear una aplicación de Azure Functions

Para probar la aplicación de ejemplo, debe crear una nueva aplicación de Azure Functions en Azure portal. Tome nota de la **nombre de la aplicación** como esta dirección URL se utiliza en la aplicación de ejemplo **Constants.cs** archivo. Captura de pantalla siguiente muestra la creación de una nueva aplicación de las funciones de Azure denominado "xdocsfunctions":

[![Creación de una aplicación de funciones de captura de pantalla de Azure](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Las funciones de Azure pueden implementarse en una instancia de la aplicación de Azure Functions de Visual Studio de 2019. Las secciones siguientes describen la implementación de dos funciones en la aplicación de ejemplo a una instancia de la aplicación de Azure Functions.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Crear Azure Functions en Visual Studio de 2019

La aplicación de ejemplo contiene una biblioteca de clases denominada **ChatServer**, que incluye dos funciones de Azure sin servidor en archivos denominados **Negotiate.cs** y **Talk.cs**.

El `Negotiate` función responde a solicitudes web con un `SignalRConnectionInfo` objeto que contiene un `AccessToken` propiedad y un `Url` propiedad. La aplicación móvil usa estos valores para registrarse con el centro de SignalR. El código siguiente muestra el `Negotiate` función:

```csharp
[FunctionName("Negotiate")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous,"get",Route = "negotiate")]
    HttpRequest req,
    [SignalRConnectionInfo(HubName = "simplechat")]
    SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

El `Talk` función responde a las solicitudes de HTTP POST que proporcionan un objeto de mensaje en el cuerpo de POST. El cuerpo de POST se transforma en un `SignalRMessage` y se reenvía al centro de SignalR. El código siguiente muestra el `Talk` función:

```csharp
[FunctionName("Talk")]
public static async Task<IActionResult> Run(
    [HttpTrigger(
        AuthorizationLevel.Anonymous,
        "post",
        Route = "talk")]
    HttpRequest req,
    [SignalR(HubName = "simplechat")]
    IAsyncCollector<SignalRMessage> questionR,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string json = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic obj = JsonConvert.DeserializeObject(json);

        var name = obj.name.ToString();
        var text = obj.text.ToString();

        var jObject = new JObject(obj);

        await questionR.AddAsync(
            new SignalRMessage
            {
                Target = "newMessage",
                Arguments = new[] { jObject }
            });

        return new OkObjectResult($"Hello {name}, your message was '{text}'");
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("There was an error: " + ex.Message);
    }
}
```

Para obtener más información acerca de las funciones de Azure y Azure Functions Apps, consulte [documentación de Azure Functions](/azure/azure-functions/).

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Implementación de Azure Functions con Visual Studio de 2019

2019 de Visual Studio le permite implementar las funciones en una aplicación de Azure Functions. Hospedado en Azure functions facilitan las pruebas multiplataforma al proporcionar un punto de conexión de prueba accesible para todos los dispositivos.

Haciendo clic en la aplicación de las funciones de ejemplo y elegir **publicar** inicia el cuadro de diálogo para publicar las funciones en la aplicación de Azure Functions. Si ha seguido los pasos anteriores para configurar una aplicación de función de Azure, puede elegir **seleccionar existente** para publicar las aplicaciones de ejemplo para la aplicación de Azure Functions. Captura de pantalla siguiente muestra la publicación opciones del cuadro de diálogo en Visual Studio de 2019:

![Cuadro de diálogo de opciones de publicación en Visual Studio de 2019](azure-signalr-images/vs-publish-target.png "opciones de publicación en Visual Studio de 2019")

Una vez que haya iniciado sesión con tu cuenta Microsoft, puede buscar y elegir la aplicación de Azure Functions como el destino de publicación. Captura de pantalla siguiente muestra un ejemplo de aplicación de Azure Functions en el cuadro de diálogo de publicación de Visual Studio de 2019:

![Una aplicación de Azure Functions en el cuadro de diálogo de publicación de Visual Studio de 2019](azure-signalr-images/vs-app-selection.png "aplicación de Azure Functions en el cuadro de diálogo de publicación de Visual Studio de 2019")

Después de seleccionar una aplicación de Azure Functions se muestran la instancia, la dirección URL del sitio, configuración y otra información sobre el destino de la aplicación de Azure Functions. Elija **editar la configuración de Azure App Service** y escriba la cadena de conexión en el **remoto** campo. La cadena de conexión está usando el **Negotiate** y **hablar** funciones para conectarse a Azure SignalR Service y está disponible en el **claves** sección de Azure SignalR Servicio en el portal de Azure. Para obtener más información acerca de la cadena de conexión, consulte [crear un servicio de Azure SignalR](#create-an-azure-signalr-service).

Una vez haya escrito la cadena de conexión, haga clic en **publicar** para implementar las funciones a la aplicación de Azure Functions. Una vez que haya finalizado, se enumerarán las funciones en la aplicación de Azure Functions en Azure portal. Captura de pantalla siguiente muestra las funciones publicadas en el portal de Azure:

![Funciones que se publican en la aplicación de Azure Functions](azure-signalr-images/azure-functions-deployed.png "funciones publican en la aplicación de Azure Functions")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Integrar el servicio Azure SignalR con Xamarin.Forms

La integración entre Azure SignalR Service y la aplicación de Xamarin.Forms es una clase de servicio de SignalR que se crea una instancia en el `MainPage` clase con controladores de eventos asignados a los tres eventos. Para obtener más información acerca de estos controladores de eventos, consulte [utilizar la clase de servicio de SignalR en Xamarin.Forms](#use-the-signalr-service-class-in-xamarinforms).

La aplicación de ejemplo incluye un **Constants.cs** clase que debe personalizarse con el punto de conexión de dirección URL de la aplicación de Azure Functions. Establezca el valor de la `HostName` propiedad a su dirección de la aplicación de Azure Functions. El siguiente código muestra la **Constants.cs** propiedades con un ejemplo `HostName` valor:

```csharp
public static class Constants
{
    public static string HostName { get; set; } = "https://example-functions-app.azurewebsites.net/";

    // Used to differentiate message types sent via SignalR. This
    // sample only uses a single message type.
    public static string MessageName { get; set; } = "newMessage";

    public static string Username
    {
        get
        {
            return $"{Device.RuntimePlatform} User";
        }
    }
}
```

> [!NOTE]
> El `Username` propiedad en la aplicación de ejemplo **Constants.cs** archivo utiliza el dispositivo `RuntimePlatform` valor como el nombre de usuario. Esto facilita probar dispositivos entre plataformas e identificar qué dispositivo está enviando el mensaje. En una aplicación real, este valor sería un nombre de usuario único, recopilados durante un inicio de sesión de seguridad o inicie sesión en proceso.

### <a name="the-signalr-service-class"></a>La clase de servicio SignalR

El `SignalRService` clase en el **ChatClient** proyecto en la aplicación de ejemplo muestra una implementación que invoca las funciones en una aplicación de Azure Functions para conectarse a un servicio de Azure SignalR.

El `SendMessageAsync` método en el `SignalRService` clase se utiliza para enviar mensajes a los clientes conectados a Azure SignalR Service. Este método realiza una solicitud HTTP POST a la **hablar** función hospedado en la aplicación de Azure Functions, incluido un serializada `Message` objeto como la carga POST. El **hablar** función pasa el mensaje al servicio Azure SignalR para difusión a todos los clientes. En el código siguiente se muestra el método `SendMessageAsync`:

```csharp
public async Task SendMessageAsync(string username, string message)
{
    IsBusy = true;

    var newMessage = new Message
    {
        Name = username,
        Text = message
    };

    var json = JsonConvert.SerializeObject(newMessage);
    var content = new StringContent(json, Encoding.UTF8, "application/json");
    var result = await client.PostAsync($"{Constants.HostName}/api/talk", content);

    IsBusy = false;
}
```

El `ConnectAsync` método en el `SignalRService` clase realiza una solicitud HTTP GET a la **Negotiate** función hospedado en la aplicación de Azure Functions. El **Negotiate** función devuelve JSON que se deserializa en una instancia de la `NegotiateInfo` clase. Una vez el `NegotiateInfo` se recupera el objeto, se usa para registrar directamente con Azure SignalR Service mediante una instancia de la `HubConnection` clase.

SignalR de ASP.NET Core traduce los datos de entrada de la conexión abierta en mensajes y permite a los desarrolladores definir tipos de mensajes y enlazar controladores de eventos a los mensajes entrantes por tipo. El `ConnectAsync` método registra un controlador de eventos para el nombre de mensaje definido en la aplicación de ejemplo **Constants.cs** archivo, que es "newMessage" de forma predeterminada.

En el código siguiente se muestra el método `ConnectAsync`:

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
            .WithUrl(negotiate.Url, options =>
            {
                options.AccessTokenProvider = async () => negotiate.AccessToken;
            })
            .Build();

        connection.On<JObject>(Constants.MessageName, AddNewMessage);
        await connection.StartAsync();

        IsConnected = true;
        IsBusy = false;

        Connected?.Invoke(this, true, "Connection successful.");
    }
    catch (Exception ex)
    {
        ConnectionFailed?.Invoke(this, false, ex.Message);
    }
}
```

El `AddNewMessage` método se enlaza como el controlador de eventos en el `ConnectAsync` del mensaje como se muestra en el código anterior. Cuando se recibe un mensaje, el `AddNewMessage` se llama al método con los datos del mensaje proporcionados como un `JObject`. El `AddNewMessage` método convierte el `JObject` a una instancia de la `Message` clase y, a continuación, invoca el controlador para `NewMessageReceived` si uno se ha enlazado. En el código siguiente se muestra el método `AddNewMessage`:

```csharp
public void AddNewMessage(JObject message)
{
    Message messageModel = new Message
    {
        Name = message.GetValue("name").ToString(),
        Text = message.GetValue("text").ToString(),
        TimeReceived = DateTime.Now
    };

    NewMessageReceived?.Invoke(this, messageModel);
}
```

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Utilice la clase de servicio de SignalR en Xamarin.Forms

Uso de la clase de servicio SignalR en Xamarin.Forms se logra enlazando el `SignalRService` clase de eventos en el `MainPage` clase de código subyacente.

El `Connected` eventos en el `SignalRService` clase se desencadena cuando una conexión SignalR se completó correctamente. El `ConnectionFailed` eventos en el `SignalRService` clase se desencadena cuando se produce un error en una conexión de SignalR. El `SignalR_ConnectionChanged` está enlazado el método controlador de eventos para eventos en el `MainPage` constructor. Este controlador de eventos actualiza los Estados del botón Conectar y envío basándose en la conexión `success` argumento y agrega el mensaje proporcionado por el evento a la cola de chat mediante el `AddMessage` método. El código siguiente muestra el `SignalR_ConnectionChanged` método controlador de eventos:

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

El `NewMessageReceived` eventos en el `SignalRService` clase se desencadena cuando se recibe un mensaje nuevo de Azure SignalR Service. El `SignalR_NewMessageReceived` método controlador de eventos está enlazado a la `NewMessageReceived` eventos en el `MainPage` constructor. Este controlador de eventos convierte entrante `Message` objeto en una cadena y lo agrega a la cola de chat mediante el `AddMessage` método. El código siguiente muestra el `SignalR_NewMessageReceived` método controlador de eventos:

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

El `AddMessage` método agrega un nuevo mensaje como un `Label` objeto a la cola de chat. El `AddMessage` método se suele denominar los controladores de eventos desde fuera el subproceso principal de la interfaz de usuario, por lo que obliga a las actualizaciones de la interfaz de usuario que se produzca en el subproceso principal para evitar excepciones. En el código siguiente se muestra el método `AddMessage`:

```csharp
void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        Label label = new Label
        {
            Text = message,
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        };

        messageList.Children.Add(label);
    });
}
```

## <a name="test-the-application"></a>Probar la aplicación

Puede probar la aplicación de chat de SignalR en iOS, Android y UWP siempre que tenga:

1. Crea un servicio de Azure SignalR.
1. Crea una aplicación de Azure Functions.
1. Personalizar el **Constants.cs** archivo con el punto de conexión de la aplicación de Azure Functions.

Una vez completados estos pasos y se ejecuta la aplicación, haga clic en el **Connect** botón forma una conexión con Azure SignalR Service. Escribe un mensaje y haga clic en el **enviar** resultados de botón en mensajes que aparecen en la cola de chat en cualquier conectado aplicaciones móviles.

## <a name="related-links"></a>Vínculos relacionados

* [Creación de aplicaciones móviles en tiempo real con Xamarin y SignalR](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Introducción a SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Introducción a Azure Functions](/azure/azure-functions/functions-overview)
* [Documentación de Azure Functions](/azure/azure-functions/)
* [Ejemplo de chat de SignalR MVVM](https://github.com/lbugnion/sample-xamarin-signalr)
