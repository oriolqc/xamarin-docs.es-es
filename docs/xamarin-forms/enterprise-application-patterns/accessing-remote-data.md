---
title: Acceso a datos remotos
description: Este capítulo explica cómo la aplicación móvil de eShopOnContainers tiene acceso a datos de los microservicios en contenedores.
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 3a46b939fa87cd6535c9f86c46981c098542e7c9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105485"
---
# <a name="accessing-remote-data"></a>Acceso a datos remotos

Muchas soluciones modernas basadas en web hacen uso de servicios web, hospedadas por servidores web, para proporcionar funcionalidad de cliente remoto a aplicaciones. Las operaciones que expone un servicio web constituyen una API web.

Las aplicaciones cliente deben ser capaces de usar la API web sin necesidad de saber cómo se implementan los datos o las operaciones que expone la API. Esto requiere que la API se rija por las normas comunes que permiten a un servicio web y aplicación de cliente acuerden qué formatos de datos que se usarán y la estructura de los datos que se intercambian entre las aplicaciones cliente y el servicio web.

## <a name="introduction-to-representational-state-transfer"></a>Introducción a la transferencia de estado representacional

Representational State Transfer (REST) es un estilo de arquitectura para la creación de sistemas distribuidos basados en hipermedia. Una ventaja importante del modelo REST es que se basa en estándares abiertos y no vincula la implementación del modelo o las aplicaciones cliente que acceden a ellos a cualquier implementación específica. Por lo tanto, un servicio web REST podría implementarse mediante Microsoft ASP.NET Core MVC y se podrían desarrollar aplicaciones cliente mediante cualquier lenguaje y conjunto de herramientas que puede generar solicitudes HTTP y analizar las respuestas HTTP.

El modelo REST usa un esquema de navegación para representar los objetos y servicios a través de una red, que se conoce como recursos. Los sistemas que implementan REST normalmente usan el protocolo HTTP para transmitir solicitudes de acceso a estos recursos. En tales sistemas, una aplicación cliente envía una solicitud en forma de un URI que identifica un recurso y un método HTTP (por ejemplo, GET, POST, PUT o DELETE) que indica la operación que se realizará en ese recurso. El cuerpo de la solicitud HTTP contiene los datos necesarios para realizar la operación.

> [!NOTE]
> REST define un modelo de solicitud sin estado. Por lo tanto, las solicitudes HTTP deben ser independientes y pueden producirse en cualquier orden.

Solicitud de la respuesta de una REST hace uso de códigos de estado HTTP estándar. Por ejemplo, una solicitud que devuelve datos válidos debe incluir el código de respuesta HTTP 200 (correcto), mientras que una solicitud que no se puede encontrar o eliminar un recurso especificado debe devolver una respuesta que incluye el código de estado HTTP 404 (no encontrado).

Una API web RESTful expone un conjunto de recursos conectados y proporciona las operaciones básicas que permiten a una aplicación manipular esos recursos y navegar fácilmente entre ellos. Por este motivo, los URI que constituyen una API web RESTful típica están orientados a los datos que expone y usar las funciones proporcionadas por HTTP para operar con estos datos.

Los datos incluidos por una aplicación cliente en una solicitud HTTP y los correspondientes mensajes de respuesta del servidor web, podrían presentarse en una variedad de formatos, conocida como tipos de medios. Cuando una aplicación cliente envía una solicitud que devuelve los datos en el cuerpo de un mensaje, puede especificar los tipos de medios que puede controlar en el `Accept` encabezado de la solicitud. Si el servidor web admite este tipo de medio, puede responder con una respuesta que incluye el `Content-Type` encabezado que especifica el formato de los datos en el cuerpo del mensaje. A continuación, es responsabilidad de la aplicación cliente para analizar el mensaje de respuesta e interpretar los resultados en el cuerpo del mensaje de forma adecuada.

