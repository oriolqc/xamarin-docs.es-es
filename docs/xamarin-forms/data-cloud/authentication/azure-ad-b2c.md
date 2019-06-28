---
title: Autenticar a los usuarios con Azure Active Directory B2C
description: Azure B2C de Active Directory proporciona administración de identidad en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se muestra cómo usar Azure Active Directory B2C para integrar la administración de identidad en una aplicación móvil con la biblioteca de autenticación de Microsoft.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2019
ms.openlocfilehash: 06f5716c8decb21de39fd46abe734b5fdcd6bd43
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2019
ms.locfileid: "67417942"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>Autenticar a los usuarios con Azure Active Directory B2C

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure B2C de Active Directory proporciona administración de identidad en la nube para aplicaciones móviles y web orientadas al consumidor. En este artículo se muestra cómo usar Azure Active Directory B2C para integrar la administración de identidad en una aplicación móvil con la biblioteca de autenticación de Microsoft._

## <a name="overview"></a>Información general

Azure B2C de Active Directory (ADB2C) es un servicio de administración de identidades para aplicaciones orientadas al consumidor. Permite que los usuarios inicien sesión su aplicación con sus cuentas sociales existentes o las credenciales personalizadas, como correo electrónico o nombre de usuario y contraseña. Las cuentas de credenciales personalizadas se conocen como _local_ cuentas.

El proceso para integrar el servicio de administración de identidades de Azure Active Directory B2C en una aplicación móvil es como sigue:

1. Crear a un inquilino de Azure Active Directory B2C
1. Registrar la aplicación móvil con el inquilino de Azure Active Directory B2C
1. Crear directivas de registro e inicio de sesión y ha olvidado flujos de usuario de contraseña
1. Use la biblioteca de autenticación de Microsoft (MSAL) para iniciar un flujo de trabajo de autenticación con el inquilino de Azure Active Directory B2C.

> [!NOTE]
> Azure B2C de Active Directory admite varios proveedores de identidades, incluidos Microsoft, GitHub, Facebook, Twitter y mucho más. Para obtener más información sobre las capacidades de Azure Active Directory B2C, consulte [documentación de Azure Active Directory B2C](/azure/active-directory-b2c/).
>
> Biblioteca de autenticación de Microsoft admite varias arquitecturas de aplicaciones y plataformas. Para obtener información sobre las capacidades MSAL, consulte [biblioteca de autenticación de Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) en GitHub.

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>Configurar a un inquilino de Azure Active Directory B2C

Para ejecutar el proyecto de ejemplo, debe crear a un inquilino de Azure Active Directory B2C. Para obtener más información, consulte [crear un inquilino de Azure Active Directory B2C en Azure portal](/azure/active-directory-b2c/active-directory-b2c-get-started/).

Una vez creado un inquilino, necesitará el **nombredeinquilino** y **Id. de inquilino** para configurar la aplicación móvil. El Id. de inquilino y el nombre se definen por el dominio generado al crear la dirección URL del inquilino. Si la dirección URL de inquilino generada es `https://contoso20190410tenant.onmicrosoft.com/` el **Id. de inquilino** es `contoso20190410tenant.onmicrosoft.com` y **nombredeinquilino** es `contoso20190410tenant`. Busque el dominio del inquilino en Azure portal, haga clic en el **filtro de directorio y suscripción** en el menú superior. Captura de pantalla siguiente muestra el directorio de Azure y el botón de filtro de suscripción y el dominio del inquilino:

