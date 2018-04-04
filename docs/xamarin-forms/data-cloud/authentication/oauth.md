---
title: Autenticar a los usuarios con un proveedor de identidades
description: Xamarin.Auth es un SDK de multiplataforma para autenticar a los usuarios y almacenar sus cuentas. Incluye los autenticadores de OAuth que proporcionan compatibilidad para consumir los proveedores de identidad como Google, Microsoft, Facebook y Twitter. Este artículo explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: 26e85a37cfd36b5d4f045273548efafccca79e1a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="authenticating-users-with-an-identity-provider"></a>Autenticar a los usuarios con un proveedor de identidades

_Xamarin.Auth es un SDK de multiplataforma para autenticar a los usuarios y almacenar sus cuentas. Incluye los autenticadores de OAuth que proporcionan compatibilidad para consumir los proveedores de identidad como Google, Microsoft, Facebook y Twitter. Este artículo explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms._

OAuth es un estándar abierto para la autenticación y permite que el propietario de un recurso notificar a un proveedor de recursos que se deben conceder permisos a un tercero para tener acceso a su información sin compartir la identidad de los propietarios del recurso. Un ejemplo de esto podría habilitar un usuario notificar a un proveedor de identidades (por ejemplo, Google, Microsoft, Facebook o Twitter) que se debe conceder permiso a una aplicación para tener acceso a sus datos, sin compartir la identidad del usuario. Normalmente se utiliza como método para que los usuarios iniciar sesión en sitios Web y aplicaciones mediante un proveedor de identidad, pero sin exponer su contraseña para el sitio Web o aplicación.

Una descripción general del flujo de autenticación al utilizar un proveedor de identidades de OAuth es como sigue:

1. La aplicación navega en un explorador a una dirección URL de proveedor de identidad.
1. El proveedor de identidades controla la autenticación de usuario y devuelve un código de autorización para la aplicación.
1. La aplicación intercambia el código de autorización para un token de acceso del proveedor de identidades.
1. La aplicación utiliza el token de acceso para acceder a las API en el proveedor de identidad, como una API para solicitar datos de usuario básica.

