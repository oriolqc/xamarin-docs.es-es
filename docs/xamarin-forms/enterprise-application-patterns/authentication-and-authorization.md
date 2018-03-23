---
title: Autenticación y autorización
ms.topic: article
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 16cd5571a8c3b0e179046351e9996e55b040a9de
ms.sourcegitcommit: 7b76c3d761b3ffb49541e2e2bcf292de6587c4e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="authentication-and-authorization"></a>Autenticación y autorización

La autenticación es el proceso de obtener las credenciales de identificación, como el nombre y la contraseña de un usuario y validar esas credenciales en una entidad. Si las credenciales son válidas, la entidad que envió las credenciales se considera una identidad autenticada. Una vez que se ha autenticado una identidad, un proceso de autorización determina si esa identidad tiene acceso a un recurso determinado.

Existen muchos enfoques a la integración de autenticación y autorización en una aplicación de Xamarin.Forms que se comunica con una aplicación web de ASP.NET MVC, incluido el uso de ASP.NET Core Identity, proveedores de autenticación externos, como Microsoft, Google, Middleware de Facebook o Twitter y autenticación. La aplicación móvil eShopOnContainers realiza la autenticación y autorización con un microservicio de identidad en contenedores que usa IdentityServer 4. La aplicación móvil solicita los tokens de seguridad de IdentityServer, para autenticar a un usuario o para tener acceso a un recurso. Para IdentityServer para emitir tokens en nombre del usuario, el usuario debe iniciar sesión en IdentityServer. Sin embargo, IdentityServer no proporciona una interfaz de usuario o la base de datos para la autenticación. Por lo tanto, en la aplicación de referencia eShopOnContainers, ASP.NET Core Identity se utiliza para este propósito.

## <a name="authentication"></a>Autenticación

Se requiere autenticación, cuando una aplicación necesita conocer la identidad del usuario actual. Mecanismo principal del núcleo de ASP.NET para identificar a los usuarios es el sistema de pertenencia a ASP.NET Core Identity, que almacena información de usuario en un almacén de datos configurado por el programador. Normalmente, este almacén de datos será un almacén de Entity Framework, aunque almacenes personalizados o paquetes de terceros pueden usarse para almacenar información de identidad en el almacenamiento de Azure, base de datos de Azure Cosmos u otras ubicaciones.

Para los escenarios de autenticación que hacen uso de un almacén de datos de usuario local, y que conservan información de identidad entre las solicitudes a través de las cookies (como es habitual en aplicaciones web ASP.NET MVC), ASP.NET Core Identity es una solución adecuada. Sin embargo, las cookies no son siempre un medio natural de conservación y transmisión de datos. Por ejemplo, una aplicación web de ASP.NET Core que expone extremos REST que se tiene acceso desde una aplicación móvil normalmente necesitará usar la autenticación de token de portador, puesto que las cookies no se pueden usar en este escenario. Sin embargo, tokens de portador puede recuperar y fácilmente incluidos en el encabezado de autorización de solicitudes web realizadas desde la aplicación móvil.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emitir Tokens de portador con IdentityServer 4

