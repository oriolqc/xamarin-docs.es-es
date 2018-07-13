---
title: Xamarin.Forms en proyectos nativos de Xamarin
description: En este artículo se explica cómo consumir derivado de ContentPage las páginas que se agregan directamente a proyectos nativos de Xamarin y cómo navegar entre ellos.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/11/2018
ms.openlocfilehash: 65bb3fa070c082fa6c6c489e326a870a80fb9502
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997528"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms en proyectos nativos de Xamarin

_Formularios nativos permitir que las páginas de Xamarin.Forms ContentPage derivadas a ser consumidos por los proyectos nativos de Xamarin.iOS, Xamarin.Android y plataforma Universal de Windows (UWP). Proyectos nativos pueden consumir derivado de ContentPage las páginas que se agregan directamente al proyecto, o desde una biblioteca .NET Standard, la biblioteca estándar de .NET o el proyecto compartido. En este artículo se explica cómo consumir derivado de ContentPage las páginas que se agregan directamente a proyectos nativos y cómo navegar entre ellos._

Normalmente, una aplicación de Xamarin.Forms incluye una o más páginas que se derivan de [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), y estas páginas se comparten con todas las plataformas en un proyecto compartido o un proyecto de biblioteca de .NET Standard. Sin embargo, permite formularios nativos `ContentPage`-derivada páginas agregarse directamente a las aplicaciones de Xamarin.iOS, Xamarin.Android y UWP nativas. En comparación a tener el proyecto nativo consumir `ContentPage`-derivadas páginas desde un proyecto de biblioteca estándar de .NET o un proyecto compartido, la ventaja de agregar páginas directamente a proyectos nativos es que las páginas se pueden extender con vistas nativas. A continuación, se pueden denominar vistas nativas en XAML con `x:Name` y que se hace referencia desde el código subyacente. Para obtener más información acerca de vistas nativas, consulte [vistas nativas](~/xamarin-forms/platform/native-views/index.md).

El proceso para consumir un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-página derivada en un proyecto nativo es como sigue:

