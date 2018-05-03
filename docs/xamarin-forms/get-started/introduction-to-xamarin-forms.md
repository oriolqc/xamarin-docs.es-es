---
title: Introducción a Xamarin.Forms
description: Xamarin.Forms es que una abstracción de kit de herramientas de interfaz de usuario multiplataforma respaldada de forma nativa que permite a los desarrolladores crear fácilmente interfaces de usuario que se pueden compartir entre Android, iOS y Plataforma universal de Windows. Las interfaces de usuario se representan mediante los controles nativos de la plataforma de destino, lo que permite que las aplicaciones de Xamarin.Forms conserven la apariencia adecuada para cada plataforma. En este artículo se proporciona una introducción a Xamarin.Forms y la manera de empezar a escribir aplicaciones con él.
ms.prod: xamarin
ms.assetid: f619595f-3ee7-439b-a1bc-d13e5106e6e9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 9f7c9d1b410d9d1d699644148903fdc6cfeec4fd
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="an-introduction-to-xamarinforms"></a>Introducción a Xamarin.Forms

_Xamarin.Forms es que una abstracción de kit de herramientas de interfaz de usuario multiplataforma respaldada de forma nativa que permite a los desarrolladores crear fácilmente interfaces de usuario que se pueden compartir entre Android, iOS, Windows y Plataforma universal de Windows. Las interfaces de usuario se representan mediante los controles nativos de la plataforma de destino, lo que permite que las aplicaciones de Xamarin.Forms conserven la apariencia adecuada para cada plataforma. En este artículo se proporciona una introducción a Xamarin.Forms y la manera de empezar a escribir aplicaciones con él._

<a name="Overview" />

## <a name="overview"></a>Información general

Xamarin.Forms es un marco que permite a los desarrolladores crear rápidamente interfaces de usuario multiplataforma. Proporciona su propia abstracción para la interfaz de usuario, que se representa mediante controles nativos de iOS, Android o Plataforma universal de Windows (UWP). Esto significa que las aplicaciones pueden compartir gran parte del código de su interfaz de usuario y conservar la apariencia nativa de la plataforma de destino.

Xamarin.Forms permite el rápido desarrollo de prototipos de aplicaciones que pueden evolucionar con el tiempo hasta convertirse en aplicaciones complejas. Dado que las aplicaciones de Xamarin.Forms son aplicaciones nativas, no tienen las limitaciones de otros kits de herramientas, como el espacio aislado del explorador, las API limitadas o un rendimiento deficiente. Las aplicaciones escritas con Xamarin.Forms pueden usar cualquiera de las API o las características de la plataforma subyacente, entre otras CoreMotion, PassKit y StoreKit en iOS; NFC y Google Play Services en Android; y Mosaicos en Windows. Además, es posible crear aplicaciones que tengan partes de la interfaz de usuario creadas con Xamarin.Forms, mientras otras partes están creadas mediante el kit de herramientas de interfaz de usuario nativa.

Las aplicaciones de Xamarin.Forms tienen la misma arquitectura que las aplicaciones multiplataforma tradicionales. El enfoque más común es usar [bibliotecas portátiles](~/cross-platform/app-fundamentals/pcl.md) o [proyectos compartidos](~/cross-platform/app-fundamentals/shared-projects.md) para alojar el código compartido y crear aplicaciones específicas de la plataforma que usen el código compartido.

Hay dos técnicas para crear interfaces de usuario en Xamarin.Forms. La primera técnica consiste en crear las interfaces de usuario por completo con código fuente de C#. La segunda técnica consiste en usar *XAML*, un lenguaje de marcado declarativo que se usa para describir interfaces de usuario. Para obtener más información sobre XAML, consulte [XAML Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Conceptos básicos de XAML).

En este artículo se explican los elementos fundamentales del marco de Xamarin.Forms y se tratan los temas siguientes:

