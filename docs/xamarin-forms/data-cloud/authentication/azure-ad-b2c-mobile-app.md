---
title: "Integración de Azure Active Directory B2C con aplicaciones móviles de Azure"
description: "Azure Active B2C de directorio es una solución de administración de identidades de nube para aplicaciones web de consumo y móviles. Este artículo demuestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de aplicaciones móviles de Azure con Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: c28ddc09b07066de67f5c974cf5c2128726c6932
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>Integración de Azure Active Directory B2C con aplicaciones móviles de Azure

_Azure Active B2C de directorio es una solución de administración de identidades de nube para aplicaciones web de consumo y móviles. Este artículo demuestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de aplicaciones móviles de Azure con Xamarin.Forms._

![](~/media/shared/preview.png "Esta API es actualmente una versión preliminar")

> [!NOTE]
> El [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) está en vista previa, pero es adecuado para su uso en un entorno de producción. Sin embargo, se puede ser cambios importantes en la API, formato de la memoria caché interna y otros mecanismos de la biblioteca, que puede afectar a la aplicación.

## <a name="overview"></a>Información general

Aplicaciones móviles de Azure le permiten desarrollar aplicaciones con back-ends de escalable hospedado en el servicio de aplicación de Azure, con compatibilidad para la autenticación de dispositivos móvil, sincronización sin conexión y las notificaciones de inserción. Para obtener más información acerca de las aplicaciones móviles de Azure, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md), y [autenticar a los usuarios con aplicaciones de Azure Mobile](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure B2C directorio activo es un servicio de administración de identidad para las aplicaciones de consumo, que permite a los consumidores puedan iniciar sesión en la aplicación por:

- Uso de sus cuentas sociales existentes (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Crear nuevas credenciales (dirección de correo electrónico y contraseña, o nombre de usuario y contraseña). Estas credenciales se conocen como *local* cuentas.

Para obtener más información acerca de Azure Active Directory B2C, consulte [autenticar a los usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure B2C directorio activo puede utilizarse para administrar el flujo de trabajo de autenticación para una aplicación móvil de Azure. Con este enfoque, la experiencia de administración de identidad está totalmente definida en la nube y puede modificarse sin cambiar el código de aplicaciones móviles.

Hay dos flujos de trabajo de autenticación que se pueden adoptar cuando se integra a un inquilino de Azure Active Directory B2C con una instancia de aplicaciones móviles de Azure:

- [Administrado por el cliente](#client_managed) : en este enfoque el iniciado por la aplicación móvil Xamarin.Forms el proceso de autenticación con el inquilino de Azure Active Directory B2C y pasa el token de autenticación recibido a la instancia de aplicaciones móviles de Azure.
- [Servidor administrado](#server_managed) : en este enfoque, las aplicaciones móviles de Azure instancia utiliza el inquilino de Azure Active Directory B2C para iniciar el proceso de autenticación a través de un flujo de trabajo basado en web.

En ambos casos, la experiencia de autenticación es proporcionada por el inquilino de Azure Active Directory B2C. En la aplicación de ejemplo, el resultado en la pantalla de inicio de sesión que se muestra en las capturas de pantalla siguiente:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Página de inicio de sesión")

Inicio de sesión de proveedores de identidades sociales o con una cuenta local, se permiten. Aunque Facebook, Google y Microsoft se usan como proveedores de identidades sociales en este ejemplo, también pueden utilizarse otros proveedores de identidad.

## <a name="setup"></a>Programa de instalación

Sin tener en cuenta el flujo de trabajo de autenticación utilizado, el proceso inicial de integrar a un inquilino de Azure Active Directory B2C con una instancia de aplicaciones móviles de Azure es la siguiente:

1. Cree una instancia de aplicaciones móviles de Azure. Para obtener más información, consulte [consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Habilitar la autenticación en la instancia de aplicaciones móviles de Azure y la aplicación de Xamarin.Forms. Para obtener más información, consulte [autenticar a los usuarios con aplicaciones de Azure Mobile](~/xamarin-forms/data-cloud/authentication/azure.md).
1. Crear a un inquilino de Azure Active Directory B2C. Para obtener más información, consulte [autenticar a los usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Tenga en cuenta que la biblioteca de autenticación de Microsoft (MSAL) se requiere cuando se utiliza un flujo de trabajo de autenticación de cliente administrado. MSAL utiliza el explorador web del dispositivo para realizar la autenticación. Esto mejora la facilidad de uso de una aplicación, como los usuarios solo necesitan iniciar sesión una vez por cada dispositivo, mejorar las tasas de conversión de inicio de sesión y la autorización fluye en la aplicación. El explorador del dispositivo también proporciona una mayor seguridad. Después de que el usuario completa el proceso de autenticación, el control se devuelve a la aplicación desde la pestaña de explorador web. Esto se logra registrando una combinación de dirección URL personalizada para la dirección URL de redireccionamiento que se devuelve desde el proceso de autenticación y, a continuación, detectar y controlar la dirección URL personalizada una vez que se envía. Para obtener más información sobre cómo usar MSAL para comunicarse con un inquilino de Azure Active Directory B2C, consulte [autenticar a los usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

<a name="client_managed" />

## <a name="client-managed-authentication"></a>Autenticación de cliente administrado

En la autenticación de cliente administrado, una aplicación móvil de Xamarin.Forms se pone en contacto con un inquilino de Azure Active Directory B2C para iniciar un flujo de autenticación. Tras el inicio de sesión correcto del B2C de Azure Active Directory inquilino devuelve un token de identidad que, a continuación, se proporciona durante el inicio de sesión en la instancia de aplicaciones móviles de Azure. Esto permite que la aplicación de Xamarin.Forms realizar acciones en la instancia de aplicaciones móviles de Azure que requiera permisos de usuario autenticado.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configuración de inquilinos de Azure Active Directory B2C

Un flujo de trabajo administrado por el cliente de autenticación, el inquilino de Azure Active Directory B2C debe configurarse como sigue:

- Incluir a un cliente nativo.
- Establece el URI de redireccionamiento personalizado en un esquema de dirección URL que identifica de forma única la aplicación móvil, seguida de `://auth/`. Para obtener más información acerca de cómo elegir una combinación de dirección URL personalizada, vea [elegir un URI de redireccionamiento de aplicación nativa](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri).

Captura de pantalla siguiente muestra esta configuración:

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Configuración de Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "configuración de Azure Active Directory B2C")

La directiva utilizada en el B2C de Azure Active Directory inquilino también debe configurarse para que la dirección URL de respuesta se establece en el mismo esquema de dirección URL personalizado, seguido por `://auth/`. Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Directivas de Azure Active Directory B2C")

### <a name="azure-mobile-app-configuration"></a>Configuración de la aplicación móvil Azure

Un flujo de trabajo administrado por el cliente de autenticación, la instancia de aplicaciones móviles de Azure debe configurarse como sigue:

- Autenticación de servicio de aplicaciones debe estar activada.
- La acción a realizar cuando no se autentica una solicitud debe establecerse en **iniciar sesión con Azure Active Directory**.

Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Configuración de aplicaciones móviles de Azure")

La instancia de aplicaciones móviles de Azure también se debería configurar para comunicarse con el inquilino de Azure Active Directory B2C. Esto puede realizarse habilitando **avanzadas** modo para el proveedor de autenticación de Azure Active Directory, con el **Id. de cliente** que se va a la **Id. de aplicación** de Azure Inquilino de Active Directory B2C y el **dirección Url del emisor** ser el punto de conexión de metadatos para la directiva de Azure Active Directory B2C. Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "Configuración avanzada de las aplicaciones móviles Azure")

### <a name="signing-in"></a>Inicio de sesión

En el ejemplo de código siguiente se muestra cómo iniciar un flujo de trabajo administrado por el cliente de autenticación:

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

La biblioteca de autenticación de Microsoft (MSAL) se usa para iniciar un flujo de trabajo de autenticación con el inquilino de Azure Active Directory B2C. El `AcquireTokenAsync` método inicia el explorador web del dispositivo y muestra las opciones de autenticación definidos en la directiva de Azure Active Directory B2C especificado por la directiva hace referencia a través de la `Constants.Authority` constante. Esta directiva define las experiencias de los consumidores pasará a través de durante la suscripción e inicio de sesión y las notificaciones de inicio de sesión o inicio de sesión, la aplicación recibirá correcta.

El resultado de la `AcquireTokenAsync` llamada al método es un `AuthenticationResult` instancia. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contendrá un token de identidad, que se almacenarán en caché localmente. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contiene datos que indica el motivo del error de autenticación. Para obtener información sobre cómo usar MSAL para comunicarse con un inquilino de Azure Active Directory B2C, consulte [autenticar a los usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Cuando el `MobileServiceClient.LoginAsync` se invoca el método, la instancia de aplicaciones móviles de Azure recibe el token de identidad que se encapsulan en un `JObject`. La presencia de un símbolo (token) válido, significa que la instancia de aplicaciones móviles de Azure no es necesario iniciar su propio flujo de autenticación de OAuth 2.0. En su lugar, el `MobileServiceClient.LoginAsync` método devuelve un `MobileServiceUser` instancia que se almacenará en la `MobileServiceClient.CurrentUser` propiedad. Esta propiedad proporciona `UserId` y `MobileServiceAuthenticationToken` propiedades. Estos representan al usuario autenticado y un token de autenticación para el usuario, que se puede usar hasta que expire. El token de autenticación se incluirán en todas las solicitudes realizadas a la instancia de aplicaciones móviles de Azure, lo que permite la aplicación de Xamarin.Forms para realizar acciones en la instancia de aplicaciones móviles de Azure que requieren permisos de usuario autenticado.

### <a name="signing-out"></a>Cierre de sesión

En el ejemplo de código siguiente se muestra cómo se invoca el proceso de cierre de sesión administrados por el cliente:

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

El `MobileServiceClient.LogoutAsync` método Anular autentica al usuario con la instancia de aplicaciones móviles de Azure y, a continuación, se borran todos los tokens de autenticación de la memoria caché local creada por MSAL.

<a name="server_managed" />

## <a name="server-managed-authentication"></a>Autenticación de servidor administrado

En la autenticación de servidor administrado, una aplicación de Xamarin.Forms se pone en contacto con una instancia de aplicaciones móviles de Azure, que utiliza al inquilino de Azure Active Directory B2C para administrar el flujo de autenticación de OAuth 2.0, mostrando una página de inicio de sesión, tal como se define en la directiva B2C. A continuación sesión correctamente, la instancia de aplicaciones móviles de Azure devuelve un símbolo (token) que permite que la aplicación de Xamarin.Forms realizar acciones en la instancia de aplicaciones móviles de Azure que requieren permisos de usuario autenticado.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configuración de inquilinos de Azure Active Directory B2C

Un flujo de trabajo de autenticación de servidor administrado, el inquilino de Azure Active Directory B2C debe configurarse como sigue:

- Incluir una aplicación web o web API y permitir el flujo implícito.
- Establece la dirección URL de respuesta en la dirección de la aplicación móvil de Azure, a continuación, `/.auth/login/aad/callback`.

Captura de pantalla siguiente muestra esta configuración:

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Configuración de Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configuración de Azure Active Directory B2C")

La directiva utilizada en el B2C de Azure Active Directory inquilino también debe configurarse para que la dirección URL de respuesta se establece en la dirección de la aplicación móvil de Azure, seguido por `/.auth/login/aad/callback`. Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Directivas de Azure Active Directory B2C")

### <a name="azure-mobile-apps-instance-configuration"></a>Configuración de la instancia de aplicaciones móviles de Azure

Un flujo de trabajo de autenticación de servidor administrado, la instancia de aplicaciones móviles de Azure debe configurarse como sigue:

- Autenticación de servicio de aplicaciones debe estar activada.
- La acción a realizar cuando no se autentica una solicitud debe establecerse en **iniciar sesión con Azure Active Directory**.

Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Configuración de aplicaciones móviles de Azure")

La instancia de aplicaciones móviles de Azure también se debería configurar para comunicarse con el inquilino de Azure Active Directory B2C. Esto puede realizarse habilitando **avanzadas** modo para el proveedor de autenticación de Azure Active Directory, con el **Id. de cliente** que se va a la **Id. de aplicación** de Azure Inquilino de Active Directory B2C y el **dirección Url del emisor** ser el punto de conexión de metadatos para la directiva de Azure Active Directory B2C. Captura de pantalla siguiente muestra esta configuración:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "Configuración avanzada de las aplicaciones móviles Azure")

### <a name="signing-in"></a>Inicio de sesión

En el ejemplo de código siguiente se muestra cómo iniciar un flujo de trabajo de autenticación de servidor administrado:

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

Cuando el `MobileServiceClient.LoginAsync` se invoca el método, la instancia de aplicaciones móviles de Azure ejecuta la directiva de Azure Active Directory B2C vinculada, que inicia el flujo de autenticación de OAuth 2.0. Tenga en cuenta que cada `AuthenticateAsync` método es específica de la plataforma. Sin embargo, cada `AuthenticateAsync` método usa la `MobileServiceClient.LoginAsync` método y especifica que se utilizará un inquilino de Azure Active Directory en el proceso de autenticación. Para obtener más información, consulte [inicio de sesión a los usuarios](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

El `MobileServiceClient.LoginAsync` método devuelve un `MobileServiceUser` instancia que se almacenará en la `MobileServiceClient.CurrentUser` propiedad. Esta propiedad proporciona `UserId` y `MobileServiceAuthenticationToken` propiedades. Estos representan al usuario autenticado y un token de autenticación para el usuario, que se puede usar hasta que expire. El token de autenticación se incluirán en todas las solicitudes realizadas a la instancia de aplicaciones móviles de Azure, lo que permite la aplicación de Xamarin.Forms para realizar acciones en la instancia de aplicaciones móviles de Azure que requieren permisos de usuario autenticado.

### <a name="signing-out"></a>Cierre de sesión

En el ejemplo de código siguiente se muestra cómo se invoca el proceso de cierre de sesión de servidor administrado:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

El `MobileServiceClient.LogoutAsync` método Anular autentica al usuario con la instancia de aplicaciones móviles de Azure. Para obtener más información, consulte [registro Out usuarios](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out).

## <a name="summary"></a>Resumen

Este artículo muestra cómo usar Azure Active Directory B2C para proporcionar autenticación y autorización a una instancia de aplicaciones móviles de Azure con Xamarin.Forms. Azure Active B2C de directorio es una solución de administración de identidades de nube para aplicaciones web de consumo y móviles.


## <a name="related-links"></a>Vínculos relacionados

- [TodoAzureAuth ServerFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [Consumir una aplicación móvil de Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticar a los usuarios con aplicaciones móviles de Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Autenticar a los usuarios con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
