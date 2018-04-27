---
title: Autenticar a los usuarios con Azure Active Directory B2C
description: Azure Active B2C de directorio es una solución de administración de identidades de nube para aplicaciones web de consumo y móviles. Este artículo demuestra cómo usar la biblioteca de autenticación de Microsoft y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación móvil.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 627c6773c099c9cf45f871a9bb73a201bf98271a
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>Autenticar a los usuarios con Azure Active Directory B2C

_Azure Active B2C de directorio es una solución de administración de identidades de nube para aplicaciones web de consumo y móviles. Este artículo demuestra cómo usar la biblioteca de autenticación de Microsoft y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación móvil._

![](~/media/shared/preview.png "Esta API es actualmente una versión preliminar")

> [!NOTE]
> El [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) está en vista previa, pero es adecuado para su uso en un entorno de producción. Sin embargo, se puede ser cambios importantes en la API, formato de la memoria caché interna y otros mecanismos de la biblioteca, que puede afectar a la aplicación.

## <a name="overview"></a>Información general

Azure B2C directorio activo es un servicio de administración de identidad para las aplicaciones de consumo, que permite a los consumidores puedan iniciar sesión en la aplicación por:

- Uso de sus cuentas sociales existentes (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Crear nuevas credenciales (dirección de correo electrónico y contraseña, o nombre de usuario y contraseña). Estas credenciales se conocen como *local* cuentas.

El proceso para integrar el servicio de administración de identidades de Azure Active Directory B2C en una aplicación móvil es como sigue:

1. Crear a un inquilino de Azure Active Directory B2C. Para obtener más información, consulte [crear un inquilino de Azure Active Directory B2C en el portal de Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).
1. Registrar su aplicación móvil con el inquilino de Azure Active Directory B2C. El proceso de registro asigna un **Id. de aplicación** que identifica de forma única la aplicación y un **dirección URL de redireccionamiento** que puede utilizarse para dirigir las respuestas de nuevo a la aplicación. Para obtener más información, consulte [Azure Active Directory B2C: registrar la aplicación](/azure/active-directory-b2c/active-directory-b2c-app-registration/).
1. Crear una directiva de inicio de sesión e inicie sesión. Esta directiva definirá las experiencias que recorrerá los consumidores durante el inicio de sesión y de inicio de sesión y también especifica el contenido de la aplicación recibirá los tokens correcta suscribirse o inicio de sesión. Para obtener más información, consulte [Azure Active Directory B2C: directivas integradas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).
1. Use la [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) (MSAL) en la aplicación móvil para iniciar un flujo de trabajo de autenticación con su inquilino de Azure Active Directory B2C.

> [!NOTE]
> Así como integrar la administración de identidades de Azure Active Directory B2C en aplicaciones móviles, MSAL también puede utilizarse para integrar la administración de identidades de Azure Active Directory en aplicaciones móviles. Esto puede realizarse mediante el registro de una aplicación móvil con Azure Active Directory en el [Portal de registro de aplicación](https://apps.dev.microsoft.com/). El proceso de registro asigna un **identificador de la aplicación** que identifica de forma única la aplicación, que debe especificarse cuando se usa MSAL. Para obtener más información, consulte [cómo registrar una aplicación con el punto de conexión de v2.0](/azure/active-directory/develop/active-directory-v2-app-registration/), y [autenticar su móvil aplicaciones utilizando autenticación biblioteca Microsoft](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/) en el blog de Xamarin.

MSAL utiliza el explorador web del dispositivo para realizar la autenticación. Esto mejora la facilidad de uso de una aplicación, como los usuarios solo necesitan iniciar sesión una vez por cada dispositivo, mejorar las tasas de conversión de inicio de sesión y la autorización fluye en la aplicación. El explorador del dispositivo también proporciona una mayor seguridad. Después de que el usuario completa el proceso de autenticación, el control se devuelve a la aplicación desde la pestaña de explorador web. Esto se logra registrando una combinación de dirección URL personalizada para la dirección URL de redireccionamiento que se devuelve desde el proceso de autenticación y, a continuación, detectar y controlar la dirección URL personalizada una vez que se envía. Para obtener más información acerca de cómo elegir una combinación de dirección URL personalizada, vea [elegir un URI de redireccionamiento de aplicación nativa](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/).

> [!NOTE]
> El mecanismo de registro de una combinación de dirección URL personalizada con el sistema operativo y el esquema de control es específico para cada plataforma.

Cada solicitud que se envía a un inquilino de Azure Active Directory B2C especifica un *directiva*. Las directivas describen experiencias de identidad de consumidor como inicio de sesión o inicio de sesión. Por ejemplo, una directiva de inicio de sesión permite el comportamiento del inquilino de Azure Active Directory B2C configurarse a través de las siguientes opciones:

- Tipos de cuenta que los consumidores pueden usar para iniciar sesión en la aplicación.
- Recopilar datos del consumidor al realizar el registro.
- La autenticación multifactor.
- Contenido de la página de inicio de sesión.
- Notificaciones de tokens que recibe la aplicación móvil cuando se ejecuta la directiva.

Un inquilino de Azure Active Directory puede contener varias directivas de tipos diferentes, que, a continuación, se pueden usar en la aplicación según sea necesario. Además, las directivas se pueden reutilizar en todas las aplicaciones, lo que le permite definir y modificar experiencias de identidad de consumidor sin cambiar el código. Para obtener más información acerca de las directivas, consulte [Azure Active Directory B2C: directivas integradas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

## <a name="setup"></a>Programa de instalación

La biblioteca de NuGet de biblioteca de autenticación de Microsoft (MSAL) debe agregarse al proyecto de biblioteca de clases portables (PCL) y proyectos de plataforma en una solución de Xamarin.Forms. Las secciones siguientes proporcionan instrucciones de instalación adicionales para usar MSAL para comunicarse con un inquilino de Azure Active Directory B2C desde una aplicación móvil.

### <a name="portable-class-library"></a>Biblioteca de clases portable

PCLs que consumen MSAL deberá sea redirigida para usar Profile7. Para obtener más información sobre PCL, consulte [Introducción a las bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md).

### <a name="ios"></a>iOS

En iOS, el esquema de dirección URL personalizado que se haya registrado con Azure Active Directory B2C debe estar registrado en **Info.plist**, tal y como se muestra en la captura de pantalla siguiente:

![](azure-ad-b2c-images/customurl-ios.png "Registrar un esquema de dirección URL personalizada de iOS")

Cuando se completa la solicitud de autorización en Azure Active Directory B2C, redirige a la dirección URL de redireccionamiento registrado. Dado que la dirección URL utiliza un esquema personalizado se produce en el inicio de la aplicación móvil de iOS, pasando la dirección URL como un parámetro de inicio, donde lo procese el `OpenUrl` la invalidación de la aplicación `AppDelegate` (clase), que se muestra en el código siguiente ejemplo:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return true;
        }
    }
}
```

El código en el `OpenURL` método garantiza que el control vuelve al MSAL una vez que ha finalizado la parte interactiva del flujo de trabajo de autenticación.

### <a name="android"></a>Android

En Android, el esquema de dirección URL personalizado que se haya registrado con Azure Active Directory B2C debe estar registrado en **AndroidManifest.xml**, agregando una `<activity>` elemento dentro de las existentes `<application>` elemento. El `<activity>` elemento especifica la `IntentFilter` en el `Activity` que controla el esquema y se muestra en el ejemplo siguiente:

```xml
<application ...>
  <activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />
      <data android:scheme="INSERT_URL_SCHEME_HERE" android:host="auth" />
    </intent-filter>
  </activity>
