---
title: Formularios nativos
description: Formas nativas permiten un uso derivados Xamarin.Forms contenidoPage páginas a ser consumidos por los proyectos nativos de Xamarin.iOS y Xamarin.Android, plataforma Universal de Windows (UWP). Los proyectos nativos pueden consumir derivado contenidoPage páginas que se agregan directamente al proyecto, o desde una biblioteca estándar. NET, la biblioteca estándar de .NET o el proyecto compartido. Este artículo explica cómo consumir derivado contenidoPage páginas que se agregan directamente a los proyectos nativos y cómo navegar entre ellos.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/11/2018
ms.openlocfilehash: a103d360221650ee4f679ee285dbedd65e62f947
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2018
---
# <a name="native-forms"></a>Formularios nativos

_Formas nativas permiten un uso derivados Xamarin.Forms contenidoPage páginas a ser consumidos por los proyectos nativos de Xamarin.iOS y Xamarin.Android, plataforma Universal de Windows (UWP). Los proyectos nativos pueden consumir derivado contenidoPage páginas que se agregan directamente al proyecto, o desde una biblioteca estándar. NET, la biblioteca estándar de .NET o el proyecto compartido. Este artículo explica cómo consumir derivado contenidoPage páginas que se agregan directamente a los proyectos nativos y cómo navegar entre ellos._

Normalmente, una aplicación de Xamarin.Forms incluye una o varias páginas que se derivan de [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), y estas páginas se comparten entre todas las plataformas en un proyecto compartido o un proyecto de biblioteca de .NET estándar. Sin embargo, permite formularios nativos `ContentPage`-derivado que se agreguen directamente a las aplicaciones nativas de Xamarin.iOS y Xamarin.Android, UWP páginas. En comparación a tener el proyecto nativo consumir `ContentPage`-pages derivadas de un proyecto de biblioteca estándar de .NET o un proyecto compartido, la ventaja de agregar páginas directamente a los proyectos nativos es que las páginas se pueden extender con vistas nativo. Vistas nativo, a continuación, pueden llamarse en XAML con `x:Name` y que se hace referencia desde el código subyacente. Para obtener más información acerca de las vistas nativo, vea [vistas Native](~/xamarin-forms/platform/native-views/index.md).

El proceso para consumir un Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página derivada en un proyecto nativo es el siguiente:

