---
title: Acceso a datos remotos
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0f29377c52dcf65247c11079178efe0745c654eb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="accessing-remote-data"></a>Acceso a datos remotos

Muchas soluciones modernas basadas en web hacen uso de servicios web, hospedadas por servidores web, para proporcionar funcionalidad de cliente remoto a aplicaciones. Las operaciones que expone un servicio web constituyen una API web.

Las aplicaciones cliente deben ser capaces de usar la API web sin conocer cómo se implementan las operaciones que expone la API o datos. Esto requiere que la API se rige por los estándares comunes que permiten a un servicio de aplicación y web de cliente coincidir con los formatos de datos que se usará y la estructura de los datos que se intercambian entre las aplicaciones de cliente y el servicio web.

## <a name="introduction-to-representational-state-transfer"></a>Introducción a la transferencia de estado representacional

Representational State Transfer (REST) es un estilo de arquitectura para la creación de sistemas distribuidos en función de hipermedia. Una ventaja importante del modelo REST es que se basa en estándares abiertos y no enlace la implementación del modelo o de las aplicaciones cliente que tienen acceso a él a cualquier implementación específica. Por lo tanto, se pueden implementar un servicio web REST mediante núcleo de Microsoft ASP.NET MVC y pueden desarrollar aplicaciones de cliente mediante cualquier lenguaje y un conjunto de herramientas que puede generar las solicitudes HTTP y analizar las respuestas HTTP.

El modelo REST usa un esquema de navegación para representar objetos y servicios a través de una red, que se conoce como recursos. Sistemas que implementan REST normalmente utilizan el protocolo HTTP para transmitir solicitudes para tener acceso a estos recursos. En estos sistemas, una aplicación cliente envía una solicitud en forma de un URI que identifica un recurso y un método HTTP (por ejemplo, GET, POST, PUT o DELETE) que indica la operación que se realizará en ese recurso. El cuerpo de la solicitud HTTP contiene los datos necesarios para realizar la operación.

> [!NOTE]
> REST define un modelo de solicitud sin estado. Por lo tanto, las solicitudes HTTP deben ser independientes y se pueden producir en cualquier orden.

La respuesta desde un resto solicitar facilita el uso de códigos de estado HTTP estándar. Por ejemplo, una solicitud que devuelve datos válido debe incluir el código de respuesta HTTP 200 (correcto), mientras que una solicitud que no se puede encontrar o eliminar un recurso especificado debe devolver una respuesta que incluye el código de estado HTTP 404 (no encontrado).

Una API web RESTful expone un conjunto de recursos conectados y proporciona las operaciones básicas que permiten a una aplicación manipular esos recursos y navegar fácilmente entre ellos. Por esta razón, los URI que constituyen una API web RESTful típico están orientados a los datos que expone y utilice las funciones proporcionadas por HTTP para operar en estos datos.

Los datos incluidos en una aplicación de cliente en una solicitud HTTP y los mensajes de respuesta correspondiente desde el servidor web, pueden presentarse en una variedad de formatos, conocida como tipos de medios. Cuando una aplicación cliente envía una solicitud que devuelve datos en el cuerpo de un mensaje, puede especificar los tipos de medios que puede controlar en el `Accept` encabezado de la solicitud. Si el servidor web es compatible con este tipo de medio, puede responder con una respuesta que incluye el `Content-Type` encabezado que especifica el formato de los datos en el cuerpo del mensaje. A continuación, es responsabilidad de la aplicación cliente para analizar el mensaje de respuesta e interpretar los resultados en el cuerpo del mensaje de forma adecuada.

