---
title: Autenticar a los usuarios con Azure Active Directory B2C
description: Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se muestra cómo usar la biblioteca de autenticación de Microsoft y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación móvil.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 7c12136a0dad0165c46f1559e7a2d61abaf7af1e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331504"
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>Autenticar a los usuarios con Azure Active Directory B2C

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se muestra cómo usar la biblioteca de autenticación de Microsoft y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación móvil._

![](~/media/shared/preview.png "Esta API es actualmente una versión preliminar")

> [!NOTE]
> El [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) está todavía en versión preliminar, pero es adecuado para su uso en un entorno de producción. Sin embargo, existe puede ser cambios importantes en la API, formato de la memoria caché interna y otros mecanismos de la biblioteca, que puede afectar a la aplicación.

## <a name="overview"></a>Información general

Azure B2C de Active Directory es un servicio de administración de identidad para las aplicaciones orientadas al consumidor, que permite que los consumidores iniciar sesión con su aplicación por:

- Con sus cuentas de redes sociales existentes (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Creación de nuevas credenciales (dirección de correo electrónico y contraseña, o nombre de usuario y contraseña). Estas credenciales se conocen como *local* cuentas.

El proceso para integrar el servicio de administración de identidades de Azure Active Directory B2C en una aplicación móvil es como sigue:

1. Crear a un inquilino de Azure Active Directory B2C. Para obtener más información, consulte [crear un inquilino de Azure Active Directory B2C en Azure portal](/azure/active-directory-b2c/active-directory-b2c-get-started/).
1. Registrar la aplicación móvil con el inquilino de Azure Active Directory B2C. El proceso de registro, se asigna un **Id. de aplicación** que identifica la aplicación y un **dirección URL de redireccionamiento** que puede utilizarse para dirigir las respuestas de nuevo a la aplicación. Para obtener más información, consulte [Azure Active Directory B2C: Registrar la aplicación](/azure/active-directory-b2c/active-directory-b2c-app-registration/).
1. Crear una directiva de registro e inicio de sesión. Esta directiva definirá las experiencias que tendrán los consumidores durante el registro e inicio de sesión y también especifica el contenido de los tokens que recibirá la aplicación en correcta registro o inicio de sesión. Para obtener más información, consulte [Azure Active Directory B2C: Las directivas integradas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).
1. Use la [biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) (MSAL) en la aplicación móvil para iniciar un flujo de trabajo de autenticación con el inquilino de Azure Active Directory B2C.

> [!NOTE]
> Así como la integración de administración de identidades de Azure Active Directory B2C en aplicaciones móviles, también se puede usar MSAL para integrar la administración de identidades de Azure Active Directory en aplicaciones móviles. Esto puede realizarse mediante el registro de una aplicación móvil con Azure Active Directory en el [Portal de registro de aplicación](https://apps.dev.microsoft.com/). El proceso de registro, se asigna un **Id. de aplicación** que identifica la aplicación, que se debe especificar con MSAL. Para obtener más información, consulte [cómo registrar una aplicación con el punto de conexión v2.0](/azure/active-directory/develop/active-directory-v2-app-registration/), y [autenticar Your Mobile aplicaciones utilizando autenticación biblioteca Microsoft](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/) en el blog de Xamarin.

MSAL usa el explorador web del dispositivo para realizar la autenticación. Esto mejora la facilidad de uso de una aplicación, como los usuarios solo necesitan iniciar sesión una vez por cada dispositivo, mejora las tasas de conversión de inicio de sesión y la autorización flujos en la aplicación. El explorador del dispositivo también proporciona seguridad mejorada. Después de que el usuario completa el proceso de autenticación, el control se devuelve a la aplicación desde la pestaña del explorador web. Esto se logra mediante el registro de un esquema de dirección URL personalizado para la dirección URL de redireccionamiento que se devuelve desde el proceso de autenticación y, a continuación, detectar y controlar la dirección URL personalizada, una vez que se envía. Para obtener más información acerca de cómo elegir una combinación de dirección URL personalizada, vea [elegir un URI de redirección de aplicación nativa](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/).

> [!NOTE]
> El mecanismo para registrar un esquema de dirección URL personalizado con el sistema operativo y el esquema de control es específico para cada plataforma.

Cada solicitud que se envía a un inquilino de Azure Active Directory B2C se especifica un *directiva*. Las directivas describen experiencias de identidad de consumidor como el registro o inicio de sesión. Por ejemplo, una directiva de registro permite el comportamiento del inquilino de Azure Active Directory B2C para configurarse a través de las siguientes opciones:

- Tipos de cuenta que los consumidores pueden usar para iniciar sesión en la aplicación.
- Datos que se recopilarán del consumidor durante el registro.
- Autenticación multifactor.
- Contenido de la página de registro.
- Notificaciones de tokens que recibe la aplicación móvil cuando la directiva se ha ejecutado.

Un inquilino de Azure Active Directory puede contener varias directivas de diferentes tipos, que, a continuación, se pueden usar en la aplicación según sea necesario. Además, las directivas se pueden reutilizar en las aplicaciones, lo que le permite definir y modificar experiencias de identidad de consumidor sin cambiar el código. Para obtener más información acerca de las directivas, consulte [Azure Active Directory B2C: Las directivas integradas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

## <a name="setup"></a>Programa de instalación

La biblioteca NuGet de biblioteca de autenticación de Microsoft (MSAL) debe agregarse al proyecto de biblioteca de clases portables (PCL) y proyectos de plataforma en una solución de Xamarin.Forms. Las secciones siguientes proporcionan instrucciones de instalación adicionales para el uso de MSAL para comunicarse con un inquilino de Azure Active Directory B2C desde una aplicación móvil.

### <a name="portable-class-library"></a>Biblioteca de clases portable

Las PCL que consumen MSAL debe cambiarse para usar Profile7. Para obtener más información sobre PCL, consulte [Introducción a las bibliotecas de clases portables](~/cross-platform/app-fundamentals/pcl.md).

### <a name="ios"></a>iOS

En iOS, el esquema de dirección URL personalizado que se registró con Azure Active Directory B2C debe estar registrado en **Info.plist**, tal y como se muestra en la captura de pantalla siguiente:

![](azure-ad-b2c-images/customurl-ios.png "Registrar un esquema de dirección URL personalizada en iOS")

Cuando Azure Active Directory B2C se completa la solicitud de autorización, redirige a la URL de redireccionamiento registrados. Dado que la dirección URL usa un esquema personalizado, se producirá iniciando la aplicación móvil de iOS, pasando la dirección URL como un parámetro de inicio, donde se procesó la `OpenUrl` la invalidación de la aplicación `AppDelegate` (clase), que se muestra en el código siguiente ejemplo:

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

El código en el `OpenURL` método garantiza que el control vuelve a MSAL una vez que la parte interactiva del flujo de trabajo de autenticación ha finalizado.

### <a name="android"></a>Android

En Android, el esquema de dirección URL personalizado que se registró con Azure Active Directory B2C debe estar registrado en **AndroidManifest.xml**, agregando un `<activity>` elemento dentro de las existentes `<application>` elemento. El `<activity>` elemento especifica el `IntentFilter` en el `Activity` que controla el esquema y se muestra en el ejemplo siguiente:

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

Cuando Azure Active Directory B2C se completa la solicitud de autorización, redirige a la URL de redireccionamiento registrados. Dado que la dirección URL usa un esquema personalizado, se producirá iniciando la aplicación móvil de Android, pasando la dirección URL como un parámetro de inicio, donde se procesó la `microsoft.identity.client.BrowserTabActivity`. Tenga en cuenta que el `data android:scheme` propiedad debe establecerse en el esquema de dirección URL personalizado que se registra con la aplicación de Azure Active Directory B2C.

Además, el `MainActivity` clase se debe modificar, como se muestra en el ejemplo de código siguiente:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : FormsAppCompatActivity
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

El código en el `OnActivityResult` método garantiza que el control vuelve a MSAL una vez que la parte interactiva del flujo de trabajo de autenticación ha finalizado.

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En la plataforma Universal de Windows, no se requiere ninguna configuración adicional para usar MSAL.

## <a name="initialization"></a>Inicialización

La biblioteca de autenticación de Microsoft usa los miembros de la `PublicClientApplication` clase para iniciar un flujo de trabajo de autenticación. La aplicación de ejemplo declara e inicializa un `public` propiedad de este tipo, denominado `ADB2CClient`, en el `AuthenticationProvider` clase. El ejemplo de código siguiente muestra cómo se inicializa esta propiedad:

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

Cuando se registró la aplicación móvil con el inquilino de Azure Active Directory B2C, el proceso de registro se le asigna un **Id. de aplicación**. Este identificador debe especificarse en el `PublicClientApplication` constructor, junto con un `Authority` constante que consta de una dirección URL base y la directiva de Azure Active Directory B2C para ejecutarse.

## <a name="signing-in"></a>Iniciar sesión

En las capturas de pantalla siguiente se muestra la pantalla de inicio de sesión en la aplicación de ejemplo:

![](azure-ad-b2c-images/login.png "Página de inicio de sesión")

Inicie sesión con proveedores de identidades sociales o con una cuenta local, se permiten. Aunque Microsoft, Google y Facebook, como se indicó anteriormente, se usan como proveedores de identidades sociales, también pueden utilizarse otros proveedores de identidades.

El ejemplo de código siguiente muestra cómo se invoca el proceso de inicio de sesión:

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

El `AcquireTokenAsync` método inicia el explorador web del dispositivo y muestra las opciones de autenticación definidas en la directiva de Azure Active Directory B2C especificado por la directiva que hace referencia a través de la `Constants.Authority` constante. Esta directiva define las experiencias que tendrán los consumidores durante el registro e inicio de sesión y las notificaciones que recibirá la aplicación en correcta registro o inicio de sesión.

El resultado de la `AcquireTokenAsync` llamada al método es un `AuthenticationResult` instancia. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contendrá un token de identidad, que se almacenarán en caché localmente. Si la autenticación se realiza correctamente, el `AuthenticationResult` instancia contendrá los datos que indica el motivo del error de autenticación.

En la aplicación de ejemplo, si la autenticación se realiza correctamente, el `TodoList` página se navega.

## <a name="silent-re-authentication"></a>Vuelva a autenticarse en modo silencioso

Cuando el `LoginPage` aparece en el ejemplo de aplicación, se intenta recuperar un token de usuario sin mostrar ninguna interfaz de usuario de autenticación. Esto se logra con la `AcquireTokenSilentAsync` método, como se muestra en el ejemplo de código siguiente:

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

El `AcquireTokenSilentAsync` método intenta recuperar un token de usuario de la memoria caché, sin solicitar al usuario que inicie sesión. Esto controla el escenario donde un token adecuado ya puede estar presente en la memoria caché de sesiones anteriores. Si el intento de obtener un token es correcto, el `TodoList` página se navega. Si el intento de obtener un token es incorrecto, no ocurre nada y el usuario tendrá la opción de iniciar un nuevo flujo de trabajo de autenticación.

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

Esto borra todos los tokens de autenticación de la caché local.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar la biblioteca de autenticación de Microsoft (MSAL) y Azure Active Directory B2C para integrar la administración de identidades de consumidor en una aplicación móvil. Azure B2C de Active Directory es una solución de administración de identidades en la nube para aplicaciones móviles y web orientadas al consumidor.


## <a name="related-links"></a>Vínculos relacionados

- [AzureADB2CAuth (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
