---
title: Clase de aplicación de Xamarin.Forms
description: En este artículo se explica las características de la clase de aplicación predeterminado, que incluye una propiedad para establecer en la página inicial de la aplicación, y un diccionario persistente para almacenar valores simples en todos los cambios de estado del ciclo de vida.
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 9acd1b8f25696267578f5cc269eb1b0c738be571
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675099"
---
# <a name="xamarinforms-app-class"></a>Clase de aplicación de Xamarin.Forms

El `Application` clase base ofrece las siguientes características, que se exponen en su valor predeterminado de los proyectos `App` subclase:

* Un `MainPage` propiedad, que es donde puede establecer la página inicial de la aplicación.
* Un persistente [ `Properties` diccionario](#Properties_Dictionary) para almacenar valores simples en todos los cambios de estado del ciclo de vida.
* Estático `Current` propiedad que contiene una referencia al objeto de aplicación actual.

También expone [métodos del ciclo de vida](~/xamarin-forms/app-fundamentals/app-lifecycle.md) como `OnStart`, `OnSleep`, y `OnResume` , así como los eventos de navegación modal.

Según la plantilla que eligió, la `App` clase podría definirse de alguna de estas dos maneras:

* **C#**, o
* **XAML Y C#**

Para crear un **aplicación** mediante XAML, el valor predeterminado de la clase **aplicación** clase debe reemplazarse por un XAML **aplicación** clase y código subyacente asociado, como se muestra en el ejemplo de código siguiente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

El ejemplo de código siguiente muestra el código subyacente asociado:

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

Así como la configuración de la [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propiedad, el código subyacente también debe llamar a la `InitializeComponent` método para cargar y analizar el XAML asociado.

## <a name="mainpage-property"></a>Propiedad de MainPage

El `MainPage` propiedad en el `Application` clase establece la página raíz de la aplicación.

Por ejemplo, puede crear lógica en su `App` clase para mostrar una página diferente dependiendo de si el usuario inició sesión o no.

El `MainPage` propiedad debe establecerse el `App` constructor,

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

<a name="Properties_Dictionary" />

## <a name="properties-dictionary"></a>Diccionario de propiedades

El `Application` subclase tiene estático `Properties` diccionario que se puede usar para almacenar datos, en particular para su uso en el `OnStart`, `OnSleep`, y `OnResume` métodos. Esto se puede acceder desde cualquier lugar en el código de Xamarin.Forms con `Application.Current.Properties`.

El `Properties` dictionary usa un `string` clave y almacena una `object` valor.

Por ejemplo, podría establecer un persistente `"id"` propiedad en cualquier lugar en el código (cuando se selecciona un elemento, en una página `OnDisappearing` método, o en el `OnSleep` método) similar al siguiente:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

En el `OnStart` o `OnResume` métodos, a continuación, puede usar este valor para volver a crear la experiencia del usuario de alguna manera. El `Properties` diccionario almacenes `object`s, por lo que deberá convertir su valor antes de usarlo.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Compruebe siempre la presencia de la clave antes de acceder a él para evitar errores inesperados.

> [!NOTE]
> El `Properties` diccionario solo puede serializar los tipos primitivos para el almacenamiento. Intentar almacenar otros tipos (como `List<string>`) puede producir un error en modo silencioso.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistencia

El `Properties` diccionario se guarda automáticamente en el dispositivo.
Agregada al diccionario de datos estará disponibles cuando la aplicación que se devuelve desde un segundo plano o incluso después de reiniciarlo.

Xamarin.Forms 1.4 introdujo un método adicional en el `Application` class - `SavePropertiesAsync()` -que se pueda llamar para conservar de forma proactiva el `Properties` diccionario. Esto es para que pueda guardar las propiedades después de las actualizaciones importantes en lugar de ellos no serializando debido a un bloqueo o que el sistema operativo que se termine de riesgo.

Puede encontrar las referencias al uso de la `Properties` diccionario en el **Creating Mobile Apps with Xamarin.Forms** reservar capítulos [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf), y [20 ](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)y el asociado [ejemplos](https://github.com/xamarin/xamarin-forms-book-preview-2).



## <a name="the-application-class"></a>La clase Application

Una completa `Application` implementación de la clase se muestra a continuación como referencia:

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}

```

Esta clase, a continuación, se crea una instancia en cada proyecto específico de plataforma y se pasa a la `LoadApplication` método que es donde el `MainPage` se cargan y se muestra al usuario.
El código para cada plataforma se muestra en las secciones siguientes. Las plantillas de solución de Xamarin.Forms más recientes ya contienen todo este código, preconfigurado para la aplicación.


### <a name="ios-project"></a>Proyecto de iOS

IOS `AppDelegate` clase hereda de `FormsApplicationDelegate`. Debe ser así:

* Llame a `LoadApplication` con una instancia de la `App` clase.

* Devolver siempre `base.FinishedLaunching (app, options);`.

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

### <a name="android-project"></a>Proyecto de Android

Android `MainActivity` hereda de `FormsAppCompatActivity`. En el `OnCreate` invalidar el `LoadApplication` se llama al método con una instancia de la `App` clase.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>Proyecto universal de Windows (UWP) para Windows 10

Consulte [proyectos de instalación de Windows](~/xamarin-forms/platform/windows/installation/index.md) para obtener información sobre la compatibilidad con UWP de Xamarin.Forms.

La página principal, en el proyecto para UWP debe heredar de `WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

El C# debe llamar código de construcción `LoadApplication` para crear una instancia de su Xamarin.Forms `App`. Tenga en cuenta que es recomendable utilizar explícitamente el espacio de nombres de aplicación para calificar el `App` dado que las aplicaciones de UWP también tienen sus propios `App` clase no relacionada con Xamarin.Forms.

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

Tenga en cuenta que `Forms.Init()` debe llamarse **App.xaml.cs** alrededor de la línea 63.
