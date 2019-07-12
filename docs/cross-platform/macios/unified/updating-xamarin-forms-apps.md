---
title: Actualizar aplicaciones Xamarin.Forms existentes
description: Este documento describe los pasos que se deben seguir para actualizar una aplicación Xamarin.Forms desde la API clásica a Unified API.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 36a4c6b66f7f724bfccc3c2a3b81c17f1d34a9c5
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829735"
---
# <a name="updating-existing-xamarinforms-apps"></a>Actualizar aplicaciones Xamarin.Forms existentes

_Siga estos pasos para actualizar una aplicación de Xamarin.Forms existente para usar la API unificada y actualizar a la versión 1.3.1_

> [!IMPORTANT]
> Dado que Xamarin.Forms 1.3.1 es la primera versión que admita la API unificada, toda la solución debe actualizarse para usar la versión más reciente al mismo tiempo que la migración de la aplicación de iOS a unificado. Esto significa que además de actualizar el proyecto de iOS para la compatibilidad con unificados, también tendrá que editar el código en _todas_ los proyectos de la solución.

La actualización se realiza en dos pasos:

1. Migrar la aplicación de iOS a Unified API con Visual Studio para la compilación de Mac en la herramienta de migración.

    - Use la herramienta de migración para actualizar automáticamente el proyecto.

    - Actualización de iOS nativo API tal como se describe en las instrucciones para [actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (específicamente en el código de servicio dependencia o el representador personalizado).

2. Actualizar toda la solución a la versión 1.3 de Xamarin.Forms.

    1. Instale el paquete de NuGet Xamarin.Forms 1.3.1.

    2. Actualización de la `App` clase en el código compartido.

    3. Actualización de la `AppDelegate` en el proyecto de iOS.

    4. Actualización de la `MainActivity` en el proyecto de Android.

    5. Actualización de la `MainPage` en el proyecto de Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. (migración unificada) de la aplicación de iOS

Parte de la migración requiere actualizar Xamarin.Forms en la versión 1.3, que es compatible con la API unificada. En orden para las referencias de ensamblado correcto al crearse, primero deberá actualizar el proyecto de iOS para usar la API unificada.

### <a name="migration-tool"></a>Herramienta de migración

Haga clic en el proyecto de iOS para que lo está, a continuación, elija **proyecto > migrar a Unified API de Xamarin.iOS...**  y acepta el mensaje de advertencia que aparece.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Elija el proyecto > migrar a Unified API de Xamarin.iOS... y está de acuerdo con el mensaje de advertencia que aparece")

Esto configurará automáticamente:

- Cambiar el tipo de proyecto para admitir la API unificada de 64 bits.
- Cambiar la referencia de framework a **Xamarin.iOS** (reemplaza al antiguo **monotouch** referencia).
- Cambie las referencias de espacio de nombres en el código para quitar el `MonoTouch` prefijo.
- Actualización de la **csproj** archivo debe usar los destinos de compilación correcta para la API unificada.

**Limpia** y **compilar** el proyecto para asegurarse de que no hay otros errores para corregir. No es necesaria realizar ninguna otra acción. Estos pasos se explican con más detalle en la [documentos de API unificada](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Actualizar las API nativas para iOS (si es necesario)

Si ha agregado código nativo de iOS adicionales (como representadores personalizados o servicios de dependencia) deberá realizar las correcciones de código manual adicional. Volver a compilar la aplicación y hacer referencia a la [actualizar existentes de instrucciones de las aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md) para obtener más información sobre los cambios que podrían ser necesarios. [Estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) también le ayudará a identificar los cambios necesarios.

## <a name="2-xamarinforms-131-update"></a>2. Actualización de Xamarin.Forms 1.3.1

Una vez que la aplicación de iOS se ha actualizado a Unified API, el resto de la solución debe actualizarse a Xamarin.Forms versión 1.3.1. Esto incluye:

- Actualización del paquete de Xamarin.Forms NuGet en cada proyecto.
- Cambiar el código para usar el nuevo Xamarin.Forms `Application`, `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android), y `FormsApplicationPage` clases (Windows Phone).

Estos pasos se explican a continuación:

### <a name="21-update-nuget-in-all-projects"></a>2.1 actualización de NuGet en todos los proyectos

Actualizar Xamarin.Forms a 1.3.1 mediante el Administrador de paquetes de NuGet para todos los proyectos de la solución de versión preliminar: PCL (si existe), iOS, Android y Windows Phone. Se recomienda que **eliminar y volver a agregar** el paquete Xamarin.Forms NuGet para actualizar a la versión 1.3.

> [!NOTE]
> Está actualmente en versión de Xamarin.Forms 1.3.1 *preliminares*. Esto significa que debe seleccionar la **preliminares** opción NuGet a través (un TIC incluidos en Visual Studio para Mac) o una-lista desplegable en Visual Studio para ver la versión preliminar más reciente.

> [!IMPORTANT]
> Si utiliza Visual Studio, asegúrese de que está instalada la versión más reciente del Administrador de paquetes de NuGet. Las versiones anteriores de NuGet en Visual Studio no instalan la versión unificado de Xamarin.Forms 1.3.1 correctamente. Vaya a **Herramientas > extensiones y actualizaciones...**  y haga clic en el **instalado** lista para comprobar que la **Administrador de paquetes de NuGet para Visual Studio** es al menos versión 2.8.5. Si es anterior, haga clic en el **actualizaciones** lista para descargar la versión más reciente.

Una vez que se ha actualizado el paquete NuGet a Xamarin.Forms 1.3.1, realice los siguientes cambios en cada proyecto para actualizar al nuevo `Xamarin.Forms.Application` clase.

### <a name="22-portable-class-library-or-shared-project"></a>2.2 biblioteca de clases portable (o el proyecto compartido)

Cambiar el **App.cs** archivo para que:

- El `App` clase ahora se hereda de `Application`.
- El `MainPage` propiedad está establecida en la primera página de contenido que desea mostrar.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

Hemos quitado por completo el `GetMainPage` método y en su lugar, establezca el `MainPage` *propiedad* en el `Application` subclase.

Esta nueva `Application` también es compatible con la clase base la `OnStart`, `OnSleep`, y `OnResume` las invalidaciones para ayudarle a administrar el ciclo de vida de la aplicación.

El `App` clase, a continuación, se pasa a un nuevo `LoadApplication` método en cada proyecto de aplicación, como se describe a continuación:

### <a name="23-ios-app"></a>Aplicación de 2.3 iOS

Cambiar el **AppDelegate.cs** archivo para que:

- La clase hereda de `FormsApplicationDelegate` (en lugar de `UIApplicationDelegate` anteriormente).
- `LoadApplication` se llama con una nueva instancia de `App`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>2.3 aplicación de android

Cambiar el **MainActivity.cs** archivo para que:

- La clase hereda de `FormsApplicationActivity` (en lugar de `FormsActivity` anteriormente).
- `LoadApplication` se llama con una nueva instancia de `App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>2.4 Windows Phone App

Es necesario actualizar el **MainPage** -el XAML y el código subyacente.

Cambiar el **MainPage.xaml** archivo para que:

- Debe ser el elemento raíz XAML `winPhone:FormsApplicationPage`.
- El `xmlns:phone` debe ser el atributo *cambiado* a `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Un ejemplo actualizado se muestra a continuación: solo debe tener que editar estos elementos (el resto de los atributos debe permanecer igual):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Cambiar el **MainPage.xaml.cs** archivo para que:

- La clase hereda de `FormsApplicationPage` (en lugar de `PhoneApplicationPage` anteriormente).
- `LoadApplication` se llama con una nueva instancia de Xamarin.Forms `App` clase. Es posible que deba calificar totalmente esta referencia, ya que Windows Phone tiene su propio `App` clase ya definida.

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>solución de problemas

En ocasiones verá un error similar al siguiente después de actualizar el paquete Xamarin.Forms NuGet. Se produce cuando el actualizador de NuGet no quita por completo las referencias a versiones anteriores de sus **csproj** archivos.

>SU\_PROJECT.csproj: Error: Este proyecto hace referencia a los paquetes de NuGet que faltan en este equipo. Habilitar la restauración del paquete NuGet descargarlos.  Para obtener más información, consulta http://go.microsoft.com/fwlink/?LinkID=322105. El archivo que falta es... /.. /Packages/Xamarin.Forms.1.2.3.6257/Build/Portable-Win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets. (SU\_PROYECTO)

Para corregir estos errores, abra el **csproj** de archivos en un editor de texto y busque `<Target` elementos que hacen referencia a las versiones anteriores de Xamarin.Forms, por ejemplo, el elemento se muestra a continuación. Debe eliminar manualmente este elemento de todo el **csproj** de archivo y guarde los cambios.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

El proyecto debería compilarse correctamente una vez que estos las referencias antiguas se eliminan.

## <a name="considerations"></a>Consideraciones

Las siguientes consideraciones se deben tener en cuenta al convertir un proyecto de Xamarin.Forms existente de la API clásica a la nueva Unified API si esa aplicación se basa en uno o varios componentes o paquetes de NuGet.

### <a name="components"></a>Componentes

También tendrá que actualizarse a Unified API de cualquier componente que se ha incluido en la aplicación o se producirá un conflicto al intentar compilar. Para cualquier componente incluido, reemplace la versión actual con una nueva versión desde el Store del componente de Xamarin que es compatible con la API unificada y realizar una compilación limpia. Cualquier componente que aún no se ha convertido el autor, se mostrará una advertencia solo en el almacén de componentes de 32 bits.

### <a name="nuget-support"></a>Compatibilidad con NuGet

Mientras nos ha aportado los cambios de NuGet para trabajar con el soporte técnico de Unified API, no ha habido una nueva versión de NuGet, por lo que estamos evaluando la obtención de NuGet para reconocer las nuevas API.

Hasta ese momento, al igual que los componentes, deberá cambiar cualquier paquete de NuGet se han incluido en el proyecto a una versión que admite las API unificada y realizar después una compilación limpia.

> [!IMPORTANT]
> Si tiene un error en el formulario _"Error 3 no puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS: 'Xamarin.iOS.dll' se hace referencia explícitamente, mientras que se hace referencia a 'monotouch.dll' por ' xxx, versión = 0.0.000, Referencia cultural = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificada, suele ser debido a que un componente o el paquete NuGet en el proyecto que no se ha actualizado a Unified API. Deberá quitar el componente o NuGet existente, actualice a una versión que admite las API unificada y realizar una compilación limpia.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitación de 64 bits la compilación de aplicaciones de Xamarin.iOS

Para una aplicación móvil Xamarin.iOS que se ha convertido a Unified API, el desarrollador debe seguir habilitar la compilación de la aplicación para equipos de 64 bits de las opciones de la aplicación. Consulte la **habilitación de 64 bits se basa de aplicaciones de Xamarin.iOS** de la [consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) generaciones de documentos para obtener instrucciones detalladas sobre la habilitación de 64 bits.

## <a name="summary"></a>Resumen

La aplicación de Xamarin.Forms ahora debe actualizarse a la versión 1.3.1 y migra la aplicación de iOS a Unified API (que es compatible con arquitecturas de 64 bits en la plataforma iOS).

Como se mencionó anteriormente, si la aplicación de Xamarin.Forms incluye código nativo como representadores personalizados o, a continuación, es posible que también deben actualizar para usar los nuevos tipos de servicios de dependencia [introducidas en la API unificada](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Actualización de sugerencias](~/cross-platform/macios/unified/updating-tips.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
