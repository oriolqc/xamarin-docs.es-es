---
title: Autenticación y autorización
description: Este capítulo explica cómo la aplicación móvil de eShopOnContainers realiza la autenticación y autorización con los microservicios en contenedores.
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: beb9e8f351a1cecc6017a08345f7cfc5e207ba35
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996222"
---
# <a name="authentication-and-authorization"></a>Autenticación y autorización

La autenticación es el proceso de obtención de credenciales de identificación como el nombre y la contraseña de un usuario y validar dichas credenciales con una entidad. Si las credenciales son válidas, la entidad que envió las credenciales se considera una identidad autenticada. Una vez que se ha autenticado una identidad, un proceso de autorización determina si esa identidad tiene acceso a un recurso determinado.

Existen varios enfoques para integrar la autenticación y autorización en una aplicación de Xamarin.Forms que se comunica con una aplicación web de ASP.NET MVC, incluido el uso de ASP.NET Core Identity, proveedores de autenticación externos como Microsoft, Google, Middleware de Twitter o Facebook y la autenticación. La aplicación móvil de eShopOnContainers realiza la autenticación y autorización con un microservicio en contenedor de identidad que usa 4 IdentityServer. La aplicación móvil solicita tokens de seguridad de IdentityServer, para autenticar un usuario o para tener acceso a un recurso. Para IdentityServer para emitir tokens en nombre de un usuario, el usuario debe iniciar sesión para IdentityServer. Sin embargo, IdentityServer no proporciona una interfaz de usuario o la base de datos para la autenticación. Por lo tanto, en la aplicación de referencia eShopOnContainers, ASP.NET Core Identity se utiliza para este propósito.

## <a name="authentication"></a>Autenticación

Se requiere autenticación, cuando una aplicación necesita conocer la identidad del usuario actual. Mecanismo principal de ASP.NET Core para identificar a los usuarios es el sistema de pertenencia de ASP.NET Core Identity, que almacena información de usuario en un almacén de datos configurado por el programador. Normalmente, este almacén de datos será un almacén de Entity Framework, aunque almacenes personalizados o paquetes de terceros pueden usarse para almacenar información de identidad en Azure storage, Azure Cosmos DB u otras ubicaciones.

Para escenarios de autenticación que usan un almacén de datos de usuario local y que conservan la información de identidad entre las solicitudes a través de las cookies (como es habitual en aplicaciones web ASP.NET MVC), ASP.NET Core Identity es una solución adecuada. Sin embargo, las cookies no son siempre una manera natural de conservar y transmitir datos. Por ejemplo, una aplicación web de ASP.NET Core que expone extremos REST que se accede desde una aplicación móvil normalmente deberá usar la autenticación de token de portador, puesto que las cookies no se puede usar en este escenario. Sin embargo, los tokens de portador pueden fácilmente recuperar e incluir en el encabezado de autorización de solicitudes web realizadas desde la aplicación móvil.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emisión de Tokens de portador con IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4) es un marco de código abierto OAuth 2.0 y OpenID Connect para ASP.NET Core, que puede usarse para muchos escenarios de autenticación y autorización incluyendo la emisión de tokens de seguridad para los usuarios locales de ASP.NET Core Identity.

> [!NOTE]
> OpenID Connect y OAuth 2.0 son muy similares, al tiempo que tiene responsabilidades diferentes.

OpenID Connect es una capa de autenticación sobre el protocolo OAuth 2.0. OAuth 2 es un protocolo que permite a las aplicaciones solicitar tokens de acceso de un servicio de token de seguridad y usar para comunicarse con las API. Esta delegación reduce la complejidad en las aplicaciones cliente y las API desde que se pueden centralizar la autenticación y autorización.

La combinación de OpenID Connect y OAuth 2.0 se combinan los dos problemas de seguridad fundamentales de autenticación y acceso de API y IdentityServer 4 es una implementación de estos protocolos.

