---
title: "Clase de aplicación"
description: "Características de la clase de aplicación predeterminado, que puede ser C# o XAML"
ms.topic: article
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: c383808d443685c1561113e418aed62467f1d5bd
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="app-class"></a>Clase de aplicación

El `Application` clase base ofrece las siguientes características, que se exponen en el valor predeterminado de proyectos `App` subclase:

* Un `MainPage` propiedad, que es donde puede establecer la página inicial de la aplicación.
* Un persistente [ `Properties` diccionario](#Properties_Dictionary) para almacenar valores simples en todos los cambios de estado de ciclo de vida.
* Una variable static `Current` propiedad que contiene una referencia al objeto de aplicación actual.

Si también expone [métodos del ciclo de vida](~/xamarin-forms/app-fundamentals/app-lifecycle.md) como `OnStart`, `OnSleep`, y `OnResume` , así como eventos de navegación modal.

Dependiendo de la plantilla eligió, la `App` clase puede definirse en uno de dos maneras:

* **C#**, o
* **XAML & C#**

Para crear un **aplicación** mediante XAML, el valor predeterminado de la clase **aplicación** clase debe sustituirse por un XAML **aplicación** clase y código subyacente asociado, tal como se muestra en el ejemplo de código siguiente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

En el ejemplo de código siguiente se muestra el código subyacente asociado:

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

Así como la configuración de la [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) propiedad, el código subyacente se debe llamar a la `InitializeComponent` para cargar y analizar el XAML asociado.

## <a name="mainpage-property"></a>Propiedad MainPage

El `MainPage` propiedad en la `Application` clase establece la página raíz de la aplicación.

Por ejemplo, puede crear lógica en su `App` clase para mostrar una página diferente dependiendo de si el usuario ha iniciado sesión o no.

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

El `Application` subclase tiene una variable static `Properties` diccionario que se puede usar para almacenar los datos, en particular para su uso en el `OnStart`, `OnSleep`, y `OnResume` métodos. Esto puede tener acceso desde cualquier lugar en el código de Xamarin.Forms utilizando `Application.Current.Properties`.

El `Properties` diccionario usa un `string` clave y almacena una `object` valor.

Por ejemplo, podría establecer un persistente `"id"` propiedad en cualquier lugar en el código (cuando se selecciona un elemento en una página `OnDisappearing` método, o en la `OnSleep` método) similar a la siguiente:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

En el `OnStart` o `OnResume` métodos, a continuación, puede utilizar este valor para volver a crear la experiencia del usuario de alguna manera. El `Properties` almacenes de diccionario `object`s, por lo que necesita convertir su valor antes de usarlo.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Compruebe siempre la presencia de la clave antes de acceder a bloquearla para evitar errores inesperados.

> [!NOTE]
> El `Properties` diccionario solo puede serializar los tipos primitivos de almacenamiento. Intentar almacenar otros tipos (como `List<string>`) puede producir un error en modo silencioso.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistencia

El `Properties` diccionario se guarda automáticamente en el dispositivo.
Datos agregados al diccionario estará disponibles cuando la aplicación devuelve desde el fondo o incluso después de que se reinicie.

Xamarin.Forms 1.4 introdujo un método adicional en la `Application` class - `SavePropertiesAsync()` -que puede llamarse para conservar de forma proactiva la `Properties` diccionario. Esto es para que pueda guardar propiedades después de las actualizaciones importantes, en lugar de riesgo de ellos no obtener serialicen debido a un bloqueo o que se ha eliminado con el sistema operativo.

Puede buscar referencias al uso de la `Properties` diccionario en la **crear aplicaciones móviles con Xamarin.Forms** libro capítulos [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf), y [20 ](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)y en el asociado [ejemplos](https://github.com/xamarin/xamarin-forms-book-preview-2).



## <a name="the-application-class"></a>La clase Application

Una completa `Application` a continuación se muestra la implementación de clase de referencia:

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

Esta clase, a continuación, se crea una instancia en cada proyecto específico de plataforma y se pasa a la `LoadApplication` método que es donde el `MainPage` se carga y se muestra al usuario.
En las secciones siguientes, se muestra el código para cada plataforma. Las plantillas de solución de Xamarin.Forms más recientes ya contienen todo este código, preconfigurado para la aplicación.


### <a name="ios-project"></a>Proyecto de iOS

El archivo iOS `AppDelegate` clase ahora hereda de `FormsApplicationDelegate`. Lo que debería:

* Llame a `LoadApplication` con una instancia de la `App` clase.

* Siempre devuelve `base.FinishedLaunching (app, options);`.

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

El Android `MainActivity` ahora se hereda de `FormsApplicationActivity`. En el `OnCreate` invalidar la `LoadApplication` método se llama con una instancia de la `App` clase.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", MainLauncher = true,
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

> [!NOTE]
> Hay una versión [ `FormsAppCompatActivity` ](~/xamarin-forms/platform/android/appcompat.md) clase base que puede utilizarse para admitir mejor Android de Material de diseño.
> Esto se convertirá en la plantilla predeterminada de Android en el futuro, pero puede seguir [estas instrucciones](~/xamarin-forms/platform/android/appcompat.md) para actualizar las aplicaciones existentes de Android.


### <a name="windows-phone-project"></a>Proyecto de Windows Phone

La página principal en el proyecto de Windows Phone (basado en Silverlight) debe heredar de `FormsApplicationPage`. Esto significa que el XAML y C# para `MainPage` referencia la `FormsApplicationPage` clase tal como se muestra.

El código XAML usa un espacio de nombres personalizado para que refleje el elemento raíz del `FormsApplicationPage` clase:

```xaml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

C# se hereda de la `FormsApplicationPage` clase y las llamadas `LoadApplication` para crear una instancia de su Xamarin.Forms `App`. Tenga en cuenta que es recomendable utilizar explícitamente el espacio de nombres de aplicación para calificar el `App` dado que las aplicaciones de Windows Phone también tienen sus propios `App` clase no relacionada con Xamarin.Forms.

```csharp
public partial class MainPage :
    global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3, use the correct namespace
    }
 }