</application>
```

Cuando se completa la solicitud de autorización en Azure Active Directory B2C, redirige a la dirección URL de redireccionamiento registrado. Dado que la dirección URL utiliza un esquema personalizado se produce en el inicio de la aplicación móvil de Android, pasando la dirección URL como un parámetro de inicio, donde lo procese el `microsoft.identity.client.BrowserTabActivity`. Tenga en cuenta que el `data android:scheme` propiedad debe establecerse en el esquema de direcciones URL personalizado que se registra con la aplicación de Azure Active Directory B2C.

Además, la `MainActivity` clase debe modificarse, tal como se muestra en el ejemplo de código siguiente:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            global::Xamarin.Forms.Forms.Init(this, bundle);
            Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
            LoadApplication(new App());
            App.UiParent = new UIParent(this);
        }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
        }
    }
}

```

El `OnCreate` método se modifica mediante la asignación de un `UIParent` de instancia para el `App.UiParent` propiedad. Esto garantiza que el flujo de autenticación se produce en el contexto de la actividad actual.

El código en el `OnActivityResult` método garantiza que el control vuelve al MSAL una vez que ha finalizado la parte interactiva del flujo de trabajo de autenticación.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En la plataforma Universal de Windows, se requiere ninguna configuración adicional para usar MSAL.