-  [Examinar una aplicación de Xamarin.Forms](#Examining_A_Xamarin.Forms_Application).
-  [Cómo se usan las páginas y los controles de Xamarin.Forms](#Views_and_Layouts).
-  [Cómo se muestra una lista de datos](#Lists_in_Xamarin.Forms).
-  [Cómo se configura el enlace de datos](#Data_Binding).
-  [Cómo se navega entre páginas](#Navigation).
-  [Pasos siguientes](#Next_Steps).

<a name="Examining_A_Xamarin_Forms_Application" />

### <a name="examining-a-xamarinforms-application"></a>Examinar una aplicación de Xamarin.Forms

En Visual Studio para Mac y Visual Studio, la plantilla de aplicación predeterminada de Xamarin.Forms crea la solución más sencilla posible de Xamarin.Forms, que muestra texto al usuario. Si ejecuta la aplicación, debería tener un aspecto similar al que se muestra en las capturas de pantalla siguientes:

[![](introduction-to-xamarin-forms-images/image05-sml.png "Aplicación predeterminada de Xamarin.Forms")](introduction-to-xamarin-forms-images/image05.png#lightbox "Aplicación predeterminada de Xamarin.Forms")

Cada una de las pantallas de estas capturas corresponde a un *página* de Xamarin.Forms. Un elemento [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) representa una *Actividad* en Android, un *Controlador de vista* en iOS o una *Página* en la Plataforma universal de Windows (UWP). En el ejemplo de las capturas de pantalla anteriores se crea una instancia de un objeto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) y se usa para mostrar un elemento [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/).

Para maximizar la reutilización del código de inicio, las aplicaciones de Xamarin.Forms tienen una clase única denominada `App` que es responsable de crear instancias del primer elemento [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) que se mostrará. Puede verse un ejemplo de la clase `App` en el código siguiente:

```csharp
public class App : Application
{
  public App ()
  {
    MainPage = new ContentPage {
      Content =  new Label
      {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
      }
    };
  }
}
```

Este código crea una instancia de un nuevo objeto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) que mostrará un único elemento [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) centrado tanto vertical como horizontalmente en la página.

<a name="Launching_the_Initial_Xamarin_Forms_Page_on_Each_Platform" />

### <a name="launching-the-initial-xamarinforms-page-on-each-platform"></a>Iniciar la página inicial de Xamarin.Forms en cada plataforma

Para usar este elemento [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) dentro de una aplicación, cada aplicación de la plataforma debe inicializar el marco de Xamarin.Forms y proporcionar una instancia del elemento [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) al iniciarse. Este paso de inicialización varía en función de la plataforma y se describe en las secciones siguientes.

<a name="Launching_in_iOS" />

#### <a name="ios"></a>iOS

Para iniciar la página de Xamarin.Forms inicial en iOS, el proyecto de la plataforma incluye la clase `AppDelegate` que hereda de la clase `Xamarin.Forms.Platform.iOS.FormsApplicationDelegate`, como se muestra en el siguiente ejemplo de código:

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
      global::Xamarin.Forms.Forms.Init ();
      LoadApplication (new App ());
      return base.FinishedLaunching (app, options);
    }
}
```

El reemplazo `FinishedLoading` inicializa el marco de Xamarin.Forms mediante una llamada al método `Init`. Esto provoca que la implementación específica de iOS de Xamarin.Forms se cargue en la aplicación antes de que se establezca el controlador de vista raíz mediante la llamada al método `LoadApplication`.

<a name="Launching_in_Android" />

#### <a name="android"></a>Android

Para iniciar la página inicial de Xamarin.Forms en Android, el proyecto de la plataforma incluye el código que crea un elemento `Activity` con el atributo `MainLauncher`, con la actividad que se hereda de la clase `FormsApplicationActivity`, como se muestra en el siguiente ejemplo de código:

```csharp
namespace HelloXamarinFormsWorld.Android
{
    [Activity(Label = "HelloXamarinFormsWorld", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication (new App ());
        }
    }
}
```

El reemplazo `OnCreate` inicializa el marco de Xamarin.Forms mediante una llamada al método `Init`. Esto provoca que la implementación específica de Android de Xamarin.Forms se cargue en la aplicación antes de que lo haga la aplicación de Xamarin.Forms.

#### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En las aplicaciones de plataforma universal de Windows (UWP), el método `Init` que inicializa el marco de Xamarin.Forms se invoca desde la clase `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Esto provoca que la implementación específica de UWP de Xamarin.Forms se cargue en la aplicación. La página de Xamarin.Forms inicial se inicia mediante la clase `MainPage`, como se muestra en el siguiente ejemplo de código:

```csharp
public partial class MainPage
{
    public MainPage()
    {
      this.InitializeComponent();
      this.LoadApplication(new HelloXamarinFormsWorld.App());
    }
}
```

La aplicación de Xamarin.Forms se carga con el método `LoadApplication`.

<a name="Views_and_Layouts" />

### <a name="views-and-layouts"></a>Vistas y diseños

Existen cuatro grupos de control principales que se usan para crear la interfaz de usuario de una aplicación de Xamarin.Forms.

1. **Páginas**: las páginas de Xamarin.Forms representan pantallas de aplicaciones móviles multiplataforma. Para obtener más información sobre las páginas, consulte [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Páginas de Xamarin.Forms).
1. **Diseños**: los diseños de Xamarin.Forms son contenedores que se usan para crear vistas en estructuras lógicas. Para obtener más información sobre los diseños, consulte [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Diseños de Xamarin.Forms).
1. **Vistas**: las vistas de Xamarin.Forms son los controles que se muestran en la interfaz de usuario, como etiquetas, botones y cuadros de entrada de texto. Para obtener más información sobre las vistas, consulte [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Vistas de Xamarin.Forms).
1. **Celdas**: las celdas de Xamarin.Forms son elementos especializados que se usan para los elementos de una lista, y describen cómo debe dibujarse cada elemento de una lista. Para obtener más información sobre las celdas, consulte [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Celdas de Xamarin.Forms).

En tiempo de ejecución, cada control se asignará a su equivalente nativo, que es lo que se representará.

Los controles se hospedan en un diseño. A continuación examinaremos la clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), que implementa un diseño usado con frecuencia.

<a name="StackLayout" />

#### <a name="stacklayout"></a>StackLayout

La clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) simplifica el desarrollo de aplicaciones multiplataforma al organizar de manera automática los controles de la pantalla, independientemente del tamaño de esta. Cada elemento secundario se coloca uno detrás del otro, ya sea horizontal o verticalmente, en el orden en el que se ha agregado. La cantidad de espacio que usará la clase `StackLayout` depende de cómo se establezcan las propiedades [`HorizontalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) y [`VerticalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/), pero `StackLayout` intentará usar toda la pantalla de forma predeterminada.

En el siguiente código XAML se muestra un ejemplo del uso de una clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) para organizar tres controles [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/):

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample1" Padding="20">
  <StackLayout Spacing="10">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