Para obtener más información acerca de REST, consulte [diseño de API](/azure/architecture/best-practices/api-design/) y [implementación de la API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Usar API de RESTful

La aplicación móvil de eShopOnContainers usa el patrón Model-View-ViewModel (MVVM) y los elementos del modelo de la representen patrón las entidades de dominio que se usan en la aplicación. Las clases de controlador y el repositorio en la aplicación de referencia eShopOnContainers aceptan y devuelven muchos de estos objetos de modelo. Por lo tanto, se usan como objetos de transferencia de datos (dto) que contienen todos los datos que se pasan entre la aplicación móvil y los microservicios en contenedores. La principal ventaja de usar dto para pasar los datos y recibir datos de un servicio web es mediante la transmisión de más datos en una única llamada remota, la aplicación puede reducir el número de llamadas remotas que deben realizarse.

### <a name="making-web-requests"></a>Realización de las solicitudes web

La aplicación móvil de eShopOnContainers utiliza la `HttpClient` clase para realizar solicitudes a través de HTTP con JSON que se va a usar como el tipo de medio. Esta clase proporciona la funcionalidad para enviar de forma asincrónica las solicitudes HTTP y recibir respuestas HTTP de un URI identifica el recurso. La `HttpResponseMessage` clase representa un mensaje de respuesta HTTP recibido desde una API de REST después de realizar una solicitud HTTP. Contiene información acerca de la respuesta, incluido el código de estado, los encabezados y cualquier cuerpo. El `HttpContent` clase representa el cuerpo HTTP y encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer utilizando cualquiera de los `ReadAs` métodos, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, según el formato de los datos.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Realizar una solicitud GET

La `CatalogService` clase se utiliza para administrar el proceso de recuperación de datos desde el microservicio de catálogo. En el `RegisterDependencies` método en el `ViewModelLocator` (clase), el `CatalogService` clase se registra como una asignación de tipo con el `ICatalogService` tipo con el contenedor de inserción de dependencia de Autofac. A continuación, cuando una instancia de la `CatalogViewModel` se crea una clase, su constructor acepta un `ICatalogService` escribe, que se resuelve como Autofac, devolver una instancia de la `CatalogService` clase. Para obtener más información acerca de la inserción de dependencias, consulte [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-1 se muestra la interacción de las clases que leen datos del catálogo desde el microservicio de catálogo para su visualización en el `CatalogView`.

[![](accessing-remote-data-images/catalogdata.png "Recuperación de datos desde el microservicio de catálogo")](accessing-remote-data-images/catalogdata-large.png#lightbox "recuperar datos desde el microservicio de catálogo")

**Figura 10-1**: recuperación de datos desde el microservicio de catálogo

Cuando el `CatalogView` se navega a, el `OnInitialize` método en el `CatalogViewModel` se llama a la clase. Este método recupera datos del catálogo de microservicio de catálogo, como se muestra en el ejemplo de código siguiente:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Este método llama a la `GetCatalogAsync` método de la `CatalogService` instancia insertado en el `CatalogViewModel` por Autofac. El siguiente ejemplo de código muestra la `GetCatalogAsync` método:

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

Este método genera el identificador URI que identifica el recurso de la solicitud se enviará a y utiliza el `RequestProvider` clase para invocar el método HTTP GET en el recurso, antes de devolver los resultados a la `CatalogViewModel`. La `RequestProvider` clase contiene la funcionalidad que envía una solicitud en forma de un URI que identifica un recurso, un método HTTP que indica la operación que se realizará en ese recurso, y un cuerpo que contiene todos los datos necesarios para realizar la operación. Para obtener información acerca de cómo los `RequestProvider` clase se inserta en la `CatalogService class`, consulte [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

El siguiente ejemplo de código muestra la `GetAsync` método en el `RequestProvider` clase:

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Este método llama a la `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuados. A continuación, envía una solicitud GET asincrónica en el recurso identificado por el identificador URI, con la respuesta que se almacenan en el `HttpResponseMessage` instancia. El `HandleResponse` , a continuación, se invoca el método, que produce una excepción si la respuesta no incluye un código de estado HTTP correcto. A continuación, se lee la respuesta como una cadena, convertida a partir de JSON a un `CatalogRoot` de objetos y devuelve al `CatalogService`.

El `CreateHttpClient` método se muestra en el ejemplo de código siguiente:

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

Este método crea una nueva instancia de la `HttpClient` clase y se establece la `Accept` encabezado de las solicitudes realizadas por el `HttpClient` instancia a `application/json`, lo que indica que espera que el contenido de cualquier respuesta tenga el formato mediante JSON. A continuación, si un token de acceso que se pasó como argumento a la `CreateHttpClient` método, se agrega a la `Authorization` encabezado de las solicitudes realizadas por el `HttpClient` instancia, el prefijo con la cadena `Bearer`. Para obtener más información acerca de la autorización, consulte [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Cuando el `GetAsync` método en el `RequestProvider` clase llamadas `HttpClient.GetAsync`, el `Items` método en el `CatalogController` se invoca la clase en el proyecto Catalog.API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

Este método recupera los datos del catálogo de SQL database mediante Entity Framework y lo devuelve como un mensaje de respuesta que incluye un código de estado HTTP correcto y una colección de JSON con formato `CatalogItem` instancias.

#### <a name="making-a-post-request"></a>Realizar una solicitud POST

La `BasketService` clase se utiliza para administrar la recuperación de datos y el proceso de actualización con el microservicio de cesta. En el `RegisterDependencies` método en el `ViewModelLocator` (clase), el `BasketService` clase se registra como una asignación de tipo con el `IBasketService` tipo con el contenedor de inserción de dependencia de Autofac. A continuación, cuando una instancia de la `BasketViewModel` se crea una clase, su constructor acepta un `IBasketService` escribe, que se resuelve como Autofac, devolver una instancia de la `BasketService `clase. Para obtener más información acerca de la inserción de dependencias, consulte [Introducción a la inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-2 se muestra la interacción de las clases que envían los datos de la cesta de compras mostrados por la `BasketView`, el microservicio de cesta de la compra.

[![](accessing-remote-data-images/basketdata.png "Envío de datos para el microservicio de cesta")](accessing-remote-data-images/basketdata-large.png#lightbox "envío de datos para el microservicio de cesta")

**Figura 10-2**: envío de datos para el microservicio de cesta

Cuando se agrega un elemento a la cesta de compra, el `ReCalculateTotalAsync` método en el `BasketViewModel` se llama a la clase. Este método actualiza el valor total de elementos de la cesta y envía los datos de la cesta de compras para el microservicio de cesta, como se muestra en el siguiente ejemplo de código:

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

Este método llama a la `UpdateBasketAsync` método de la `BasketService` instancia insertado en el `BasketViewModel` por Autofac. El método siguiente se muestra el `UpdateBasketAsync` método:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Este método genera el identificador URI que identifica el recurso de la solicitud se enviará a y utiliza el `RequestProvider` clase para invocar el método HTTP POST en el recurso, antes de devolver los resultados a la `BasketViewModel`. Tenga en cuenta que un token de acceso obtenida IdentityServer durante el proceso de autenticación, es necesario para autorizar las solicitudes para el microservicio de cesta. Para obtener más información acerca de la autorización, consulte [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

En el ejemplo de código siguiente se muestra uno de los `PostAsync` métodos en el `RequestProvider` clase:

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Este método llama a la `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuados. A continuación, envía una solicitud POST asincrónica en el recurso identificado por el identificador URI, con los datos serializados de la cesta de compras que se envían en formato JSON y la respuesta que se almacenan en el `HttpResponseMessage` instancia. El `HandleResponse` , a continuación, se invoca el método, que produce una excepción si la respuesta no incluye un código de estado HTTP correcto. A continuación, se lee la respuesta como una cadena, convertida a partir de JSON a un `CustomerBasket` de objetos y devuelve a la `BasketService`. Para obtener más información sobre la `CreateHttpClient` método, consulte [realizar una solicitud GET de](#making_a_get_request).

Cuando el `PostAsync` método en el `RequestProvider` clase llamadas `HttpClient.PostAsync`, el `Post` método en el `BasketController` se invoca la clase en el proyecto Basket.API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Este método utiliza una instancia de la `RedisBasketRepository` clase para conservar los datos de la cesta de compras en la caché en Redis y lo devuelve como un mensaje de respuesta que incluye un código de estado HTTP de éxito y un archivo JSON con formato `CustomerBasket` instancia.

#### <a name="making-a-delete-request"></a>Realizar una solicitud DELETE

Figura 10-3 se muestran las interacciones de clases que eliminarán datos de la cesta de compras desde el microservicio de cesta de la `CheckoutView`.

![](accessing-remote-data-images/checkoutdata.png "Datos eliminando el desde el microservicio de cesta")

**Figura 10-3**: eliminar datos en el microservicio de cesta

Cuando se invoca el proceso de formalización, la `CheckoutAsync` método en el `CheckoutViewModel` se llama a la clase. Este método crea un nuevo pedido, antes de borrar la cesta de compra, como se muestra en el ejemplo de código siguiente:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Este método llama a la `ClearBasketAsync` método de la `BasketService` instancia insertado en el `CheckoutViewModel` por Autofac. El método siguiente se muestra el `ClearBasketAsync` método:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Este método genera el identificador URI que identifica el recurso que se enviará la solicitud a y utiliza el `RequestProvider` clase para invocar el método HTTP DELETE en el recurso. Tenga en cuenta que un token de acceso obtenida IdentityServer durante el proceso de autenticación, es necesario para autorizar las solicitudes para el microservicio de cesta. Para obtener más información acerca de la autorización, consulte [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

El siguiente ejemplo de código muestra la `DeleteAsync` método en el `RequestProvider` clase:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Este método llama a la `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuados. A continuación, envía una solicitud de eliminación asincrónica en el recurso identificado por el URI. Para obtener más información sobre la `CreateHttpClient` método, consulte [realizar una solicitud GET de](#making_a_get_request).

Cuando el `DeleteAsync` método en el `RequestProvider` clase llamadas `HttpClient.DeleteAsync`, el `Delete` método en el `BasketController` se invoca la clase en el proyecto Basket.API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Este método utiliza una instancia de la `RedisBasketRepository` clase para eliminar los datos de la cesta de compras desde la caché en Redis.

## <a name="caching-data"></a>Almacenar datos en caché

Se puede mejorar el rendimiento de una aplicación almacenando en caché datos de acceso con frecuencia en almacenamiento rápido ubicado cerca la aplicación. Si el almacenamiento rápido se encuentra más cerca de la aplicación que el origen original, el almacenamiento en caché puede mejorar significativamente la respuesta si se agota el tiempo de cuando se recuperan datos.

La forma más común de almacenamiento en caché es el almacenamiento en caché plano, donde una aplicación recupera los datos haciendo referencia a la memoria caché. Si los datos no están en la memoria caché, ha recuperado desde el almacén de datos y agrega a la caché. Pueden implementar aplicaciones de almacenamiento en caché de lectura simultánea con el patrón cache-aside. Este patrón se determina si el elemento está actualmente en la memoria caché. Si el elemento no está en la memoria caché, ha leído desde el almacén de datos y agrega a la caché. Para obtener más información, consulte el [Cache-Aside](/azure/architecture/patterns/cache-aside/) patrón.

> [!TIP]
> Almacenar en caché los datos que se leen con frecuencia y que cambian con poca frecuencia. Estos datos se pueden agregar a la memoria caché a petición la primera vez que se recupera mediante una aplicación. Esto significa que la aplicación debe capturar los datos de una sola vez desde el almacén de datos, y que el posterior acceso se puede satisfacer mediante el uso de la memoria caché.

Las aplicaciones distribuidas, como eShopOnContainers hacen referencia a la aplicación, deben proporcionar una o ambas de las memorias caché siguientes:

-   Una memoria caché compartida, que se puede acceder mediante varios procesos o equipos.
-   Una caché privada, donde los datos se guardan localmente en el dispositivo que ejecuta la aplicación.

La aplicación móvil de eShopOnContainers utiliza una caché privada, donde los datos se guardan localmente en el dispositivo que se está ejecutando una instancia de la aplicación. Para obtener información acerca de la memoria caché utilizada por la aplicación de referencia eShopOnContainers, consulte [Microservicios de. NET: arquitectura para aplicaciones .NET en contenedor](https://aka.ms/microservicesebook).

> [!TIP]
> Piense en la memoria caché como un almacén de datos transitorios que podría desaparecer en cualquier momento. Asegúrese de que los datos se mantienen en el almacén de datos original, así como la memoria caché. A continuación, se minimiza el riesgo de perder datos si la caché deja de estar disponible.

### <a name="managing-data-expiration"></a>Administrar la expiración de los datos

No resulta práctico que puede esperar que los datos almacenados en caché siempre será coherentes con los datos originales. Datos de almacén de datos original pueden cambiar después de se haya almacenado en caché, haciendo que los datos almacenados en caché se vuelvan obsoletos. Por lo tanto, las aplicaciones deben implementar una estrategia que ayuda a garantizar que los datos en la memoria caché son tan actualizados como sea posible, pero también puede detectar y tratar situaciones que surgen cuando los datos en la memoria caché ha quedado obsoletos. Mecanismos de almacenamiento en caché más habilitar la memoria caché deberá estar configurado para caducar los datos y, por lo tanto, reducen el período para el que los datos podrían ser actualizados.

> [!TIP]
> Establecer una expiración predeterminada al configurar una memoria caché de tiempo. Muchas de las memorias caché implementan expiración, que invalida los datos y lo quita de la memoria caché si no se accede durante un período especificado. Sin embargo, debe tener cuidado al elegir el período de expiración. Si se realiza es demasiado corta, datos expirará demasiado rápido y se reducirá las ventajas del almacenamiento en caché. Si se realiza demasiado largo, los riesgos de datos que se vuelvan obsoletos. Por lo tanto, la hora de expiración debe coincidir con el patrón de acceso para las aplicaciones que usan los datos.

Cuando los datos almacenados en caché expiran, se debe quitar de la memoria caché y la aplicación debe recuperar los datos de los datos originales almacenarán y lo colocan en la caché.

También es posible que una memoria caché se rellene si se permiten datos va a permanecer durante un período demasiado largo. Por lo tanto, las solicitudes para agregar nuevos elementos a la memoria caché podrían ser necesario quitar algunos elementos en un proceso conocido como *expulsión*. Servicios de almacenamiento en caché normalmente expulsan los datos de forma usados menos recientemente. Sin embargo, hay otras directivas de expulsión, incluidos los usados más recientemente y primero en el primero en salir. Para obtener más información, consulte [almacenamiento en caché de instrucciones](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>Almacenamiento en caché de imágenes

La aplicación móvil de eShopOnContainers consume las imágenes de producto remoto que se benefician del almacenamiento en caché. Estas imágenes se muestran mediante el [ `Image` ](xref:Xamarin.Forms.Image) control y el `CachedImage` control proporcionado por el [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) biblioteca.

Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image) control admite el almacenamiento en caché de imágenes descargadas. Almacenamiento en caché está habilitado de forma predeterminada y almacenará la imagen localmente durante 24 horas. Además, la hora de expiración se puede configurar con el [ `CacheValidity` ](xref:Xamarin.Forms.UriImageSource.CacheValidity) propiedad. Para obtener más información, consulte [descargar imagen de almacenamiento en caché](~/xamarin-forms/user-interface/images.md#downloaded-image-caching).

Del FFImageLoading `CachedImage` control es un sustituto de Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image) control, que proporciona propiedades adicionales que habilitan la funcionalidad adicional. Entre esta funcionalidad, el control proporciona configurable de almacenamiento en caché, mientras que admiten el error y cargar los marcadores de posición de imagen. El ejemplo de código siguiente muestra cómo se usa la aplicación móvil de eShopOnContainers el `CachedImage` controlar en el `ProductTemplate`, que es la plantilla de datos utilizada por el [ `ListView` ](xref:Xamarin.Forms.ListView) controlar en el `CatalogView`:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

El `CachedImage` conjuntos de controles el `LoadingPlaceholder` y `ErrorPlaceholder` propiedades a las imágenes específicas de la plataforma. El `LoadingPlaceholder` propiedad especifica la imagen que se mostrará mientras la imagen especificada por el `Source` se recupera la propiedad y el `ErrorPlaceholder` propiedad especifica la imagen que se mostrará si se produce un error al intentar recuperar la imagen especificado por el `Source` propiedad.

Como el nombre implica, el `CachedImage` control almacena en caché remotas imágenes en el dispositivo durante el tiempo especificado por el valor de la `CacheDuration` propiedad. Cuando este valor de propiedad no se establece explícitamente, se aplica el valor predeterminado de 30 días.

## <a name="increasing-resilience"></a>Aumentar la resistencia

Todas las aplicaciones que se comunican con los recursos y servicios remotos deben ser sensibles a errores transitorios. Los errores transitorios incluyen la pérdida momentánea de conectividad de red a los servicios, la indisponibilidad temporal de un servicio o tiempos de espera que surgen cuando un servicio está ocupado. Estos errores suelen ser corrección automática, y si se repite la acción tras un retraso adecuado es probable que tenga éxito.

Los errores transitorios pueden tener un impacto enorme en la calidad percibida de una aplicación, incluso si se ha probado exhaustivamente en todas las circunstancias previsibles. Para asegurarse de que una aplicación que se comunica con los servicios remoto funcione de manera confiable, debe ser capaz de hacerlo siguiente:

-   Detectar errores cuando se producen y determinar si los errores suelen ser transitorio.
-   Si determina que el error es probable que sea transitorio y realizar un seguimiento de la cantidad de veces que se vuelve a intentar la operación, vuelva a intentar la operación.
-   Usar una estrategia adecuada de reintentos, que especifica el número de reintentos, el retraso entre cada intento y las acciones a realizar después de un intento fallido.

Este control de errores transitorios puede lograrse ajustando todos los intentos de acceso a un servicio remoto en el código que implementa el patrón de reintento.

### <a name="retry-pattern"></a>Patrón Retry

Si una aplicación detecta un error al intentar enviar una solicitud a un servicio remoto, puede tratar el error en cualquiera de las maneras siguientes:

-   Volver a intentar la operación. La aplicación se vuelva a intentar la solicitud con error inmediatamente.
-   Reintentando la operación después de un retraso. La aplicación debe esperar una cantidad adecuada de tiempo antes de reintentar la solicitud.
-   Cancelando la operación. La aplicación debe cancelar la operación y notificar una excepción.

La estrategia de reintento debe optimizarse para que coincida con los requisitos empresariales de la aplicación. Por ejemplo, es importante optimizar el número de reintentos y el intervalo de reintento que la operación que se está intenta. Si la operación forma parte de una interacción del usuario, el intervalo de reintentos debe ser corto y solo unos pocos reintentos para evitar hacer que los usuarios a esperar una respuesta. Si la operación forma parte de un flujo de trabajo de larga ejecución, donde la cancelación o reiniciar el flujo de trabajo es lento o costoso, conviene esperar más tiempo entre intentos y efectuar más reintentos.

> [!NOTE]
> Una estrategia de reintento agresiva con un retraso mínimo entre intentos y un gran número de reintentos, podría degradar un servicio remoto que se está ejecutando en su capacidad o próximo a. Además, esta estrategia de reintentos también podría afectar a la capacidad de respuesta de la aplicación si continuamente intenta realizar una operación con error.

Si una solicitud sigue sin funcionar después de un número de reintentos, es mejor para la aplicación para evitar que las solicitudes posteriores en el mismo recurso y para notificar un error. A continuación, tras un período establecido, la aplicación puede realizar una o varias solicitudes al recurso para ver si son correctas. Para obtener más información, consulte [patrón Circuit Breaker](#circuit_breaker_pattern).

> [!TIP]
> Nunca implemente un mecanismo de reintento infinito. Use un número finito de reintentos o implemente el [disyuntor](/azure/architecture/patterns/circuit-breaker/) patrón para permitir que un servicio recuperar.

La aplicación móvil de eShopOnContainers implementa actualmente el patrón de reintento al realizar solicitudes web RESTful. Sin embargo, el `CachedImage` control, que proporciona el [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) biblioteca admite el control de errores transitorios Reintentando la carga de la imagen. Si se produce un error en la carga de imágenes, se realizarán más intentos. El número de intentos especificado por el `RetryCount` propiedad y reintentos, se producirán después de un retraso especificado por el `RetryDelay` propiedad. Si estos valores de propiedad no se establecen explícitamente, su valor predeterminado es se aplican los valores: 3 para el `RetryCount` propiedad y 250 ms para el `RetryDelay` propiedad. Para obtener más información sobre la `CachedImage` control, vea [almacenamiento en caché de imágenes](#caching_images).

La aplicación de referencia eShopOnContainers implementa el patrón de reintento. Para obtener más información, incluida una discusión acerca de cómo combinar el patrón de reintento con el `HttpClient` de clases, vea [Microservicios de. NET: arquitectura para aplicaciones .NET en contenedor](https://aka.ms/microservicesebook).

Para obtener más información sobre el patrón de reintento, consulte el [vuelva a intentar](/azure/architecture/patterns/retry/) patrón.

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Patrón de disyuntor

En algunas situaciones, pueden producirse errores debidos a eventos anticipados que tardan más tiempo para corregir. Estos errores pueden oscilar entre una pérdida parcial de conectividad y el error completo de un servicio. En estas situaciones, es inútil para una aplicación volver a intentar una operación que no es probable que se realiza correctamente y en su lugar, debe aceptar que la operación ha fallado y controlar este error en consecuencia.

El patrón circuit breaker puede impedir que una aplicación intente repetidamente ejecutar una operación que es propenso a errores, al mismo tiempo que la aplicación detectar si se ha resuelto el error.

> [!NOTE]
> El propósito del patrón de disyuntor es diferente del patrón de reintento. El patrón de reintento permite que una aplicación reintentar la operación con la expectativa de que se podrá ejecutar correctamente. El patrón de interruptor impide que una aplicación realizando una operación que es propenso a errores.

Un disyuntor actúa como un proxy para las operaciones que puede producir un error. El proxy debe supervisar el número de errores recientes que se han producido y utilizar esta información para decidir si se permite la operación para continuar, o para devolver una excepción inmediatamente.

La aplicación móvil de eShopOnContainers no implementa el patrón circuit breaker actualmente. Sin embargo, no el de eShopOnContainers. Para obtener más información, consulte [Microservicios de. NET: arquitectura para aplicaciones .NET en contenedor](https://aka.ms/microservicesebook).

> [!TIP]
> Combinar los patrones de interruptor y reinténtelo. Una aplicación puede combinar los patrones de reintento y el interruptor de circuito mediante el patrón de reintento para invocar una operación a través de un disyuntor. Sin embargo, la lógica de reintento debe ser sensible a las excepciones devueltas por el disyuntor y dejar de reintentar si el interruptor indica que un error no es transitorio.

Para obtener más información sobre el patrón de disyuntor, consulte el [disyuntor](/azure/architecture/patterns/circuit-breaker/) patrón.

## <a name="summary"></a>Resumen

Muchas soluciones modernas basadas en web hacen uso de servicios web, hospedadas por servidores web, para proporcionar funcionalidad de cliente remoto a aplicaciones. Las operaciones que expone un servicio web constituyen una API web y aplicaciones de cliente deben ser capaces de usar la API web sin necesidad de saber cómo se implementan los datos o las operaciones que expone la API.

Se puede mejorar el rendimiento de una aplicación almacenando en caché datos de acceso con frecuencia en almacenamiento rápido ubicado cerca la aplicación. Pueden implementar aplicaciones de almacenamiento en caché de lectura simultánea con el patrón cache-aside. Este patrón se determina si el elemento está actualmente en la memoria caché. Si el elemento no está en la memoria caché, ha leído desde el almacén de datos y agrega a la caché.

Cuando se comunica con las API web, las aplicaciones deben ser sensibles a errores transitorios. Los errores transitorios incluyen la pérdida momentánea de conectividad de red a los servicios, la indisponibilidad temporal de un servicio o tiempos de espera que surgen cuando un servicio está ocupado. Estos errores suelen ser corrección automática, y si se repite la acción tras un retraso adecuado, a continuación, es probable que se realice correctamente. Por lo tanto, las aplicaciones deben ajustar todos los intentos de acceso a una API web en el código que implementa un mecanismo de control de errores transitorios.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
