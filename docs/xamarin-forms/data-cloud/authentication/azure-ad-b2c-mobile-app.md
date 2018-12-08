---
title: La integración de Azure Active Directory B2C con Azure Mobile Apps
description: Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se muestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de Azure Mobile Apps con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 135977329e2a190dd4c611937f6b8a664f135f5c
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051949"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>La integración de Azure Active Directory B2C con Azure Mobile Apps

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se muestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de Azure Mobile Apps con Xamarin.Forms._

![](~/media/shared/preview.png "Esta API es actualmente una versión preliminar")

> [!NOTE]
> El [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) está todavía en versión preliminar, pero es adecuado para su uso en un entorno de producción. Sin embargo, existe puede ser cambios importantes en la API, formato de la memoria caché interna y otros mecanismos de la biblioteca, que puede afectar a la aplicación.

## <a name="overview"></a>Información general

Azure Mobile Apps le permiten desarrollar aplicaciones con escalables back-ends hospedados en Azure App Service, con compatibilidad con autenticación móvil, sincronización sin conexión y las notificaciones de inserción. Para obtener más información acerca de Azure Mobile Apps, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md), y [la autenticación de usuarios con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure B2C de Active Directory es un servicio de administración de identidad para las aplicaciones orientadas al consumidor, que permite que los consumidores iniciar sesión con su aplicación por:

- Con sus cuentas de redes sociales existentes (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Creación de nuevas credenciales (dirección de correo electrónico y contraseña, o nombre de usuario y contraseña). Estas credenciales se conocen como *local* cuentas.

Para obtener más información acerca de Azure Active Directory B2C, consulte [la autenticación de usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure B2C de Active Directory puede usarse para administrar el flujo de trabajo de autenticación para aplicaciones móviles de Azure. Con este enfoque, la experiencia de administración de identidad está totalmente definida en la nube y puede modificarse sin cambiar el código de aplicación móvil.

Hay dos flujos de trabajo de autenticación que se pueden adoptar cuando se integra a un inquilino de Azure Active Directory B2C con una instancia de Azure Mobile Apps:

- [Administrado por el cliente](#client_managed) : en este enfoque la inicia la aplicación móvil Xamarin.Forms el proceso de autenticación con el inquilino de Azure Active Directory B2C y pasa el token de autenticación recibido a la instancia de Azure Mobile Apps.
- [Administrado por el servidor](#server_managed) : en este enfoque de Azure Mobile Apps instancia usa el inquilino de Azure Active Directory B2C para iniciar el proceso de autenticación a través de un flujo de trabajo basado en web.

En ambos casos, se proporciona la experiencia de autenticación del inquilino de Azure Active Directory B2C. En la aplicación de ejemplo, el resultado en la pantalla de inicio de sesión que se muestra en las capturas de pantalla siguiente:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Página de inicio de sesión")

Inicie sesión con proveedores de identidades sociales o con una cuenta local, se permiten. Aunque Facebook, Google y Microsoft se usan como proveedores de identidades sociales en este ejemplo, también se pueden usar otros proveedores de identidades.

## <a name="setup"></a>Programa de instalación

Independientemente del flujo de trabajo de autenticación usado, el proceso inicial para la integración de un inquilino de Azure Active Directory B2C con una instancia de Azure Mobile Apps es como sigue:

1. Cree una instancia de Azure Mobile Apps. Para obtener más información, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Habilitar la autenticación en la instancia de Azure Mobile Apps y la aplicación de Xamarin.Forms. Para obtener más información, consulte [la autenticación de usuarios con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).
1. Crear a un inquilino de Azure Active Directory B2C. Para obtener más información, consulte [la autenticación de usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Tenga en cuenta que la biblioteca de autenticación de Microsoft (MSAL) se requiere cuando se usa un flujo de trabajo de autenticación administrada por el cliente. MSAL usa el explorador web del dispositivo para realizar la autenticación. Esto mejora la facilidad de uso de una aplicación, como los usuarios solo necesitan iniciar sesión una vez por cada dispositivo, mejora las tasas de conversión de inicio de sesión y la autorización flujos en la aplicación. El explorador del dispositivo también proporciona seguridad mejorada. Después de que el usuario completa el proceso de autenticación, el control se devuelve a la aplicación desde la pestaña del explorador web. Esto se logra mediante el registro de un esquema de dirección URL personalizado para la dirección URL de redireccionamiento que se devuelve desde el proceso de autenticación y, a continuación, detectar y controlar la dirección URL personalizada, una vez que se envía. Para obtener más información sobre cómo usar MSAL para comunicarse con un inquilino de Azure Active Directory B2C, consulte [la autenticación de usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

<a name="client_managed" />

## <a name="client-managed-authentication"></a>Autenticación administrada por el cliente

En la autenticación administrada por el cliente, una aplicación móvil Xamarin.Forms pone en contacto con un inquilino de Azure Active Directory B2C para iniciar un flujo de autenticación. Tras el inicio de sesión correcto del B2C de Azure Active Directory inquilino devuelve un token de identidad que, a continuación, se proporciona durante el inicio de sesión en la instancia de Azure Mobile Apps. Esto permite que la aplicación de Xamarin.Forms realizar acciones en la instancia de Azure Mobile Apps que requiere permisos de usuario autenticado.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configuración de inquilino de B2C de Azure Active Directory

Un flujo de trabajo de autenticación administrada por el cliente, el inquilino de B2C de Azure Active Directory debe configurarse como sigue:

- Incluya a un cliente nativo.
- Establece el URI de redireccionamiento personalizado en un esquema de dirección URL que identifica la aplicación móvil, seguida de `://auth/`. Para obtener más información acerca de cómo elegir una combinación de dirección URL personalizada, vea [elegir un URI de redirección de aplicación nativa](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri).

Captura de pantalla siguiente muestra esta configuración:

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Configuración de B2C de Azure Active Directory")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "configuración de B2C de Azure Active Directory")

La directiva utilizada en el B2C de Azure Active Directory inquilino también debe configurarse para que la dirección URL de respuesta se establece en el mismo esquema de dirección URL personalizado, seguido por `://auth/`. Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Directivas de Azure Active Directory B2C")

### <a name="azure-mobile-app-configuration"></a>Configuración de aplicación móvil de Azure

Un flujo de trabajo de autenticación administrada por el cliente, la instancia de Azure Mobile Apps debe configurarse como sigue:

- Autenticación de App Service debe estar activada.
- La acción que se realizará cuando una solicitud no está autenticada debe establecerse en **inicie sesión con Azure Active Directory**.

Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Configuración de aplicaciones móviles de Azure")

La instancia de Azure Mobile Apps también debe configurarse para comunicarse con el inquilino de Azure Active Directory B2C. Esto puede realizarse habilitando **avanzadas** modo para el proveedor de autenticación de Azure Active Directory, con el **Id. de cliente** que se va a la **Id. de aplicación** de Azure Inquilino de Active Directory B2C y el **Url del emisor** ser el punto de conexión de metadatos para la directiva de Azure Active Directory B2C. Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "Azure Mobile Apps configuración avanzada")

### <a name="signing-in"></a>Iniciar sesión

El ejemplo de código siguiente muestra cómo iniciar un flujo de trabajo de autenticación administrada por el cliente:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);

    ...
    var payload = new JObject();
    payload["access_token"] = authenticationResult.IdToken;

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    ...
}
```

La biblioteca de autenticación de Microsoft (MSAL) se usa para iniciar un flujo de trabajo de autenticación con el inquilino de Azure Active Directory B2C. El `AcquireTokenAsync` método inicia el explorador web del dispositivo y muestra las opciones de autenticación definidas en la directiva de Azure Active Directory B2C especificado por la directiva que hace referencia a través de la `Constants.Authority` constante. Esta directiva define las experiencias que tendrán los consumidores durante el registro e inicio de sesión y las notificaciones que recibirá la aplicación en correcta registro o inicio de sesión.

El resultado de la `AcquireTokenAsync` llamada al método es un `AuthenticationResult` instancia. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contendrá un token de identidad, que se almacenarán en caché localmente. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contendrá los datos que indica el motivo del error de autenticación. Para obtener información sobre cómo usar MSAL para comunicarse con un inquilino de Azure Active Directory B2C, consulte [la autenticación de usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Cuando el `MobileServiceClient.LoginAsync` se invoca el método, la instancia de Azure Mobile Apps recibe el token de identidad que se encapsulan en un `JObject`. La presencia de un medio de símbolo (token) válido que no necesite la instancia de Azure Mobile Apps para iniciar su propio flujo de autenticación de OAuth 2.0. En su lugar, el `MobileServiceClient.LoginAsync` método devuelve un `MobileServiceUser` instancia que se almacenará en el `MobileServiceClient.CurrentUser` propiedad. Esta propiedad proporciona `UserId` y `MobileServiceAuthenticationToken` propiedades. Estos representan el usuario autenticado y un token de autenticación para el usuario, que se puede usar hasta que expire. El token de autenticación se incluirán en todas las solicitudes realizadas a la instancia de Azure Mobile Apps, lo que permite la aplicación de Xamarin.Forms para realizar acciones en la instancia de Azure Mobile Apps que requieren permisos de usuario autenticado.

### <a name="signing-out"></a>Cierre de sesión

En el ejemplo de código siguiente se muestra cómo se invoca el proceso de cierre de sesión administrado por el cliente:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

El `MobileServiceClient.LogoutAsync` método Anular autentica al usuario con la instancia de Azure Mobile Apps y, a continuación, se borran todos los tokens de autenticación de la caché local creada por MSAL.

<a name="server_managed" />

## <a name="server-managed-authentication"></a>Autenticación administrada por el servidor

En la autenticación administrada por el servidor, una aplicación de Xamarin.Forms se pone en contacto con una instancia de Azure Mobile Apps, que utiliza al inquilino de Azure Active Directory B2C para administrar el flujo de autenticación de OAuth 2.0 mostrando una página de inicio de sesión, tal como se define en la directiva de B2C. Después de iniciar sesión, la instancia de Azure Mobile Apps devuelve un token que permite que la aplicación de Xamarin.Forms realizar acciones en la instancia de Azure Mobile Apps que requieren permisos de usuario autenticado.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configuración de inquilino de B2C de Azure Active Directory

Un flujo de trabajo administrado por el servidor de autenticación, el inquilino de B2C de Azure Active Directory debe configurarse como sigue:

- Incluir una aplicación web o web API y permitir el flujo implícito.
- Establece la dirección URL de respuesta en la dirección de la aplicación móvil de Azure, a continuación, `/.auth/login/aad/callback`.

Captura de pantalla siguiente muestra esta configuración:

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Configuración de B2C de Azure Active Directory")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configuración de B2C de Azure Active Directory")

La directiva utilizada en el B2C de Azure Active Directory inquilino también debe configurarse para que se establece la dirección URL de respuesta a la dirección de la aplicación móvil de Azure, seguido por `/.auth/login/aad/callback`. Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Directivas de Azure Active Directory B2C")

### <a name="azure-mobile-apps-instance-configuration"></a>Configuración de la instancia de Azure Mobile Apps

Un flujo de trabajo administrado por el servidor de autenticación, la instancia de Azure Mobile Apps debe configurarse como sigue:

- Autenticación de App Service debe estar activada.
- La acción que se realizará cuando una solicitud no está autenticada debe establecerse en **inicie sesión con Azure Active Directory**.

Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Configuración de aplicaciones móviles de Azure")

La instancia de Azure Mobile Apps también debe configurarse para comunicarse con el inquilino de Azure Active Directory B2C. Esto puede realizarse habilitando **avanzadas** modo para el proveedor de autenticación de Azure Active Directory, con el **Id. de cliente** que se va a la **Id. de aplicación** de Azure Inquilino de Active Directory B2C y el **Url del emisor** ser el punto de conexión de metadatos para la directiva de Azure Active Directory B2C. Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "Azure Mobile Apps configuración avanzada")

### <a name="signing-in"></a>Iniciar sesión

El ejemplo de código siguiente muestra cómo iniciar un flujo de trabajo administrado por el servidor de autenticación:

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...
    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        UIApplication.SharedApplication.KeyWindow.RootViewController,
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);
    ...
}
```

Cuando el `MobileServiceClient.LoginAsync` se invoca el método, la instancia de Azure Mobile Apps ejecuta la directiva de Azure Active Directory B2C vinculada, que inicia el flujo de autenticación de OAuth 2.0. Tenga en cuenta que cada `AuthenticateAsync` método es específica de la plataforma. Sin embargo, cada `AuthenticateAsync` método usa el `MobileServiceClient.LoginAsync` método y especifica que se utilizará un inquilino de Azure Active Directory en el proceso de autenticación. Para obtener más información, consulte [iniciar sesión los usuarios](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

El `MobileServiceClient.LoginAsync` método devuelve un `MobileServiceUser` instancia que se almacenará en el `MobileServiceClient.CurrentUser` propiedad. Esta propiedad proporciona `UserId` y `MobileServiceAuthenticationToken` propiedades. Estos representan el usuario autenticado y un token de autenticación para el usuario, que se puede usar hasta que expire. El token de autenticación se incluirán en todas las solicitudes realizadas a la instancia de Azure Mobile Apps, lo que permite la aplicación de Xamarin.Forms para realizar acciones en la instancia de Azure Mobile Apps que requieren permisos de usuario autenticado.

### <a name="signing-out"></a>Cierre de sesión

El ejemplo de código siguiente muestra cómo se invoca el proceso de cierre de sesión administrado por el servidor:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

El `MobileServiceClient.LogoutAsync` método Anular autentica al usuario con la instancia de Azure Mobile Apps. Para obtener más información, consulte [registro Out usuarios](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out).

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de Azure Mobile Apps con Xamarin.Forms. Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzureAuth ServerFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticar a los usuarios con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Autenticar a los usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
