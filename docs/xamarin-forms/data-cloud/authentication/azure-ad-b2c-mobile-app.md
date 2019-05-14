---
title: La integración de Azure Active Directory B2C con Azure Mobile Apps
description: Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se muestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de Azure Mobile Apps con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2019
ms.openlocfilehash: 4c3cc489f7c65636ad9f2b5541e552665b10980e
ms.sourcegitcommit: 0cb62b02a7efb5426f2356d7dbdfd9afd85f2f4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/13/2019
ms.locfileid: "65557264"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>La integración de Azure Active Directory B2C con Azure Mobile Apps

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_Azure B2C de Active Directory proporciona administración de identidad en la nube para aplicaciones orientadas al consumidor. En este artículo se explica cómo usar Azure Active Directory B2C para integrar la administración de identidad en una instancia de Azure Mobile Apps con Xamarin.Forms._

## <a name="overview"></a>Información general

Azure Mobile Apps le permiten desarrollar aplicaciones con un backend escalable, hospedada en Azure App Service. Admite la autenticación de dispositivos móvil, sincronización sin conexión e insertarlo notificaciones. Para obtener más información acerca de Azure Mobile Apps, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md), y [la autenticación de usuarios con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure B2C de Active Directory es un servicio de administración de identidad para las aplicaciones orientadas al consumidor, que permite que los consumidores:

- Inicie sesión con sus cuentas sociales existentes (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Crear nuevas credenciales (dirección de correo electrónico y contraseña, o nombre de usuario y contraseña). Estas credenciales se conocen como *local* cuentas.

Para obtener más información acerca de Azure Active Directory B2C, consulte [la autenticación de usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure B2C de Active Directory puede usarse para administrar el flujo de trabajo de autenticación para aplicaciones móviles de Azure. Este enfoque configura la administración de identidades en la nube y permite que los cambios sin modificar el código de la aplicación.


Azure Mobile Apps con Azure Active Directory B2C permite dos métodos de autenticación:

- [Administrado por el cliente](#client-managed-authentication) : la aplicación móvil Xamarin.Forms se inicia el proceso de autenticación con el inquilino de Azure Active Directory B2C y pasa el token de autenticación recibido a la instancia de Azure Mobile Apps.
- [Administrado por el servidor](#server-managed-authentication) : la instancia de Azure Mobile Apps usa el inquilino de Azure Active Directory B2C para iniciar el proceso de autenticación a través de un flujo de trabajo basado en web.

En ambos casos, se proporciona la experiencia de autenticación del inquilino de Azure Active Directory B2C. En la aplicación de ejemplo, la pantalla Inicio de sesión debería parecerse a las capturas de pantalla siguiente:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Página de inicio de sesión")

En este ejemplo se muestra que inicie sesión con una cuenta local, pero también puede habilitar Microsoft, Google, Facebook y otros proveedores de identidades.

## <a name="setup"></a>Programa de instalación

En ambos flujos de trabajo, el proceso inicial para la integración de un inquilino de Azure Active Directory B2C con Azure Mobile Apps es como sigue:

1. Cree una instancia de Azure Mobile Apps en Azure portal. Esto genera un proyecto de inicio similar para el proyecto de ejemplo. Para obtener más información, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Crear a un inquilino de Azure Active Directory B2C. Para obtener más información, consulte [la autenticación de usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).
1. Habilitar la autenticación en la instancia de Azure Mobile Apps y la aplicación de Xamarin.Forms. Para obtener más información, consulte [la autenticación de usuarios con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).

> [!NOTE]
> La biblioteca de autenticación de Microsoft (MSAL) se requiere cuando se usa un flujo de trabajo de autenticación administrada por el cliente. MSAL usa el explorador web del dispositivo para autenticar. Una vez completada la autenticación en el explorador, se redirige al usuario a un esquema de dirección URL personalizado. La aplicación registra el esquema de dirección URL personalizado, lo que le permite recuperar el control de la experiencia del usuario y responder a la autenticación. Para obtener más información sobre cómo usar MSAL para comunicarse con un inquilino de Azure Active Directory B2C, consulte [la autenticación de usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

## <a name="client-managed-authentication"></a>Autenticación administrada por el cliente

En la autenticación administrada por el cliente, una aplicación de Xamarin.Forms se pone en contacto con un inquilino de Azure Active Directory B2C para iniciar el flujo de autenticación. Después de la sesión, el inquilino de B2C de Azure Active Directory devuelve un token de identidad, que se proporciona a la instancia de Azure Mobile Apps. Esto permite que la aplicación de Xamarin.Forms realizar acciones que requieren permisos de usuario autenticado.

### <a name="azure-active-directory-b2c-client-managed-tenant-configuration"></a>Configuración de inquilino administrada por el cliente de Azure B2C de Active Directory

Un flujo de trabajo de autenticación administrada por el cliente, el inquilino de B2C de Azure Active Directory debe configurarse como sigue:

- Establecer **incluir cliente nativo** en "Sí".
- Establecer el URI de redireccionamiento personalizado. La documentación de MSAL recomienda con "msal" combinada con el identificador de la aplicación y seguido de ":/ / auth /". Para obtener más información, consulte [URI de redirección de las aplicaciones cliente MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#redirect-uri).

Captura de pantalla siguiente muestra esta configuración:

[!["Configuración de azure B2C de Active Directory"](azure-ad-b2c-mobile-app-images/azure-redirect-uri-cropped.png)](azure-ad-b2c-mobile-app-images/azure-redirect-uri.png#lightbox "configuración de Azure Active Directory B2C")

También se debe configurar la directiva de inicio de sesión usada en el inquilino de Azure Active Directory B2C para que la dirección URL de respuesta se establece en el mismo esquema de dirección URL personalizado. Esto puede lograrse seleccionando **ejecutar flujo de usuario** en el portal de Azure para tener acceso a la configuración de directiva. Guarde la dirección URL de metadatos se encuentra en esta pantalla porque lo necesitará para la configuración de la aplicación móvil. Captura de pantalla siguiente muestra esta configuración y la dirección URL que se debe copiar:

[!["Azure Active Directory B2C las directivas"](azure-ad-b2c-mobile-app-images/client-flow-policies-cropped.png)](azure-ad-b2c-mobile-app-images/client-flow-policies.png#lightbox "configuración de directiva de Azure B2C de Active Directory")

### <a name="azure-mobile-app-configuration"></a>Configuración de aplicación móvil de Azure

Un flujo de trabajo de autenticación administrada por el cliente, configure la instancia de Azure Mobile Apps como sigue:

- Autenticación de App Service debe estar activada.
- La acción necesaria cuando una solicitud no está autenticada debe establecerse en **inicie sesión con Azure Active Directory**.

Captura de pantalla siguiente muestra esta configuración:

[!["Configuración de autenticación de azure Mobile Apps"](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "configuración de autenticación de Azure Mobile Apps")

Configure la instancia de Azure Mobile Apps para comunicarse con el inquilino de Azure Active Directory B2C:

- Haga clic en configuración de Azure Active Directory y habilitar **avanzadas** modo para el proveedor de autenticación de Azure Active Directory.
- Establecer **Id. de cliente** a la **Id. de aplicación** del inquilino de Azure Active Directory B2C.
- Establecer el **Url del emisor** a la dirección URL de metadatos de la directiva que ha copiado anteriormente durante la configuración de inquilino.

Captura de pantalla siguiente muestra esta configuración:

!["Configuración avanzada de azure Mobile Apps"](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>Iniciar sesión

El ejemplo de código siguiente muestra las llamadas de método clave para iniciar un flujo de trabajo de autenticación administrada por el cliente:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...

    authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        string.Empty,
        UIBehavior.SelectAccount,
        string.Empty,
        App.UiParent);

    ...

    var payload = new JObject();
    if (authenticationResult != null && !string.IsNullOrWhiteSpace(authenticationResult.IdToken))
    {
        payload["access_token"] = authenticationResult.IdToken;
    }

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    success = true;

    ...
}
```

La biblioteca de autenticación de Microsoft (MSAL) se usa para iniciar un flujo de trabajo de autenticación con el inquilino de Azure Active Directory B2C. El `AcquireTokenAsync` método inicia el explorador web del dispositivo y muestra las opciones de autenticación definidas en la directiva de Azure Active Directory B2C especificado por la directiva que hace referencia a través de la `Constants.AuthoritySignin` constante. Esta directiva define la experiencia de inicio de sesión y registro del usuario y las notificaciones que recibe la aplicación tras una autenticación correcta.

El resultado de la `AcquireTokenAsync` llamada al método es un `AuthenticationResult` instancia. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contendrá un token de identidad, que se almacenarán en caché localmente. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contendrá los datos que indica el motivo del error de autenticación. Para obtener información sobre cómo usar MSAL para comunicarse con un inquilino de Azure Active Directory B2C, consulte [la autenticación de usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Cuando el `CurrentClient.LoginAsync` se invoca el método, la instancia de Azure Mobile Apps recibe el token de identidad que se encapsulan en un `JObject`. La presencia de un medio de símbolo (token) válido que no necesite la instancia de Azure Mobile Apps para iniciar su propio flujo de autenticación de OAuth 2.0. En su lugar, el `CurrentClient.LoginAsync` método devuelve un `MobileServiceUser` instancia que se almacenará en el `MobileServiceClient.CurrentUser` propiedad. Esta propiedad proporciona `UserId` y `MobileServiceAuthenticationToken` propiedades. Estos representan el usuario autenticado y el token, que se puede usar hasta que expire. El token de autenticación se incluirán en todas las solicitudes realizadas a la instancia de Azure Mobile Apps, lo que permite la aplicación de Xamarin.Forms para realizar acciones que requieren permisos de usuario autenticado.

### <a name="signing-out"></a>Cierre de sesión

En el ejemplo de código siguiente se muestra cómo se invoca el proceso de cierre de sesión administrado por el cliente:

```csharp
public async Task<bool> LogoutAsync()
{
    ...

    IEnumerable<IAccount> accounts = await ADB2CClient.GetAccountsAsync();
    while (accounts.Any())
    {
        await ADB2CClient.RemoveAsync(accounts.First());
        accounts = await ADB2CClient.GetAccountsAsync();
    }
    User = null;

    ...
}
```

El `CurrentClient.LogoutAsync` método Anular autentica al usuario con la instancia de Azure Mobile Apps y, a continuación, se borran todos los tokens de autenticación de la caché local creada por MSAL.

## <a name="server-managed-authentication"></a>Autenticación administrada por el servidor

En la autenticación administrada por el servidor, una aplicación de Xamarin.Forms se pone en contacto con una instancia de Azure Mobile Apps, que utiliza al inquilino de Azure Active Directory B2C para administrar el flujo de autenticación de OAuth 2.0 mostrando una página de inicio de sesión, tal como se define en la directiva de B2C. Después de iniciar sesión, la instancia de Azure Mobile Apps devuelve un token que permite que la aplicación de Xamarin.Forms realizar acciones que requieren permisos de usuario autenticado.

### <a name="azure-active-directory-b2c-server-managed-tenant-configuration"></a>Configuración de inquilino administrada por el servidor de Azure B2C de Active Directory

Un flujo de trabajo administrado por el servidor de autenticación, el inquilino de B2C de Azure Active Directory debe configurarse como sigue:

- Incluir una aplicación web o web API y permitir el flujo implícito.
- Establece la dirección URL de respuesta en la dirección de la aplicación móvil de Azure, a continuación, `/.auth/login/aad/callback`.

Captura de pantalla siguiente muestra esta configuración:

[!["Configuración de azure B2C de Active Directory"](azure-ad-b2c-mobile-app-images/server-flow-config-cropped.png)](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configuración de Azure Active Directory B2C")

La directiva usada en el inquilino de B2C de Azure Active Directory debe tener la dirección URL de respuesta configurada. Esto se consigue estableciendo la dirección URL de respuesta a la dirección de la aplicación móvil de Azure, seguido de `/.auth/login/aad/callback`. También debe guardar la dirección URL de metadatos se encuentra en la parte superior de esta pantalla porque lo necesitará para la configuración de la aplicación móvil. Captura de pantalla siguiente muestra esta configuración y la dirección URL de metadatos debería guardar:

!["Configuración de directiva de azure B2C de Active Directory"](azure-ad-b2c-mobile-app-images/server-flow-policies.png)

### <a name="azure-mobile-apps-instance-configuration"></a>Configuración de instancia de Azure Mobile Apps

Un flujo de trabajo administrado por el servidor de autenticación, la instancia de Azure Mobile Apps debe configurarse como sigue:

- Autenticación de App Service debe estar activada.
- La acción necesaria cuando una solicitud no está autenticada debe establecerse en **inicie sesión con Azure Active Directory**.

Captura de pantalla siguiente muestra esta configuración:

[!["Configuración de autenticación de azure Mobile Apps"](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "configuración de autenticación de Azure Mobile Apps")

La instancia de Azure Mobile Apps también debe configurarse para comunicarse con el inquilino de Azure Active Directory B2C:

- Haga clic en configuración de Azure Active Directory y habilitar **avanzadas** modo para el proveedor de autenticación de Azure Active Directory.
- Establecer **Id. de cliente** a la **Id. de aplicación** del inquilino de Azure Active Directory B2C.
- El **Url del emisor** es la dirección URL de metadatos de la directiva que ha copiado anteriormente durante la configuración de inquilino.

Captura de pantalla siguiente muestra esta configuración:

!["Configuración avanzada de azure Mobile Apps"](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>Iniciar sesión

El ejemplo de código siguiente muestra cómo iniciar un flujo de trabajo administrado por el servidor de autenticación:

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...

    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);

    ...
}
```

Cuando el `CurrentClient.LoginAsync` se invoca el método, la instancia de Azure Mobile Apps ejecuta la directiva de Azure Active Directory B2C vinculada, que se inicia el flujo de autenticación de OAuth 2.0. Cada `AuthenticateAsync` método es específica de la plataforma. Sin embargo, cada `AuthenticateAsync` llamadas al método el `CurrentClient.LoginAsync` método y especifica que se utilizará un inquilino de Azure Active Directory en el proceso de autenticación. Para obtener más información, consulte [iniciar sesión los usuarios](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

El `CurrentClient.LoginAsync` método devuelve un `MobileServiceUser` instancia que se almacenará en el `CurrentClient.CurrentUser` propiedad. Esta propiedad proporciona `UserId` y `MobileServiceAuthenticationToken` propiedades. Estos representan el usuario autenticado y un token de autenticación para el usuario, que se puede usar hasta que expire. El token de autenticación se incluirán en todas las solicitudes realizadas a la instancia de Azure Mobile Apps, lo que permite la aplicación de Xamarin.Forms para realizar acciones en la instancia de Azure Mobile Apps que requieren permisos de usuario autenticado.

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


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzureAuth ClientFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [TodoAzureAuth ServerFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticar a los usuarios con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Autenticar a los usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Paquete de nuget de biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentación de la biblioteca de autenticación de Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)