1. Agregue el paquete Xamarin.Forms NuGet para el proyecto nativo.
1. Agregar el [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivados de página y las dependencias, en el proyecto nativo.
1. Llame al método `Forms.Init`.
1. Construir una instancia de la [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página derivada y convertir al tipo nativo apropiado con uno de los siguientes métodos de extensión: `CreateViewController` para iOS, `CreateFragment` o `CreateSupportFragment` para Android, o `CreateFrameworkElement` para UWP.
1. Navegue a la representación de un tipo nativo de la [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página mediante la API de navegación nativo derivada.

Xamarin.Forms debe inicializarse mediante una llamada a la `Forms.Init` método antes de que un proyecto nativo puede construir un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página derivada. Elegir cuándo deben hacerlo principalmente depende de cuándo es más conveniente en el flujo de la aplicación: puede realizarse al iniciar la aplicación, o justo antes de la `ContentPage`-página derivada se construye. En este artículo y las aplicaciones de ejemplo que lo acompaña, la `Forms.Init` método se llama al iniciar la aplicación.

> [!NOTE]
> El **NativeForms** solución de aplicación de ejemplo no contiene ningún proyecto de Xamarin.Forms. En su lugar, se compone de un proyecto de Xamarin.iOS, un proyecto de Xamarin.Android y un proyecto UWP. Cada proyecto es un proyecto nativo que usa formas nativas para consumir [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado páginas. Sin embargo, no hay ninguna razón por qué no se ha podido usar los proyectos nativos `ContentPage`-derivado páginas de un proyecto de biblioteca estándar de .NET o un proyecto compartido.

Al utilizar formularios nativos, Xamarin.Forms características como [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/), [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)y el motor de enlace de datos, todo el trabajo todavía.

## <a name="ios"></a>iOS

En iOS, el `FinishedLaunching` invalidar en la `AppDelegate` clase normalmente es el lugar para ejecutar la aplicación las tareas relacionadas con el inicio. Se llama después de que la aplicación se inicia y normalmente se invalida para configurar la ventana principal y ver el controlador. El siguiente ejemplo de código muestra la `AppDelegate` clase en la aplicación de ejemplo:

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;

    UIWindow _window;
    UINavigationController _navigation;

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        var mainPage = new PhonewordPage().CreateViewController();
        mainPage.Title = "Phoneword";

        _navigation = new UINavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    ...
}
```

El `FinishedLaunching` método realiza las tareas siguientes:

- Xamarin.Forms se inicializa mediante una llamada a la `Forms.Init` método.
- Una referencia a la `AppDelegate` clase se almacena en la `static` `Instance` campo. Esto sirve para proporcionar un mecanismo para otras clases llamar a métodos definidos en la `AppDelegate` clase.
- El `UIWindow`, que es el contenedor principal para las vistas en las aplicaciones nativas para iOS, se crea.
- El `PhonewordPage` (clase), que es un Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de la página definida en XAML, que se crea y se convierte en una `UIViewController` mediante el `CreateViewController` método de extensión.
- El `Title` propiedad de la `UIViewController` está establecido, que se mostrará en el `UINavigationBar`.
- Un `UINavigationController` se crea para administrar la navegación jerárquica. El `UINavigationController` clase administra una pila de controladores de la vista y el `UIViewController` pasado al constructor aparecerá inicialmente cuando el `UINavigationController` se carga.
- El `UINavigationController` instancia se establece como el nivel superior `UIViewController` para el `UIWindow`y el `UIWindow` está establecida como la ventana de clave para la aplicación y se hacen visibles.

Una vez el `FinishedLaunching` ha ejecutado el método, la interfaz de usuario definidos en el Xamarin.Forms `PhonewordPage` clase se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png#lightbox "iOS PhonewordPage")

Interactuar con la interfaz de usuario, por ejemplo, puntee en un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), se producirá en controladores de eventos en el `PhonewordPage` la ejecución de código subyacente. Por ejemplo, cuando un usuario puntea el **historial de llamadas** botón, el controlador de eventos siguiente se ejecuta:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

El `static` `AppDelegate.Instance` campo permite que el `AppDelegate.NavigateToCallHistoryPage` invocar método, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

El `NavigateToCallHistoryPage` método convierte el Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página a derivada un `UIViewController` con el `CreateViewController` método de extensión y conjuntos de la `Title` propiedad de la `UIViewController`. El `UIViewController` , a continuación, se inserta en `UINavigationController` por el `PushViewController` método. Por lo tanto, la interfaz de usuario definidos en el Xamarin.Forms `CallHistoryPage` clase se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png#lightbox "iOS CallHistoryPage")

Cuando el `CallHistoryPage` se muestra, puntee en la parte posterior flecha mostrará el `UIViewController` para el `CallHistoryPage` clase desde el `UINavigationController`, devolver al usuario la `UIViewController` para el `PhonewordPage` clase.

## <a name="android"></a>Android

En Android, el `OnCreate` invalidar en la `MainActivity` clase normalmente es el lugar para ejecutar la aplicación las tareas relacionadas con el inicio. El siguiente ejemplo de código muestra la `MainActivity` clase en la aplicación de ejemplo:

```csharp
public class MainActivity : AppCompatActivity
{
    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Phoneword";

        var mainPage = new PhonewordPage().CreateFragment(this);
        FragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

El `OnCreate` método realiza las tareas siguientes:

- Xamarin.Forms se inicializa mediante una llamada a la `Forms.Init` método.
- Una referencia a la `MainActivity` clase se almacena en la `static` `Instance` campo. Esto sirve para proporcionar un mecanismo para otras clases llamar a métodos definidos en la `MainActivity` clase.
- El `Activity` se establece el contenido de un recurso de diseño. En la aplicación de ejemplo, el diseño está formada por un `LinearLayout` que contiene un `Toolbar`y un `FrameLayout` para actuar como un contenedor de fragmento.
- El `Toolbar` se recupera y se establece como la barra de acciones para el `Activity`, y se establece el título de la barra de acción.
- El `PhonewordPage` (clase), que es un Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de la página definida en XAML, que se crea y se convierte en una `Fragment` mediante el `CreateFragment` método de extensión.
- El `FragmentManager` clase crea y confirma una transacción que reemplaza el `FrameLayout` instancia con la `Fragment` para el `PhonewordPage` clase.

Para obtener más información sobre los fragmentos, vea [fragmentos](~/android/platform/fragments/index.md).

> [!NOTE]
> Además el `CreateFragment` Xamarin.Forms el método de extensión, también incluye una `CreateSupportFragment` método. El `CreateFragment` método crea un `Android.App.Fragment` que puede utilizar en aplicaciones que tienen como destino el 11 de API y mayor. El `CreateSupportFragment` método crea un `Android.Support.V4.App.Fragment` que se puede utilizar en aplicaciones que tienen como destino las versiones de API anteriores a 11.

Una vez el `OnCreate` ha ejecutado el método, la interfaz de usuario definidos en el Xamarin.Forms `PhonewordPage` clase se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png#lightbox "PhonewordPage Android")

Interactuar con la interfaz de usuario, por ejemplo, puntee en un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), se producirá en controladores de eventos en el `PhonewordPage` la ejecución de código subyacente. Por ejemplo, cuando un usuario puntea el **historial de llamadas** botón, el controlador de eventos siguiente se ejecuta:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

El `static` `MainActivity.Instance` campo permite que el `MainActivity.NavigateToCallHistoryPage` invocar método, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateFragment(this);
    FragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, callHistoryPage)
        .Commit();
}
```

El `NavigateToCallHistoryPage` método convierte el Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página a derivada un `Fragment` con el `CreateFragment` método de extensión y agrega el `Fragment` en el fragmento de la copia de pila. Por lo tanto, la interfaz de usuario definidos en el Xamarin.Forms `CallHistoryPage` se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png#lightbox "CallHistoryPage Android")

Cuando el `CallHistoryPage` se muestra, puntee en la parte posterior flecha mostrará el `Fragment` para el `CallHistoryPage` de la pila de retroceso de fragmento, devolver al usuario la `Fragment` para el `PhonewordPage` clase.

### <a name="enabling-back-navigation-support"></a>Habilitar la compatibilidad de navegación hacia atrás

El `FragmentManager` clase tiene un `BackStackChanged` evento que se activa siempre que cambia el contenido de la pila de retroceso de fragmento. El `OnCreate` método en la `MainActivity` clase contiene un controlador de eventos anónimo para este evento:

```csharp
FragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = FragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
};
```

Este controlador de eventos muestra un botón Atrás en la barra de acciones siempre que hay uno o más `Fragment` instancias en el fragmento de la pila de retroceso. La respuesta a pulsar el botón Atrás se controla mediante el `OnOptionsItemSelected` invalidar:

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && FragmentManager.BackStackEntryCount > 0)
    {
        FragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

El `OnOptionsItemSelected` invalidación se llama cada vez que se selecciona un elemento en el menú de opciones. Esta implementación aparece en el fragmento actual de la pila de retroceso de fragmento, siempre que se ha seleccionado el botón Atrás y hay uno o más `Fragment` instancias en el fragmento de la pila de retroceso.

### <a name="multiple-activities"></a>Varias actividades

Cuando una aplicación se compone de varias actividades, [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-páginas derivadas pueden incluirse en cada una de las actividades. En este escenario, el `Forms.Init` método debe llamarse únicamente en el `OnCreate` la invalidación de la primera `Activity` que incrusta un Xamarin.Forms `ContentPage`. Sin embargo, esto tiene las siguientes consecuencias:

- El valor de `Xamarin.Forms.Color.Accent` se tomará de la `Activity` que llama el `Forms.Init` método.
- El valor de `Xamarin.Forms.Application.Current` estará asociado el `Activity` que llama el `Forms.Init` método.

### <a name="choosing-a-file"></a>Elegir un archivo

Al incrustar un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de la página que usa un [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) que necesita para admitir HTML "Elegir archivo" botón, el `Activity` será necesario invalidar los `OnActivityResult` método:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

En UWP, nativo `App` clase normalmente es el lugar para ejecutar la aplicación las tareas relacionadas con el inicio. Xamarin.Forms se suelen inicializar, en las aplicaciones UWP de Xamarin.Forms, en la `OnLaunched` invalidar en nativo `App` (clase), para pasar el `LaunchActivatedEventArgs` argumento pasado a la `Forms.Init` método. Por esta razón, aplicaciones nativas de UWP que usan un Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página derivada puede llamar más fácilmente el `Forms.Init` método desde el `App.OnLaunched` método.

De forma predeterminada, nativo `App` clase inicia la `MainPage` clase como la primera página de la aplicación. El siguiente ejemplo de código muestra la `MainPage` clase en la aplicación de ejemplo:

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        this.Content = new Phoneword.UWP.Views.PhonewordPage().CreateFrameworkElement();
    }
    ...
}
```