[4 IdentityServer](https://github.com/IdentityServer/IdentityServer4) es un marco de trabajo de código abierto OAuth 2.0 y OpenID Connect para ASP.NET Core, que se puede usar para muchos escenarios de autenticación y autorización, incluyendo la emisión de tokens de seguridad para los usuarios locales de ASP.NET Core Identity.

> [!NOTE]
> OpenID Connect y OAuth 2.0 son muy similares, al tiempo que tiene responsabilidades diferentes.

OpenID Connect es una capa de autenticación sobre el protocolo OAuth 2.0. OAuth 2 es un protocolo que permite a las aplicaciones solicitar tokens de acceso de un servicio de token de seguridad y usar para comunicarse con las API. Esta delegación reduce la complejidad de las aplicaciones cliente y las API desde que se pueden centralizar la autenticación y autorización.

La combinación de OpenID Connect y OAuth 2.0 combinar los dos problemas de seguridad fundamentales de autenticación y el acceso de API y IdentityServer 4 es una implementación de estos protocolos.

En las aplicaciones que utilizan la comunicación de cliente a microservicio directa, por ejemplo, la aplicación de referencia eShopOnContainers, un microservicio autenticación dedicado que actúa como un servicio de Token de seguridad (STS) puede utilizarse para autenticar a los usuarios, como se muestra en la figura 9-1. Para obtener más información acerca de la comunicación de cliente a microservicio directa, vea [comunicación entre cliente y Microservicios](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Autenticación de un microservicio autenticación dedicado")

**Figura 9-1:** autenticación por un microservicio autenticación dedicado

La aplicación móvil eShopOnContainers se comunica con el microservicio de identidad, que utiliza 4 IdentityServer para realizar la autenticación y control de acceso para las API. Por lo tanto, la aplicación móvil solicita tokens de IdentityServer, para autenticar a un usuario o para tener acceso a un recurso:

-   Autenticar a los usuarios con IdentityServer se logra mediante la aplicación móvil que solicita un *identidad* símbolo (token), que representa el resultado de un proceso de autenticación. Como mínimo, contiene un identificador para el usuario e información sobre cómo y cuándo el usuario autenticado. También puede contener datos de identidad adicional.
-   Acceso a un recurso con IdentityServer se logra mediante la aplicación móvil que solicita un *acceso* símbolo (token), que permite el acceso a un recurso de la API. Los clientes solicitar tokens de acceso y las reenvían a la API. Tokens de acceso contienen información sobre el cliente y el usuario (si existe). Las API, a continuación, utilizar esa información para autorizar el acceso a sus datos.

> [!NOTE]
> Un cliente debe registrarse con IdentityServer antes de que puede solicitar tokens.

### <a name="adding-identityserver-to-a-web-application"></a>Agregar IdentityServer a una aplicación Web

En orden para una aplicación web de ASP.NET Core usar IdentityServer 4, debe agregarse a la solución de Visual Studio de la aplicación web. Para obtener más información, consulte [información general y el programa de instalación](https://identityserver4.readthedocs.io/en/release/quickstarts/0_overview.html) en la documentación de IdentityServer.

Una vez que IdentityServer se incluye en la solución de Visual Studio de la aplicación web, debe agregarse a la solicitud HTTP de la aplicación web procesa la canalización, por lo que puede servir las solicitudes a los extremos de OpenID Connect y OAuth 2.0. Esto se logra en el `Configure` método en la aplicación web `Startup` de la clase, como se muestra en el ejemplo de código siguiente:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Es importante el orden de procesamiento de canalización de solicitudes HTTP de la aplicación web. Por lo tanto, IdentityServer deben agregarse a la canalización antes el marco de interfaz de usuario que implementa la pantalla de inicio de sesión.

### <a name="configuring-identityserver"></a>Configurar IdentityServer

IdentityServer debe estar configurado en el `ConfigureServices` método en la aplicación web `Startup` clase mediante una llamada a la `services.AddIdentityServer` método, como se muestra en el siguiente ejemplo de código de la aplicación de referencia de eShopOnContainers:

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

Después de llamar a la `services.AddIdentityServer` API fluidas adicionales se llama al método, para configurar lo siguiente:

-   Credenciales usadas para la firma.
-   API y la identidad de los recursos que los usuarios pueden solicitar acceso.
-   Clientes que se conecten al solicitar tokens.
-   Identidad principal de ASP.NET.

>💡 **Sugerencia**: cargar dinámicamente la configuración de IdentityServer 4. Las API del IdentityServer 4 permiten configurar IdentityServer de una lista en memoria de objetos de configuración. En la aplicación de referencia eShopOnContainers, estas colecciones en memoria están codificados en la aplicación. Sin embargo, en escenarios de producción se pueden cargar dinámicamente desde un archivo de configuración o desde una base de datos.

Para obtener información acerca de cómo configurar IdentityServer para usar identidad de núcleo de ASP.NET, vea [utilizando ASP.NET Core Identity](https://identityserver4.readthedocs.io/en/release/quickstarts/6_aspnet_identity.html) en la documentación de IdentityServer.

#### <a name="configuring-api-resources"></a>Configuración de recursos de la API

Al configurar los recursos de la API, el `AddInMemoryApiResources` método espera un `IEnumerable<ApiResource>` colección. El siguiente ejemplo de código muestra la `GetApis` aplicación hacen referencia a método que proporciona esta recopilación en el eShopOnContainers:

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

Este método especifica que IdentityServer debe proteger los pedidos y las API de la cesta de compra. Por consiguiente, IdentityServer administra el acceso de símbolos (tokens) será necesario cuando se realizan llamadas a estas API. Para obtener más información sobre la `ApiResource` los tipos, vea [recurso de la API](https://identityserver4.readthedocs.io/en/release/reference/api_resource.html#refapiresource) en la documentación de IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configuración de recursos de identidad

Al configurar los recursos de identidad, el `AddInMemoryIdentityResources` método espera un `IEnumerable<IdentityResource>` colección. Recursos de identidad son datos como Id. de usuario, nombre o dirección de correo electrónico. Cada recurso de identidad tiene un nombre único y tipos de notificaciones arbitrarias pueden asignarse a él, que, a continuación, se incluirán en el token de identidad para el usuario. El siguiente ejemplo de código muestra la `GetResources` aplicación hacen referencia a método que proporciona esta recopilación en el eShopOnContainers:

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

La especificación OpenID Connect especifica algunos [recursos de identidad estándar](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). El requisito mínimo es que se admiten para emitir un identificador único para los usuarios. Esto se consigue mediante la exposición de la `IdentityResources.OpenId` recursos de identidad.

> [!NOTE]
> La `IdentityResources` clase es compatible con todos los ámbitos definidos en la especificación OpenID Connect (openid, correo electrónico, perfiles, teléfono y dirección).

IdentityServer también admite la definición de recursos de identidad personalizada. Para obtener más información, consulte [definir recursos de identidad personalizada](https://identityserver4.readthedocs.io/en/release/topics/resources.html#defining-custom-identity-resources) en la documentación de IdentityServer. Para obtener más información sobre la `IdentityResource` los tipos, vea [identidad recursos](https://identityserver4.readthedocs.io/en/release/reference/identity_resource.html) en la documentación de IdentityServer 4.

#### <a name="configuring-clients"></a>Configuración de clientes

Los clientes son aplicaciones que se pueden solicitar tokens de IdentityServer. Normalmente, la siguiente configuración se debe definir para cada cliente como mínimo:

-   Un identificador de cliente único.
-   Las interacciones permitidas con el servicio de token (conocido como el tipo de concesión).
-   La ubicación donde se envían los tokens de acceso e identidad (conocido como un URI de redireccionamiento).
-   Una lista de recursos que el cliente puede tener acceso a (conocido como ámbitos).

Al configurar los clientes, el `AddInMemoryClients` método espera un `IEnumerable<Client>` colección. En el ejemplo de código siguiente se muestra la configuración de la aplicación móvil eShopOnContainers en la `GetClients` aplicación hacen referencia a método que proporciona esta recopilación en el eShopOnContainers:

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

Esta configuración especifica los datos de las siguientes propiedades:

-   `ClientId`: Un identificador único para el cliente.
-   `ClientName`: El nombre de presentación de cliente, que se usa para el registro y la pantalla de consentimiento.
-   `AllowedGrantTypes`: Especifica cómo un cliente desea interactuar con IdentityServer. Para obtener más información, consulte [configurar el flujo de autenticación](#configuring_the_authentication_flow).
-   `ClientSecrets`: Especifica las credenciales de secreto de cliente que se usan al solicitar tokens desde el extremo de token.
-   `RedirectUris`: Especifica al URI permitido que se va a devolver tokens o códigos de autorización.
-   `RequireConsent`: Especifica si se requiere una pantalla de consentimiento.
-   `RequirePkce`: Especifica si los clientes que usan un código de autorización deben enviar una clave de prueba.
-   `PostLogoutRedirectUris`: Especifica el URI permitido para redirigir a después de cerrar la sesión.
-   `AllowedCorsOrigins`: Especifica el origen del cliente para que IdentityServer puede permitir llamadas entre orígenes desde el origen.
-   `AllowedScopes`: Especifica los recursos que el cliente tiene acceso a. De forma predeterminada, un cliente no tiene acceso a los recursos.
-   `AllowOfflineAccess`: Especifica si el cliente puede solicitar tokens de actualización.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configurar el flujo de autenticación

El flujo de autenticación entre un cliente y IdentityServer puede configurarse mediante la especificación de los tipos de concesión en el `Client.AllowedGrantTypes` propiedad. Las especificaciones de OpenID Connect y OAuth 2.0 definen una serie de flujos de autenticación, incluidos:

-   Implícita. Este flujo está optimizado para aplicaciones basadas en el explorador y debe usarse para ver si sólo la autenticación de usuario o las solicitudes de token de acceso y autenticación. Todos los tokens se transmiten a través del explorador y, por tanto, avanzados características como tokens de actualización no se permiten.
-   Código de autorización. Este flujo proporciona la capacidad de recuperar los tokens en un canal secundario, en lugar del canal frontal de explorador, mientras que también admita la autenticación de cliente.
-   Híbrido. Este flujo es una combinación de la parte implícita y tipos de concesión de código de autorización. El token de identidad se transmite a través del canal de explorador y contiene la respuesta de protocolo con signo junto con otros artefactos, como el código de autorización. Después de la validación correcta de la respuesta, el canal secundario debe usarse para recuperar el acceso y token de actualización.

> [!TIP]
> Use el flujo de autenticación híbrida. El flujo de autenticación híbrida mitiga una serie de ataques que se aplican al canal de explorador y es el flujo recomendado para aplicaciones nativas que desea recuperar los tokens de acceso (y posiblemente tokens de actualización).

Para obtener más información sobre los flujos de autenticación, vea [tipos de concesión](https://identityserver4.readthedocs.io/en/release/topics/grant_types.html) en la documentación de IdentityServer 4.

### <a name="performing-authentication"></a>Realizar la autenticación

Para IdentityServer para emitir tokens en nombre del usuario, el usuario debe iniciar sesión en IdentityServer. Sin embargo, IdentityServer no proporciona una interfaz de usuario o la base de datos para la autenticación. Por lo tanto, en la aplicación de referencia eShopOnContainers, ASP.NET Core Identity se utiliza para este propósito.

La aplicación móvil eShopOnContainers se autentica con IdentityServer con el flujo de autenticación híbrido, que se muestra en la figura 9-2.

![](authentication-and-authorization-images/sign-in.png "Información general de alto nivel del proceso de inicio de sesión")

**Figura 9-2:** descripción general del proceso de inicio de sesión

Se realiza una solicitud de inicio de sesión a `<base endpoint>:5105/connect/authorize`. A continuación una autenticación correcta, IdentityServer devuelve una respuesta de autenticación que contiene un código de autorización y un token de identidad. El código de autorización, a continuación, se envía a `<base endpoint>:5105/connect/token`, que responde con tokens de actualización, identidad y acceso.

El eShopOnContainers aplicación móvil signos horizontal de IdentityServer enviando una solicitud a `<base endpoint>:5105/connect/endsession`, con parámetros adicionales. Después de que se cierre de sesión, IdentityServer responde enviando un URI de redireccionamiento de cierre de sesión de entrada de nuevo a la aplicación móvil. Figura 9-3 ilustra este proceso.

![](authentication-and-authorization-images/sign-out.png "Información general de alto nivel del proceso de cierre de sesión")

**Figura 9-3:** descripción general del proceso de cierre de sesión

En la aplicación móvil eShopOnContainers, realiza la comunicación con IdentityServer el `IdentityService` de la clase, que implementa el `IIdentityService` interfaz. Esta interfaz especifica que la clase de implementación debe proporcionar `CreateAuthorizationRequest`, `CreateLogoutRequest`, y `GetTokenAsync` métodos.

#### <a name="signing-in"></a>Iniciar sesión

Cuando el usuario puntea el **inicio de sesión** situado en la `LoginView`, el `SignInCommand` en el `LoginViewModel` clase se ejecuta, que a su vez se ejecuta la `SignInAsync` método. En el ejemplo de código siguiente se muestra este método:

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Este método invoca el `CreateAuthorizationRequest` método en el `IdentityService` (clase), que se muestra en el ejemplo de código siguiente:

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

Este método crea el URI para del IdentityServer [extremo de autorización](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html), con los parámetros necesarios. El extremo de autorización está en `/connect/authorize` en el puerto 5105 del extremo base que se expone como una configuración de usuario. Para obtener más información acerca de la configuración de usuario, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Se reduce la superficie de ataque de la aplicación móvil eShopOnContainers mediante la implementación de la clave de prueba para la extensión de Exchange de código (PKCE) para OAuth. PKCE evita que el código de autorización que se va a utilizar si se interceptan. Esto se logra mediante el cliente genera un comprobador de secreto, un hash de los cuales se pasa en la solicitud de autorización, y que se presenta este tipo al canjear el código de autorización. Para obtener más información sobre PKCE, consulte [clave de prueba para el intercambio de código mediante clientes públicos de OAuth](https://tools.ietf.org/html/rfc7636) en el sitio web de Internet Engineering Task Force.

URI devuelto se almacena en la `LoginUrl` propiedad de la `LoginViewModel` clase. Cuando el `IsLogin` propiedad deja de estar `true`, [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) en el `LoginView` se vuelve visible. El `WebView` enlaces de datos su [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) propiedad a la `LoginUrl` propiedad de la `LoginViewModel` clase y, por lo que realiza una solicitud de inicio de sesión a IdentityServer cuando el `LoginUrl` propiedad está establecida en Extremo de autorización del IdentityServer. Cuando IdentityServer recibe esta solicitud y el usuario no está autenticado, el `WebView` se redirigirán a la página de inicio de sesión configurada, que se muestra en la figura 9-4.

![](authentication-and-authorization-images/login.png "Página de inicio de sesión que se muestra la vista Web")

**Figura 9-4:** página de inicio de sesión que se muestra la vista Web

Cuando se haya completado el inicio de sesión, el [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) se redirigirán a un URI devuelto. Esto `WebView` navegación provocará la `NavigateAsync` método en la `LoginViewModel` clase que va a ejecutar, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

Este método analiza la respuesta de autenticación que se encuentra en el URI de valor devuelto, y siempre que un código de autorización válido está presente, realiza una solicitud del IdentityServer [extremo de token](https://identityserver4.readthedocs.io/en/release/endpoints/token.html), pasar el código de autorización, el Comprobador de secreto PKCE y otros parámetros necesitan. El extremo de token es en `/connect/token` en el puerto 5105 del extremo base que se expone como una configuración de usuario. Para obtener más información acerca de la configuración de usuario, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

>💡 **Sugerencia**: validar devolver URI. Aunque la aplicación móvil eShopOnContainers no valida el URI devuelto, el procedimiento recomendado es validar que el URI devuelto hace referencia a una ubicación conocida, para evitar los ataques de redirección de abrir.

Si el extremo de token recibe un código de autorización válido y el Comprobador de secreto PKCE, responde con un token de acceso, el token de identidad y el token de actualización. El token de acceso (que permite el acceso a recursos de la API) y el token de identidad, a continuación, se almacenan como configuración de la aplicación y se realiza la navegación en páginas. Por lo tanto, el efecto general en la aplicación móvil eShopOnContainers es esto: siempre que los usuarios pueden autenticarse correctamente con IdentityServer, se les dirige a la `MainView` página, que es un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) que muestra la `CatalogView` como su pestaña seleccionada.

Para obtener información sobre la navegación de página, vea [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obtener información acerca de cómo [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) navegación hace que un método de modelo de vista se ejecuta, consulte [utilizando los comportamientos de navegación invocar](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obtener información acerca de la configuración de la aplicación, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> El eShopOnContainers también permite que un inicio de sesión de simulacro cuando la aplicación se configura para usar los servicios ficticios en el `SettingsView`. En este modo, la aplicación no se comunicará con IdentityServer, en su lugar, lo que permite al usuario que inicie sesión con las credenciales.

#### <a name="signing-out"></a>Firma horizontal

Cuando el usuario puntea el **LOG OUT** botón en el `ProfileView`, el `LogoutCommand` en el `ProfileViewModel` clase se ejecuta, que a su vez se ejecuta la `LogoutAsync` método. Este método realiza la navegación de una página a la `LoginView` página, y pasar un `LogoutParameter` instancia establecida en `true` como un parámetro. Para obtener más información acerca de cómo pasar parámetros durante la navegación en páginas, vea [pasar parámetros durante la navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Cuando se crea una vista y se abrirá, el `InitializeAsync` se ejecuta el método del modelo de vista asociada de la vista, que, a continuación, se ejecuta el `Logout` método de la `LoginViewModel` (clase), que se muestra en el ejemplo de código siguiente:

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

Este método invoca el `CreateLogoutRequest` método en el `IdentityService` (clase), pasando el token de identidad se recupera desde la configuración de la aplicación como un parámetro. Para obtener más información acerca de la configuración de la aplicación, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). El siguiente ejemplo de código muestra la `CreateLogoutRequest` método:

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

Este método crea el URI del IdentityServer [finalizar el punto de conexión de sesión](https://identityserver4.readthedocs.io/en/release/endpoints/endsession.html#refendsession), con los parámetros necesarios. Es el punto de conexión de la sesión final en `/connect/endsession` en el puerto 5105 del extremo base que se expone como una configuración de usuario. Para obtener más información acerca de la configuración de usuario, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

URI devuelto se almacena en la `LoginUrl` propiedad de la `LoginViewModel` clase. Mientras el `IsLogin` propiedad es `true`, [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) en la `LoginView` está visible. El `WebView` enlaces de datos su [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) propiedad a la `LoginUrl` propiedad de la `LoginViewModel` clase y, por lo que realiza una solicitud de cierre de sesión a IdentityServer cuando el `LoginUrl` propiedad está establecida en Punto de conexión de sesión de final del IdentityServer. Cuando IdentityServer recibe esta solicitud, siempre que sea el usuario con sesión iniciada, se produce cierre de sesión. La autenticación se realiza un seguimiento con una cookie administrada por el middleware de autenticación de la cookie de ASP.NET Core. Por lo tanto, la firma de IdentityServer quita la cookie de autenticación y envía un redireccionamiento posterior al cierre de sesión URI se devuelva al cliente.

En la aplicación móvil, la [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) se redirigirán a la URI de redireccionamiento de cierre de sesión de post. Esto `WebView` navegación provocará la `NavigateAsync` método en la `LoginViewModel` clase que va a ejecutar, que se muestra en el ejemplo de código siguiente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

Este método borra el token de identidad y el token de acceso de configuración de la aplicación y establece la `IsLogin` propiedad `false`, lo que hace que la [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) en la `LoginView` página esté invisible . Por último, el `LoginUrl` propiedad está establecida en el URI de IdentityServer [extremo de autorización](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html), con los parámetros necesarios, como preparación para la próxima vez que el usuario inicia una sesión.

Para obtener información sobre la navegación de página, vea [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obtener información acerca de cómo [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) navegación hace que un método de modelo de vista se ejecuta, consulte [utilizando los comportamientos de navegación invocar](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obtener información acerca de la configuración de la aplicación, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> El eShopOnContainers también permite un simulacro cierre de sesión cuando la aplicación se configura para usar los servicios ficticios en el SettingsView. En este modo, la aplicación no se comunica con IdentityServer y en su lugar, borra los tokens almacenados de configuración de la aplicación.

<a name="authorization" />

## <a name="authorization"></a>Autorización

Después de la autenticación, web de ASP.NET Core API a menudo necesitan autorizar el acceso, que permite que las API de un servicio disponible para algunos usuarios autenticados, pero no a todos.

Restringir el acceso a una ruta de MVC de ASP.NET Core se puede lograr mediante la aplicación de un atributo de autorizar a un controlador o acción, que limita el acceso al controlador o una acción a los usuarios autenticados, tal como se muestra en el ejemplo de código siguiente:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Si un usuario no autorizado intenta obtener acceso a un controlador o acción que se marca con el `Authorize` atributo, el marco de MVC devuelve un código de estado HTTP 401 (no autorizado).

> [!NOTE]
> Se pueden especificar parámetros en el `Authorize` atributo para restringir una API a usuarios específicos. Para obtener más información, consulte [autorización](/aspnet/core/security/authorization/introduction/).

IdentityServer se puede integrar en el flujo de trabajo de autorización para que los tokens de acceso proporciona control de la autorización. Este enfoque se muestra en la figura 9-5.

![](authentication-and-authorization-images/authorization.png "Autorización de token de acceso")

**Figura 9-5:** autorización token de acceso

La aplicación móvil eShopOnContainers se comunica con el microservicio de identidad y solicita un token de acceso como parte del proceso de autenticación. El token de acceso, a continuación, se reenvía a las API expuestas por la ordenación y cesta microservicios como parte de las solicitudes de acceso. Tokens de acceso contienen información sobre el cliente y el usuario. Las API, a continuación, utilizar esa información para autorizar el acceso a sus datos. Para obtener información sobre cómo configurar IdentityServer para proteger las API, consulte [recursos de la API de configuración](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configurar IdentityServer para realizar la autorización

Para realizar la autorización con IdentityServer, su middleware de autorización debe agregarse a la canalización de solicitudes HTTP de la aplicación web. El software intermedio se agrega en el `ConfigureAuth` método en la aplicación web `Startup` (clase), que se invoca desde la `Configure` método y se muestra en el siguiente ejemplo de código de la aplicación de referencia de eShopOnContainers:

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

Este método garantiza que la API solo puede tener acceso mediante un token de acceso válido. El middleware valida el token entrante para asegurarse de que se envían desde un emisor de confianza y valida que el token es válido para usarse con la API que lo recibe. Por lo tanto, la exploración al controlador de ordenación o de la cesta de compras devolverá un 401 (no autorizado) código de estado HTTP, que indica que se requiere un token de acceso.

> [!NOTE]
> Middleware de autorización del IdentityServer debe agregarse a la canalización de solicitudes HTTP de la aplicación web antes de agregar MVC con `app.UseMvc()` o `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Hacer que las solicitudes de acceso a las API

Al realizar solicitudes al orden y cesta microservicios, el acceso token, obtenidos de IdentityServer durante el proceso de autenticación, deben incluirse en la solicitud, como se muestra en el ejemplo de código siguiente:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

El token de acceso se almacena como una configuración de aplicación, se recuperan de almacenamiento específicas de la plataforma e incluido en la llamada a la `GetOrderAsync` método en la `OrderService` clase.

De forma similar, el token de acceso se debe incluir al enviar datos a un IdentityServer protegidos API, como se muestra en el ejemplo de código siguiente:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

El token de acceso se recupera desde almacenamiento de información específica de la plataforma y se incluyen en la llamada a la `UpdateBasketAsync` método en la `BasketService` clase.

El `RequestProvider` (clase), en la aplicación móvil eShopOnContainers, usa la `HttpClient` clase para realizar solicitudes a las API de REST expuestos por la aplicación de referencia de eShopOnContainers. Al realizar solicitudes a la ordenación y la cesta de la API, que requieren autorización, se debe incluir con la solicitud de un token de acceso válido. Esto se consigue agregando el token de acceso a los encabezados de la `HttpClient` de instancia, como se muestra en el ejemplo de código siguiente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

El `DefaultRequestHeaders` propiedad de la `HttpClient` clase expone los encabezados que se envían con cada solicitud y el token de acceso se agrega a la `Authorization` prefijado con la cadena de encabezado `Bearer`. Cuando la solicitud se envía a una API de REST, el valor de la `Authorization` encabezado se extrae y se validan para asegurarse de que se ha enviado desde un emisor de confianza y usa para determinar si el usuario tiene permiso para invocar la API que lo recibe.

Para obtener más información acerca de cómo la aplicación móvil eShopOnContainers hace que las solicitudes web, consulte [obtiene acceso a datos remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Resumen

Existen muchos enfoques a la integración de autenticación y autorización en una aplicación de Xamarin.Forms que se comunica con una aplicación web de ASP.NET MVC. La aplicación móvil eShopOnContainers realiza la autenticación y autorización con un microservicio de identidad en contenedores que usa IdentityServer 4. IdentityServer es un marco de trabajo de código abierto OAuth 2.0 y OpenID Connect de ASP.NET Core que se integra con la identidad de núcleo de ASP.NET para realizar la autenticación de token de portador.

La aplicación móvil solicita los tokens de seguridad de IdentityServer, para autenticar a un usuario o para tener acceso a un recurso. Al obtener acceso a un recurso, un token de acceso debe incluirse en la solicitud a las API que requieren autorización. Middleware de IdentityServer valida los tokens de acceso entrante para asegurarse de que se envían desde un emisor de confianza y que son válidos para su uso con la API que recibe de ellos.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libros electrónicos (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