## <a name="initialization"></a>Inicialización

La biblioteca de autenticación de Microsoft usa los miembros de la `PublicClientApplication` clase para iniciar un flujo de trabajo de autenticación. La aplicación de ejemplo declara e inicializa un `public` propiedad de este tipo, denominado `ADB2CClient`, en la `AuthenticationProvider` clase. En el ejemplo de código siguiente se muestra cómo se inicializa esta propiedad:

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

Cuando la aplicación móvil se ha registrado con el inquilino de Azure Active Directory B2C, el proceso de registro se le asigna un **identificador de la aplicación**. Este identificador debe especificarse en el `PublicClientApplication` constructor, junto con un `Authority` constante que consta de una dirección URL base y la directiva de Azure Active Directory B2C va a ejecutar.

## <a name="signing-in"></a>Inicio de sesión

En las capturas de pantalla siguiente se muestra la pantalla de inicio de sesión en la aplicación de ejemplo:

![](azure-ad-b2c-images/login.png "Página de inicio de sesión")

Inicio de sesión de proveedores de identidades sociales o con una cuenta local, se permiten. Mientras que Microsoft, Google y Facebook, como se indicó anteriormente, se utilizan como proveedores de identidades sociales, también pueden utilizarse otros proveedores de identidad.

En el ejemplo de código siguiente se muestra cómo se invoca el proceso de inicio de sesión:

```csharp
using Microsoft.Identity.Client;

public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);
    ...
}

```

El `AcquireTokenAsync` método inicia el explorador web del dispositivo y se muestran las opciones de autenticación definidas en la directiva de Azure Active Directory B2C especificado por la directiva hace referencia a través de la `Constants.Authority` constante. Esta directiva define las experiencias de los consumidores pasará a través de durante la suscripción e inicio de sesión y las notificaciones de inicio de sesión o inicio de sesión, la aplicación recibirá correcta.

El resultado de la `AcquireTokenAsync` llamada al método es un `AuthenticationResult` instancia. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contendrá un token de identidad, que se almacenarán en caché localmente. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contiene datos que indica el motivo del error de autenticación.

En la aplicación de ejemplo, si la autenticación se realiza correctamente, la `TodoList` página se abrirá.

## <a name="silent-re-authentication"></a>Reautenticación silenciosa

Cuando el `LoginPage` aparece en el ejemplo de aplicación, se realiza un intento para recuperar un token de usuario sin mostrar ninguna interfaz de usuario de autenticación. Esto se logra con la `AcquireTokenSilentAsync` método, como se muestra en el ejemplo de código siguiente:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult;

    if (useSilent)
    {
        authenticationResult = await ADB2CClient.AcquireTokenSilentAsync(
            Constants.Scopes,
            GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
            Constants.Authority,
            false);
    }
    ...
}
```

El `AcquireTokenSilentAsync` método intenta recuperar un token de usuario de la memoria caché, sin solicitar al usuario que inicie sesión. Esto controla el escenario donde un token adecuado esté presente en la caché de sesiones anteriores. Si el intento de obtener un token es correcto, la `TodoList` página se abrirá. Si el intento de obtener un token es correcta, no ocurre nada y el usuario tendrá la opción de iniciar un nuevo flujo de trabajo de autenticación.

## <a name="signing-out"></a>Cierre de sesión

En el ejemplo de código siguiente se muestra cómo se invoca el proceso de cierre de sesión:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

Esto borra todos los tokens de autenticación de la memoria caché local.

## <a name="summary"></a>Resumen

Este artículo muestra cómo usar la biblioteca de autenticación de Microsoft (MSAL) y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación móvil. Azure Active B2C de directorio es una solución de administración de identidades de nube para aplicaciones web de consumo y móviles.


## <a name="related-links"></a>Vínculos relacionados

- [AzureADB2CAuth (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
