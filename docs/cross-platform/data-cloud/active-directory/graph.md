---
title: Acceso a Graph API
description: Este documento describe cómo agregar autenticación de Azure Active Directory a una aplicación móvil con Xamarin.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c43dfa79831f22e55490b27c3c360602ae717627
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61190021"
---
# <a name="accessing-the-graph-api"></a>Acceso a Graph API

Siga estos pasos para usar la API Graph desde dentro de una aplicación de Xamarin:

1. [Registrar con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) en el *windowsazure.com* portal, a continuación,
2. [Configurar servicios](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Paso 3. Adición de autenticación de Active Directory a una aplicación

En la aplicación, agregue una referencia a **Azure Active Directory Authentication Library (ADAL de Azure)** con el Administrador de paquetes de NuGet en Visual Studio o Visual Studio para Mac.
Asegúrese de seleccionar **mostrar los paquetes preliminares** para incluir este paquete, ya que es aún en versión preliminar.

> [!IMPORTANT]
> Nota: 3.0 ADAL Azure actualmente es una versión preliminar y puede haber cambios importantes antes de que se publique la versión final. 


![](graph-images/06.-adal-nuget-package.jpg "Agregue una referencia a Azure Active Directory Authentication Library (ADAL de Azure)")

En la aplicación, ahora deberá agregar las siguientes variables de nivel de clase que son necesarias para el flujo de autenticación.

```csharp
//Client ID
public static string clientId = "25927d3c-.....-63f2304b90de";
public static string commonAuthority = "https://login.windows.net/common"
//Redirect URI
public static Uri returnUri = new Uri("http://xam-demo-redirect");
//Graph URI if you've given permission to Azure Active Directory
const string graphResourceUri = "https://graph.windows.net";
public static string graphApiVersion = "2013-11-08";
//AuthenticationResult will hold the result after authentication completes
AuthenticationResult authResult = null;
```

Hay que destacar aquí es `commonAuthority`. Cuando el punto de conexión de autenticación es `common`, la aplicación se convierte en **multiinquilino**, lo que significa que cualquier usuario puede utilizar inicio de sesión con sus credenciales de Active Directory. Después de la autenticación, dicho usuario funcionará en el contexto de su propio Active Directory: es decir, verá los detalles relacionados con su Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Método para adquirir un Token de acceso de escritura

El código siguiente (para Android), inicie la autenticación y la finalización asignará el resultado en `authResult`. Las implementaciones de Windows Phone y iOS difieren ligeramente: el segundo parámetro (`Activity`) es diferente en iOS y no están presentes en Windows Phone.

```csharp
public static async Task<AuthenticationResult> GetAccessToken
            (string serviceResourceId, Activity activity)
{
    authContext = new AuthenticationContext(Authority);
    if (authContext.TokenCache.ReadItems().Count() > 0)
        authContext = new AuthenticationContext(authContext.TokenCache.ReadItems().First().Authority);
    var authResult = await authContext.AcquireTokenAsync(serviceResourceId, clientId, returnUri, new AuthorizationParameters(activity));
    return authResult;
}  
```

En el código anterior, el `AuthenticationContext` es responsable de la autenticación con commonAuthority. Tiene un `AcquireTokenAsync` método, que toman parámetros como un recurso que debe tener acceso, en este caso `graphResourceUri`, `clientId`, y `returnUri`. La aplicación volverá a la `returnUri` cuando se completa la autenticación. Este código seguirá siendo el mismo para todas las plataformas, sin embargo, el último parámetro, `AuthorizationParameters`, serán diferentes en distintas plataformas y es responsable de la que rige el flujo de autenticación.

En el caso de Android o iOS, pasamos `this` parámetro `AuthorizationParameters(this)` para compartir el contexto, mientras que en Windows que se pasa sin ningún parámetro como nuevo `AuthorizationParameters()`.

### <a name="handle-continuation-for-android"></a>Identificador de continuación para Android

Una vez completada la autenticación, el flujo debe volver a la aplicación. En el caso de Android se controla mediante el código siguiente, que deben agregarse a **MainActivity.cs**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>Identificador de continuación para Windows Phone

Para Windows Phone, modifique la `OnActivated` método en el **App.xaml.cs** archivo con el siguiente código:

```csharp
protected override void OnActivated(IActivatedEventArgs args)
{
#if WINDOWS_PHONE_APP
  if (args is IWebAuthenticationBrokerContinuationEventArgs)
  {
     WebAuthenticationBrokerContinuationHelper.SetWebAuthenticationBrokerContinuationEventArgs(args as IWebAuthenticationBrokerContinuationEventArgs);
  }
#endif
  base.OnActivated(args);
}
```

Ahora si ejecuta la aplicación, debería ver un cuadro de diálogo de autenticación.
Tras una autenticación correcta, se le pedirá sus permisos para tener acceso a los recursos (en nuestro caso, Graph API):

![](graph-images/08.-authentication-flow.jpg "Tras una autenticación correcta, se le pedirá sus permisos para tener acceso a los recursos en nuestro caso, Graph API")

Si la autenticación se realiza correctamente y ha autorizado la aplicación para tener acceso a los recursos, debe obtener un `AccessToken` y `RefreshToken` combinado en `authResult`. Estos tokens son necesarios para aún más las llamadas de API y autorización con Azure Active Directory en segundo plano.

![](graph-images/07.-access-token-for-authentication.jpg "Estos tokens son necesarios para aún más las llamadas de API y autorización con Azure Active Directory en segundo plano")

Por ejemplo, el código siguiente permite obtener una lista de usuarios de Active Directory. Puede reemplazar la dirección URL de API Web con la API Web que está protegida por Azure AD.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

