---
title: Autenticar a los usuarios con Azure Mobile Apps
description: En este artículo se explica cómo usar Azure Mobile Apps para administrar el proceso de autenticación en una aplicación de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 428e536d6895ff16a928f8cc40a8a7976d087471
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331332"
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>Autenticar a los usuarios con Azure Mobile Apps

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)

_Azure Mobile Apps usa una variedad de proveedores de identidades externos para admitir la autenticación y autorización de usuarios de la aplicación, incluidas Facebook, Google, Microsoft, Twitter y Azure Active Directory. Pueden establecerse para restringir el acceso a los usuarios autenticados solo permisos en las tablas. En este artículo se explica cómo usar Azure Mobile Apps para administrar el proceso de autenticación en una aplicación de Xamarin.Forms._

## <a name="overview"></a>Información general

El proceso para que Azure Mobile Apps administre el proceso de autenticación en una aplicación de Xamarin.Forms es como sigue:

1. Registrar la aplicación móvil de Azure en el sitio del proveedor de identidades y, a continuación, establezca las credenciales generadas por el proveedor en el back-end de Mobile Apps. Para obtener más información, consulte [registrar la aplicación para la autenticación y configurar servicios de aplicación](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services).
1. Definir un nuevo esquema de dirección URL para la aplicación de Xamarin.Forms, que permite que el sistema de autenticación se redirija a aplicación de Xamarin.Forms, una vez completado el proceso de autenticación. Para obtener más información, consulte [agregar la aplicación a la URL de redirección de externas permitidas](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl).
1. Restringir el acceso a lo back-end de Azure Mobile Apps para sólo clientes autenticados. Para obtener más información, consulte [restringir permisos a los usuarios autenticados](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users).
1. Invoca la autenticación de la aplicación de Xamarin.Forms. Para obtener más información, consulte [agregar autenticación a la biblioteca de clases portable](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library), [agregar autenticación a la aplicación de iOS](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app), [agregar autenticación a la aplicación de Android](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)y [ Agregar autenticación a proyectos de aplicaciones de Windows 10](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects).

> [!NOTE]
> En iOS 9 y versiones posteriores, App Transport Security (ATS) exige que las conexiones seguras entre los recursos de internet (por ejemplo, el servidor back-end de la aplicación) y la aplicación, lo que impide la divulgación accidental de información confidencial. Puesto que ATS está habilitada de forma predeterminada en las aplicaciones compiladas para iOS 9, todas las conexiones estará sujeto a los requisitos de seguridad ATS. Si las conexiones no cumplen estos requisitos, se producirá un error con una excepción.
> Se puede optar por en ATS de si no es posible usar la `HTTPS` del protocolo y proteger la comunicación de los recursos de internet. Esto puede lograrse mediante la actualización de la aplicación **Info.plist** archivo. Para obtener más información, consulte [App Transport Security](~/ios/app-fundamentals/ats.md).

Históricamente, las aplicaciones móviles han usado las vistas web incrustadas para realizar la autenticación con su proveedor de identidades. Esto ya no se recomienda por las razones siguientes:

- La aplicación que hospeda la vista web puede tener acceso a las credenciales del usuario autenticación completa, no solo la concesión de autorización que fue diseñada para la aplicación. Esto infringe el principio de privilegio mínimo, ya que la aplicación tiene acceso a las credenciales más eficaces que requiere, aumentando potencialmente la superficie de ataque de la aplicación.
- La aplicación host podría capturar los nombres de usuario y contraseñas, automáticamente enviar formularios y omitir el consentimiento del usuario y copie las cookies de sesión y usarlos para realizar acciones autenticadas como usuario.
- Vistas web incrustadas no compartan el estado de autenticación con otras aplicaciones o el explorador web del dispositivo, solicitar al usuario que inicie sesión para cada solicitud de autorización que se considera una experiencia de usuario inferiores.
- Algunos puntos de conexión de autorización tomar medidas para detectar y bloquear las solicitudes de autorización que procedan de vistas web.

La alternativa es usar el explorador web del dispositivo para realizar la autenticación, que es el enfoque adoptado por la versión más reciente de la [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/). Utilizando el explorador del dispositivo para las solicitudes de autenticación mejora la facilidad de uso de una aplicación, como los usuarios solo necesitan iniciar sesión en el proveedor de identidad una vez por dispositivo, mejora las tasas de conversión de flujos de inicio de sesión y la autorización en la aplicación. El explorador del dispositivo también proporciona seguridad mejorada, como las aplicaciones pueden inspeccionar y modificar el contenido en una vista web, pero no el contenido que se muestra en el explorador.

