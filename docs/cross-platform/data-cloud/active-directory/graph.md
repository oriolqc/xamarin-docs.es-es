---
title: Obtener acceso a la API Graph
description: Este documento describe cómo agregar autenticación de Azure Active Directory a una aplicación móvil creada con Xamarin.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c43dfa79831f22e55490b27c3c360602ae717627
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781135"
---
# <a name="accessing-the-graph-api"></a>Obtener acceso a la API Graph

Siga estos pasos para usar la API de Graph desde dentro de una aplicación de Xamarin:

1. [Registrar con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) en el *windowsazure.com* portal, a continuación,
2. [Configurar servicios](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Paso 3. Agrega la autenticación de Active Directory a una aplicación

En la aplicación, agregue una referencia a **Azure autenticación biblioteca de Active Directory (Azure AAL)** mediante el Administrador de paquetes de NuGet en Visual Studio o Visual Studio para Mac.
Asegúrese de seleccionar **mostrar los paquetes de versión preliminar** para incluir este paquete, tal y como está todavía en vista previa.

> [!IMPORTANT]
> Nota: Azure 3.0 AAL actualmente es una vista previa y puede haber cambios importantes antes de que se publique la versión final. 


![](graph-images/06.-adal-nuget-package.jpg "Agregue una referencia a la biblioteca Azure Active Directory Authentication (AAL de Azure)")

En la aplicación, ahora debe agregar las siguientes variables de nivel de clase que son necesarias para el flujo de autenticación.

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

Hay que destacar aquí es `commonAuthority`. Cuando el punto de conexión de autenticación es `common`, la aplicación se convierte en **multiempresa**, lo que significa que cualquier usuario puede utilizar inicio de sesión con sus credenciales de Active Directory. Después de la autenticación, que el usuario se pueden usar en el contexto de su propio Active Directory: es decir, verá detalles relacionados con su Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Método para adquirir un Token de acceso de escritura

El código siguiente (para Android) se inicie la autenticación y tras la finalización, asigne el resultado en `authResult`. Las implementaciones de Windows Phone y iOS son ligeramente distintas: el segundo parámetro (`Activity`) es diferente en iOS y no están presentes en Windows Phone.

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

En el código anterior, el `AuthenticationContext` es responsable de la autenticación con commonAuthority. Tiene una `AcquireTokenAsync` método, que toman parámetros como un recurso que debe tener acceso a, en este caso `graphResourceUri`, `clientId`, y `returnUri`. La aplicación volverá a la `returnUri` cuando se completa la autenticación. Este código seguirá siendo el mismo para todas las plataformas, sin embargo, el último parámetro, `AuthorizationParameters`, será diferente en distintas plataformas y es responsable de que rigen el flujo de autenticación.

En el caso de Android o iOS, pasamos `this` parámetro `AuthorizationParameters(this)` para compartir el contexto, mientras que en Windows que se pasa sin ningún parámetro como nuevo `AuthorizationParameters()`.

### <a name="handle-continuation-for-android"></a>Identificador de continuación para Android

Una vez completada la autenticación, el flujo debe volver a la aplicación. En el caso de Android es controlada por el código siguiente, que deben agregarse a **MainActivity.cs**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>Identificador de continuación para Windows Phone

Para Windows Phone modificar el `OnActivated` método en el **App.xaml.cs** archivo con el siguiente código:

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

Ahora, si se ejecuta la aplicación, verá un cuadro de diálogo de autenticación.
Después de autenticarse correctamente, le solicitará los permisos para tener acceso a los recursos (en nuestro caso, API Graph):

![](graph-images/08.-authentication-flow.jpg "Después de autenticarse correctamente, le pedirá sus permisos para tener acceso a los recursos en nuestro caso API Graph")

Si la autenticación se realiza correctamente y ha autorizado la aplicación para tener acceso a los recursos, debe obtener un `AccessToken` y `RefreshToken` combinado en `authResult`. Estos tokens son necesarios para las llamadas de API aún más y para la autorización con Azure Active Directory en segundo plano.

![](graph-images/07.-access-token-for-authentication.jpg "Estos tokens son necesarios para las llamadas de API aún más y para la autorización con Azure Active Directory en segundo plano")

Por ejemplo, el código siguiente le permite obtener una lista de usuarios de Active Directory. Puede sustituir la URL de la API de Web con la API Web que está protegido por Azure AD.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