La aplicación de ejemplo muestra cómo utilizar Xamarin.Auth para implementar un flujo de autenticación nativo con Google. Mientras se utiliza Google como proveedor de identidades en este tema, el enfoque es igualmente aplicable a otros proveedores de identidad. Para obtener más información acerca de la autenticación con el extremo de OAuth 2.0 de Google, consulte [utilizando OAuth2.0 a APIs de Google de acceso](https://developers.google.com/identity/protocols/OAuth2) en el sitio Web de Google.

> [!NOTE]
> En iOS 9 y versiones posteriores, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se pueden alta ATS fuera de si no es posible utilizar la `HTTPS` del protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>Uso de Xamarin.Auth para autenticar a los usuarios

Xamarin.Auth admite dos métodos para que las aplicaciones interactúan con el extremo de autorización del proveedor de identidades:

1. Usar una vista web incrustado. Si bien esto ha sido una práctica común, ya no se recomienda por las razones siguientes:

    - La aplicación que hospeda la vista web puede tener acceso a las credenciales de autenticación completa del usuario, no solo la concesión de autorización de OAuth que se ha diseñado para la aplicación. Esto infringe el principio de privilegios mínimos, como la aplicación tiene acceso a credenciales más eficaces que el que necesita, puede aumentar la superficie de ataque de la aplicación.
    - La aplicación host podría capturar los nombres de usuario y contraseñas, automáticamente envíe formularios y omitir el consentimiento del usuario y copie las cookies de sesión y usarlos para realizar acciones autenticadas como usuario.
    - Vistas web incrustado no compartan el estado de autenticación con otras aplicaciones o el explorador del dispositivo web, exigir que el usuario al inicio de sesión para cada solicitud de autorización que se considera una experiencia del usuario inferior.
    - Algunos extremos de autorización toman medidas para detectar y bloquear las solicitudes de autorización que proceden de vistas web.

1. Usar el explorador del dispositivo web, que es el enfoque recomendado. Usar el explorador del dispositivo para las solicitudes de OAuth mejora la facilidad de uso de una aplicación, ya que los usuarios sólo deben iniciar sesión en el proveedor de identidades una vez por cada dispositivo, mejorar las tasas de conversión de flujos de inicio de sesión y la autorización en la aplicación. El explorador del dispositivo también proporciona una mayor seguridad, como las aplicaciones pueden inspeccionar y modificar el contenido de una vista web, pero no se muestra en el Explorador de contenido. Este es el enfoque adoptado en esta aplicación de ejemplo y el artículo.

En el diagrama siguiente, se muestra una descripción general de cómo utiliza la aplicación de ejemplo Xamarin.Auth para autenticar a los usuarios y recuperar sus datos básicos:

![](oauth-images/google-auth.png "Usar Xamarin.Auth para autenticar con Google")

La aplicación realiza una solicitud de autenticación al uso de Google la `OAuth2Authenticator` clase. Se devuelve una respuesta de autenticación, una vez que el usuario se autentica correctamente con Google a través de su página de inicio de sesión, que incluye un token de acceso. A continuación, la aplicación realiza una solicitud a Google para datos de usuario básica mediante el `OAuth2Request` (clase), con el token de acceso que se incluye en la solicitud.

### <a name="setup"></a>Programa de instalación

Debe crearse un proyecto de consola de API de Google para integrar el inicio de sesión en Google con una aplicación de Xamarin.Forms. Esto se puede lograr de la siguiente manera:

1. Vaya a la [consola de API de Google](http://console.developers.google.com) sitio Web e inicie sesión con credenciales de la cuenta de Google.
1. En la lista desplegable proyecto, seleccione un proyecto existente o crear uno nuevo.
1. En la barra lateral en "Administrador de API", seleccione **credenciales**, a continuación, seleccione la **ficha de pantalla de consentimiento de OAuth**. Elija un **dirección de correo electrónico**, especifique un **nombre de producto que se muestra a los usuarios**y presione **guardar**.
1. En el **credenciales** ficha, seleccione la **crear credenciales** lista desplegable lista y elija **identificador de cliente OAuth**.
1. En **tipo de aplicación**, seleccione la plataforma que se ejecutará la aplicación móvil (**iOS** o **Android**).
1. Rellene los detalles necesarios y seleccione el **crear** botón.

> [!NOTE]
> Un identificador de cliente permite que una aplicación tener acceso a habilitado APIs Google y para aplicaciones móviles es único en una plataforma única. Por lo tanto, un **identificador de cliente OAuth** deben crearse para cada plataforma que se va a usar en el inicio de sesión de Google.

Después de realizar estos pasos, Xamarin.Auth puede utilizarse para iniciar un flujo de autenticación de OAuth2 con Google.

### <a name="creating-and-configuring-an-authenticator"></a>Crear y configurar un autenticador

Del Xamarin.Auth `OAuth2Authenticator` clase es responsable de controlar el flujo de autenticación de OAuth. En el ejemplo de código siguiente se muestra la creación de instancias de la `OAuth2Authenticator` clase al realizar la autenticación mediante el explorador web del dispositivo:

```csharp
var authenticator = new OAuth2Authenticator(
    clientId,
    null,
    Constants.Scope,
    new Uri(Constants.AuthorizeUrl),
    new Uri(redirectUri),
    new Uri(Constants.AccessTokenUrl),
    null,
    true);
```

La `OAuth2Authenticator` clase requiere un número de parámetros, que son los siguientes:

- **Id. de cliente** : Esto identifica al cliente que está realizando la solicitud y se puede recuperar desde el proyecto en el [consola de API de Google](http://console.developers.google.com).
- **Secreto del cliente** : debe ser `null` o `string.Empty`.
- **Ámbito** : identifica el acceso de API que se solicita la aplicación y el valor informa a la pantalla de consentimiento que se muestra al usuario. Para obtener más información sobre los ámbitos, consulte [solicitud de autorización de API](https://developers.google.com/+/web/api/rest/oauth) en el sitio Web de Google.
- **Autorizar la dirección URL** : Esto identifica a la dirección URL donde se obtendrán el código de autorización de.
- **Dirección URL de redireccionamiento** : Esto identifica a la dirección URL donde se enviará la respuesta. El valor de este parámetro debe coincidir con uno de los valores que aparece en el **credenciales** pestaña para el proyecto en el [Google Developers Console](https://console.developers.google.com/).
- **Dirección AccessToken Url** : Esto identifica a la dirección URL utilizada para solicitar tokens de acceso después de que se obtiene un código de autorización.
- **GetUserNameAsync Func** : opcional `Func` que se usará para recuperar de forma asincrónica el nombre de usuario de la cuenta después de que se ha autenticado correctamente.
- **Usar la interfaz de usuario nativa** : `boolean` valor que indica si se debe usar el explorador web del dispositivo para realizar la solicitud de autenticación.

### <a name="setup-authentication-event-handlers"></a>La instalación de controladores de eventos de autenticación

Antes de presentar la interfaz de usuario, un controlador de eventos para el `OAuth2Authenticator.Completed` evento debe estar registrado, como se muestra en el ejemplo de código siguiente:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Este evento se desencadenará cuando el usuario se autentica correctamente o cancela el inicio de sesión de.

Si lo desea, un controlador de eventos para el `OAuth2Authenticator.Error` también se pueden registrar eventos.

### <a name="presenting-the-sign-in-user-interface"></a>Presentación de la interfaz de usuario de inicio de sesión

La interfaz de usuario de inicio de sesión se puede presentar al usuario mediante el uso de un presentador de inicio de sesión Xamarin.Auth, que se debe inicializar en cada proyecto de la plataforma. En el ejemplo de código siguiente se muestra cómo inicializar un presentador de inicio de sesión en la `AppDelegate` clase en el proyecto de iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

En el ejemplo de código siguiente se muestra cómo inicializar un presentador de inicio de sesión en la `MainActivity` clase en el proyecto de Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

El proyecto de biblioteca de clases portables (PCL), a continuación, puede invocar el presentador de inicio de sesión como sigue:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Tenga en cuenta que el argumento de la `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` método es el `OAuth2Authenticator` instancia. Cuando el `Login` se invoca el método, la interfaz de usuario de inicio de sesión se presenta al usuario en una pestaña de explorador de web del dispositivo, que se muestra en las capturas de pantalla siguiente:

![](oauth-images/login.png "Inicio de sesión de Google")

### <a name="processing-the-redirect-url"></a>Procesar la dirección URL de redireccionamiento

Después de que el usuario completa el proceso de autenticación, el control se devuelve a la aplicación desde la pestaña de explorador web. Esto se logra registrando una combinación de dirección URL personalizada para la dirección URL de redireccionamiento que se devuelve desde el proceso de autenticación y, a continuación, detectar y controlar la dirección URL personalizada una vez que se envía.

Al elegir una combinación de dirección URL personalizada para asociar a una aplicación, las aplicaciones deben usar un esquema basado en un nombre bajo su control. Esto se consigue con el nombre de identificador de paquete en iOS y el nombre del paquete en Android y, a continuación, invertir para realizar el esquema de dirección URL. Sin embargo, algunos proveedores de identidad, como Google, asignan identificadores de cliente basados en nombres de dominio, que, a continuación, se invierte y se utilizan como el esquema de dirección URL. Por ejemplo, si Google crea un identificador de cliente de `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, el esquema de dirección URL será `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Tenga en cuenta que solo un único `/` puede aparecer después del componente de esquema. Por lo tanto, es un ejemplo completo de una dirección URL de redireccionamiento utilizando una combinación de dirección URL personalizada `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Cuando el explorador web recibe una respuesta del proveedor de identidad que contiene una combinación de dirección URL personalizada, intenta cargar la dirección URL, que se producirá un error. En su lugar, el esquema de direcciones URL personalizado se notifica al sistema operativo cuando se genera un evento. El sistema operativo, a continuación, busca los esquemas registrados, y si encuentra alguno, el sistema operativo se inicie la aplicación registra el esquema y enviar la dirección URL de redireccionamiento.

El mecanismo de registro de una combinación de dirección URL personalizada con el sistema operativo y el esquema de control es específico para cada plataforma.

#### <a name="ios"></a>iOS

En iOS, una combinación de dirección URL personalizada se registra en **Info.plist**, tal y como se muestra en la captura de pantalla siguiente:

![](oauth-images/info-plist.png "Registro del esquema de direcciones URL")

El **identificador** valor puede ser cualquier cosa y el **rol** valor debe establecerse en **Visor**. El **esquemas de Url** valor, que comienza con `com.googleusercontent.apps`, puede obtenerse a partir del identificador de cliente de iOS para el proyecto en [consola de API de Google](http://console.developers.google.com).

Cuando el proveedor de identidades completa la solicitud de autorización, redirige a la dirección URL de redireccionamiento de la aplicación. Dado que la dirección URL utiliza un esquema personalizado se produce en el inicio de la aplicación de iOS, pasando la dirección URL como un parámetro de inicio, donde lo procese el `OpenUrl` la invalidación de la aplicación `AppDelegate` (clase), que se muestra en el ejemplo de código siguiente:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    // Convert NSUrl to Uri
    var uri = new Uri(url.AbsoluteString);

    // Load redirectUrl page
    AuthenticationState.Authenticator.OnPageLoading(uri);

    return true;
}
```

El `OpenUrl` método convierte la dirección URL recibida desde un `NSUrl` para .NET `Uri`, antes de procesar la dirección URL de redirección con el `OnPageLoading` método de un complemento público `OAuth2Authenticator` objeto. Esto hace que Xamarin.Auth para cerrar la pestaña de explorador web y para analizar los datos recibidos de OAuth.

#### <a name="android"></a>Android

En Android, una combinación de dirección URL personalizada está registrada mediante la especificación de un [ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) del atributo en el `Activity` que va a controlar el esquema. Cuando el proveedor de identidades completa la solicitud de autorización, redirige a la dirección URL de redireccionamiento de la aplicación. Como la dirección URL utiliza un esquema personalizado se produce en el inicio de la aplicación de Android, pasando la dirección URL como un parámetro de inicio, donde se procesan por el `OnCreate` método de la `Activity` registrado para controlar el esquema de dirección URL personalizada. En el ejemplo de código siguiente se muestra la clase de la aplicación de ejemplo que controla el esquema de direcciones URL personalizado:

```csharp
[Activity(Label = "CustomUrlSchemeInterceptorActivity", NoHistory = true, LaunchMode = LaunchMode.SingleTop )]
[IntentFilter(
    new[] { Intent.ActionView },
    Categories = new [] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataSchemes = new [] { "<insert custom URL here>" },
    DataPath = "/oauth2redirect")]
public class CustomUrlSchemeInterceptorActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Convert Android.Net.Url to Uri
        var uri = new Uri(Intent.Data.ToString());

        // Load redirectUrl page
        AuthenticationState.Authenticator.OnPageLoading(uri);

        Finish();
    }
}
```

El `DataSchemes` propiedad de la [ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) debe establecerse en el identificador de cliente invertido que se obtiene del identificador de cliente de Android para el proyecto en [consola de API de Google](http://console.developers.google.com).

El `OnCreate` método convierte la dirección URL recibida desde un `Android.Net.Url` para .NET `Uri`, antes de procesar la dirección URL de redirección con el `OnPageLoading` método de un complemento público `OAuth2Authenticator` objeto. Esto hace que Xamarin.Auth cerrar la pestaña de explorador web y analizar los datos recibidos de OAuth.

> [!IMPORTANT]
> En Android, Xamarin.Auth utiliza el `CustomTabs` API para comunicarse con el explorador web y el sistema operativo. Sin embargo, no garantiza que un `CustomTabs` explorador compatible se instalará en el dispositivo del usuario.

### <a name="examining-the-oauth-response"></a>Examen de la respuesta de OAuth

Después de analizar los datos recibidos de OAuth, Xamarin.Auth, se producirá la `OAuth2Authenticator.Completed` eventos. En el controlador de eventos para este evento, el `AuthenticatorCompletedEventArgs.IsAuthenticated` propiedad puede utilizarse para identificar si la autenticación se realizó correctamente, tal como se muestra en el ejemplo de código siguiente:

```csharp
async void OnAuthCompleted(object sender, AuthenticatorCompletedEventArgs e)
{
  ...
  if (e.IsAuthenticated)
  {
    ...
  }
}
```

Los datos recopilados de una autenticación correcta están disponibles en la `AuthenticatorCompletedEventArgs.Account` propiedad. Esto incluye un token de acceso, que se puede usar para firmar las solicitudes de datos a una API proporcionada por el proveedor de identidades.

### <a name="making-requests-for-data"></a>Realizar solicitudes de datos

Después de que la aplicación obtiene un token de acceso, se utiliza para realizar una solicitud a la `https://www.googleapis.com/oauth2/v2/userinfo` API para solicitar datos de usuario básica del proveedor de identidades. Esta solicitud se realiza con del Xamarin.Auth `OAuth2Request` (clase), que representa una solicitud que se autentica con una cuenta que se recuperan de un `OAuth2Authenticator` de instancia, tal como se muestra en el ejemplo de código siguiente:

```csharp
// UserInfoUrl = https://www.googleapis.com/oauth2/v2/userinfo
var request = new OAuth2Request ("GET", new Uri (Constants.UserInfoUrl), null, e.Account);
var response = await request.GetResponseAsync ();
if (response != null)
{
  string userJson = response.GetResponseText ();
  var user = JsonConvert.DeserializeObject<User> (userJson);
}
```

Así como el método HTTP y la dirección URL de API, el `OAuth2Request` instancia también especifica un `Account` instancia que contiene el token de acceso que firma la solicitud a la dirección URL especificada por el `Constants.UserInfoUrl` propiedad. El proveedor de identidades, a continuación, devuelve los datos de usuario básica como una respuesta JSON, incluido el nombre de los usuarios y la dirección de correo electrónico, siempre que el token de acceso reconoce como válido. La respuesta JSON, a continuación, se lee y se deserializa en el `user` variable.

Para obtener más información, consulte [al llamar a una API de Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) en el portal de desarrolladores de Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Almacenar y recuperar información de cuenta en los dispositivos

Almacena de forma segura Xamarin.Auth `Account` objetos en una cuenta de almacén para que las aplicaciones no siempre tiene que volver a autenticar a los usuarios. El `AccountStore` clase es responsable de almacenar información de la cuenta y está respaldado por servicios de llaves en iOS y la `KeyStore` clase en Android.

El siguiente ejemplo de código muestra cómo un `Account` segura se guarda el objeto:

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Cuentas de guardado se identifican mediante una clave compuesta de la cuenta `Username` propiedad y un identificador de servicio, que es una cadena que se utiliza al obtener las cuentas del almacén de cuentas. Si un `Account` previamente guardada, una llamada a la `Save` método nuevo lo sobrescribirá.

`Account` objetos de un servicio se puede recuperar mediante una llamada a la `FindAccountsForService` método, tal como se muestra en el ejemplo de código siguiente:

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

El `FindAccountsForService` método devuelve un `IEnumerable` colección de `Account` objetos, con el primer elemento de la colección que se establece como la cuenta coincidente.

## <a name="summary"></a>Resumen

Este artículo explica cómo usar Xamarin.Auth para administrar el proceso de autenticación en una aplicación de Xamarin.Forms. Xamarin.Auth proporciona el `OAuth2Authenticator` y `OAuth2Request` las clases que se usan las aplicaciones de Xamarin.Forms para consumir los proveedores de identidad como Google, Microsoft, Facebook y Twitter.


## <a name="related-links"></a>Vínculos relacionados

- [OAuthNativeFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)
- [OAuth 2.0 para aplicaciones nativas](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Uso de OAuth2.0 para acceder a las API de Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