## <a name="using-an-azure-mobile-apps-instance"></a>Uso de una instancia de Azure Mobile Apps

El [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) proporciona el `MobileServiceClient` (clase), que usa una aplicación de Xamarin.Forms para tener acceso a la instancia de Azure Mobile Apps.

La aplicación de ejemplo utiliza Google como proveedor de identidades, que permite a los usuarios con cuentas de Google para iniciar sesión en la aplicación de Xamarin.Forms. Mientras se usa Google como proveedor de identidades en este artículo, el enfoque es igualmente aplicable a otros proveedores de identidades.

<a name="logging-in" />

### <a name="logging-in-users"></a>Los usuarios

En las capturas de pantalla siguiente se muestra la pantalla de inicio de sesión en la aplicación de ejemplo:

![](azure-images/login.png "Página de inicio de sesión")

Mientras se usa Google como proveedor de identidades, se puede usar una variedad de otros proveedores de identidades, incluidos Microsoft, Facebook, Twitter y Azure Active Directory.

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

El `App.Authenticator` propiedad es una `IAuthenticate` instancia establecido por cada proyecto específico de plataforma. El `IAuthenticate` interfaz especifica un `AuthenticateAsync` operación que se debe proporcionar cada proyecto de la plataforma. Por lo tanto, invoca el `App.Authenticator.AuthenticateAsync` método se ejecuta el `IAuthenticate.AuthenticateAsync` método en un proyecto de plataforma.

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

En todas las plataformas, la `MobileServiceAuthenticationProvider` enumeración se utiliza para especificar el proveedor de identidades que se usará en el proceso de autenticación. Cuando el `MobileServiceClient.LoginAsync` se invoca el método, Azure Mobile Apps se inicia un flujo de autenticación al mostrar la página de inicio de sesión del proveedor seleccionado y generando un token de autenticación después de iniciar sesión correctamente con el proveedor de identidades. El `MobileServiceClient.LoginAsync` método devuelve un `MobileServiceUser` instancia que se almacenará en el `MobileServiceClient.CurrentUser` propiedad. Esta propiedad proporciona `UserId` y `MobileServiceAuthenticationToken` propiedades. Estos representan el usuario autenticado y un token de autenticación para el usuario. El token de autenticación se incluirán en todas las solicitudes realizadas a la instancia de Azure Mobile Apps, lo que permite la aplicación de Xamarin.Forms para realizar acciones en la instancia de aplicación móvil de Azure que requieren permisos de usuario autenticado.

<a name="logging-out" />

### <a name="logging-out-users"></a>Cerrar la sesión de usuarios

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

El `App.Authenticator` propiedad es una `IAuthenticate` instancia establecido por cada platformproject. El `IAuthenticate` interfaz especifica un `LogoutAsync` operación que se debe proporcionar cada proyecto de la plataforma. Por lo tanto, invoca el `App.Authenticator.LogoutAsync` método se ejecuta el `IAuthenticate.LogoutAsync` método en un proyecto de plataforma.

Todos de la plataforma `IAuthenticate.LogoutAsync` métodos llamada la `MobileServiceClient.LogoutAsync` método para autenticar el usuario ha iniciado sesión con el proveedor de identidades a anular. El siguiente ejemplo de código muestra la `LogoutAsync` método para la plataforma de iOS:

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

Cuando el `IAuthenticate.LogoutAsync` se invoca el método, se borran las cookies establecidas por el proveedor de identidades, antes del `MobileServiceClient.LogoutAsync` método se invoca para anular autenticar el usuario ha iniciado sesión con el proveedor de identidades.

## <a name="summary"></a>Resumen

En este artículo se explica cómo usar Azure Mobile Apps para administrar el proceso de autenticación en una aplicación de Xamarin.Forms. Azure Mobile Apps usa una variedad de proveedores de identidades externos para admitir la autenticación y autorización de usuarios de la aplicación, incluidas Facebook, Google, Microsoft, Twitter y Azure Active Directory. La `MobileServiceClient` clase se utiliza la aplicación de Xamarin.Forms para controlar el acceso a la instancia de Azure Mobile Apps.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzureAuth (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Agregar autenticación a la aplicación de Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [SDK de Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
