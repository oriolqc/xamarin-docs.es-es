---
title: Navegación jerárquica
description: En este artículo se muestra cómo usar la clase NavigationPage para realizar la navegación en una pila de último en salir (LIFO) páginas.
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: f8f8f9b4e5755e8b1707178fef633321b64e4e94
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994681"
---
# <a name="hierarchical-navigation"></a>Navegación jerárquica

_La clase NavigationPage proporciona una experiencia de navegación jerárquica donde el usuario es capaz de navegar a través de páginas hacia delante y hacia atrás, según sea necesario. La clase implementa la navegación como una pila, último en salir (LIFO) de objetos de la página. En este artículo se muestra cómo usar la clase NavigationPage para realizar la navegación en una pila de páginas._

En este artículo se trata los temas siguientes:

- [Realizar la navegación](#Performing_Navigation) : creación de la página raíz, inserción de páginas en la pila de navegación, aparecerán las páginas de la pila de navegación y animar las transiciones de página.
- [Pasar datos al navegar](#Passing_Data_when_Navigating) : pasar datos a través de un constructor de la página y un `BindingContext`.
- [Manipulación de la pila de navegación](#Manipulating_the_Navigation_Stack) : manipulación de la pila al insertar o quitar páginas.

## <a name="overview"></a>Información general

Para pasar de una página a otra, una aplicación insertará una nueva página en la pila de navegación, donde se convertirá en la página activa, tal como se muestra en el diagrama siguiente:

![](hierarchical-images/pushing.png "Inserción de una página en la pila de navegación")

Para volver a la página anterior, la aplicación mostrará la página actual de la pila de navegación y la nueva página de nivel superior se convierte en la página activa, tal como se muestra en el diagrama siguiente:

![](hierarchical-images/popping.png "Sacar una página de la pila de navegación")

Métodos de navegación expuestos por la [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propiedad en cualquier [ `Page` ](xref:Xamarin.Forms.Page) tipos derivados. Estos métodos proporcionan la capacidad para insertar páginas en la pila de navegación en las páginas de confirmación de la pila de navegación y manipulación de pila.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Realizar la navegación

En la navegación jerárquica, se usa la clase [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) para navegar por una pila de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). Las capturas de pantalla siguientes muestran los componentes principales de la `NavigationPage` en cada plataforma:

![](hierarchical-images/navigationpage-components.png "Componentes de NavigationPage")

El diseño de un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) depende de la plataforma:

- En iOS, una barra de navegación está presente en la parte superior de la página que muestra un título y que tiene un *volver* botón que se devuelve a la página anterior.
- En Android, una barra de navegación está presente en la parte superior de la página que muestra un título, un icono, y un *volver* botón que se devuelve a la página anterior. El icono se define en el `[Activity]` atributo que decora el `MainActivity` clase en el proyecto específico de la plataforma Android.
- En la plataforma Universal de Windows está presente en la parte superior de la página que muestra un título de una barra de navegación.

En todas las plataformas, el valor de la [ `Page.Title` ](xref:Xamarin.Forms.Page.Title) propiedad aparecerá como título de la página.

> [!NOTE]
> Se recomienda que un `NavigationPage` debe rellenarse con `ContentPage` solo instancias.

### <a name="creating-the-root-page"></a>Creación de la página raíz

La primera página que se agrega a una pila de navegación se denomina página *raíz* de la aplicación. En el ejemplo de código siguiente se muestra cómo se consigue:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

Esto hace que el `Page1Xaml` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancia se inserte en la pila de navegación, donde se convertirá en la página activa y la página raíz de la aplicación. Esto se muestra en las capturas de pantalla siguiente:

![](hierarchical-images/mainpage.png "Página de la raíz de la pila de navegación")

> [!NOTE]
> El [ `RootPage` ](xref:Xamarin.Forms.NavigationPage.RootPage) propiedad de un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instancia proporciona acceso a la primera página de la pila de navegación.

### <a name="pushing-pages-to-the-navigation-stack"></a>Inserción de páginas en la pila de navegación

Para navegar a `Page2Xaml`, es necesario invocar la [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) método en el [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propiedad de la página actual, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Esto hace que la instancia de `Page2Xaml` se inserte en la pila de navegación, donde se convertirá en la página activa. Esto se muestra en las capturas de pantalla siguiente:

![](hierarchical-images/secondpage.png "Página que se inserta en la pila de navegación")

Cuando el [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) se invoca el método, ocurre lo siguiente:

- La llamada a la página `PushAsync` tiene su [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) invalidación que se invoca.
- La página que se navega tiene su [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) invalidación que se invoca.
- El `PushAsync` se complete la tarea.

Sin embargo, el orden exacto en el que se producen estos eventos es depende de la plataforma. Para obtener más información, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro de Petzold Xamarin.Forms.

> [!NOTE]
> Las llamadas a la [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) y [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) invalidaciones no se puede tratar como indicaciones garantizadas de navegación de páginas. Por ejemplo, en iOS, el `OnDisappearing` invalidación se llama en la página activa cuando la aplicación finaliza.

### <a name="popping-pages-from-the-navigation-stack"></a>Páginas que se extrae de la pila de navegación

La página activa se puede extraer de la pila de navegación. Para ello, pulse el botón *Atrás* del dispositivo, independientemente de si se trata de un botón físico en el dispositivo o de un botón en la pantalla.

Para volver mediante programación a la página original, la instancia `Page2Xaml` debe invocar el método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), como se muestra en el ejemplo de código siguiente:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Esto hace que la instancia de `Page2Xaml` se quite de la pila de navegación y que la nueva página de nivel superior se convierta en la página activa. Cuando el [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) se invoca el método, ocurre lo siguiente:

- La llamada a la página `PopAsync` tiene su [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) invalidación que se invoca.
- La página se devuelva a tiene su [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) invalidación que se invoca.
- El `PopAsync` devuelve la tarea.

Sin embargo, el orden exacto en el que se producen estos eventos es depende de la plataforma. Para obtener más información, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) del libro de Petzold Xamarin.Forms.

Como [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) y [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) métodos, el [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) también proporciona la propiedad de cada página un [ `PopToRootAsync` ](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) método, que se muestra en el ejemplo de código siguiente:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Este método extrae todas excepto la raíz [ `Page` ](xref:Xamarin.Forms.Page) la pila de navegación, lo que la página raíz de la aplicación de la página activa.

### <a name="animating-page-transitions"></a>Animación de transiciones de página

El [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propiedad de cada página también proporciona invalidado push y pop métodos que incluyen un `boolean` parámetro que controla si se muestra una animación de página durante la navegación, como se muestra en el código siguiente ejemplo:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

Establecer el `boolean` parámetro `false` deshabilita la animación de transición de página, mientras se establece el parámetro a `true` habilita la animación de transición de página, siempre que es compatible con la plataforma subyacente. Sin embargo, los métodos push y pop que carecen de este parámetro habilitan la animación predeterminada.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Pasar datos al navegar

A veces es necesario para una página pasar datos a otra página durante la navegación. Dos técnicas para llevar a cabo esto pasan datos a través de un constructor de la página y estableciendo la nueva página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) a los datos. Cada uno de ellos ahora se explicará a su vez.

### <a name="passing-data-through-a-page-constructor"></a>Pasar datos a través de un Constructor de la página

Es la técnica más sencilla para pasar datos a otra página durante la navegación a través de un parámetro de constructor de la página, que se muestra en el ejemplo de código siguiente:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Este código crea un `MainPage` de la instancia, pasando la fecha y hora actuales en formato ISO8601, que se encapsula en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instancia.

El `MainPage` instancia recibe los datos a través de un parámetro de constructor, tal como se muestra en el ejemplo de código siguiente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Los datos se muestran a continuación, en la página estableciendo la [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propiedad, como se muestra en las capturas de pantalla siguiente:

![](hierarchical-images/passing-data-constructor.png "Datos que se pasan a través de un Constructor de la página")

### <a name="passing-data-through-a-bindingcontext"></a>Pasar datos a través de un objeto BindingContext

Un enfoque alternativo para pasar datos a otra página durante la navegación está estableciendo la nueva página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) a los datos, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

Este código establece la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la `SecondPage` de instancia para el `Contact` de instancia y, a continuación, navega a la `SecondPage`.

El `SecondPage` , a continuación, usa el enlace de datos para mostrar el `Contact` instancia datos, tal como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El ejemplo de código siguiente muestra cómo se puede realizar el enlace de datos de C#:

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

Los datos se muestran a continuación, en la página mediante una serie de [ `Label` ](xref:Xamarin.Forms.Label) controles, como se muestra en las capturas de pantalla siguiente:

![](hierarchical-images/passing-data-bindingcontext.png "Datos que se pasan a través de un objeto BindingContext")

Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulación de la pila de navegación

El [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propiedad expone un [ `NavigationStack` ](xref:Xamarin.Forms.INavigation.NavigationStack) propiedad desde la que se pueden obtener las páginas en la pila de navegación. Mientras mantiene el acceso a la pila de navegación, Xamarin.Forms el `Navigation` propiedad proporciona la [ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*) y [ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*) métodos para manipular la pila mediante la inserción páginas o quitarlos.

El [ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*) método inserta una página especificada en la pila de navegación antes de una página existente especificada, tal como se muestra en el diagrama siguiente:

![](hierarchical-images/insert-page-before.png "Para insertar una página en la pila de navegación")

El [ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*) método quita la página especificada de la pila de navegación, como se muestra en el diagrama siguiente:

![](hierarchical-images/remove-page.png "Quitar una página de la pila de navegación")

Estos métodos permiten una experiencia de navegación personalizada, como el reemplazo de una página de inicio de sesión con una página, después de iniciar sesión correctamente. En el ejemplo de código siguiente se muestra este escenario:

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

Siempre que las credenciales del usuario son correctas, el `MainPage` instancia se inserta en la pila de navegación antes de la página actual. El [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) método, a continuación, quita la página actual de la pila de navegación con el `MainPage` instancia convertirse en la página activa.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) clase para realizar la navegación en una pila de páginas. Esta clase proporciona una experiencia de navegación jerárquica donde el usuario es capaz de navegar a través de páginas hacia delante y hacia atrás, según sea necesario. La clase implementa la navegación como una pila de objetos [`Page`](xref:Xamarin.Forms.Page) en la que el último en entrar es el primero en salir (LIFO).


## <a name="related-links"></a>Vínculos relacionados

- [Navegación de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Jerárquico (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [Cómo crear un inicio de sesión en el flujo de pantalla de ejemplo de Xamarin.Forms (vídeo de Xamarin University)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Cómo crear un inicio de sesión en el flujo de pantalla en Xamarin.Forms (vídeo de Xamarin University)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