public class StackLayoutExample : ContentPage
{
    public StackLayoutExample()
    {
        Padding = new Thickness(20);
        var red = new Label
        {
            Text = "Stop", BackgroundColor = Color.Red, FontSize = 20
        };
        var yellow = new Label
        {
            Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20
        };
        var green = new Label
        {
            Text = "Go", BackgroundColor = Color.Green, FontSize = 20
        };

        Content = new StackLayout
        {
            Spacing = 10,
            Children = { red, yellow, green }
        };
    }
}
```

De forma predeterminada, la clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) asume una orientación vertical, como se muestra en las siguientes capturas de pantalla:

[![](introduction-to-xamarin-forms-images/image09-sml.png "StackLayout vertical")](introduction-to-xamarin-forms-images/image09.png#lightbox "StackLayout vertical")

La orientación de la clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) puede cambiarse a una orientación horizontal, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample2" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
public class StackLayoutExample: ContentPage
{
    public StackLayoutExample()
    {
        // Code that creates labels removed for clarity
        Content = new StackLayout
        {
            Spacing = 10,
            VerticalOptions = LayoutOptions.End,
            Orientation = StackOrientation.Horizontal,
            HorizontalOptions = LayoutOptions.Start,
            Children = { red, yellow, green }
        };
    }
}
```

En las capturas de pantalla siguientes se muestra el diseño resultante:

[![](introduction-to-xamarin-forms-images/image10-sml.png "StackLayout horizontal")](introduction-to-xamarin-forms-images/image10.png#lightbox "StackLayout horizontal")

Se puede establecer el tamaño de los controles mediante las propiedades `HeightRequest` y `WidthRequest`, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample3" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" WidthRequest="100" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" WidthRequest="100" />
    <Label Text="Go" BackgroundColor="Green" Font="20" WidthRequest="200" />
  </StackLayout>
</ContentPage>
```

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
var red = new Label
{
    Text = "Stop", BackgroundColor = Color.Red, FontSize = 20, WidthRequest = 100
};
var yellow = new Label
{
    Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20, WidthRequest = 100
};
var green = new Label
{
    Text = "Go", BackgroundColor = Color.Green, FontSize = 20, WidthRequest = 200
};

Content = new StackLayout
{
    Spacing = 10,
    VerticalOptions = LayoutOptions.End,
    Orientation = StackOrientation.Horizontal,
    HorizontalOptions = LayoutOptions.Start,
    Children = { red, yellow, green }
};
```

En las capturas de pantalla siguientes se muestra el diseño resultante:

[![](introduction-to-xamarin-forms-images/image11-sml.png "StackLayout horizontal con LayoutOptions")](introduction-to-xamarin-forms-images/image11.png#lightbox "StackLayout horizontal con LayoutOptions")

Para obtener más información sobre la clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), consulte [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

<a name="Lists_in_Xamarin_Forms" />

## <a name="lists-in-xamarinforms"></a>Listas en Xamarin.Forms

El control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) es responsable de mostrar una colección de elementos en la pantalla. Cada elemento de `ListView` se incluirá en una celda. De forma predeterminada, el control `ListView` usará la plantilla [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) integrada y representará una sola línea de texto.

En el ejemplo de código siguiente se muestra un ejemplo de [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) simple:

```csharp
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = new string []
{
    "Buy pears", "Buy oranges", "Buy mangos", "Buy apples", "Buy bananas"
};
Content = new StackLayout
{
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = { listView }
};
```

En la captura de pantalla siguiente se muestra el resultado de [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/):

 ![](introduction-to-xamarin-forms-images/image13.png "ListView")

Para obtener más información sobre el control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), consulte [ListView](~/xamarin-forms/user-interface/listview/index.md).