El `MainPage` constructor realiza las tareas siguientes:

- Almacenamiento en caché está habilitado para la página, para que un nuevo `MainPage` no crea cuando un usuario navega a la página.
- Una referencia a la `MainPage` clase se almacena en la `static` `Instance` campo. Esto sirve para proporcionar un mecanismo para otras clases llamar a métodos definidos en la `MainPage` clase.
- El `PhonewordPage` (clase), que es un Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado de la página definida en XAML, que se crea y se convierte en una `FrameworkElement` mediante el `CreateFrameworkElement` método de extensión y después se establece como el contenido de la `MainPage` clase.

Una vez el `MainPage` constructor se ejecuta, la interfaz de usuario definidos en el Xamarin.Forms `PhonewordPage` clase se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/uwp-phonewordpage.png "UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png#lightbox "PhonewordPage UWP")

Interactuar con la interfaz de usuario, por ejemplo, puntee en un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), se producirá en controladores de eventos en el `PhonewordPage` la ejecución de código subyacente. Por ejemplo, cuando un usuario puntea el **historial de llamadas** botón, el controlador de eventos siguiente se ejecuta:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

El `static` `MainPage.Instance` campo permite que el `MainPage.NavigateToCallHistoryPage` invocar método, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

La navegación en UWP se suele realizar con la `Frame.Navigate` método, que toma un `Page` argumento. Xamarin.Forms define un `Frame.Navigate` método de extensión que toma un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-página instancia derivada. Por lo tanto, cuando la `NavigateToCallHistoryPage` método se ejecuta, la interfaz de usuario definido en el Xamarin.Forms `CallHistoryPage` se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/uwp-callhistorypage.png "UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png#lightbox "CallHistoryPage UWP")

