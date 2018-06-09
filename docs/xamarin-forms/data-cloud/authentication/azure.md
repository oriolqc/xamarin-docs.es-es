---
title: Autenticar a los usuarios con aplicaciones móviles de Azure
description: Este artículo explica cómo usar aplicaciones móviles de Azure para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: fc6206a22d7527ea38a39ab034c424bfe7730abb
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241720"
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>Autenticar a los usuarios con aplicaciones móviles de Azure

_Aplicaciones móviles de Azure utilizar una variedad de proveedores de identidades externo para admitir autenticar y autorizar usuarios de la aplicación, como Facebook, Google, Microsoft, Twitter y Azure Active Directory. Para restringir el acceso a los usuarios autenticados solo se pueden establecer permisos en las tablas. Este artículo explica cómo usar aplicaciones móviles de Azure para administrar el proceso de autenticación en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

El proceso para hacer que las aplicaciones móviles de Azure a administrar el proceso de autenticación en una aplicación de Xamarin.Forms es como sigue:

1. Registrar la aplicación móvil de Azure en el sitio del proveedor de identidades y, a continuación, establezca las credenciales generados por el proveedor en el back-end de aplicaciones móviles. Para obtener más información, consulte [registrar la aplicación para la autenticación y configurar servicios de aplicaciones](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services).
1. Definir un nuevo esquema de dirección URL para la aplicación de Xamarin.Forms, lo que permite el sistema de autenticación redirigir a Xamarin.Forms aplicación una vez completado el proceso de autenticación. Para obtener más información, consulte [se agrega la aplicación a las direcciones URL permite redirigir de externo](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl).
1. Restringir el acceso al aplicaciones móviles de Azure de back-end para sólo clientes autenticados. Para obtener más información, consulte [restrinja los permisos para los usuarios autenticados](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users).
1. Invoca la autenticación de la aplicación de Xamarin.Forms. Para obtener más información, consulte [agregar autenticación a la biblioteca de clases portable](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library), [agregar autenticación a la aplicación de iOS](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app), [agregar autenticación a la aplicación de Android](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)y [ Agregar autenticación a los proyectos de aplicación de Windows 10](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects).

> [!NOTE]
> En iOS 9 y versiones posteriores, seguridad de transporte de aplicación (ATS) exige conexiones seguras entre los recursos de internet (por ejemplo, el servidor de back-end de la aplicación) y la aplicación, lo que evita que la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se pueden alta ATS fuera de si no es posible utilizar la `HTTPS` del protocolo y proteger la comunicación para los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [seguridad de transporte de la aplicación](~/ios/app-fundamentals/ats.md).

Históricamente, las aplicaciones móviles usaron vistas web incrustado para realizar la autenticación con el proveedor de identidades. Esto ya no se recomienda por las razones siguientes:

- La aplicación que hospeda la vista web puede tener acceso a las credenciales de autenticación completa del usuario, no solo la concesión de autorización que se ha diseñado para la aplicación. Esto infringe el principio de privilegios mínimos, como la aplicación tiene acceso a credenciales más eficaces que el que necesita, puede aumentar la superficie de ataque de la aplicación.
- La aplicación host podría capturar los nombres de usuario y contraseñas, automáticamente envíe formularios y omitir el consentimiento del usuario y copie las cookies de sesión y usarlos para realizar acciones autenticadas como usuario.
- Vistas web incrustado no compartan el estado de autenticación con otras aplicaciones o el explorador del dispositivo web, exigir que el usuario al inicio de sesión para cada solicitud de autorización que se considera una experiencia del usuario inferior.
- Algunos extremos de autorización toman medidas para detectar y bloquear las solicitudes de autorización que proceden de vistas web.

La alternativa es usar el explorador web del dispositivo para realizar la autenticación, que es el enfoque tomado por la versión más reciente de la [SDK de cliente de Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/). Utilizando el explorador del dispositivo para las solicitudes de autenticación, mejora la facilidad de uso de una aplicación, ya que los usuarios sólo deben iniciar sesión en el proveedor de identidades una vez por cada dispositivo, mejorar las tasas de conversión de flujos de inicio de sesión y la autorización en la aplicación. El explorador del dispositivo también proporciona una mayor seguridad, como las aplicaciones pueden inspeccionar y modificar el contenido de una vista web, pero no se muestra en el Explorador de contenido.

## <a name="using-an-azure-mobile-apps-instance"></a>Uso de una instancia de aplicaciones móviles de Azure

El [SDK de cliente de Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) proporciona el `MobileServiceClient` (clase), que se utiliza una aplicación de Xamarin.Forms para tener acceso a la instancia de aplicaciones móviles de Azure.

La aplicación de ejemplo utiliza Google como proveedor de identidades, lo que permite a los usuarios con cuentas de Google para iniciar sesión en la aplicación de Xamarin.Forms. Mientras se utiliza Google como proveedor de identidades en este artículo, el enfoque es igualmente aplicable a otros proveedores de identidad.

<a name="logging-in" />

### <a name="logging-in-users"></a>Inicio de sesión a los usuarios

En las capturas de pantalla siguiente se muestra la pantalla de inicio de sesión en la aplicación de ejemplo:

