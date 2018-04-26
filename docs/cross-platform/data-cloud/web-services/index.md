---
title: Introducción a servicios Web
description: Esta guía demuestra cómo utilizar tecnologías de servicios web diferente. Los temas tratados son comunicarse con servicios de REST, servicios de Windows Communication Foundation y servicios SOAP.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6de16095d9e1267587a050e8081f87f896f3153e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2018
---
# <a name="introduction-to-web-services"></a>Introducción a servicios Web

_Esta guía demuestra cómo utilizar tecnologías de servicios web diferente. Los temas tratados son comunicarse con servicios de REST, servicios de Windows Communication Foundation y servicios SOAP._

Para que funcione correctamente, muchas aplicaciones móviles son dependientes de la nube y, por lo que la integración de servicios web en aplicaciones móviles es un escenario común. La plataforma Xamarin compatible con el uso de tecnologías de servicios web diferente e incluye compatibilidad integrada y de otros fabricantes para consumir servicios RESTful, ASMX y Windows Communication Foundation (WCF).

En este artículo se trata los temas siguientes:

- [Servicios de REST](#rest)
- [Servicios Web ASP.Net (ASMX)](#asmx)
- [Servicios de WCF](#wcf)

Para los clientes que usan Xamarin.Forms, hay ejemplos completos con cada una de estas tecnologías en la [Xamarin.Forms Web Services](~/xamarin-forms/data-cloud/index.md) documentación.

> [!IMPORTANT]
> En iOS 9, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial.
> Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.

Puede optar por no ATS si no es posible utilizar la `HTTPS` del protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).



<a name="rest" />

## <a name="rest"></a>REST

Representational State Transfer (REST) es un estilo de arquitectura para la creación de servicios web. Solicitudes REST se realizan a través de HTTP utilizando los mismos verbos HTTP que los exploradores web que se utilizan para recuperar las páginas web y para enviar datos a los servidores. Los verbos son:

- **OBTENER** : esta operación se usa para recuperar datos desde el servicio web.
- **POST** : esta operación se utiliza para crear un nuevo elemento de datos en el servicio web.
- **COLOCAR** : esta operación se utiliza para actualizar un elemento de datos en el servicio web.
- **REVISIÓN** : esta operación se utiliza para actualizar un elemento de datos en el servicio web mediante la descripción de un conjunto de instrucciones sobre cómo se debe modificar el elemento. Este verbo no se utiliza en la aplicación de ejemplo.
- **ELIMINAR** : esta operación se usa para eliminar un elemento de datos en el servicio web.

Se llama a las API de REST API que se ajustan al resto del servicio Web y se definen mediante:

- Un identificador URI base.
- Métodos HTTP, como GET, POST, PUT, PATCH o DELETE.
- Tipo de medio de los datos, como JavaScript Object Notation (JSON).

La sencillez de REST ha ayudado a hacer el método principal para tener acceso a servicios web en aplicaciones móviles.

## <a name="consuming-rest-services"></a>Consumir servicios REST

Hay una serie de clases que pueden usarse para consumir servicios REST y bibliotecas, y en las siguientes subsecciones hablar sobre ellos. Para obtener más información acerca de cómo consumir un servicio REST, consulte [consumir un servicio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md).

### <a name="httpclient"></a>HttpClient

El [Microsoft HTTP Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http) proporciona el `HttpClient` (clase), que se usa para enviar y recibir solicitudes a través de HTTP. Proporciona funcionalidad para enviar solicitudes HTTP y recibir respuestas HTTP desde un recurso identificado por el URI. Cada solicitud se envía como una operación asincrónica. Para obtener más información acerca de las operaciones asincrónicas, vea [Introducción al soporte técnico Async](~/cross-platform/platform/async.md).

La `HttpResponseMessage` clase representa un mensaje de respuesta HTTP recibido desde el servicio web después de que se ha realizado una solicitud HTTP. Contiene información acerca de la respuesta, incluido el código de estado, los encabezados y cuerpo. El `HttpContent` clase representa el cuerpo HTTP y encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer utilizando cualquiera de los `ReadAs` métodos, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, dependiendo del formato de los datos.

Para obtener más información sobre la `HttpClient` de clases, consulte [crear el objeto HTTPClient](~/xamarin-forms/data-cloud/consuming/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

Llamar a servicios web con `HTTPWebRequest` implica:

-  Crear una instancia de la solicitud para un URI determinado.
-  Establecer diversas propiedades HTTP en la instancia de la solicitud.
-  Recuperar un `HttpWebResponse` de la solicitud.
-  Leer datos de la respuesta.

Por ejemplo, el código siguiente recupera los datos de los Estados Unidos Servicio de web de biblioteca de medicina nacional:

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

En el ejemplo anterior, se crea un `HttpWebRequest` que devuelvan los datos con formato JSON. Los datos se devuelven en un `HttpWebResponse`, desde el que un `StreamReader` puede obtenerse para leer los datos.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Otro enfoque para consumir servicios REST está usando la [RestSharp](http://restsharp.org/) biblioteca. RestSharp encapsula las solicitudes HTTP, incluida la compatibilidad para recuperar los resultados como contenido de la cadena sin formato o como un objeto deserializado de C#. Por ejemplo, el código siguiente realiza una solicitud en Estados Unidos Servicio web de biblioteca de medicina nacionales y recupera los resultados como JSON la cadena con formato:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` es un método que tardará la cadena JSON sin formato desde el `RestSharp.RestResponse.Content` propiedad y convertirla en un objeto de C#. Deserializar los datos devueltos desde los servicios web se describe más adelante en este artículo.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Además de las clases disponibles en la base de Mono biblioteca de clases (BCL), como `HttpWebRequest`y las bibliotecas de terceros C#, por ejemplo, RestSharp, clases específicas de la plataforma también están disponibles para consumir servicios web. Por ejemplo, en iOS, el `NSUrlConnection` y `NSMutableUrlRequest` se pueden usar clases.

En el ejemplo de código siguiente se muestra cómo llamar a los Estados Unidos Servicio de web Biblioteca de medicina nacional mediante clases de iOS:

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

Por lo general, las clases específicas de la plataforma para consumir servicios web deberían limitarse a escenarios donde es migrar el código nativo a C#. Siempre que sea posible, código de acceso del servicio web debe ser portátil para que pueda compartido entre plataformas.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Otra opción para llamar a servicios web es el [pila de servicio](http://www.servicestack.net/) biblioteca. Por ejemplo, el código siguiente muestra cómo utilizar la pila de servicio `IServiceClient.GetAsync` método para emitir una solicitud de servicio:

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> Mientras que herramientas como ServiceStack y RestSharp permiten llamar a y consumir servicios de REST, a veces es no trivial para consumir XML o JSON que no se ajusta al estándar _DataContract_ convenciones de serialización. Si es necesario, invocar la solicitud y controlar la serialización adecuada explícitamente mediante la biblioteca de ServiceStack.Text descrita a continuación.


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Consumo de datos RESTful

Servicios web rESTful normalmente utilizan mensajes JSON para devolver datos al cliente. JSON es un basado en texto, intercambio de datos formato que genera compacta cargas, lo que resulta en los requisitos de ancho de banda reducido al enviar datos. En esta sección, se examinarán los mecanismos para consumir RESTful respuestas en JSON y XML de antiguo de simple (POX).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

La plataforma Xamarin se suministra con compatibilidad con JSON de fábrica. Mediante el uso de un `JsonObject`, se pueden recuperar los resultados como se muestra en el ejemplo de código siguiente:

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

Sin embargo, es importante tener en cuenta que el `System.Json` herramientas cargan la totalidad de los datos en la memoria.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

El [NewtonSoft JSON.NET biblioteca](http://www.newtonsoft.com/json) es una biblioteca ampliamente utilizada para serializar y deserializar los mensajes JSON. En el ejemplo de código siguiente se muestra cómo usar JSON.NET para deserializar un mensaje JSON en un objeto de C#:

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack.Text

ServiceStack.Text es una biblioteca de serialización de JSON diseñada para trabajar con la biblioteca ServiceStack. En el ejemplo de código siguiente se muestra cómo analizar JSON mediante un `ServiceStack.Text.JsonObject`:

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

En el caso de consumir un servicio web REST basado en XML, LINQ to XML se puede utilizar para analizar el XML y rellenar un C# en línea de objeto, como se muestra en el ejemplo de código siguiente:

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>Servicio Web ASP.NET (ASMX)

ASMX proporciona la capacidad para crear servicios web que envían mensajes mediante el protocolo Simple de acceso a objetos (SOAP). SOAP es un protocolo independiente de la plataforma e independientes del lenguaje para crear y obtener acceso a servicios web. Los consumidores de un servicio ASMX no es necesario saber nada acerca de la plataforma, el modelo de objetos o el lenguaje de programación utilizado para implementar el servicio. Solo necesitan saber cómo enviar y recibir mensajes SOAP.

Un mensaje SOAP es un documento XML que contiene los elementos siguientes:

- Un elemento raíz denominado *sobres* que identifica el documento XML como un mensaje SOAP.
- Opcional *encabezado* elemento que contiene información específica de la aplicación, como datos de autenticación. Si el *encabezado* elemento está presente debe ser el primer elemento secundario de la *sobres* elemento.
- Obligatoria *cuerpo* elemento que contiene el mensaje SOAP, diseñado para el destinatario.
- Opcional *error* elemento que se usa para indicar los mensajes de error. Si el *error* elemento está presente, debe ser un elemento secundario de la *cuerpo* elemento.

SOAP puede operar en muchos de los protocolos de transporte, como HTTP, SMTP, TCP y UDP. Sin embargo, un servicio de ASMX solo puede funcionar a través de HTTP. La plataforma Xamarin es compatible con las implementaciones estándar de SOAP 1.1 a través de HTTP, y esto incluye la compatibilidad para muchas de las configuraciones estándar de servicio ASMX.

### <a name="generating-a-proxy"></a>Generar un Proxy

A *proxy* debe generarse para consumir un servicio ASMX, que permite que la aplicación para conectarse al servicio. El proxy se construye por consumo metadatos de servicio que definen los métodos y la configuración del servicio asociado. Estos metadatos se exponen como un documento de lenguaje de descripción de servicios Web (WSDL) que se genera mediante el servicio web. El proxy se genera mediante Visual Studio para Mac o en Visual Studio para agregar una referencia web para el servicio web a los proyectos específicos de la plataforma.

La dirección URL del servicio web puede ser un origen remoto hospedado o el recurso del sistema de archivos local es accesible a través de la `file:///` prefijo de ruta de acceso, por ejemplo:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "La dirección URL del servicio web puede ser un origen remoto hospedado o accesible mediante el prefijo de ruta de acceso del archivo de recurso de sistema de archivos local")](images/add-webreference-dialog.png#lightbox)

Esto genera al servidor proxy en la carpeta Web o referencias de servicio del proyecto. Dado que se genera un proxy de código, que no puede modificar.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Cómo agregar manualmente un Proxy a un proyecto

Si tiene un proxy existente que se ha generado mediante herramientas compatibles, este resultado se puede utilizar cuando se incluye como parte de su proyecto. En Visual Studio para Mac, use la **agregar archivos...** opción de menú para agregar al proxy. Además, esto requiere *System.Web.Services.dll* que hace referencia explícitamente utilizando el **agregar referencias...** cuadro de diálogo.

### <a name="consuming-the-proxy"></a>Consumir el Proxy

Las clases de proxy generado proporcionan métodos para consumir el servicio web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este patrón de una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que empiezan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso que realiza la llamada, mientras la operación asincrónica tiene lugar en un subproceso del grupo.

Para cada llamada a *BeginOperationName*, la aplicación debe llamar a *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es del mismo tipo devuelto por el método de servicio web sincrónico. En el ejemplo de código siguiente se muestra un ejemplo de esto:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

La biblioteca (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM encapsulando las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporcionan varias sobrecargas de la `Task.Factory.FromAsync` método. Este método crea un `Task` que se ejecuta el `TodoService.EndGetTodoItems` una vez al método el `TodoService.BeginGetTodoItems` método se completa, con el `null` parámetro que indica que no hay datos se pasan a la `BeginGetTodoItems` delegar. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

Para obtener más información acerca de APM, vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y .NET Framework programación asincrónica tradicional de](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

Para obtener más información acerca de cómo consumir un servicio ASMX, consulte [consumir un servicio Web de ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF es el marco unificado de Microsoft para compilar aplicaciones orientadas a servicios. Permite a los desarrolladores crear aplicaciones distribuidas seguras, confiables, transacciones e interoperables.

WCF describe un servicio con una variedad de contratos diferentes que son los siguientes:

- **Contratos de datos** : definen las estructuras de datos que forman la base para el contenido dentro de un mensaje.
- **Contratos de mensajes** : redactar mensajes de contratos de datos existentes.
- **Contratos de error** : permitir que los errores de SOAP personalizados que se especifique.
- **Contratos de servicio** : especificar las operaciones que admiten servicios y los mensajes necesarios para interactuar con cada operación. También especifican cualquier comportamiento de error personalizado que se puede asociar con las operaciones en cada servicio.

Existen diferencias entre los servicios Web de ASP.NET (ASMX) y WCF, pero es importante comprender que WCF admite las mismas capacidades que proporciona ASMX: mensajes SOAP a través de HTTP.

En general, la plataforma Xamarin es compatible con el mismo subconjunto de cliente de WCF que se suministra con el tiempo de ejecución de Silverlight. Esto incluye las implementaciones más comunes de codificación y protocolo de WCF: texto codificado de mensajes SOAP a través de HTTP utilizando el protocolo de transporte la `BasicHttpBinding` clase. Además, la compatibilidad WCF requiere el uso de herramientas solo está disponibles en un entorno de Windows para generar al proxy.

Para obtener más información sobre el uso de la plataforma Xamarin para consumir un WCF web servicio con el `BasicHttpBinding` de clases, consulte [Tutorial: trabajar con WCF](walkthrough-working-with-wcf.md).

### <a name="generating-a-proxy"></a>Generar un Proxy

A *proxy* debe generarse para consumir un servicio WCF, que permite que la aplicación para conectarse al servicio. El proxy se construye por consumo metadatos del servicio que definen los métodos y la configuración del servicio asociado. Estos metadatos se muestran en forma de un documento de lenguaje de descripción de servicios Web (WSDL) que se genera mediante el servicio web. Mediante el proveedor de referencia de servicio de Web de Microsoft WCF en Visual Studio de 2017 para agregar una referencia de servicio para el servicio web en una biblioteca estándar de .NET se puede generar el proxy.

Una alternativa para crear al proxy con el proveedor de referencia de servicio de Web de Microsoft WCF en Visual Studio de 2017 es usar la herramienta de utilidad de metadatos de ServiceModel (svcutil.exe). Para obtener más información, consulte [la herramienta de utilidad de metadatos de ServiceModel (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configuración del servidor Proxy

Configurar el proxy generado por lo general se toman dos argumentos de configuración (función de SOAP 1.1/ASMX o WCF) durante la inicialización: la `EndpointAddress` o la información de enlace asociado, tal como se muestra en el ejemplo siguiente:

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

Un enlace se utiliza para especificar el transporte, codificación y detalles protocolares requeridos para que las aplicaciones y servicios para comunicarse entre sí. El `BasicHttpBinding` especifica que se enviará los mensajes SOAP codificados por texto sobre el protocolo de transporte HTTP. Al especificar una dirección de punto de conexión permite a la aplicación para conectarse a distintas instancias del servicio WCF, siempre que hay varias instancias publicadas.

### <a name="consuming-the-proxy"></a>Consumir el Proxy

Las clases de proxy generado proporcionan métodos para utilizar los servicios web que usan el patrón de diseño del modelo de programación asincrónica (APM). En este modelo, una operación asincrónica se implementa como dos métodos denominados *BeginOperationName* y *EndOperationName*, que empiezan y terminan la operación asincrónica.

El *BeginOperationName* método comienza la operación asincrónica y devuelve un objeto que implementa el `IAsyncResult` interfaz. Después de llamar a *BeginOperationName*, una aplicación puede seguir ejecutando instrucciones en el subproceso que realiza la llamada, mientras la operación asincrónica tiene lugar en un subproceso del grupo.

Para cada llamada a *BeginOperationName*, la aplicación debe llamar a *EndOperationName* para obtener los resultados de la operación. El valor devuelto de *EndOperationName* es del mismo tipo devuelto por el método de servicio web sincrónico. En el ejemplo de código siguiente se muestra un ejemplo de esto:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

La biblioteca (TPL) puede simplificar el proceso de consumo de un par de métodos begin/end APM encapsulando las operaciones asincrónicas en la misma `Task` objeto. Esta encapsulación proporcionan varias sobrecargas de la `Task.Factory.FromAsync` método. Este método crea un `Task` que se ejecuta el `TodoServiceClient.EndGetTodoItems` una vez al método el `TodoServiceClient.BeginGetTodoItems` método se completa, con el `null` parámetro que indica que no hay datos se pasan a la `BeginGetTodoItems` delegar. Por último, el valor de la `TaskCreationOptions` enumeración especifica que debe usarse el comportamiento predeterminado para la creación y ejecución de tareas.

Para obtener más información acerca de APM, vea [modelo de programación asincrónica](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) y [TPL y .NET Framework programación asincrónica tradicional de](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) en MSDN.

Para obtener más información acerca de cómo consumir un servicio WCF, vea [consumir un servicio Web de Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Mediante la seguridad de transporte

Los servicios de WCF puede emplear la seguridad de nivel de transporte para proteger contra la intercepción de mensajes. La plataforma Xamarin admite los enlaces que emplean la seguridad de nivel de transporte con SSL. Sin embargo, puede haber casos en los que puede que tenga la pila validar el certificado, que da lugar a comportamientos inesperados. La validación puede reemplazarse por registrar un `ServerCertificateValidationCallback` delegado antes de invocar el servicio, como se muestra en el ejemplo de código siguiente:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Esto mantiene el cifrado de transporte y pasar por alto la validación del certificado de servidor. Sin embargo, este enfoque no tiene en cuenta las preocupaciones de confianza asociadas al certificado eficazmente y puede no ser adecuado. Para obtener más información, consulte [utilizando respetuoso raíces de confianza](http://www.mono-project.com/UsingTrustedRootsRespectfully) en [mono-project.com](http://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Mediante la seguridad de credencial de cliente

Los servicios de WCF también pueden requerir los clientes de servicio para autenticarse mediante las credenciales. La plataforma Xamarin no admite el protocolo WS-Security, que permite a los clientes enviar credenciales en el sobre del mensaje SOAP. Sin embargo, la plataforma Xamarin es compatible con la capacidad de enviar las credenciales de autenticación básica HTTP al servidor mediante la especificación de la correspondiente `ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

A continuación, se pueden especificar las credenciales de autenticación básica:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

En el ejemplo anterior, si recibe el mensaje "Que se quedó sin camas elásticas de tipo 0" puede aumentar el número de camas elásticas de tipo 0 agregando el `–aot “trampolines={number of trampolines}”` argumento pasado a la compilación. Para obtener más información, consulte [solución de problemas](~/ios/troubleshooting/troubleshooting.md#trampolines).

Para obtener más información acerca de la autenticación básica HTTP, aunque en el contexto de un servicio web REST, consulte [autenticar un servicio Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="summary"></a>Resumen

Esta guía muestra cómo utilizar tecnologías de servicios web diferente. Los temas tratados son comunicarse con servicios de REST, servicios de Windows Communication Foundation y servicios SOAP.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de servicios Web](https://developer.xamarin.com/samples/mobile/WebServices/WebServiceSamples/)
- [Servicios Web de Xamarin.Forms](~/xamarin-forms/data-cloud/index.md)
- [Herramienta de utilidad de metadatos de ServiceModel (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](http://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