Para obtener más información acerca de REST, consulte [diseño de la API](/azure/architecture/best-practices/api-design/) y [implementación de la API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>API de REST de consumo

La aplicación móvil eShopOnContainers usa el modelo Model-View-ViewModel (MVVM) y los elementos del modelo de patrón representan las entidades de dominio que se usan en la aplicación. Las clases de controlador y del repositorio en la aplicación de referencia eShopOnContainers aceptan y devuelven muchos de estos objetos de modelo. Por lo tanto, se utilizan como objetos de transferencia de datos (dto) que contienen todos los datos que se pasan entre la aplicación móvil y el microservicios en contenedores. La principal ventaja de utilizar dto para pasar datos a y recibir datos desde un servicio web es mediante la transmisión de más datos en una sola llamada remota, la aplicación puede reducir el número de llamadas remotas que deben realizarse.

### <a name="making-web-requests"></a>Realización de las solicitudes web

Los usos de la aplicación móvil eShopOnContainers la `HttpClient` clase para realizar solicitudes a través de HTTP con JSON que se va a usar como el tipo de medio. Esta clase proporciona la funcionalidad para enviar de forma asincrónica las solicitudes HTTP y recibir respuestas HTTP desde un URI identifica el recurso. La `HttpResponseMessage` clase representa un mensaje de respuesta HTTP recibido a través de una API de REST después de que se ha realizado una solicitud HTTP. Contiene información acerca de la respuesta, incluido el código de estado, los encabezados y cualquier cuerpo. El `HttpContent` clase representa el cuerpo HTTP y encabezados de contenido, como `Content-Type` y `Content-Encoding`. El contenido se puede leer utilizando cualquiera de los `ReadAs` métodos, como `ReadAsStringAsync` y `ReadAsByteArrayAsync`, según el formato de los datos.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Realizar una solicitud GET

La `CatalogService` clase se utiliza para administrar el proceso de recuperación de datos desde el catálogo de microservicio. En el `RegisterDependencies` método en el `ViewModelLocator` (clase), el `CatalogService` clase se registra como una asignación de tipo con el `ICatalogService` tipo con el contenedor de inyección de dependencia de Autofac. A continuación, cuando una instancia de la `CatalogViewModel` se crea la clase, su constructor acepta una `ICatalogService` escribe, que resuelve Autofac, devolver una instancia de la `CatalogService` clase. Para obtener más información acerca de la inserción de dependencias, consulte [Introducción a la inyección de dependencia](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

La figura 10-1 muestra la interacción de las clases que leen datos del catálogo desde la microservicio de catálogo para su visualización en el `CatalogView`.

[![](accessing-remote-data-images/catalogdata.png "Recuperación de datos desde el catálogo de microservicio")](accessing-remote-data-images/catalogdata-large.png#lightbox "recuperar datos desde el catálogo de microservicio")

**Figura 10-1**: recuperación de datos desde el catálogo de microservicio

Cuando el `CatalogView` se navega a, el `OnInitialize` método en la `CatalogViewModel` clase se denomina. Este método recupera datos del catálogo de microservicio de catálogo, como se muestra en el ejemplo de código siguiente:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Este método llama a la `GetCatalogAsync` método de la `CatalogService` instancia insertado en el `CatalogViewModel` por Autofac. El siguiente ejemplo de código muestra la `GetCatalogAsync` método:

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

Este método genera el identificador URI que identifica el recurso de la solicitud se enviará a y utiliza el `RequestProvider` clase para invocar el método HTTP GET en el recurso antes de devolver los resultados a la `CatalogViewModel`. La `RequestProvider` clase contiene la funcionalidad que envía una solicitud en forma de un URI que identifica un recurso, un método HTTP que indica la operación que se realizará en ese recurso, y un cuerpo que contiene todos los datos necesarios para realizar la operación. Para obtener información acerca de cómo los `RequestProvider` clase se aplica en el `CatalogService class`, consulte [Introducción a la inyección de dependencia](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

El siguiente ejemplo de código muestra la `GetAsync` método en la `RequestProvider` clase:

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Este método llama a la `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuados. A continuación, envía una solicitud GET asincrónica al recurso identificado por el identificador URI, con la respuesta que se almacena en la `HttpResponseMessage` instancia. El `HandleResponse` , a continuación, se invoca el método, que produce una excepción si la respuesta no incluye un código de estado HTTP de éxito. A continuación, se lee la respuesta como una cadena, convertida a partir de JSON a un `CatalogRoot` de objetos y devuelve a la `CatalogService`.

El `CreateHttpClient` método se muestra en el ejemplo de código siguiente:

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

Este método crea una nueva instancia de la `HttpClient` clase y se establece la `Accept` encabezado de todas las solicitudes realizadas por el `HttpClient` instancia `application/json`, lo que indica que espera que el contenido de cualquier respuesta en formato mediante JSON. A continuación, si un token de acceso se ha pasado como argumento a la `CreateHttpClient` método, se agrega a la `Authorization` encabezado de todas las solicitudes realizadas por el `HttpClient` instancia, el prefijo con la cadena `Bearer`. Para obtener más información acerca de la autorización, consulte [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Cuando el `GetAsync` método en el `RequestProvider` clase llamadas `HttpClient.GetAsync`, `Items` método en el `CatalogController` se invoca la clase en el proyecto Catalog.API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

Este método recupera los datos del catálogo de la base de datos SQL mediante EntityFramework y lo devuelve como un mensaje de respuesta que incluye un código de estado HTTP de éxito y una colección de JSON con formato `CatalogItem` instancias.

#### <a name="making-a-post-request"></a>Realizar una solicitud POST

La `BasketService` clase se utiliza para administrar la recuperación de datos y proceso de actualización con el microservicio de la cesta de compra. En el `RegisterDependencies` método en el `ViewModelLocator` (clase), el `BasketService` clase se registra como una asignación de tipo con el `IBasketService` tipo con el contenedor de inyección de dependencia de Autofac. A continuación, cuando una instancia de la `BasketViewModel` se crea la clase, su constructor acepta una `IBasketService` escribe, que resuelve Autofac, devolver una instancia de la `BasketService `clase. Para obtener más información acerca de la inserción de dependencias, consulte [Introducción a la inyección de dependencia](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-2 se muestra la interacción de las clases que enviar los datos de la cesta de compras mostrados por el `BasketView`, a la cesta de la compra microservicio.

[![](accessing-remote-data-images/basketdata.png "Enviar datos a la cesta de microservicio")](accessing-remote-data-images/basketdata-large.png#lightbox "enviar datos a la microservicio de la cesta de compra")

**Figura 10-2**: enviar datos a la microservicio de la cesta de compra

Cuando se agrega un elemento a la cesta de compra, la `ReCalculateTotalAsync` método en la `BasketViewModel` clase se denomina. Este método actualiza el valor total de elementos de la cesta y envía los datos de la cesta de compra a microservicio de la cesta de compra, como se muestra en el ejemplo de código siguiente:

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

Este método llama a la `UpdateBasketAsync` método de la `BasketService` instancia insertado en el `BasketViewModel` por Autofac. El método siguiente se muestra el `UpdateBasketAsync` método:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Este método genera el identificador URI que identifica el recurso de la solicitud se enviará a y utiliza el `RequestProvider` clase para invocar el método HTTP POST en el recurso antes de devolver los resultados a la `BasketViewModel`. Tenga en cuenta que un token de acceso, obtenidos de IdentityServer durante el proceso de autenticación, es necesario para autorizar las solicitudes a la cesta de la compra microservicio. Para obtener más información acerca de la autorización, consulte [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

En el ejemplo de código siguiente se muestra uno de los `PostAsync` métodos en la `RequestProvider` clase:

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Este método llama a la `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuados. A continuación, envía una solicitud POST asincrónica al recurso identificado por el identificador URI, con los datos serializados de la cesta de compra se envían en formato JSON y la respuesta que se almacena en la `HttpResponseMessage` instancia. El `HandleResponse` , a continuación, se invoca el método, que produce una excepción si la respuesta no incluye un código de estado HTTP de éxito. A continuación, se lee la respuesta como una cadena, convertida a partir de JSON a un `CustomerBasket` de objetos y devuelve a la `BasketService`. Para obtener más información sobre la `CreateHttpClient` método, consulte [realizar una solicitud GET](#making_a_get_request).

Cuando el `PostAsync` método en el `RequestProvider` clase llamadas `HttpClient.PostAsync`, `Post` método en el `BasketController` se invoca la clase en el proyecto Basket.API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Este método usa una instancia de la `RedisBasketRepository` clase para conservar los datos de la cesta de compra a la caché de Redis y lo devuelve como un mensaje de respuesta que incluye un código de estado HTTP de éxito y JSON con formato `CustomerBasket` instancia.

#### <a name="making-a-delete-request"></a>Realizar una solicitud de eliminación

Figura 10-3 muestra las interacciones de clases que se eliminan los datos de la cesta de compras de microservicio de la cesta de compra, para la `CheckoutView`.

![](accessing-remote-data-images/checkoutdata.png "Datos de eliminar de la cesta de la compra microservicio")

**Figura 10-3**: eliminar datos de la cesta de la compra microservicio

Cuando se invoca el proceso de pago, el `CheckoutAsync` método en la `CheckoutViewModel` clase se denomina. Este método crea un nuevo pedido, antes de borrar la cesta de compra, como se muestra en el ejemplo de código siguiente:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Este método llama a la `ClearBasketAsync` método de la `BasketService` instancia insertado en el `CheckoutViewModel` por Autofac. El método siguiente se muestra el `ClearBasketAsync` método:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Este método genera el URI que identifica el recurso que se enviará la solicitud y utiliza la `RequestProvider` clase para invocar el método HTTP DELETE en el recurso. Tenga en cuenta que un token de acceso, obtenidos de IdentityServer durante el proceso de autenticación, es necesario para autorizar las solicitudes a la cesta de la compra microservicio. Para obtener más información acerca de la autorización, consulte [autorización](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

El siguiente ejemplo de código muestra la `DeleteAsync` método en la `RequestProvider` clase:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Este método llama a la `CreateHttpClient` método, que devuelve una instancia de la `HttpClient` clase con el conjunto de encabezados adecuados. A continuación, envía una solicitud de eliminación asincrónica al recurso identificado por el URI. Para obtener más información sobre la `CreateHttpClient` método, consulte [realizar una solicitud GET](#making_a_get_request).

Cuando el `DeleteAsync` método en el `RequestProvider` clase llamadas `HttpClient.DeleteAsync`, `Delete` método en el `BasketController` se invoca la clase en el proyecto Basket.API, que se muestra en el ejemplo de código siguiente:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Este método usa una instancia de la `RedisBasketRepository` clase para eliminar los datos de la cesta de compras desde la caché en Redis.

## <a name="caching-data"></a>Almacenar datos en caché

Se puede mejorar el rendimiento de una aplicación al almacenar en caché datos de acceso frecuente a almacenamiento rápido que se encuentra cierra la aplicación. Si el dispositivo de almacenamiento rápido se encuentra más cerca de la aplicación a la de origen original, el almacenamiento en caché puede mejorar considerablemente la respuesta el tiempo de espera cuando se recuperan datos.

La forma más común de almacenamiento en caché es lectura almacenamiento en caché, donde una aplicación recupera datos haciendo referencia a la memoria caché. Si los datos no se están en la memoria caché, ha recuperado desde el almacén de datos y agregados a la caché. Pueden implementar aplicaciones de almacenamiento en caché de lectura con el modelo de cache-aside. Este patrón determina si el elemento está actualmente en la memoria caché. Si el elemento no está en la memoria caché, ha leído desde el almacén de datos y se agregan a la caché. Para obtener más información, consulte el [Cache-Aside](/azure/architecture/patterns/cache-aside/) patrón.

> [!TIP]
> Almacenar en caché los datos que se han leído con frecuencia y que no cambian con frecuencia. Estos datos pueden agregarse a la memoria caché a petición la primera vez que se recuperó por una aplicación. Esto significa que la aplicación necesita para capturar los datos de una sola vez desde el almacén de datos, y ese acceso subsiguiente se puede satisfacer mediante el uso de la memoria caché.

Aplicaciones distribuidas, como el eShopOnContainers hacen referencia a la aplicación, deben proporcionar una o ambas de las memorias caché siguientes:

-   Una memoria caché compartida, que se puede acceder por varios procesos o equipos.
-   Una caché privada, donde los datos están guardados localmente en el dispositivo que ejecuta la aplicación.

La aplicación móvil eShopOnContainers utiliza una caché privada, donde los datos están guardados localmente en el dispositivo que está ejecutando una instancia de la aplicación. Para obtener información acerca de la memoria caché usada por la aplicación de referencia de eShopOnContainers, consulte [Microservicios. NET: arquitectura de aplicaciones de .NET en contenedores](https://aka.ms/microservicesebook).

> [!TIP]
> Piense en la memoria caché como un almacén de datos transitorios que podría desaparecer en cualquier momento. Asegúrese de que los datos se mantienen en el almacén de datos original, así como la memoria caché. A continuación, se minimizan las posibilidades de perder datos si la memoria caché no está disponible.

### <a name="managing-data-expiration"></a>Administración de la expiración de datos

No resulta práctico esperar que los datos almacenados en caché siempre será coherentes con los datos originales. Pueden cambiar datos en el almacén de datos original después de se haya almacenado en caché, haciendo que los datos almacenados en caché se conviertan en obsoletos. Por lo tanto, las aplicaciones deben implementar una estrategia que ayuda a garantizar que los datos en la memoria caché están tan actualizados como sea posible, pero también puede detectar y controlar las situaciones que surgen cuando los datos en la memoria caché se ha vuelto obsoletos. Mecanismos de almacenamiento en caché más habilitar la memoria caché a configurarse para que expiren los datos y, por lo tanto, reducen el período para el que los datos podrían ser obsoletos.

> [!TIP]
> Establecer una expiración predeterminada al configurar una memoria caché de tiempo. Muchas de las memorias caché implementan expiración, que invalida los datos y lo quita de la memoria caché si no se tiene acceso durante un período especificado. Sin embargo, debe tener cuidado al elegir el período de expiración. Si se realiza demasiado corto, datos expirará demasiado rápidamente y se reducirán las ventajas del almacenamiento en caché. Si se hace demasiado largo, los riesgos de datos pase a ser obsoletos. Por lo tanto, el tiempo de expiración debe coincidir con el patrón de acceso para las aplicaciones que usan los datos.

Cuando expiren los datos almacenados en memoria caché, debe quitarse de la memoria caché y la aplicación debe recuperar los datos de los datos originales almacenarán y lo coloca en la memoria caché.

También es posible que una memoria caché podría llenar si datos puede permanecer durante un periodo demasiado largo. Por lo tanto, las solicitudes para agregar nuevos elementos a la memoria caché podrían ser necesario para quitar algunos elementos en un proceso conocido como *expulsión*. Servicios de almacenamiento en caché normalmente expulsar datos de forma usados menos recientemente. Sin embargo, hay otras directivas de expulsión, incluidos los usados más recientemente y primero en el primero en salir. Para obtener más información, consulte [almacenamiento en caché de instrucciones](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>Almacenamiento en caché de imágenes

La aplicación móvil eShopOnContainers consume imágenes de producto remoto que se benefician de está almacenando en caché. Estas imágenes se muestran por la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) (control) y el `CachedImage` control proporcionado por el [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) biblioteca.

El Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) control admite el almacenamiento en caché de imágenes descargadas. Almacenamiento en caché está habilitado de forma predeterminada y almacenará la imagen de forma local durante 24 horas. Además, el tiempo de expiración puede configurarse con la [ `CacheValidity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) propiedad. Para obtener más información, consulte [descargar imagen de almacenamiento en caché](~/xamarin-forms/user-interface/images.md#Image_Caching).

Del FFImageLoading `CachedImage` control es un reemplazo para el Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) control, proporcionar propiedades adicionales que habilitar la funcionalidad adicional. Entre esta funcionalidad, el control proporciona almacenamiento en caché configurable, al admitir el error y cargar los marcadores de posición de la imagen. En el ejemplo de código siguiente se muestra cómo se utiliza la aplicación móvil eShopOnContainers el `CachedImage` controlar en el `ProductTemplate`, que es la plantilla de datos utilizada por el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controlar en el `CatalogView`:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP, WinRT, WinPhone" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP, WinRT, WinPhone" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

El `CachedImage` conjuntos de controles el `LoadingPlaceholder` y `ErrorPlaceholder` propiedades para imágenes específicas de la plataforma. El `LoadingPlaceholder` propiedad especifica la imagen que se mostrará durante la imagen especificada por el `Source` se recupera la propiedad y el `ErrorPlaceholder` propiedad especifica la imagen que se mostrará si se produce un error al intentar recuperar la imagen Especifica el `Source` propiedad.

Como su nombre indica, el `CachedImage` control almacena en caché remotas imágenes en el dispositivo durante el tiempo especificado por el valor de la `CacheDuration` propiedad. Cuando este valor de propiedad no se establece explícitamente, se aplica el valor predeterminado de 30 días.

## <a name="increasing-resilience"></a>Aumentar la resistencia

Todas las aplicaciones que se comunican con los recursos y servicios remotos deben ser vulnerable a los errores transitorios. Los errores transitorios incluyen la pérdida de conectividad de red para servicios momentánea, la falta de disponibilidad temporal de un servicio o tiempos de espera que surgen cuando un servicio está ocupado. Estos errores suelen ser corrigen de manera automática y, si se repite la acción después de un retraso adecuado es probable que sea correcta.

Los errores transitorios pueden tener un gran impacto en la calidad percibida de una aplicación, incluso si se ha probado exhaustivamente en todas las circunstancias previsibles. Para asegurarse de que una aplicación que se comunica con los servicios remoto funciona de forma confiable, debe ser capaz de hacer todo lo siguiente:

-   Detectar errores cuando se producen y determinar si los errores suelen ser transitorio.
-   Vuelva a intentar la operación si se determina que el error suele ser transitorio y mantener el seguimiento del número de veces que se vuelve a intentar la operación.
-   Usar una estrategia adecuada de reintentos, que especifica el número de reintentos, el retraso entre cada intento y las acciones para usar después de un intento fallido.

Este control de errores transitorios puede lograrse ajustando todos los intentos de acceso a un servicio remoto en el código que implementa el patrón de reintento.

### <a name="retry-pattern"></a>Vuelva a intentar patrón

Si una aplicación detecta un error al intentar enviar una solicitud a un servicio remoto, puede controlar el error en cualquiera de las maneras siguientes:

-   Volver a intentar la operación. La aplicación puede reintentar la solicitud de error inmediatamente.
-   Volver a intentar la operación después de un retraso. La aplicación debe esperar una cantidad adecuada de tiempo antes de reintentar la solicitud.
-   Si cancela la operación. La aplicación debe cancelar la operación y una excepción de informes.

La estrategia de reintento debe optimizarse para que coincida con los requisitos empresariales de la aplicación. Por ejemplo, es importante optimizar el número de reintentos y el intervalo de reintento que la operación que se está intentando. Si la operación forma parte de una interacción del usuario, el intervalo de reintentos debe ser corto y solo unos cuantos reintentos realizados para evitar la realización de los usuarios esperar una respuesta. Si la operación forma parte de un flujo de trabajo de ejecución prolongada, donde cancelar o reiniciar el flujo de trabajo es lento o costoso, es adecuado para esperar más tiempo entre intentos y vuelva a intentar varias veces.

> [!NOTE]
> Una estrategia de reintento agresiva con un retraso mínimo entre intentos y un gran número de reintentos, podría degradar un servicio remoto que se ejecuta cercanos o en su capacidad. Además, esta estrategia de reintento también podría afectar a la capacidad de respuesta de la aplicación si continuamente está intentando realizar una operación de error.

Si una solicitud sigue sin funcionar después de un número de reintentos, es mejor para la aplicación para evitar que las solicitudes posteriores en el mismo recurso y a informar sobre un error. A continuación, después de un período establecido, la aplicación puede realizar una o varias solicitudes para el recurso para ver si son correctas. Para obtener más información, consulte [patrón de disyuntor](#circuit_breaker_pattern).

> [!TIP]
> Nunca se implementa un mecanismo de reintento infinito. Utilizar un número finito de reintentos o implementar la [disyuntor](/azure/architecture/patterns/circuit-breaker/) patrón para permitir que se va a recuperar un servicio.

La aplicación móvil eShopOnContainers no implementa actualmente el patrón de reintento al realizar solicitudes web RESTful. Sin embargo, el `CachedImage` control, proporcionado por el [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) biblioteca admite la administración de errores transitorios Reintentando la carga de la imagen. Si se produce un error en la carga de imágenes, se realizarán más intentos. El número de intentos especificado por el `RetryCount` propiedad y reintentos se producen después de un retraso especificado por el `RetryDelay` propiedad. Si estos valores de propiedad no se establecen explícitamente, su valor predeterminado es valores se aplican – 3 para el `RetryCount` propiedad y 250ms para el `RetryDelay` propiedad. Para obtener más información sobre la `CachedImage` control, vea [almacenamiento en caché de imágenes](#caching_images).

La aplicación de referencia de eShopOnContainers implementar el patrón de reintento. Para obtener más información, incluida una explicación de cómo combinar el patrón de reintento con el `HttpClient` de clases, consulte [Microservicios .NET: arquitectura de aplicaciones de .NET en contenedores](https://aka.ms/microservicesebook).

Para obtener más información sobre el patrón de reintento, vea el [vuelva a intentar](/azure/architecture/patterns/retry/) patrón.

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Patrón de disyuntor

En algunas situaciones, pueden deberse a errores anticipados eventos que tardan más tiempo en corregir. Estos errores pueden oscilar entre una pérdida parcial de conectividad para el error completo de un servicio. En estas situaciones, es inútil para una aplicación volver a intentar una operación que no es probable que se realiza correctamente y en su lugar, debe aceptar que la operación ha fallado y controlar este error en consecuencia.

El patrón de disyuntor puede impedir que una aplicación intenta repetidamente ejecutar una operación que es probable que genere un error, y asimismo posibilitar la aplicación detectar si se ha resuelto el error.

> [!NOTE]
> El propósito del patrón de disyuntor es diferente del patrón de reintento. El patrón de reintento permite que una aplicación volver a intentar una operación de la expectativa de que se podrá correctamente. El patrón de disyuntor evita que una aplicación realizando una operación que es probable que un error.

Un disyuntor actúa como un proxy para las operaciones que se podrían producir errores. El servidor proxy debe supervisar el número de errores recientes que se han producido y usar esta información para decidir si se permite la operación para continuar, o para devolver una excepción inmediatamente.

La aplicación móvil eShopOnContainers no implementa el patrón de disyuntor actualmente. Sin embargo, no el eShopOnContainers. Para obtener más información, consulte [Microservicios. NET: arquitectura de aplicaciones de .NET en contenedores](https://aka.ms/microservicesebook).

> [!TIP]
> Combinar los patrones de reintento y disyuntor. Una aplicación puede combinar los patrones de reintento y disyuntor mediante el patrón de reintento para invocar una operación a través de un disyuntor. Sin embargo, la lógica de reintento debe ser sensible a las excepciones devueltas por el disyuntor y abandona los intentos de reintento si el disyuntor indica que un error no es transitorio.

Para obtener más información sobre el patrón de disyuntor, consulte el [disyuntor](/azure/architecture/patterns/circuit-breaker/) patrón.

## <a name="summary"></a>Resumen

Muchas soluciones modernas basadas en web hacen uso de servicios web, hospedadas por servidores web, para proporcionar funcionalidad de cliente remoto a aplicaciones. Las operaciones que expone un servicio web constituyen una API web y aplicaciones de cliente deben ser capaces de usar la API web sin conocer cómo se implementan las operaciones que expone la API o datos.

Se puede mejorar el rendimiento de una aplicación al almacenar en caché datos de acceso frecuente a almacenamiento rápido que se encuentra cierra la aplicación. Pueden implementar aplicaciones de almacenamiento en caché de lectura con el modelo de cache-aside. Este patrón determina si el elemento está actualmente en la memoria caché. Si el elemento no está en la memoria caché, ha leído desde el almacén de datos y se agregan a la caché.

Cuando se comunica con las API web, aplicaciones deben ser vulnerable a los errores transitorios. Los errores transitorios incluyen la pérdida de conectividad de red para servicios momentánea, la falta de disponibilidad temporal de un servicio o tiempos de espera que surgen cuando un servicio está ocupado. Estos errores suelen ser corrigen de manera automática y, si se repite la acción después de un retraso adecuado, a continuación, es probable que sea correcta. Por lo tanto, las aplicaciones deben incluir todos los intentos para tener acceso a una API web en el código que implementa un mecanismo de control de errores transitorios.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
