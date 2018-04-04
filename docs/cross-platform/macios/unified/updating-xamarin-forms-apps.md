---
title: Actualizar aplicaciones existentes de Xamarin.Forms
description: Siga estos pasos para actualizar una aplicación existente de Xamarin.Forms para usar la API unificada y actualizar a la versión 1.3.1
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 49614dddf05d692d94d28b29729e7b2038be31e7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="updating-existing-xamarinforms-apps"></a>Actualizar aplicaciones existentes de Xamarin.Forms

_Siga estos pasos para actualizar una aplicación existente de Xamarin.Forms para usar la API unificada y actualizar a la versión 1.3.1_

> [!IMPORTANT]
> Dado que Xamarin.Forms 1.3.1 es la primera versión que es compatible con la API unificada, toda la solución debe actualizarse para usar la versión más reciente al mismo tiempo que la migración de la aplicación de iOS a unificado. Esto significa que además de actualizar el proyecto de iOS para la compatibilidad con unificado, también debe editar el código en _todos los_ los proyectos de la solución.

La actualización se realiza en dos pasos:

1. Migrar la aplicación de iOS a la API unificada con Visual Studio durante la compilación de Mac en la herramienta de migración.

    - Usar la herramienta de migración para actualizar automáticamente el proyecto.

    - Actualización iOS nativo APIs tal como se describe en las instrucciones para [actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (específicamente en el código de servicio dependencia o un representador personalizado).

2. Actualizar toda la solución a Xamarin.Forms versión 1.3.

    1. Instale el paquete de NuGet Xamarin.Forms 1.3.1.

    2. Actualización de la `App` clase en el código compartido.

    3. Actualización de la `AppDelegate` en el proyecto de iOS.

    4. Actualización de la `MainActivity` en el proyecto de Android.

    5. Actualización de la `MainPage` en el proyecto de Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. (migración unificado) de la aplicación de iOS

Parte de la migración requiere actualizar Xamarin.Forms a la versión 1.3, que es compatible con la API unificada. En orden para las referencias de ensamblado correcto a crearse, primero deberá actualizar el proyecto de iOS para usar la API unificada.

### <a name="migration-tool"></a>Herramienta de migración

Haga clic en el proyecto de iOS para que se selecciona, a continuación, elija **proyecto > migrar a API unificada de Xamarin.iOS...**  y está de acuerdo con el mensaje de advertencia que aparece.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Elija proyecto > migrar a API unificada de Xamarin.iOS... y está de acuerdo con el mensaje de advertencia que aparece")

Esto configurará automáticamente:

 - Cambiar el tipo de proyecto para admitir la API unificada de 64 bits.
 - Cambiar la referencia de marco de trabajo a **Xamarin.iOS** (reemplazando la antigua **monotouch** referencia).
 - Cambie las referencias de espacio de nombres en el código para quitar el `MonoTouch` prefijo.
 - Actualización de la **csproj** archivo para utilizarlo los destinos de compilación correcta para la API unificada.

**Limpia** y **crear** el proyecto para asegurarse de que no hay ningún otro error corregir. No es necesaria realizar ninguna otra acción. Estos pasos se explican con más detalle en la [documentos API unificada](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Actualizar las API nativas para iOS (si es necesario)

Si ha agregado código nativo de iOS adicionales (como representadores personalizados o servicios de dependencia) debe realizar correcciones de código manual adicional. Volver a compilar la aplicación y hacer referencia a la [actualizar existente iOS instrucciones aplicaciones](~/cross-platform/macios/unified/updating-ios-apps.md) para obtener información adicional sobre los cambios que podrían ser necesarios. [Estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) también le ayudará a identificar los cambios necesarios.

## <a name="2-xamarinforms-131-update"></a>2. Actualización de Xamarin.Forms 1.3.1

Una vez que la aplicación de iOS se ha actualizado a la API unificada, el resto de la solución debe actualizarse a Xamarin.Forms versión 1.3.1. Esto incluye:

 - La actualización del paquete de Xamarin.Forms NuGet en cada proyecto.
 - Cambiar el código para usar la nueva Xamarin.Forms `Application`, `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android), y `FormsApplicationPage` clases (Windows Phone).

A continuación se explican estos pasos:

### <a name="21-update-nuget-in-all-projects"></a>2.1 Actualizar NuGet en todos los proyectos

Actualizar Xamarin.Forms para 1.3.1 preliminares mediante el Administrador de paquetes de NuGet para todos los proyectos de la solución: PCL (si existe), iOS, Android y Windows Phone. Se recomienda que **eliminar y volver a agregar** el paquete Xamarin.Forms NuGet para actualizar a la versión 1.3.

**Nota:** Xamarin.Forms versión 1.3.1 está actualmente en *preliminares*. Esto significa que debe seleccionar la **preliminares** opción NuGet a través de (un TIC caja en Visual Studio para Mac) o una-lista desplegable en Visual Studio para ver la versión preliminar más reciente.

> [!IMPORTANT]
> Si se utiliza Visual Studio, asegúrese de que esté instalada la versión más reciente del Administrador de paquetes de NuGet. Las versiones anteriores de NuGet en Visual Studio no instalan la versión unificado de Xamarin.Forms 1.3.1 correctamente. Vaya a **Herramientas > extensiones y actualizaciones...**  y haga clic en el **instalado** lista para comprobar que la **Administrador de paquetes de NuGet para Visual Studio** sea al menos versión 2.8.5. Si es más antiguo, haga clic en el **actualizaciones** lista para descargar la versión más reciente.

Una vez que se ha actualizado el paquete NuGet a Xamarin.Forms 1.3.1, realice los cambios siguientes en cada proyecto para actualizar a la nueva `Xamarin.Forms.Application` clase.

### <a name="22-portable-class-library-or-shared-project"></a>2.2 biblioteca de clases portable (o proyecto compartido)

Cambiar el **App.cs** archivo para que:

 - El `App` clase ahora hereda de `Application`.
 - El `MainPage` propiedad está establecida en la primera página de contenido que se va a mostrar.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

Se ha quitado completamente el `GetMainPage` método y en su lugar, establezca el `MainPage` *propiedad* en el `Application` subclase.

Esta nueva `Application` también es compatible con la clase base la `OnStart`, `OnSleep`, y `OnResume` invalidaciones para ayudarle a administrar el ciclo de vida de la aplicación.

El `App` clase, a continuación, se pasa a un nuevo `LoadApplication` método en cada proyecto de aplicación, tal y como se describe a continuación:

### <a name="23-ios-app"></a>Aplicación de 2.3 iOS

Cambiar el **AppDelegate.cs** archivo para que:

 - La clase hereda de `FormsApplicationDelegate` (en lugar de `UIApplicationDelegate` previamente).
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

 - La clase hereda de `FormsApplicationActivity` (en lugar de `FormsActivity` previamente).
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

### <a name="24-windows-phone-app"></a>2.4 Windows Phone aplicación

Necesitamos actualizar la **MainPage** -el XAML y el código subyacente.

Cambiar el **MainPage.xaml** archivo para que:

 - El elemento raíz XAML debe ser `winPhone:FormsApplicationPage`.
 - El `xmlns:phone` atributo debería ser *cambiado* a `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Ha actualizado el ejemplo se muestra a continuación: solo se debe establecer que editar estos elementos (el resto de los atributos debe ser los mismos):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Cambiar el **MainPage.xaml.cs** archivo para que:

 - La clase hereda de `FormsApplicationPage` (en lugar de `PhoneApplicationPage` previamente).
 - `LoadApplication` se llama con una nueva instancia de la Xamarin.Forms `App` clase. Puede que necesite completar esta referencia puesto que Windows Phone tiene su propio `App` clase ya definido.

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

### <a name="troubleshooting"></a>Solución de problemas

En ocasiones, verá un error similar al siguiente después de actualizar el paquete Xamarin.Forms NuGet. Se produce cuando el actualizador de NuGet no quita por completo las referencias a las versiones anteriores de sus **csproj** archivos.

>SU\_PROJECT.csproj: Error: este proyecto hace referencia a los paquetes de NuGet que faltan en este equipo. Habilitar la restauración de paquetes de NuGet para descargarlos.  Para obtener más información, consulta http://go.microsoft.com/fwlink/?LinkID=322105. El archivo que falta es... /.. /Packages/Xamarin.Forms.1.2.3.6257/Build/Portable-Win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets. (EL\_PROYECTO)

Para corregir estos errores, abra la **csproj** un archivo en un editor de texto y busque `<Target` elementos que hacen referencia a las versiones anteriores de Xamarin.Forms, como el elemento que se muestra a continuación. Debe eliminar manualmente este elemento completo desde el **csproj** de archivo y guardar los cambios.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

El proyecto debería compilarse correctamente una vez que se quitan estas referencias anteriores.

## <a name="considerations"></a>Consideraciones

Deben tener en cuenta las siguientes consideraciones al convertir un proyecto existente de Xamarin.Forms de la API clásica a la nueva API unificada si esa aplicación se basa en el componente o paquete de NuGet.

### <a name="components"></a>Componentes

Cualquier componente que se ha incluido en la aplicación tendrá que actualizarse a la API unificado o se producirá un conflicto cuando intente compilar. Para cualquier componente incluye, reemplaza la versión actual con una versión nueva desde el almacén de componentes de Xamarin que es compatible con la API unificada y realice una compilación limpia. Cualquier componente que aún no se ha convertido el autor, se mostrará una advertencia sólo en el almacén de componentes de 32 bits.

### <a name="nuget-support"></a>Compatibilidad con NuGet

Mientras se han contribuido cambios de NuGet para que funcione con la compatibilidad de API unificada, aún no ha recibido una nueva versión de NuGet, por lo que estamos evaluando cómo obtener NuGet para que reconozca las nuevas API.

Hasta ese momento, igual que los componentes, debe cambiar los paquetes de NuGet se han incluido en el proyecto a una versión que admite las API unificada y realice una compilación limpia posteriormente.

> [!IMPORTANT]
> Si hay un error en el formulario _"Error 3 no puede incluir 'monotouch.dll' y 'Xamarin.iOS.dll' en el mismo proyecto de Xamarin.iOS: 'Xamarin.iOS.dll' hace referencia explícitamente, mientras que se hace referencia a 'monotouch.dll' por ' xxx, versión = 0.0.000, Referencia cultural = neutral, PublicKeyToken = null'"_ después de convertir la aplicación a las API unificado, suele ser debido a que un componente o un paquete de NuGet en el proyecto que no se ha actualizado a la API unificada. Debe quitar el componente/NuGet existente, actualice a una versión que admite las API unificada y realice una compilación limpia.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitación de 64 bits la compilación de Xamarin.iOS aplicaciones

Para una aplicación móvil de Xamarin.iOS que se ha convertido en la API unificada, el desarrollador debe seguir habilitar la creación de la aplicación para equipos de 64 bits de las opciones de la aplicación. Vea la **habilitación de 64 bits compilaciones de Xamarin.iOS aplicaciones de** de la [consideraciones de la plataforma de 32 o 64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) generaciones de documentos para obtener instrucciones detalladas sobre la habilitación de 64 bits.

## <a name="summary"></a>Resumen

La aplicación de Xamarin.Forms ahora debe actualizarse a la versión 1.3.1 y migra la aplicación de iOS a la API unificada (que es compatible con arquitecturas de 64 bits en la plataforma de iOS).

Como se mencionó anteriormente, si la aplicación de Xamarin.Forms incluye código nativo como representadores personalizados o, a continuación, estos también necesiten actualizarse para usar los nuevos tipos de servicios de dependencia [introducidas en la API unificada](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [Actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Actualizar aplicaciones de iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Actualización de sugerencias](~/cross-platform/macios/unified/updating-tips.md)
- [Diferencias de API unificada de vs clásicos](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