[![Nombre del inquilino en la vista de filtro de directorio y suscripción de Azure](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

En el proyecto de ejemplo, edite el **Constants.cs** archivo para establecer el `tenantName` y `tenantId` campos. El código siguiente muestra cómo se deben establecer estos valores si el dominio del inquilino es `https://contoso20190410tenant.onmicrosoft.com/`, reemplace estos valores con los valores desde el portal:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>Registrar la aplicación móvil con Azure Active Directory B2C

Una aplicación móvil debe registrarse con el inquilino antes de que se puede conectar y autenticar a los usuarios. El proceso de registro asigna un único **Id. de aplicación** a la aplicación y un **dirección URL de redireccionamiento** que dirige las respuestas a la aplicación tras la autenticación. Para obtener más información, consulte [Azure Active Directory B2C: Registrar la aplicación](/azure/active-directory-b2c/active-directory-b2c-app-registration/). Necesitará saber la **Id. de aplicación** asignado a la aplicación, que aparece después del nombre de la aplicación en la vista de propiedades. Captura de pantalla siguiente muestra dónde encontrar el identificador de aplicación:

[![Identificador de la aplicación en la vista de propiedades de la aplicación de Azure](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Espera que la biblioteca de autenticación de Microsoft la **dirección URL de redireccionamiento** para la aplicación sea su **Id. de aplicación** como prefijo el texto "msal" y seguido por un punto de conexión denominado "auth". Si el identificador de aplicación es "1234abcd", la dirección URL completa debe ser `msal1234abcd://auth`. Asegúrese de que la aplicación se ha habilitado la **Native client** configuración y crear un **URI de redireccionamiento personalizado** mediante su identificador de aplicación, como se muestra en la siguiente captura de pantalla:

![URI de redireccionamiento personalizado en la vista de propiedades de la aplicación de Azure](azure-ad-b2c-images/azure-redirect-uri.png)

La dirección URL que se usará más adelante en el Android **ApplicationManifest.xml** y el iOS **Info.plist**.

En el proyecto de ejemplo, edite el **Constants.cs** archivo para establecer el `clientId` campo a su **Id. de aplicación**. El código siguiente muestra cómo se debe establecer este valor si es su identificador de aplicación `1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>Crear directivas de registro e inicio de sesión y olvidar las directivas de contraseñas

Una directiva es una experiencia de los usuarios seguir para completar una tarea como la creación de una cuenta o restablecer una contraseña. Una directiva también especifica el contenido de los tokens que recibe la aplicación cuando el usuario vuelve de la experiencia. Debe configurar directivas para tanto la cuenta de registro e inicio de sesión y restablecimiento de contraseña. Azure tiene directivas integradas que simplifican la creación de directivas comunes. Para obtener más información, consulte [Azure Active Directory B2C: Las directivas integradas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

Cuando haya completado la configuración de directiva, debe tener dos directivas en el **flujos de usuario (directivas)** vista en el portal de Azure. Captura de pantalla siguiente muestra dos directivas configuradas en el portal de Azure:

![Ver dos directivas configuradas en los flujos de usuario de Azure (directivas)](azure-ad-b2c-images/azure-application-policies.png)

En el proyecto de ejemplo, edite el **Constants.cs** archivo para establecer el `policySignin` y `policyPassword` campos para reflejar los nombres que eligió durante la configuración de directiva:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    static readonly string policySignin = "B2C_1_signupsignin1";
    static readonly string policyPassword = "B2C_1_passwordreset";
    ...
}
```

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>Usar la biblioteca de autenticación de Microsoft (MSAL) para la autenticación

El paquete NuGet de biblioteca de autenticación de Microsoft (MSAL) debe agregarse el recurso compartido, un proyecto .NET Standard y los proyectos de plataforma en una solución de Xamarin.Forms. MSAL incluye un `PublicClientApplicationBuilder` clase que crea un objeto que cumple con la `IPublicClientApplication` interfaz. MSAL utiliza `With` cláusulas para proporcionar parámetros adicionales a los métodos de autenticación y el constructor.

En el proyecto de ejemplo, el código subyacente para **App.xaml** define las propiedades estáticas denominadas `AuthenticationClient` y `UIParent`y crea una instancia de la `AuthenticationClient` objeto en el constructor. El `WithIosKeychainSecurityGroup` cláusula proporciona un nombre de grupo de seguridad para las aplicaciones de iOS. El `WithB2CAuthority` cláusula proporciona el valor predeterminado **autoridad**, o la directiva, que se usará para autenticar a los usuarios. En el ejemplo siguiente se muestra cómo crear una instancia de la `PublicClientApplication`:

```csharp
public partial class App : Application
{
    public static IPublicClientApplication AuthenticationClient { get; private set; }

    public static object UIParent { get; set; } = null;

    public App()
    {
        InitializeComponent();

        AuthenticationClient = PublicClientApplicationBuilder.Create(Constants.ClientId)
            .WithIosKeychainSecurityGroup(Constants.IosKeychainSecurityGroups)
            .WithB2CAuthority(Constants.AuthoritySignin)
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

El `OnAppearing` controlador de eventos en el **LoginPage.xaml.cs** código detrás de las llamadas `AcquireTokenSilentAsync` para actualizar el token de autenticación para los usuarios que han iniciado sesión antes. El proceso de autenticación se redirige a la `LogoutPage` si se realiza correctamente y no realiza ninguna acción en caso de error. El ejemplo siguiente muestra el proceso de reautenticación silenciosa en `OnAppearing`:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    protected override async void OnAppearing()
    {
        try
        {
            // Look for existing account
            IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

            AuthenticationResult result = await App.AuthenticationClient
                .AcquireTokenSilent(Constants.Scopes, accounts.FirstOrDefault())
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch
        {
            // Do nothing - the user isn't logged in
        }
        base.OnAppearing();
    }

    ...
}
```

El `OnLoginButtonClicked` controlador de eventos (que se desencadena cuando se hace clic en el botón de inicio de sesión) llamadas `AcquireTokenAsync`. La biblioteca MSAL automáticamente abre el Explorador de dispositivos móviles y navega a la página de inicio de sesión. La URL de inicio de sesión, denominada una **autoridad**, es una combinación del nombre de inquilino y las directivas se definen en el **Constants.cs** archivo. Si el usuario elige el olvidó la opción de contraseña, se devuelven a la aplicación con una excepción, que inicia la olvidó la experiencia de contraseña. El ejemplo siguiente muestra el proceso de autenticación:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    async void OnLoginButtonClicked(object sender, EventArgs e)
    {
        AuthenticationResult result;
        try
        {
            result = await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .ExecuteAsync();
    
            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch (MsalException ex)
        {
            if (ex.Message != null && ex.Message.Contains("AADB2C90118"))
            {
                result = await OnForgotPassword();
                await Navigation.PushAsync(new LogoutPage(result));
            }
            else if (ex.ErrorCode != "authentication_canceled")
            {
                await DisplayAlert("An error has occurred", "Exception message: " + ex.Message, "Dismiss");
            }
        }
    }

    ...
}
```

El `OnForgotPassword` método es similar al proceso de inicio de sesión, pero implementa una directiva personalizada. `OnForgotPassword` utiliza una sobrecarga diferente del `AcquireTokenAsync`, que le permite proporcionar un determinado **autoridad**. El ejemplo siguiente muestra cómo especificar un personalizado **autoridad** al adquirir un token:

```csharp
public partial class LoginPage : ContentPage
{
    ...
    async Task<AuthenticationResult> OnForgotPassword()
    {
        try
        {
            return await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .WithB2CAuthority(Constants.AuthorityPasswordReset)
                .ExecuteAsync();
        }
        catch (MsalException)
        {
            // Do nothing - ErrorCode will be displayed in OnLoginButtonClicked
            return null;
        }
    }
}
```

La última pieza de autenticación es el proceso de cierre de sesión. El `OnLogoutButtonClicked` método se llama cuando el usuario presiona el botón de cierre de sesión. Recorre en bucle todas las cuentas y garantiza que se han invalidado sus tokens. El ejemplo siguiente muestra la implementación de cierre de sesión:

```csharp
public partial class LogoutPage : ContentPage
{
    ...
    async void OnLogoutButtonClicked(object sender, EventArgs e)
    {
        IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

        while (accounts.Any())
        {
            await App.AuthenticationClient.RemoveAsync(accounts.First());
            accounts = await App.AuthenticationClient.GetAccountsAsync();
        }

        await Navigation.PopAsync();
    }
}
```

### <a name="ios"></a>iOS

En iOS, el esquema de dirección URL personalizado que se registró con Azure Active Directory B2C debe estar registrado en **Info.plist**. MSAL espera que el esquema de dirección URL para adherirse a un modelo específico, se ha descrito anteriormente en [registrar la aplicación móvil con Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). Captura de pantalla siguiente muestra el esquema de dirección URL personalizado en **Info.plist**.

!["Registro de un esquema de dirección URL personalizado en iOS"](azure-ad-b2c-images/customurl-ios.png)

MSAL también requiere derechos de la cadena de claves en iOS, registrados en el **Entitilements.plist**, tal y como se muestra en la captura de pantalla siguiente:

!["Configurar los derechos de la aplicación en iOS"](azure-ad-b2c-images/entitlements-ios.png)

Cuando Azure Active Directory B2C se completa la solicitud de autorización, redirige a la URL de redireccionamiento registrados. Da como resultado el esquema de dirección URL personalizado en iOS iniciando la aplicación móvil y pasarle la dirección URL como un parámetro de inicio, donde se procesó la `OpenUrl` la invalidación de la aplicación `AppDelegate` clase y devuelve el control de la experiencia de MSAL. El `OpenUrl` implementación se muestra en el ejemplo de código siguiente:

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
            return base.OpenUrl(app, url, options);
        }
    }
}
```

### <a name="android"></a>Android

En Android, el esquema de dirección URL personalizado que se registró con Azure Active Directory B2C debe estar registrado en el **AndroidManifest.xml**. MSAL espera que el esquema de dirección URL para adherirse a un modelo específico, se ha descrito anteriormente en [registrar la aplicación móvil con Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). El ejemplo siguiente muestra el esquema de dirección URL personalizado en el **AndroidManifest.xml**.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.xamarin.adb2cauthorization">
  <uses-sdk android:minSdkVersion="15" />
  <application android:label="ADB2CAuthorization">
    <activity android:name="microsoft.identity.client.BrowserTabActivity">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />"
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

El `MainActivity` clase debe modificarse para proporcionar la `UIParent` objeto a la aplicación durante la `OnCreate` llamar. Cuando Azure Active Directory B2C se completa la solicitud de autorización, se redirige a la combinación de dirección URL registrada desde el **AndroidManifest.xml**. Da como resultado el esquema URI registrado en Android que llama a la `OnActivityResult` método con la dirección URL como un parámetro de inicio, donde se procesó la `SetAuthenticationContinuationEventArgs` método.

```csharp
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        LoadApplication(new App());
        App.UIParent = this;
    }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
    }
}
```

### <a name="universal-windows-platform"></a>Plataforma universal de Windows

No se requiere ninguna configuración adicional para usar MSAL en la plataforma Universal de Windows

## <a name="run-the-project"></a>Ejecución del proyecto

Ejecute la aplicación en un dispositivo físico o virtual. Pulsar el **inicio de sesión** botón debe abrir el explorador y navegue a una página donde puede iniciar sesión o crear una cuenta. Después de completar el proceso de inicio de sesión, debería volver a la página de cierre de sesión de la aplicación. Captura de pantalla siguiente muestra el inicio de sesión de usuario en la pantalla que se ejecutan en iOS y Android:

!["Azure ADB2C inicio de sesión de la pantalla en iOS y Android"](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>Vínculos relacionados

- [AzureADB2CAuth (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Biblioteca de autenticación de Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentación de la biblioteca de autenticación de Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