1. Agregue el paquete de Xamarin.Forms NuGet en el proyecto nativo.
1. Agregar el [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de página y las dependencias, en el proyecto nativo.
1. Llame al método `Forms.Init`.
1. Construir una instancia de la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-página derivada y convertirlo al tipo nativo apropiado con uno de los siguientes métodos de extensión: `CreateViewController` para iOS, `CreateFragment` o `CreateSupportFragment` para Android, o `CreateFrameworkElement` para UWP.
1. Vaya a la representación de tipo nativo de la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-mediante la API nativa barra de navegación de página derivada.

Xamarin.Forms debe inicializarse llamando el `Forms.Init` método antes de que un proyecto nativo se puede construir un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-página derivada. Elegir cuándo deben hacerlo principalmente depende cuando sea más conveniente en el flujo de la aplicación: puede realizarse al iniciar la aplicación, o justo antes de la `ContentPage`-página derivada se construye. En este artículo y las aplicaciones de ejemplo que lo acompaña, el `Forms.Init` se llama al método al iniciarse la aplicación.

> [!NOTE]
> El **NativeForms** solución de aplicación de ejemplo no contiene todos los proyectos de Xamarin.Forms. En su lugar, consta de un proyecto de Xamarin.iOS, un proyecto de Xamarin.Android y un proyecto de UWP. Cada proyecto es un proyecto nativo que usa formularios nativos para consumir [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de las páginas. Sin embargo, no hay ningún motivo por qué no podían consumir los proyectos nativos `ContentPage`-deriva de las páginas de un proyecto de biblioteca estándar de .NET o un proyecto compartido.

Al utilizar formularios nativos, Xamarin.Forms características como [ `DependencyService` ](xref:Xamarin.Forms.DependencyService), [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)y el motor de enlace de datos, todo el trabajo todavía.

## <a name="ios"></a>iOS

En iOS, el `FinishedLaunching` invalidar en el `AppDelegate` clase suele ser el lugar para ejecutar la aplicación las tareas relacionadas con el inicio. Se llama después de la aplicación ha lanzado y normalmente se reemplaza para configurar la ventana principal y ver el controlador. El siguiente ejemplo de código muestra la `AppDelegate` clase en la aplicación de ejemplo:

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

El `FinishedLaunching` método realiza las siguientes tareas:

- Xamarin.Forms se inicializa mediante una llamada a la `Forms.Init` método.
- Una referencia a la `AppDelegate` clase se almacena en el `static` `Instance` campo. Esto es proporcionar un mecanismo para otras clases llamar a métodos definidos en el `AppDelegate` clase.
- El `UIWindow`, que es el contenedor principal para las vistas en las aplicaciones nativas para iOS, se crea.
- El `PhonewordPage` (clase), que es un objeto Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de la página definida en XAML, que se crea y se convierte en un `UIViewController` utilizando el `CreateViewController` método de extensión.
- El `Title` propiedad de la `UIViewController` está establecida, lo que se mostrará en el `UINavigationBar`.
- Un `UINavigationController` se crea para administrar la navegación jerárquica. El `UINavigationController` clase administra una pila de controladores de vista y el `UIViewController` pasado en el constructor se presentarán inicialmente cuando el `UINavigationController` está cargado.
- El `UINavigationController` instancia se establece como el nivel superior `UIViewController` para el `UIWindow`y el `UIWindow` está establecida como la ventana de la clave para la aplicación y se hace visible.

Una vez el `FinishedLaunching` ha ejecutado el método, definido por la interfaz de usuario en Xamarin.Forms `PhonewordPage` clase se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png#lightbox "PhonewordPage de iOS")

Interactuar con la interfaz de usuario, por ejemplo, al pulsar un [ `Button` ](xref:Xamarin.Forms.Button), se producirá en controladores de eventos en el `PhonewordPage` ejecutando el código subyacente. Por ejemplo, cuando un usuario pulsa el **historial de llamadas** se ejecuta el botón, el controlador de eventos siguientes:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

El `static` `AppDelegate.Instance` campo permite la `AppDelegate.NavigateToCallHistoryPage` invocar método, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

El `NavigateToCallHistoryPage` método convierte Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de la página para un `UIViewController` con el `CreateViewController` método de extensión y los conjuntos de la `Title` propiedad de la `UIViewController`. El `UIViewController` , a continuación, se inserta en `UINavigationController` por el `PushViewController` método. Por lo tanto, se define la interfaz de usuario en Xamarin.Forms `CallHistoryPage` clase se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png#lightbox "CallHistoryPage de iOS")

Cuando el `CallHistoryPage` se muestra, puntee en la parte posterior flecha se mostrará el mensaje el `UIViewController` para el `CallHistoryPage` clase desde el `UINavigationController`, devolver al usuario la `UIViewController` para el `PhonewordPage` clase.

## <a name="android"></a>Android

En Android, el `OnCreate` invalidar en el `MainActivity` clase suele ser el lugar para ejecutar la aplicación las tareas relacionadas con el inicio. El siguiente ejemplo de código muestra la `MainActivity` clase en la aplicación de ejemplo:

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

El `OnCreate` método realiza las siguientes tareas:

- Xamarin.Forms se inicializa mediante una llamada a la `Forms.Init` método.
- Una referencia a la `MainActivity` clase se almacena en el `static` `Instance` campo. Esto es proporcionar un mecanismo para otras clases llamar a métodos definidos en el `MainActivity` clase.
- El `Activity` se establece el contenido de un recurso de diseño. En la aplicación de ejemplo, el diseño consta de un `LinearLayout` que contiene un `Toolbar`y un `FrameLayout` para que actúe como un contenedor de fragmento.
- El `Toolbar` se recupera y se establece como la barra de acciones para el `Activity`, y se establece el título de la barra de acción.
- El `PhonewordPage` (clase), que es un objeto Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de la página definida en XAML, que se crea y se convierte en un `Fragment` utilizando el `CreateFragment` método de extensión.
- El `FragmentManager` clase se crea y se confirma una transacción que reemplaza el `FrameLayout` instancia con el `Fragment` para el `PhonewordPage` clase.

Para obtener más información sobre los fragmentos, vea [fragmentos](~/android/platform/fragments/index.md).

> [!NOTE]
> Además el `CreateFragment` Xamarin.Forms el método de extensión, también incluye un `CreateSupportFragment` método. El `CreateFragment` método crea un `Android.App.Fragment` que puede ser utilizado en las aplicaciones destinadas a API 11 y versiones posteriores. El `CreateSupportFragment` método crea un `Android.Support.V4.App.Fragment` que puede utilizarse en aplicaciones que tienen como destino las versiones de API anteriores a 11.

Una vez el `OnCreate` ha ejecutado el método, definido por la interfaz de usuario en Xamarin.Forms `PhonewordPage` clase se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png#lightbox "PhonewordPage Android")

Interactuar con la interfaz de usuario, por ejemplo, al pulsar un [ `Button` ](xref:Xamarin.Forms.Button), se producirá en controladores de eventos en el `PhonewordPage` ejecutando el código subyacente. Por ejemplo, cuando un usuario pulsa el **historial de llamadas** se ejecuta el botón, el controlador de eventos siguientes:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

El `static` `MainActivity.Instance` campo permite la `MainActivity.NavigateToCallHistoryPage` invocar método, que se muestra en el ejemplo de código siguiente:

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

El `NavigateToCallHistoryPage` método convierte Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de la página a un `Fragment` con el `CreateFragment` método de extensión y agrega el `Fragment` al fragmento de la pila de retroceso. Por lo tanto, se define la interfaz de usuario en Xamarin.Forms `CallHistoryPage` se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png#lightbox "CallHistoryPage Android")

Cuando el `CallHistoryPage` se muestra, puntee en la parte posterior flecha se mostrará el mensaje el `Fragment` para el `CallHistoryPage` desde la pila de retroceso del fragmento, devolver al usuario la `Fragment` para el `PhonewordPage` clase.

### <a name="enabling-back-navigation-support"></a>Habilitar la compatibilidad de navegación hacia atrás

El `FragmentManager` clase tiene un `BackStackChanged` evento que se desencadena cada vez que cambia el contenido de la pila de retroceso de fragmento. El `OnCreate` método en el `MainActivity` clase contiene un controlador de eventos anónimos para este evento:

```csharp
FragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = FragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
};
```

Este controlador de eventos muestra un botón Atrás en la barra de acciones siempre que hay uno o más `Fragment` instancias en el fragmento de la pila de retroceso. La respuesta al pulsar el botón Atrás se controla mediante el `OnOptionsItemSelected` invalidar:

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

El `OnOptionsItemSelected` invalidación se llama siempre que se selecciona un elemento en el menú de opciones. Esta implementación aparece en el fragmento actual de la pila de retroceso del fragmento, siempre que se ha seleccionado el botón Atrás y hay uno o más `Fragment` instancias en el fragmento de la pila de retroceso.

### <a name="multiple-activities"></a>Varias actividades

Cuando una aplicación se compone de varias actividades [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-páginas derivadas se pueden incrustar en cada una de las actividades. En este escenario, el `Forms.Init` método debe llamarse únicamente en el `OnCreate` la invalidación de la primera `Activity` que incrusta un Xamarin.Forms `ContentPage`. Sin embargo, esto tiene las siguientes consecuencias:

- El valor de `Xamarin.Forms.Color.Accent` se tomará de la `Activity` que llama el `Forms.Init` método.
- El valor de `Xamarin.Forms.Application.Current` se asociará el `Activity` que llama el `Forms.Init` método.

### <a name="choosing-a-file"></a>Elegir un archivo

Al incrustar un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de la página que utiliza un [ `WebView` ](xref:Xamarin.Forms.WebView) que necesita para compatibilidad con HTML "Elegir el archivo" botón, el `Activity` será necesario invalidar el `OnActivityResult` método:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

En UWP, nativo `App` clase suele ser el lugar para ejecutar la aplicación las tareas relacionadas con el inicio. Xamarin.Forms es normalmente inicializado, en las aplicaciones de UWP de Xamarin.Forms, en el `OnLaunched` invalidar en nativo `App` (clase), para pasar el `LaunchActivatedEventArgs` argumento para el `Forms.Init` método. Por este motivo, las aplicaciones nativas de UWP que consumen un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-más fácilmente puede llamar page derivada el `Forms.Init` método desde el `App.OnLaunched` método.

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

El `MainPage` constructor realiza las siguientes tareas:

- Almacenamiento en caché está habilitado para la página, para que un nuevo `MainPage` no se construye cuando un usuario navega a la página.
- Una referencia a la `MainPage` clase se almacena en el `static` `Instance` campo. Esto es proporcionar un mecanismo para otras clases llamar a métodos definidos en el `MainPage` clase.
- El `PhonewordPage` (clase), que es un objeto Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de la página definida en XAML, que se crea y se convierte en un `FrameworkElement` utilizando el `CreateFrameworkElement` método de extensión y, después, establezca como contenido de la `MainPage` clase.

Una vez el `MainPage` ha ejecutado el constructor, definido por la interfaz de usuario en Xamarin.Forms `PhonewordPage` clase se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/uwp-phonewordpage.png "UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png#lightbox "PhonewordPage UWP")

Interactuar con la interfaz de usuario, por ejemplo, al pulsar un [ `Button` ](xref:Xamarin.Forms.Button), se producirá en controladores de eventos en el `PhonewordPage` ejecutando el código subyacente. Por ejemplo, cuando un usuario pulsa el **historial de llamadas** se ejecuta el botón, el controlador de eventos siguientes:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

El `static` `MainPage.Instance` campo permite la `MainPage.NavigateToCallHistoryPage` invocar método, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

Navegación en UWP se suele realizar con la `Frame.Navigate` método, que toma un `Page` argumento. Xamarin.Forms define una `Frame.Navigate` método de extensión que toma un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de la instancia de la página. Por lo tanto, cuando el `NavigateToCallHistoryPage` método se ejecuta, la interfaz de usuario definido en Xamarin.Forms `CallHistoryPage` se mostrará como se muestra en la captura de pantalla siguiente:

[![](native-forms-images/uwp-callhistorypage.png "UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png#lightbox "CallHistoryPage UWP")

Cuando el `CallHistoryPage` se muestra, puntee en la parte posterior flecha se mostrará el mensaje el `FrameworkElement` para el `CallHistoryPage` desde la pila de retroceso de la aplicación, se devuelve al usuario la `FrameworkElement` para el `PhonewordPage` clase.

### <a name="enabling-back-navigation-support"></a>Habilitar la compatibilidad de navegación hacia atrás

En UWP, las aplicaciones deben habilitar la navegación hacia atrás para todos los software y hardware Atrás botones, a través de diferentes factores de forma. Esto puede realizarse mediante el registro de un controlador de eventos para el `BackRequested` evento, que se puede realizar en el `OnLaunched` método nativo `App` clase:

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

Cuando se inicia la aplicación, el `GetForCurrentView` método recupera el `SystemNavigationManager` objeto asociado a la vista actual, a continuación, registra un controlador de eventos para el `BackRequested` eventos. La aplicación solo recibe este evento si es la aplicación de primer plano y, en respuesta, llama a la `OnBackRequested` controlador de eventos:

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

El `OnBackRequested` llamadas del controlador de eventos el `GoBack` método en el marco de la raíz de la aplicación y establece el `BackRequestedEventArgs.Handled` propiedad a `true` para marcar el evento como controlado. Podría provocar un error al marcar el evento como controlado en el sistema de salir de la aplicación (en la familia de dispositivos móviles) o se omite el evento (en la familia de dispositivos de escritorio).

La aplicación se basa en un botón Atrás de sistema proporcionado en un teléfono, pero decide si se deben mostrar un botón Atrás en la barra de título en dispositivos de escritorio. Esto se consigue estableciendo la `AppViewBackButtonVisibility` propiedad en uno de los `AppViewBackButtonVisibility` valores de enumeración:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

El `OnNavigated` controlador de eventos, que se ejecuta en respuesta a la `Navigated` activación de eventos, actualiza la visibilidad del título del botón Atrás de la barra cuando tenga lugar la navegación de página. Esto garantiza que el botón Atrás de barra de título está visible si la pila de retroceso de la aplicación no está vacía, o quita de la barra de título si está vacía la pila de retroceso de la aplicación.

Para obtener más información sobre la compatibilidad con la navegación hacia atrás en UWP, consulte [historial de navegación hacia atrás o navegación para aplicaciones UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="summary"></a>Resumen

Formularios nativos permiten Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de las páginas que será consumido por los proyectos nativos de Xamarin.iOS, Xamarin.Android y plataforma Universal de Windows (UWP). Proyectos nativos pueden consumir `ContentPage`-derivados de las páginas que se agregan directamente al proyecto, o desde un proyecto de biblioteca estándar de .NET o un proyecto compartido. En este artículo se explica cómo consumir `ContentPage`-derivados de las páginas que se agregan directamente a proyectos nativos y cómo navegar entre ellos.


## <a name="related-links"></a>Vínculos relacionados

- [NativeForms (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [Vistas nativas](~/xamarin-forms/platform/native-views/index.md)