<a name="Binding_to_a_Custom_Class" />

### <a name="binding-to-a-custom-class"></a>Enlazar a una clase personalizada

El control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) también puede mostrar objetos personalizados mediante la plantilla [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) predeterminada.

En el ejemplo de código siguiente se muestra la clase `TodoItem`:

```csharp
public class TodoItem
{
    public string Name { get; set; }
    public bool Done { get; set; }
}
```

El control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) se puede rellenar como se muestra en el ejemplo de código siguiente:

```csharp
listView.ItemsSource = new TodoItem [] {
    new TodoItem { Name = "Buy pears" },
    new TodoItem { Name = "Buy oranges", Done=true} ,
    new TodoItem { Name = "Buy mangos" },
    new TodoItem { Name = "Buy apples", Done=true },
    new TodoItem { Name = "Buy bananas", Done=true }
};
```

Se puede crear un enlace para establecer qué propiedad `TodoItem` muestra el control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), como se puede ver en el ejemplo de código siguiente:

```csharp
listView.ItemTemplate = new DataTemplate(typeof(TextCell));
listView.ItemTemplate.SetBinding(TextCell.TextProperty, "Name");
```

Esto crea un enlace que especifica la ruta de acceso a la propiedad `TodoItem.Name`, lo que tendrá como resultado la captura de pantalla mostrada anteriormente.

Para obtener más información sobre el enlace a una clase personalizada, consulte [ListView Data Sources](~/xamarin-forms/user-interface/listview/data-and-databinding.md) (Orígenes de datos de ListView).

<a name="Selecting_an_Item_in_a_ListView" />

### <a name="selecting-an-item-in-a-listview"></a>Seleccionar un elemento de un control ListView

Para responder a un usuario que toca una celda de un control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), es necesario controlar el evento [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/), como se muestra en el ejemplo de código siguiente:

```csharp
listView.ItemSelected += async (sender, e) => {
    await DisplayAlert("Tapped!", e.SelectedItem + " was tapped.", "OK");
};
```