```

### <a name="windows-81-project"></a>Proyecto de Windows 8.1

La página principal, en [Windows 8.1 (basado en WinRT)](~/xamarin-forms/platform/windows/installation/tablet.md) proyectos ahora deben heredar de `WindowsPage`. Esto significa que el XAML para `MainPage` referencia la `WindowsPage` clase tal como se muestra:

El código XAML usa un espacio de nombres personalizado para que refleje el elemento raíz del `FormsApplicationPage` clase:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
   ...>
</forms:WindowsPage>
```

Debe llamar la construcción de la C# código subyacente `LoadApplication` para crear una instancia de su Xamarin.Forms `App`. Tenga en cuenta que es recomendable utilizar explícitamente el espacio de nombres de aplicación para calificar el `App` dado que las aplicaciones de UWP también tienen sus propios `App` clase no relacionada con Xamarin.Forms.

```csharp
public partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();
        LoadApplication(new YOUR_APP_NAMESPACE.App());
    }
 }
```

Tenga en cuenta que `Forms.Init()` debe llamarse **App.xaml.cs** alrededor de la línea 65.

### <a name="universal-windows-project-uwp-for-windows-10"></a>Proyecto universal de Windows (UWP) para Windows 10

[Proyecto universal de Windows](~/xamarin-forms/platform/windows/installation/universal.md) compatibilidad de Xamarin.Forms se encuentra actualmente en vista previa.

La página principal, en el proyecto de UWP debe heredar de `WindowsPage`. Esto significa que el XAML y C# para `MainPage` referencia la `FormsApplicationPage` clase tal como se muestra.

El código XAML usa un espacio de nombres personalizado para que refleje el elemento raíz del `FormsApplicationPage` clase:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

Debe llamar la construcción de la C# código subyacente `LoadApplication` para crear una instancia de su Xamarin.Forms `App`. Tenga en cuenta que es recomendable utilizar explícitamente el espacio de nombres de aplicación para calificar el `App` dado que las aplicaciones de UWP también tienen sus propios `App` clase no relacionada con Xamarin.Forms.

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