![](azure-images/login.png "Página de inicio de sesión")

Mientras se utiliza Google como proveedor de identidades, se puede usar una variedad de otros proveedores de identidades e incluir Azure Active Directory, Microsoft, Twitter y Facebook.

En el ejemplo de código siguiente se muestra cómo se invoca el proceso de inicio de sesión:

```csharp
async void OnLoginButtonClicked(object sender, EventArgs e)
{
  ...
  if (App.Authenticator != null)
  {
    authenticated = await App.Authenticator.AuthenticateAsync();
  }
  ...
}
```

El `App.Authenticator` propiedad es una `IAuthenticate` instancia establecido por cada proyecto específico de la plataforma. El `IAuthenticate` interfaz especifica un `AuthenticateAsync` operación que se debe proporcionar cada proyecto de la plataforma. Por lo tanto, invocar el `App.Authenticator.AuthenticateAsync` el método se ejecuta el `IAuthenticate.AuthenticateAsync` método en un proyecto de plataforma.

Todos los de la plataforma `IAuthenticate.AuthenticateAsync` métodos llamada la `MobileServiceClient.LoginAsync` método para mostrar una interfaz y caché de datos de inicio de sesión. El siguiente ejemplo de código muestra la `LoginAsync` método para la plataforma de iOS:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    UIApplication.SharedApplication.KeyWindow.RootViewController,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

El siguiente ejemplo de código muestra la `LoginAsync` método para la plataforma Android:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    this,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

El siguiente ejemplo de código muestra la `LoginAsync` método para la plataforma Universal de Windows:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

En todas las plataformas, el `MobileServiceAuthenticationProvider` enumeración se utiliza para especificar el proveedor de identidad que se utilizará en el proceso de autenticación. Cuando el `MobileServiceClient.LoginAsync` se invoca el método, aplicaciones móviles de Azure iniciará un flujo de autenticación, mostrando la página de inicio de sesión del proveedor seleccionado y generando un token de autenticación después de iniciar sesión correctamente con el proveedor de identidades. El `MobileServiceClient.LoginAsync` método devuelve un `MobileServiceUser` instancia que se almacenará en la `MobileServiceClient.CurrentUser` propiedad. Esta propiedad proporciona `UserId` y `MobileServiceAuthenticationToken` propiedades. Estos representan al usuario autenticado y un token de autenticación para el usuario. El token de autenticación se incluirán en todas las solicitudes realizadas a la instancia de aplicaciones móviles de Azure, lo que permite la aplicación de Xamarin.Forms para realizar acciones en la instancia de aplicación móvil de Azure que requieren permisos de usuario autenticado.

<a name="logging-out" />

### <a name="logging-out-users"></a>Cerrar la sesión de los usuarios

En el ejemplo de código siguiente se muestra cómo se invoca el proceso de cierre de sesión:

```csharp
async void OnLogoutButtonClicked(object sender, EventArgs e)
{
  bool loggedOut = false;

  if (App.Authenticator != null)
  {
    loggedOut = await App.Authenticator.LogoutAsync ();
  }
  ...
}
```

El `App.Authenticator` propiedad es una `IAuthenticate` instancia establecido por cada platformproject. El `IAuthenticate` interfaz especifica un `LogoutAsync` operación que se debe proporcionar cada proyecto de la plataforma. Por lo tanto, invocar el `App.Authenticator.LogoutAsync` el método se ejecuta el `IAuthenticate.LogoutAsync` método en un proyecto de plataforma.

Todos los de la plataforma `IAuthenticate.LogoutAsync` llamadas de métodos el `MobileServiceClient.LogoutAsync` método Anular autenticar el usuario ha iniciado la sesión con el proveedor de identidades. El siguiente ejemplo de código muestra la `LogoutAsync` método para la plataforma de iOS:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  foreach (var cookie in NSHttpCookieStorage.SharedStorage.Cookies)
  {
    NSHttpCookieStorage.SharedStorage.DeleteCookie (cookie);
  }
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

El siguiente ejemplo de código muestra la `LogoutAsync` método para la plataforma Android:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  CookieManager.Instance.RemoveAllCookie();
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

El siguiente ejemplo de código muestra la `LogoutAsync` método para la plataforma Universal de Windows:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

Cuando el `IAuthenticate.LogoutAsync` se invoca el método, se borran las cookies establecidas por el proveedor de identidad, antes del `MobileServiceClient.LogoutAsync` método se invoca para anular autenticar el usuario ha iniciado la sesión con el proveedor de identidades.

## <a name="summary"></a>Resumen

Este artículo explica cómo usar aplicaciones móviles de Azure para administrar el proceso de autenticación en una aplicación de Xamarin.Forms. Aplicaciones móviles de Azure utilizar una variedad de proveedores de identidades externo para admitir autenticar y autorizar usuarios de la aplicación, como Facebook, Google, Microsoft, Twitter y Azure Active Directory. La `MobileServiceClient` clase es utilizada por la aplicación de Xamarin.Forms para controlar el acceso a la instancia de aplicaciones móviles de Azure.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzureAuth (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Agregar autenticación a la aplicación de Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [Cliente móvil Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