En las aplicaciones que utilizan la comunicación directa de cliente a microservicio, por ejemplo, la aplicación de referencia eShopOnContainers, un microservicio de autenticación dedicado que actúe como un servicio de Token de seguridad (STS) puede usarse para autenticar a los usuarios, como se muestra en figura 9-1. Para obtener más información acerca de la comunicación directa de cliente a microservicio, consulte [comunicación entre cliente y Microservicios](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Autenticación de un microservicio de autenticación dedicado")

**Figura 9-1:** autenticación a un microservicio de autenticación dedicado

La aplicación móvil de eShopOnContainers se comunica con el microservicio de identidad, que IdentityServer 4 se utiliza para realizar la autenticación y control de acceso para las API. Por lo tanto, la aplicación móvil solicita tokens desde IdentityServer, para autenticar un usuario o para tener acceso a un recurso:

-   Autenticar a los usuarios con IdentityServer se logra mediante la aplicación móvil que solicita un *identidad* token, que representa el resultado de un proceso de autenticación. Como mínimo, contiene un identificador para el usuario e información sobre cómo y cuándo el usuario autenticado. También puede contener datos de identidad adicionales.
-   Acceso a un recurso con IdentityServer se logra mediante la aplicación móvil que solicita un *acceso* token, que permite el acceso a un recurso de API. Los clientes solicitar tokens de acceso y las reenvían a la API. Los tokens de acceso contienen información sobre el cliente y el usuario (si existe). Las API, a continuación, usan esa información para autorizar el acceso a sus datos.

> [!NOTE]
> Un cliente debe registrarse con IdentityServer antes de que puede solicitar tokens.

### <a name="adding-identityserver-to-a-web-application"></a>Agregar IdentityServer a una aplicación Web

En el orden de una aplicación web ASP.NET Core usan IdentityServer 4, debe agregarse a la solución de Visual Studio de la aplicación web. Para obtener más información, consulte [instalación e información general sobre](https://identityserver4.readthedocs.io/en/release/quickstarts/0_overview.html) en la documentación de IdentityServer.

Una vez que IdentityServer se incluye en la solución de Visual Studio de la aplicación web, debe agregarse a la canalización de procesamiento de solicitudes HTTP de la aplicación web para que puede atender solicitudes a puntos de conexión de OpenID Connect y OAuth 2.0. Esto se logra en el `Configure` método en la aplicación web `Startup` clase, como se muestra en el ejemplo de código siguiente:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Es importante el orden de procesamiento de canalización de solicitudes HTTP de la aplicación web. Por lo tanto, IdentityServer debe agregarse a la canalización antes el marco de interfaz de usuario que implementa la pantalla de inicio de sesión.

### <a name="configuring-identityserver"></a>Configurar IdentityServer

IdentityServer debe estar configurado en el `ConfigureServices` método en la aplicación web `Startup` clase mediante una llamada a la `services.AddIdentityServer` método, como se muestra en el siguiente ejemplo de código de la aplicación de referencia eShopOnContainers:

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
-   Recursos de API y la identidad que los usuarios podrían solicitar acceso.
-   Clientes que se va a conectar para solicitar tokens.
-   Identidad de ASP.NET Core.

>💡 **Sugerencia**: cargar dinámicamente la configuración de IdentityServer 4. Las API del IdentityServer 4 permiten configurar IdentityServer desde una lista en memoria de objetos de configuración. En la aplicación de referencia eShopOnContainers, estas colecciones en memoria están codificados en la aplicación. Sin embargo, en escenarios de producción se pueden cargar dinámicamente desde un archivo de configuración o desde una base de datos.

Para obtener información acerca de cómo configurar IdentityServer para usar ASP.NET Core Identity, vea [usando ASP.NET Core Identity](https://identityserver4.readthedocs.io/en/release/quickstarts/6_aspnet_identity.html) en la documentación de IdentityServer.

#### <a name="configuring-api-resources"></a>Configuración de recursos de API

Al configurar los recursos de la API, el `AddInMemoryApiResources` método espera un `IEnumerable<ApiResource>` colección. El siguiente ejemplo de código muestra la `GetApis` aplicación de referencia de método que proporciona esta recopilación en eShopOnContainers:

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

Este método especifica que debe proteger IdentityServer los pedidos y las API de la cesta de compras. Por lo tanto, IdentityServer administra el acceso a los tokens se solicitará al realizar llamadas a estas API. Para obtener más información sobre la `ApiResource` , vea [recurso de la API](https://identityserver4.readthedocs.io/en/release/reference/api_resource.html#refapiresource) en la documentación IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configuración de recursos de identidad

Al configurar los recursos de identidad, el `AddInMemoryIdentityResources` método espera un `IEnumerable<IdentityResource>` colección. Recursos de identidad son datos como Id. de usuario, nombre o dirección de correo electrónico. Cada recurso de identidad tiene un nombre único y tipos de notificaciones arbitrarias se pueden asignar a él, que, a continuación, se incluirá en el token de identidad para el usuario. El siguiente ejemplo de código muestra la `GetResources` aplicación de referencia de método que proporciona esta recopilación en eShopOnContainers:

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

La especificación de OpenID Connect especifica algunos [recursos estándar de identidad](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). El requisito mínimo es que se proporciona soporte técnico para emitir un identificador único para los usuarios. Esto se consigue al exponer el `IdentityResources.OpenId` recursos de identidad.

> [!NOTE]
> La `IdentityResources` clase es compatible con todos los ámbitos definidos en la especificación de OpenID Connect (openid, correo electrónico, perfiles, teléfono y dirección).

IdentityServer también admite la definición de recursos de identidad personalizada. Para obtener más información, consulte [definir recursos de identidad personalizada](https://identityserver4.readthedocs.io/en/release/topics/resources.html#defining-custom-identity-resources) en la documentación de IdentityServer. Para obtener más información sobre la `IdentityResource` , vea [recursos de identidad](https://identityserver4.readthedocs.io/en/release/reference/identity_resource.html) en la documentación IdentityServer 4.

#### <a name="configuring-clients"></a>Configuración de clientes

Los clientes son aplicaciones que pueden solicitar tokens de IdentityServer. Normalmente, las siguientes opciones deben definirse para cada cliente como mínimo:

-   Un identificador único cliente.
-   Las interacciones permitidas con el servicio de token (conocido como el tipo de concesión).
-   La ubicación donde se envían los tokens de identidad y acceso (conocido como un URI de redirección).
-   Una lista de recursos que el cliente puede tener acceso a (conocido como ámbitos).

Al configurar los clientes, el `AddInMemoryClients` método espera un `IEnumerable<Client>` colección. En el ejemplo de código siguiente se muestra la configuración de la aplicación móvil de eShopOnContainers en el `GetClients` aplicación de referencia de método que proporciona esta recopilación en eShopOnContainers:

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
-   `ClientName`: El nombre para mostrar cliente, que se usa para el registro y la pantalla de consentimiento.
-   `AllowedGrantTypes`: Especifica cómo un cliente desea interactuar con IdentityServer. Para obtener más información, consulte [configurar el flujo de autenticación](#configuring_the_authentication_flow).
-   `ClientSecrets`: Especifica las credenciales del secreto de cliente que se usan al solicitar tokens desde el punto de conexión de token.
-   `RedirectUris`: Especifica al URI permitido que se va a devolver códigos de autorización o tokens.
-   `RequireConsent`: Especifica si se requiere una pantalla de consentimiento.
-   `RequirePkce`: Especifica si los clientes que usan un código de autorización deben enviar una clave de prueba.
-   `PostLogoutRedirectUris`: Especifica los URI para redirigir a después de cierre de sesión permitidos.
-   `AllowedCorsOrigins`: Especifica el origen del cliente para que IdentityServer puede permitir llamadas entre orígenes desde el origen.
-   `AllowedScopes`: Especifica los recursos que el cliente tiene acceso a. De forma predeterminada, un cliente no tiene acceso a todos los recursos.
-   `AllowOfflineAccess`: Especifica si el cliente puede solicitar tokens de actualización.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configurar el flujo de autenticación

El flujo de autenticación entre un cliente y IdentityServer puede configurarse mediante la especificación de los tipos de concesión en el `Client.AllowedGrantTypes` propiedad. Las especificaciones de OAuth 2.0 y OpenID Connect definen un número de flujos de autenticación, incluidos:

-   Implícita. Este flujo está optimizado para aplicaciones basadas en explorador y se debe usar sólo la autenticación de usuario o solicitudes de token de acceso y autenticación. Todos los tokens se transmiten a través del explorador y, por tanto, características avanzados, como los tokens de actualización no se permiten.
-   Código de autorización. Este flujo proporciona la capacidad de recuperar los tokens en un canal, en lugar del canal frontal de explorador, al tiempo que también admite la autenticación de cliente.
-   Híbrido. Este flujo es una combinación de implícito y tipos de concesión de código de autorización. El token de identidad se transmite a través del canal de explorador y contiene la respuesta de protocolo con signo, junto con otros artefactos, como el código de autorización. Tras la validación de la respuesta, el canal debe usarse para recuperar el acceso y el token de actualización.

> [!TIP]
> Use el flujo de autenticación híbrido. El flujo de autenticación híbrida mitiga un número de ataques que se aplican al canal de explorador y es el flujo recomendado para aplicaciones nativas que se desea recuperar los tokens de acceso (y posiblemente los tokens de actualización).

Para obtener más información sobre los flujos de autenticación, consulte [tipos de concesión](https://identityserver4.readthedocs.io/en/release/topics/grant_types.html) en la documentación IdentityServer 4.

### <a name="performing-authentication"></a>Realizar la autenticación

Para IdentityServer para emitir tokens en nombre de un usuario, el usuario debe iniciar sesión para IdentityServer. Sin embargo, IdentityServer no proporciona una interfaz de usuario o la base de datos para la autenticación. Por lo tanto, en la aplicación de referencia eShopOnContainers, ASP.NET Core Identity se utiliza para este propósito.

La aplicación móvil de eShopOnContainers se autentica con IdentityServer con el flujo de autenticación híbrido, que se muestra en la figura 9-2.

![](authentication-and-authorization-images/sign-in.png "Descripción general del proceso de inicio de sesión")

**Figura 9-2:** descripción general del proceso de inicio de sesión

Se realiza una solicitud de inicio de sesión a `<base endpoint>:5105/connect/authorize`. A continuación una autenticación correcta, IdentityServer devuelve una respuesta de autenticación que contiene un código de autorización y un token de identidad. El código de autorización, a continuación, se envía a `<base endpoint>:5105/connect/token`, que responde con tokens de actualización, identidad y acceso.

El eShopOnContainers aplicación móvil signos horizontal de IdentityServer enviando una solicitud a `<base endpoint>:5105/connect/endsession`, con parámetros adicionales. Después de cierre de sesión, IdentityServer responde enviando un URI de redireccionamiento de cierre de sesión de post a la aplicación móvil. Figura 9-3 se ilustra este proceso.

![](authentication-and-authorization-images/sign-out.png "Descripción general del proceso de cierre de sesión")

**Figura 9-3:** descripción general del proceso de cierre de sesión

En la aplicación móvil de eShopOnContainers, la comunicación con IdentityServer se realiza mediante el `IdentityService` clase que implementa el `IIdentityService` interfaz. Esta interfaz especifica que debe proporcionar la clase de implementación `CreateAuthorizationRequest`, `CreateLogoutRequest`, y `GetTokenAsync` métodos.

#### <a name="signing-in"></a>Inicio de sesión

Cuando el usuario pulsa el **inicio de sesión** situado en la `LoginView`, el `SignInCommand` en el `LoginViewModel` clase se ejecuta, que a su vez se ejecuta el `SignInAsync` método. El ejemplo de código siguiente muestra este método:

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

Este método crea el URI del IdentityServer [extremo de autorización](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html), con los parámetros necesarios. Es el punto de conexión de autorización en `/connect/authorize` 5105 del punto de conexión base que se expone como una configuración de usuario en el puerto. Para obtener más información acerca de la configuración de usuario, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Se reduce la superficie de ataque de la aplicación móvil de eShopOnContainers mediante la implementación de la clave de prueba de extensión de código Exchange (PKCE) para OAuth. PKCE evita que el código de autorización que se va a utilizar si se interceptan. Esto se logra mediante el cliente de generación de un comprobador de secreto, un hash de los cuales se pasa la solicitud de autorización, y que se presenta este tipo al canjear el código de autorización. Para obtener más información sobre PKCE, consulte [clave de prueba para el intercambio de código por los clientes públicos de OAuth](https://tools.ietf.org/html/rfc7636) en el sitio web de Internet Engineering Task Force.

El identificador URI devuelto se almacena en el `LoginUrl` propiedad de la `LoginViewModel` clase. Cuando el `IsLogin` propiedad pasa a ser `true`, [ `WebView` ](xref:Xamarin.Forms.WebView) en el `LoginView` se vuelve visible. El `WebView` enlaza su [ `Source` ](xref:Xamarin.Forms.WebView.Source) propiedad a la `LoginUrl` propiedad de la `LoginViewModel` clase y, por lo que realiza una solicitud de inicio de sesión a IdentityServer cuando el `LoginUrl` propiedad está establecida en Punto de conexión de autorización del IdentityServer. Cuando IdentityServer recibe esta solicitud y el usuario no está autenticado, el `WebView` le redirigirá a la página de inicio de sesión configurada, que se muestra en la figura 9-4.

![](authentication-and-authorization-images/login.png "Página de inicio de sesión que se muestra la vista Web")

**Figura 9-4:** mostrado por el objeto WebView de página de inicio de sesión

Una vez completado el inicio de sesión, el [ `WebView` ](xref:Xamarin.Forms.WebView) será redirigido a un URI devuelto. Esto `WebView` navegación provocará la `NavigateAsync` método en el `LoginViewModel` clase se ejecutarán, que se muestra en el ejemplo de código siguiente:

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

Este método analiza la respuesta de autenticación que se encuentra en el URI devuelto, y siempre que un código de autorización válido está presente, realiza una solicitud del IdentityServer [extremo de token](https://identityserver4.readthedocs.io/en/release/endpoints/token.html), pasando el código de autorización, el Comprobador de secreto PKCE y otros parámetros necesitan. El extremo de token es en `/connect/token` 5105 del punto de conexión base que se expone como una configuración de usuario en el puerto. Para obtener más información acerca de la configuración de usuario, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

>💡 **Sugerencia**: validar devolver los identificadores URI. Aunque la aplicación móvil de eShopOnContainers no valida el URI devuelto, el procedimiento recomendado es validar que el URI devuelto hace referencia a una ubicación conocida, para evitar ataques de redireccionamiento de abierto.

Si el extremo de token recibe un código de autorización válido y el Comprobador de secreto PKCE, responde con un token de acceso, el token de identidad y el token de actualización. El token de acceso (que permite el acceso a recursos de la API) y el token de identidad, a continuación, se almacenan como configuración de la aplicación, y se realiza la navegación de páginas. Por lo tanto, el efecto general en la aplicación móvil de eShopOnContainers es esto: siempre que los usuarios pueden autenticarse correctamente con IdentityServer, se les dirige a la `MainView` página, que es un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) que muestra el `CatalogView` como su pestaña seleccionada.

Para obtener información sobre la navegación de página, vea [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obtener información acerca de cómo [ `WebView` ](xref:Xamarin.Forms.WebView) navegación hace que un método de modelo de vista se ejecuta, consulte [utilizando los comportamientos de navegación invocar](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obtener información acerca de la configuración de la aplicación, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> EShopOnContainers también permite un inicio de sesión de simulacro cuando la aplicación está configurada para usar servicios ficticios en el `SettingsView`. En este modo, la aplicación no se comunica con IdentityServer, en su lugar, lo que permite al usuario que inicie sesión con las credenciales.

#### <a name="signing-out"></a>Firma horizontal

Cuando el usuario pulsa el **cerrar sesión** situado en la `ProfileView`, el `LogoutCommand` en el `ProfileViewModel` clase se ejecuta, que a su vez se ejecuta el `LogoutAsync` método. Este método realiza la navegación de página a la `LoginView` página, pasando un `LogoutParameter` instancia establecida en `true` como un parámetro. Para obtener más información sobre cómo pasar parámetros durante la navegación de página, vea [pasar parámetros durante la navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Cuando se crea una vista y se navega a, el `InitializeAsync` se ejecuta el método asociada de la vista modelo de vista, que, a continuación, se ejecuta el `Logout` método de la `LoginViewModel` (clase), que se muestra en el ejemplo de código siguiente:

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

Este método invoca el `CreateLogoutRequest` método en el `IdentityService` clase, pasa el token de identidad se recupera de la configuración de la aplicación como un parámetro. Para obtener más información acerca de la configuración de la aplicación, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). El siguiente ejemplo de código muestra la `CreateLogoutRequest` método:

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

Este método crea el URI del IdentityServer [terminar en punto de conexión de sesión](https://identityserver4.readthedocs.io/en/release/endpoints/endsession.html#refendsession), con los parámetros necesarios. Es el punto de conexión de sesión final en `/connect/endsession` 5105 del punto de conexión base que se expone como una configuración de usuario en el puerto. Para obtener más información acerca de la configuración de usuario, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

El identificador URI devuelto se almacena en el `LoginUrl` propiedad de la `LoginViewModel` clase. Mientras el `IsLogin` propiedad es `true`, [ `WebView` ](xref:Xamarin.Forms.WebView) en el `LoginView` está visible. El `WebView` enlaza su [ `Source` ](xref:Xamarin.Forms.WebView.Source) propiedad a la `LoginUrl` propiedad de la `LoginViewModel` clase y, por lo que realiza una solicitud de cierre de sesión a IdentityServer cuando el `LoginUrl` propiedad está establecida en Punto de conexión de sesión del IdentityServer end. Cuando IdentityServer recibe esta solicitud, siempre que sea el usuario ha iniciado sesión, se produce cierre de sesión. La autenticación se realiza un seguimiento con una cookie administrada por el middleware de autenticación de cookies de ASP.NET Core. Por lo tanto, cerrando la sesión IdentityServer quita la cookie de autenticación y envía un redireccionamiento posterior al cierre de sesión URI se devuelva al cliente.

En la aplicación móvil, el [ `WebView` ](xref:Xamarin.Forms.WebView) le redirigirá a la URI de redireccionamiento de cierre de sesión de post. Esto `WebView` navegación provocará la `NavigateAsync` método en el `LoginViewModel` clase se ejecutarán, que se muestra en el ejemplo de código siguiente:

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

Este método borra el token de identidad y el token de acceso de configuración de la aplicación y establece el `IsLogin` propiedad `false`, lo que hace que el [ `WebView` ](xref:Xamarin.Forms.WebView) en el `LoginView` página sea invisible . Por último, el `LoginUrl` propiedad está establecida en el URI de IdentityServer [extremo de autorización](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html), con los parámetros necesarios, como preparación para la próxima vez que el usuario inicia una sesión.

Para obtener información sobre la navegación de página, vea [navegación](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obtener información acerca de cómo [ `WebView` ](xref:Xamarin.Forms.WebView) navegación hace que un método de modelo de vista se ejecuta, consulte [utilizando los comportamientos de navegación invocar](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obtener información acerca de la configuración de la aplicación, consulte [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> EShopOnContainers también permite un simulacro cierre de sesión cuando la aplicación está configurada para usar servicios ficticios en el SettingsView. En este modo, la aplicación no se comunica con IdentityServer y borra los tokens almacenados de configuración de la aplicación.

<a name="authorization" />

## <a name="authorization"></a>Autorización

Tras la autenticación, la web de ASP.NET Core API a menudo se necesitan para autorizar el acceso, lo que permite un servicio para que API disponible para algunos usuarios autenticados, pero no a todos.

Restringir el acceso a una ruta de ASP.NET Core MVC puede lograrse mediante la aplicación de un atributo Authorize a un controlador o acción, lo que limita el acceso al controlador o acción que los usuarios autenticados, como se muestra en el ejemplo de código siguiente:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Si un usuario no autorizado intenta obtener acceso a un controlador o acción que está marcado con el `Authorize` atributo, el marco de MVC devuelve un código de estado HTTP 401 (no autorizado).

> [!NOTE]
> Se pueden especificar parámetros en el `Authorize` atributo para restringir una API a usuarios específicos. Para obtener más información, consulte [autorización](/aspnet/core/security/authorization/introduction/).

IdentityServer se puede integrar en el flujo de trabajo de autorización para que los tokens de acceso proporciona autorización de control. Este método se muestra en la figura 9-5.

![](authentication-and-authorization-images/authorization.png "Autorización de token de acceso")

**Figura 9-5:** autorización por token de acceso

La aplicación móvil de eShopOnContainers se comunica con el microservicio de identidad y solicita un token de acceso como parte del proceso de autenticación. El token de acceso, a continuación, se reenvía a las API expuestas por los microservicios ordering y cesta como parte de las solicitudes de acceso. Los tokens de acceso contienen información sobre el cliente y el usuario. Las API, a continuación, usan esa información para autorizar el acceso a sus datos. Para obtener información sobre cómo configurar IdentityServer para proteger las API, consulte [configurar recursos de la API](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configurar IdentityServer para realizar la autorización

Para realizar la autorización con IdentityServer, su middleware de autorización debe agregarse a la canalización de solicitudes HTTP de la aplicación web. El software intermedio se agrega en el `ConfigureAuth` método en la aplicación web `Startup` (clase), que se invoca desde el `Configure` método y se muestra en el siguiente ejemplo de código de la aplicación de referencia eShopOnContainers:

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

Este método garantiza que la API solo se puede acceder con un token de acceso válido. El software intermedio valida el token entrante para asegurarse de que se envía desde un emisor de confianza y valida que el token es válido para usarse con la API que lo recibe. Por lo tanto, devolverá un 401 (no autorizado) código de estado HTTP, que indica que se requiere un token de acceso al controlador de ordenación o de la cesta de compras de exploración.

> [!NOTE]
> Middleware de autorización del IdentityServer debe agregarse a la canalización de solicitudes HTTP de la aplicación web antes de agregar MVC con `app.UseMvc()` o `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Hacer que las solicitudes de acceso a las API

Al realizar peticiones a los microservicios de la cesta de compras y ordenación, el acceso de token, obtenida IdentityServer durante el proceso de autenticación, debe incluirse en la solicitud, tal como se muestra en el ejemplo de código siguiente:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

El token de acceso se almacena como una configuración de aplicación y es recuperar desde el almacenamiento específico de la plataforma e incluido en la llamada a la `GetOrderAsync` método en el `OrderService` clase.

De forma similar, el token de acceso debe incluirse al enviar datos a un IdentityServer protegidos API, tal como se muestra en el ejemplo de código siguiente:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

El token de acceso es recuperar desde el almacenamiento específico de la plataforma y se incluye en la llamada a la `UpdateBasketAsync` método en el `BasketService` clase.

El `RequestProvider` (clase), en la aplicación móvil de eShopOnContainers, usa el `HttpClient` clase para realizar solicitudes a las API de REST expuestas por la aplicación de referencia eShopOnContainers. Al realizar solicitudes a los pedidos y la cesta de la API, que requieren autorización, debe incluirse un token de acceso válido con la solicitud. Esto se logra agregando el token de acceso a los encabezados de la `HttpClient` de instancia, tal como se muestra en el ejemplo de código siguiente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

El `DefaultRequestHeaders` propiedad de la `HttpClient` clase expone los encabezados que se envían con cada solicitud y el token de acceso se agrega a la `Authorization` el prefijo con la cadena de encabezado `Bearer`. Cuando la solicitud se envía a una API RESTful, el valor de la `Authorization` encabezado se extrae y se validan para asegurarse de que se ha enviado desde un emisor de confianza y usa para determinar si el usuario tiene permiso para invocar la API que lo recibe.

Para obtener más información acerca de cómo la aplicación móvil de eShopOnContainers realiza solicitudes web, consulte [acceso a datos remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Resumen

Existen muchos enfoques para integrar la autenticación y autorización en una aplicación de Xamarin.Forms que se comunica con una aplicación web ASP.NET MVC. La aplicación móvil de eShopOnContainers realiza la autenticación y autorización con un microservicio en contenedor de identidad que usa 4 IdentityServer. IdentityServer es un marco de código abierto de OAuth 2.0 y OpenID Connect para ASP.NET Core que se integra con ASP.NET Core Identity para realizar la autenticación de token de portador.

La aplicación móvil solicita tokens de seguridad de IdentityServer, para autenticar un usuario o para tener acceso a un recurso. Al acceder a un recurso, un token de acceso debe incluirse en la solicitud a las API que requieren autorización. Middleware de IdentityServer valida los tokens de acceso entrante para asegurarse de que se envían desde un emisor de confianza y que son válidos para su uso con la API que recibe.


## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