Cuando está contenido en un elemento [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), el método [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) se puede usar para abrir una página nueva con la navegación hacia atrás integrada. El evento [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) puede tener acceso al objeto que estaba asociado con la celda mediante la propiedad [`e.SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem/), enlazar a una página nueva y mostrarla mediante `PushAsync`, como se muestra en el ejemplo de código siguiente:

```csharp
listView.ItemSelected += async (sender, e) => {
    var todoItem = (TodoItem)e.SelectedItem;
    var todoPage = new TodoItemPage(todoItem); // so the new page shows correct data
    await Navigation.PushAsync(todoPage);
};
```

Cada plataforma implementa a su manera la navegación hacia atrás integrada. Para obtener más información, consulte [Navigation](#Navigation) (Navegación).

Para obtener más información sobre la selección de [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), consulte [ListView Interactivity](~/xamarin-forms/user-interface/listview/interactivity.md) (Interactividad de ListView).

<a name="Customizing_the_appearance_of_a_cell" />

### <a name="customizing-the-appearance-of-a-cell"></a>Personalizar la apariencia de una celda

Se puede personalizar la apariencia de una celda mediante la creación de subclases de la clase [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) y el establecimiento del tipo de esta clase en la propiedad [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) del control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/).

La celda que se muestra en la captura de pantalla siguiente está formada por un elemento [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) y dos controles [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/):

 ![](introduction-to-xamarin-forms-images/image14.png "Aspecto de la celda personalizada de ListView")

Para crear este diseño personalizado, es necesario crear subclases de la clase [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), como se muestra en el ejemplo de código siguiente:

```csharp
class EmployeeCell : ViewCell
{
    public EmployeeCell()
    {
        var image = new Image
        {
            HorizontalOptions = LayoutOptions.Start
        };
        image.SetBinding(Image.SourceProperty, new Binding("ImageUri"));
        image.WidthRequest = image.HeightRequest = 40;

        var nameLayout = CreateNameLayout();
        var viewLayout = new StackLayout()
        {
           Orientation = StackOrientation.Horizontal,
           Children = { image, nameLayout }
        };
        View = viewLayout;
    }

    static StackLayout CreateNameLayout()
    {
        var nameLabel = new Label
        {
            HorizontalOptions= LayoutOptions.FillAndExpand
        };
        nameLabel.SetBinding(Label.TextProperty, "DisplayName");

        var twitterLabel = new Label
        {
           HorizontalOptions = LayoutOptions.FillAndExpand,
           Font = Fonts.Twitter
        };
        twitterLabel.SetBinding(Label.TextProperty, "Twitter");

        var nameLayout = new StackLayout()
        {
           HorizontalOptions = LayoutOptions.StartAndExpand,
           Orientation = StackOrientation.Vertical,
           Children = { nameLabel, twitterLabel }
        };
        return nameLayout;
    }
}
```

El código realiza las tareas siguientes:

-  Agrega un control [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) y lo enlaza con la propiedad `ImageUri` del objeto `Employee`. Para más información sobre el enlace de datos, consulte [Data Binding](#Data_Binding) (Enlace de datos).
-  Crea una clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) con una orientación vertical para contener los dos controles [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/). Los controles `Label` se enlazan a las propiedades `DisplayName` y `Twitter` del objeto `Employee`.
-  Crea una clase [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) que hospedará el elemento [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) existente y `StackLayout`. Organizará los elementos secundarios con una orientación horizontal.

Una vez creada la celda personalizada, puede usarla un control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Para ello, la incluye en [`DataTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), como se muestra en el ejemplo de código siguiente:

```csharp
List<Employee> myListOfEmployeeObjects = GetAListOfAllEmployees();
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = myListOfEmployeeObjects;
listView.ItemTemplate = new DataTemplate(typeof(EmployeeCell));
```

Este código proporcionará una lista (`List`) de empleados (`Employee`) al control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Cada celda se representará mediante la clase `EmployeeCell`. El control `ListView` pasará el objeto `Employee` a la celda `EmployeeCell` como [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/).

Para obtener más información sobre cómo personalizar la apariencia de una celda, consulte [Cell Appearance](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) (Apariencia de una celda).

<a name="Using_XAML_to_Create_and_Customize_A_List" />

### <a name="using-xaml-to-create-and-customize-a-list"></a>Usar XAML para crear y personalizar una lista

El equivalente en XAML del control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) de la sección anterior se muestra en el ejemplo de código siguiente:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:XamarinFormsXamlSample;assembly=XamarinFormsXamlSample"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="listView" IsVisible="false" ItemsSource="{x:Static local:App.Employees}" ItemSelected="EmployeeListOnItemSelected">
    <ListView.ItemTemplate>
      <DataTemplate>
        <ViewCell>
          <ViewCell.View>
            <StackLayout Orientation="Horizontal">
              <Image Source="{Binding ImageUri}" WidthRequest="40" HeightRequest="40" />
              <StackLayout Orientation="Vertical" HorizontalOptions="StartAndExpand">
                <Label Text="{Binding DisplayName}" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Twitter}" Font="{x:Static constants:Fonts.Twitter}"/>
              </StackLayout>
            </StackLayout>
          </ViewCell.View>
        </ViewCell>
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