Cuando el `CallHistoryPage` se muestra, puntee en la parte posterior flecha mostrará el `FrameworkElement` para el `CallHistoryPage` de la pila de retroceso de la aplicación, devolver al usuario la `FrameworkElement` para el `PhonewordPage` clase.

### <a name="enabling-back-navigation-support"></a>Habilitar la compatibilidad de navegación hacia atrás

En UWP, las aplicaciones deben habilitar la navegación hacia atrás para el hardware y software atrás los botones, a través de factores de forma diferente. Esto puede realizarse si se registra un controlador de eventos para el `BackRequested` evento, que se pueden realizar en el `OnLaunched` método nativo `App` clase:

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

Cuando se inicia la aplicación, el `GetForCurrentView` método recupera el `SystemNavigationManager` objeto asociado a la vista actual y después se registra un controlador de eventos para el `BackRequested` eventos. La aplicación solo recibe este evento si es la aplicación de primer plano y en respuesta, llama a la `OnBackRequested` controlador de eventos:

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

El `OnBackRequested` llamadas del controlador de eventos el `GoBack` método en el marco de la raíz de la aplicación y establece la `BackRequestedEventArgs.Handled` propiedad `true` para marcar el evento como controlado. Error al marcar el evento como controlado podría producir en el sistema si sale de la aplicación (en la familia de dispositivos móviles) o se omitirá el evento (en la familia de dispositivos de escritorio).

La aplicación se basa en un botón Atrás proporcionado por el sistema en un teléfono, pero decide si se deben mostrar un botón Atrás en la barra de título en dispositivos de escritorio. Esto se logra estableciendo la `AppViewBackButtonVisibility` propiedad en uno de los `AppViewBackButtonVisibility` valores de enumeración:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

El `OnNavigated` controlador de eventos, que se ejecuta en respuesta a la `Navigated` , que desencadenó el evento actualiza la visibilidad de la barra del botón Atrás de título cuando se produce la navegación de página. Esto garantiza que el botón Atrás de barra de título está visible si la pila de retroceso de la aplicación no está vacía, o quita de la barra de título, si la pila de retroceso de la aplicación está vacía.

Para obtener más información sobre la compatibilidad con la navegación hacia atrás en UWP, vea [historial de navegación hacia atrás o navegación para aplicaciones UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="summary"></a>Resumen

Los formularios nativo permiten Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado páginas a ser consumidos por los proyectos nativos de Xamarin.iOS y Xamarin.Android, plataforma Universal de Windows (UWP). Pueden utilizar los proyectos nativos `ContentPage`-derivados de páginas que se agregan directamente al proyecto, o desde un proyecto de biblioteca estándar de .NET o un proyecto compartido. Este artículo explica cómo consumir `ContentPage`-derivados de páginas que se agregan directamente a los proyectos nativos y cómo navegar entre ellos.


## <a name="related-links"></a>Vínculos relacionados

- [NativeForms (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [Vistas nativas](~/xamarin-forms/platform/native-views/index.md)