En este código XAML se define un objeto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) que contiene un control [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). El origen de datos del control `ListView` se establece mediante el atributo [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/). El diseño de cada fila de `ItemsSource` se define dentro del elemento [`ListView.ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/).

<a name="Data_Binding" />

## <a name="data-binding"></a>Enlace de datos

El enlace de datos conecta dos objetos, denominados *origen* y *destino*. El objeto de *origen* proporciona los datos. El objeto de *destino* usa (y, a menudo, muestra) los datos del objeto de origen. Por ejemplo, un control [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) (objeto de *destino*) normalmente enlazará su propiedad [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) a una propiedad `string` pública en un objeto de *origen*. En el diagrama siguiente se muestra la relación de enlace:

![](introduction-to-xamarin-forms-images/data-binding.png "Enlace de datos")

El principal beneficio del enlace de datos es que ya no tiene que preocuparse de sincronizar los datos entre las vistas y el origen de datos. Los cambios en el objeto de *origen* se insertan automáticamente en el objeto de *destino* en segundo plano por medio del marco de enlace, mientras que los cambios en el objeto de destino pueden insertarse de manera opcional en el objeto de *origen*.

El proceso de establecer el enlace de datos consta de dos pasos:

- La propiedad [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del objeto de *destino* se debe establecer en el de *origen*.
- Es necesario establecer un enlace entre el *destino* y el *origen*. En XAML, esto se consigue mediante la extensión de marcado [`Binding`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/). En C#, se consigue mediante el método [`SetBinding`](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/).

Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

### <a name="xaml"></a>XAML

En el código siguiente se muestra un ejemplo de cómo se realiza el enlace de datos en XAML:

```xaml
<Entry Text="{Binding FirstName}" ... />
```

Se establece un enlace entre la propiedad [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) y la propiedad `FirstName` del objeto de *origen*. Los cambios realizados en el control `Entry` se propagarán automáticamente al objeto `employeeToDisplay`. De forma similar, si se realizan cambios en la propiedad `employeeToDisplay.FirstName`, el motor de enlace de Xamarin.Forms también actualizará el contenido del control `Entry`. Esto se conoce como *enlace bidireccional*. Para que funcione el enlace bidireccional, la clase del modelo debe implementar la interfaz `INotifyPropertyChanged`.

Aunque la propiedad [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) de la clase `EmployeeDetailPage` se puede establecer en XAML, aquí está establecida en código subyacente en una instancia de un objeto `Employee`:

```csharp
public EmployeeDetailPage(Employee employee)
{
    InitializeComponent();
    this.BindingContext = employee;
}
```

Aunque la propiedad [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) de cada objeto de *destino* se puede establecer de manera individual, no es necesario hacerlo. `BindingContext` es una propiedad especial que heredan todos sus elementos secundarios. Por lo tanto, cuando la propiedad `BindingContext` de [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) se establece en una instancia `Employee`, todos los elementos secundarios de `ContentPage` tienen la misma propiedad `BindingContext` y pueden enlazar a propiedades públicas del objeto `Employee`.

### <a name="c35"></a>C&#35;

En el código siguiente se muestra un ejemplo de cómo se realiza el enlace de datos en C#:

```csharp
public EmployeeDetailPage(Employee employeeToDisplay)
{
    this.BindingContext = employeeToDisplay;
    var firstName = new Entry()
    {
        HorizontalOptions = LayoutOptions.FillAndExpand
    };
    firstName.SetBinding(Entry.TextProperty, "FirstName");
    ...
}
```

Se pasa una instancia de un objeto `Employee` al constructor [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) y este establece la propiedad [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) en el objeto al que se va a enlazar. Se crea una instancia de un control [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) y se establece un enlace entre la propiedad [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) y la propiedad `FirstName` del objeto de *origen*. Los cambios realizados en el control `Entry` se propagarán automáticamente al objeto `employeeToDisplay`. De forma similar, si se realizan cambios en la propiedad `employeeToDisplay.FirstName`, el motor de enlace de Xamarin.Forms también actualizará el contenido del control `Entry`. Esto se conoce como *enlace bidireccional*. Para que funcione el enlace bidireccional, la clase del modelo debe implementar la interfaz `INotifyPropertyChanged`.

El método `SetBinding` toma dos parámetros. El primer parámetro especifica información sobre el tipo de enlace. El segundo parámetro se usa para proporcionar información sobre el elemento al que se va a enlazar o cómo se va a enlazar. En la mayoría de los casos, el segundo parámetro es simplemente una cadena que contiene el nombre de propiedad en [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Se usa la sintaxis siguiente para enlazar directamente a `BindingContext`:

```csharp
someLabel.SetBinding(Label.TextProperty, new Binding("."));
```

La sintaxis de punto le indica a Xamarin.Forms que use [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) como origen de datos, en lugar de una propiedad en `BindingContext`. Esto es útil cuando `BindingContext` es un tipo simple, como `string` o `int`.

<a name="INotifyPropertyChanged" />

### <a name="property-change-notification"></a>Notificación de cambio de propiedad

De forma predeterminada, el objeto de *destino* solo recibe el valor del objeto de *origen* cuando se crea el enlace. Para mantener la interfaz de usuario sincronizada con el origen de datos, debe haber una manera de notificarle al objeto de *destino* cuándo ha cambiado el objeto de *origen*. Este mecanismo se proporciona mediante la interfaz `INotifyPropertyChanged`. Al implementar esta interfaz, se proporcionan notificaciones a todos los controles enlazados a datos cuando cambia el valor de propiedad subyacente.

Los objetos que implementan `INotifyPropertyChanged` deben producir el evento `PropertyChanged` cuando una de sus propiedades se actualiza con un nuevo valor, como se muestra en el ejemplo de código siguiente:

```csharp
public class MyObject : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    string _firstName;
    public string FirstName
    {
        get { return _firstName; }
        set
        {
            if (value.Equals(_firstName, StringComparison.Ordinal))
            {
                // Nothing to do - the value hasn't changed;
                return;
            }
            _firstName = value;
            OnPropertyChanged();

        }
    }

    void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        var handler = PropertyChanged;
        if (handler != null)
        {
            handler(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Cuando la propiedad `MyObject.FirstName` cambia, se invoca el método `OnPropertyChanged`, que producirá el evento `PropertyChanged`. Para evitar la activación de eventos innecesarios, no se produce el evento `PropertyChanged` si el valor de propiedad no cambia.

Tenga en cuenta que, en el método `OnPropertyChanged`, el parámetro `propertyName` está adornado con el atributo `CallerMemberName`. Esto garantiza que si se invoca el método `OnPropertyChanged` con un valor `null`, el atributo `CallerMemberName` proporcionará el nombre del método que invocó `OnPropertyChanged`.

<a name="Navigation" />

## <a name="navigation"></a>Navegación

Xamarin.Forms proporciona una serie de experiencias de navegación de páginas diferente, en función del tipo de [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) que se use. Para las instancias de [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), hay dos experiencias de navegación:

- [Navegación jerárquica](#Hierarchical_Navigation)
- [Navegación modal](#Modal_Navigation)

Las clases [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), [`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) y [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) proporcionan experiencias de navegación alternativas. Para obtener más información, consulte [Navigation](~/xamarin-forms/app-fundamentals/navigation/index.md) (Navegación).

<a name="Hierarchical_Navigation" />

### <a name="hierarchical-navigation"></a>Navegación jerárquica

La clase [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) proporciona una experiencia de navegación jerárquica en la que el usuario puede navegar por las páginas hacia delante y hacia atrás, si quiere. La clase implementa la navegación como una pila de objetos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) en la que el último en entrar es el primero en salir (LIFO).

En la navegación jerárquica, se usa la clase [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) para navegar por una pila de objetos [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Para pasar de una página a otra, una aplicación insertará una nueva página en la pila de navegación, donde se convertirá en la página activa. Para volver a la página anterior, la aplicación mostrará la página actual de la pila de navegación y la nueva página de nivel superior se convertirá en la página activa.

La primera página que se agrega a una pila de navegación se denomina página *raíz* de la aplicación. En el ejemplo de código siguiente se muestra cómo se consigue:

```csharp
public App ()
{
    MainPage = new NavigationPage(new EmployeeListPage());
}
```

Para navegar a `LoginPage`, es necesario invocar el método [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) en la propiedad [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) de la página actual, como se muestra en el ejemplo de código siguiente:

```csharp
await Navigation.PushAsync(new LoginPage());
```

Esto hace que el nuevo objeto `LoginPage` se inserte en la pila de navegación, donde se convertirá en la página activa.

La página activa se puede extraer de la pila de navegación. Para ello, pulse el botón *Atrás* del dispositivo, independientemente de si se trata de un botón físico en el dispositivo o de un botón en la pantalla. Para volver mediante programación a la página anterior, la instancia `LoginPage` debe invocar el método [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), como se muestra en el ejemplo de código siguiente:

```csharp
await Navigation.PopAsync();
```

Para obtener más información sobre la navegación jerárquica, consulte [Hierarchical Navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) (Navegación jerárquica).

<a name="Modal_Navigation" />

### <a name="modal-navigation"></a>Navegación modal

Xamarin.Forms es compatible con las páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea.

Una página modal puede ser cualquiera de los tipos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) compatibles con Xamarin.Forms. Para mostrar una página modal, la aplicación la insertará en la pila de navegación, donde se convertirá en la página activa. Para volver a la página anterior, la aplicación mostrará la página actual de la pila de navegación y la nueva página de nivel superior se convertirá en la página activa.

Los métodos de navegación modal se exponen mediante la propiedad [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) en cualquier tipo [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) derivado. La propiedad [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) también expone una propiedad [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) desde la que se pueden obtener las páginas modales de la pila de navegación. Pero no existe el concepto de realizar una manipulación de pila modal o extraer la página raíz de la navegación modal. Esto se debe a que estas operaciones no se admiten de forma universal en las plataformas subyacentes.

> [!NOTE]
> No es necesaria una instancia de [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) para realizar la navegación de páginas modal.

Para navegar de forma modal a `LoginPage`, es necesario invocar el método [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) en la propiedad [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) de la página actual, como se muestra en el ejemplo de código siguiente:

```csharp
await Navigation.PushModalAsync(new LoginPage());
```

Esto hace que la instancia de `LoginPage` se inserte en la pila de navegación, donde se convertirá en la página activa.

La página activa se puede extraer de la pila de navegación. Para ello, pulse el botón *Atrás* del dispositivo, independientemente de si se trata de un botón físico en el dispositivo o de un botón en la pantalla. Para volver mediante programación a la página original, la instancia `LoginPage` debe invocar el método [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/), como se muestra en el ejemplo de código siguiente:

```csharp
await Navigation.PopModalAsync();
```

Esto hace que la instancia de `LoginPage` se quite de la pila de navegación y que la nueva página de nivel superior se convierta en la página activa.

Para obtener más información sobre la navegación modal, consulte [Modal Pages](~/xamarin-forms/app-fundamentals/navigation/modal.md) (Páginas modales).

<a name="Next_Steps" />

## <a name="next-steps"></a>Pasos siguientes

Este artículo introductorio debería permitirle empezar a escribir aplicaciones de Xamarin.Forms. Se recomienda que, como paso siguiente, lea sobre las funcionalidades que se indican a continuación:

- Las plantillas de control permiten crear y cambiar fácilmente el tema de las páginas de la aplicación en tiempo de ejecución. Para obtener más información, consulte [Control Templates](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) (Plantillas de control).
- Las plantillas de datos permiten definir la presentación de los datos en los controles admitidos. Para obtener más información, consulte [Data Templates](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md) (Plantillas de datos).
- El código compartido puede tener acceso a la funcionalidad nativa mediante la clase [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/). Para obtener más información, consulte [Accessing Native Features with DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) (Acceso a características nativas con DependencyService).
- Xamarin.Forms incluye un servicio de mensajería simple para enviar y recibir mensajes, lo que reduce el acoplamiento entre clases. Para obtener más información, consulte [Publish and Subscribe with MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md) (Publicar y suscribirse con MessagingCenter).
- Cada página, diseño y control se representan de forma diferente en cada plataforma mediante una clase `Renderer` que, a su vez, crea un control nativo, lo organiza en la pantalla y agrega el comportamiento especificado en el código compartido. Los desarrolladores pueden implementar sus propias clases `Renderer` personalizadas para personalizar la apariencia o el comportamiento de un control. Para obtener más información, consulte [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Representadores personalizados).
- Los efectos también permiten personalizar los controles nativos de cada plataforma. Los efectos se crean en proyectos específicos de la plataforma mediante la creación de subclases del control [`PlatformEffect`](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/). Para usarlos, se adjuntan a un control adecuado de Xamarin.Forms. Para obtener más información, consulte [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Efectos).

También es recomendable el libro de Charles Petzold titulado Creating Mobile Apps with Xamarin.Forms para obtener más información sobre Xamarin.Forms. Para obtener más información, consulte [Creating Mobile Apps with Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) (Crear aplicaciones móviles con Xamarin.Forms).

## <a name="summary"></a>Resumen

En este artículo se ha proporcionado una introducción a Xamarin.Forms y la manera de empezar a escribir aplicaciones con él. Xamarin.Forms es que una abstracción de kit de herramientas de interfaz de usuario multiplataforma respaldada de forma nativa que permite a los desarrolladores crear fácilmente interfaces de usuario que se pueden compartir entre Android, iOS y Plataforma universal de Windows. Las interfaces de usuario se representan mediante los controles nativos de la plataforma de destino, lo que permite que las aplicaciones de Xamarin.Forms conserven la apariencia adecuada para cada plataforma.


## <a name="related-links"></a>Vínculos relacionados

- [XAML Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Conceptos básicos de XAML)
- [Controls Reference](~/xamarin-forms/user-interface/controls/index.md) (Referencia de controles)
- [Interfaz de usuario](~/xamarin-forms/user-interface/index.md)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Getting Started Samples](https://developer.xamarin.com/samples/xamarin-forms/GettingStarted/) (Ejemplos de introducción)
- [Xamarin.Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
- [Aprendizaje autoguiado gratuito (vídeo)](https://university.xamarin.com/self-guided)
- [Hello, Xamarin.Forms iOS Workbook (Libro de iOS de Hello, Xamarin.Forms)](https://developer.xamarin.com/workbooks/xamarin-forms/getting-started/GettingStartedWithXamarinForms-ios.workbook)
