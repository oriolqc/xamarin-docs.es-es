---
title: Navegación jerárquica
description: La clase NavigationPage proporciona una experiencia de navegación jerárquica donde el usuario tiene permiso navegar a través de páginas hacia delante y hacia atrás, según sea necesario. La clase implementa navegación como una pila, último en salir (LIFO) de objetos de la página. En este artículo se muestra cómo utilizar la clase NavigationPage para realizar la navegación en una pila de páginas.
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 3fc5b24474230fd2b2477f020ac24cd72996d7b1
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2018
---
# <a name="hierarchical-navigation"></a>Navegación jerárquica

_La clase NavigationPage proporciona una experiencia de navegación jerárquica donde el usuario tiene permiso navegar a través de páginas hacia delante y hacia atrás, según sea necesario. La clase implementa navegación como una pila, último en salir (LIFO) de objetos de la página. En este artículo se muestra cómo utilizar la clase NavigationPage para realizar la navegación en una pila de páginas._

En este artículo se trata los temas siguientes:

- [Realizar exploración](#Performing_Navigation) : creación de la página raíz, realiza la inserción de páginas en la pila de navegación, retirar páginas de la pila de navegación y animación de transiciones de página.
- [Pasar datos al navegar por](#Passing_Data_when_Navigating) : pasar datos a través de un constructor de la página y, a través de un `BindingContext`.
- [Manipulación de la pila de navegación](#Manipulating_the_Navigation_Stack) : manipular la pila al insertar o quitar páginas.

## <a name="overview"></a>Información general

Para pasar de una página a otra, una aplicación envía una nueva página en la pila de navegación, donde se convertirá en la página activa, como se muestra en el diagrama siguiente:

![](hierarchical-images/pushing.png "Insertar una página en la pila de navegación")

Para volver a la página anterior, la aplicación mostrará la página actual de la pila de navegación y la nueva página de nivel superior se convierte en la página activa, como se muestra en el diagrama siguiente:

![](hierarchical-images/popping.png "Retirar una página de la pila de navegación")

Exponen métodos para navegar por la [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad en cualquier [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) tipos derivados. Estos métodos proporcionan la capacidad para insertar páginas en la pila de navegación en páginas de confirmación de la pila de navegación y para realizar manipulaciones de la pila.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Realización de navegación

En la navegación jerárquica, se usa la clase [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) para navegar por una pila de objetos [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Las capturas de pantalla siguientes muestran los componentes principales de la `NavigationPage` en cada plataforma:

![](hierarchical-images/navigationpage-components.png "Componentes de NavigationPage")

El diseño de un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) depende de la plataforma:

- En iOS, una barra de navegación está presente en la parte superior de la página que muestra un título y que tenga un *volver* botón que se devuelve a la página anterior.
- En Android, está presente en la parte superior de la página que muestra un título, un icono, una barra de navegación y un *volver* botón que se devuelve a la página anterior. El icono se define en el `[Activity]` atributo que decora el `MainActivity` clase en el proyecto específico de la plataforma Android.
- En la plataforma Universal de Windows está presente en la parte superior de la página que muestra un título de una barra de navegación. 

En todas las plataformas, el valor de la [ `Page.Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) propiedad aparecerá como el título de página.

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

Esto hace que la `Page1Xaml` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancia se inserte en la pila de navegación, donde se convierte en la página activa y la página raíz de la aplicación. Esto se muestra en las capturas de pantalla siguiente:

![](hierarchical-images/mainpage.png "Página de la raíz de la pila de navegación")

> [!NOTE]
> El [ `RootPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.RootPage/) propiedad de un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instancia proporciona acceso a la primera página de la pila de navegación.

### <a name="pushing-pages-to-the-navigation-stack"></a>Insertar páginas en la pila de navegación

Para navegar a `Page2Xaml`, es necesario invocar la [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) método en el [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad de la página actual, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Esto hace que la instancia de `Page2Xaml` se inserte en la pila de navegación, donde se convertirá en la página activa. Esto se muestra en las capturas de pantalla siguiente:

![](hierarchical-images/secondpage.png "Página que se inserta en la pila de navegación")

Cuando el [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) se invoca el método, ocurre lo siguiente:

- La llamada a página `PushAsync` tiene su [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) invalidación invocada.
- La página que se navega tiene su [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) invalidación invocada.
- La `PushAsync` tarea se complete.

Sin embargo, el orden exacto en el que se producen estos eventos es depende de la plataforma. Para obtener más información, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de libro de Xamarin.Forms de Petzold.

> [!NOTE]
> Llamadas a la [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) y [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) invalidaciones no se puede tratar como indicaciones garantizadas de navegación en páginas. Por ejemplo, en iOS, el `OnDisappearing` invalidación se llama en la página activa cuando la aplicación finaliza.

### <a name="popping-pages-from-the-navigation-stack"></a>Extraer páginas de la pila de navegación

La página activa se puede extraer de la pila de navegación. Para ello, pulse el botón *Atrás* del dispositivo, independientemente de si se trata de un botón físico en el dispositivo o de un botón en la pantalla.

Para volver mediante programación a la página original, la instancia `Page2Xaml` debe invocar el método [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), como se muestra en el ejemplo de código siguiente:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Esto hace que la instancia de `Page2Xaml` se quite de la pila de navegación y que la nueva página de nivel superior se convierta en la página activa. Cuando el [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) se invoca el método, ocurre lo siguiente:

- La llamada a página `PopAsync` tiene su [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) invalidación invocada.
- La página que se devuelve al tiene su [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) invalidación invocada.
- El `PopAsync` devuelve la tarea.

Sin embargo, el orden exacto en el que se producen estos eventos es depende de la plataforma. Para obtener más información, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de libro de Xamarin.Forms de Petzold.

Así como [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) y [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) métodos, el [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad de cada página también proporciona un [ `PopToRootAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopToRootAsync()/) método, que se muestra en el ejemplo de código siguiente:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Este método extrae todas excepto la raíz [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) de la pila de navegación, lo que la página raíz de la aplicación de la página activa.

### <a name="animating-page-transitions"></a>Animación de transiciones de página

El [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad de cada página también proporciona inserción invalidado y métodos de confirmación que incluyen un `boolean` parámetro que controla si se muestra una animación de página durante la navegación, como se muestra en el código siguiente ejemplo:

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

Establecer el `boolean` parámetro `false` deshabilita la animación de transición de la página, al establecer el parámetro `true` habilita la animación de transición de la página, siempre que es compatible con la plataforma subyacente. Sin embargo, los métodos de inserción y extracción que no tienen este parámetro habilitan la animación predeterminada.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Pasar datos al navegar por

A veces es necesario para una página pasar datos a otra página durante la navegación. Estas dos técnicas para lograrlo son pasar datos a través de un constructor de la página y estableciendo la nueva página [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) a los datos. Cada uno de ellos ahora se explicará a su vez.

### <a name="passing-data-through-a-page-constructor"></a>Pasar datos a través de un Constructor de página

Es la técnica más sencilla para pasar datos a otra página durante la navegación a través de un parámetro de constructor de página, que se muestra en el ejemplo de código siguiente:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Este código crea un `MainPage` de la instancia, pasa la fecha y hora actuales en formato ISO8601, que se ajusta en un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instancia.

El `MainPage` instancia recibe los datos a través de un parámetro de constructor, tal como se muestra en el ejemplo de código siguiente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Los datos se muestran a continuación, en la página estableciendo la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedad, como se muestra en las capturas de pantalla siguiente:

![](hierarchical-images/passing-data-constructor.png "Datos que se pasan a través de un Constructor de página")

### <a name="passing-data-through-a-bindingcontext"></a>Pasar datos a través de un objeto BindingContext

Un enfoque alternativo para pasar datos a otra página durante la navegación está definiendo la nueva página [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) a los datos, como se muestra en el ejemplo de código siguiente:

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

Este código establece la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) de la `SecondPage` de instancia para la `Contact` una instancia y, a continuación, se desplaza a la `SecondPage`.

El `SecondPage` , a continuación, usa el enlace de datos para mostrar el `Contact` instancia datos, como se muestra en el siguiente ejemplo de código XAML:

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

En el ejemplo de código siguiente se muestra cómo se puede lograr el enlace de datos en C#:

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

Los datos se muestran a continuación, en la página mediante una serie de [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controles, como se muestra en las capturas de pantalla siguiente:

![](hierarchical-images/passing-data-bindingcontext.png "Datos que se pasan a través de un objeto BindingContext")

Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulación de la pila de navegación

El [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad expone un [ `NavigationStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/) propiedad desde el que se pueden obtener las páginas en la pila de navegación. Mientras Xamarin.Forms mantiene el acceso a la pila de navegación, la `Navigation` propiedad proporciona el [ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/) y [ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/) métodos para manipular la pila mediante la inserción páginas o la eliminación.

El [ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/) método inserta una página especificada en la pila de navegación antes de una página especificada, tal como se muestra en el diagrama siguiente:

![](hierarchical-images/insert-page-before.png "Para insertar una página en la pila de navegación")

El [ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/) método quita la página especificada de la pila de navegación, como se muestra en el diagrama siguiente:

![](hierarchical-images/remove-page.png "Quitar una página de la pila de navegación")

Estos métodos permiten una experiencia de exploración personalizada, como el reemplazo de una página de inicio de sesión con una nueva página después de un inicio de sesión correcto. En el ejemplo de código siguiente se muestra este escenario:

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

Siempre que las credenciales del usuario son correctas, el `MainPage` instancia se inserta en la pila de navegación antes de la página actual. El [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) método, a continuación, quita la página actual de la pila de navegación, con el `MainPage` instancia se convierta en la página activa.

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar el [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) clase para realizar la navegación en una pila de páginas. Esta clase proporciona una experiencia de navegación jerárquica donde el usuario tiene permiso navegar a través de páginas hacia delante y hacia atrás, según sea necesario. La clase implementa la navegación como una pila de objetos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) en la que el último en entrar es el primero en salir (LIFO).


## <a name="related-links"></a>Vínculos relacionados

- [Navegación de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Jerárquica (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [Cómo crear un inicio de sesión de flujo de pantalla de ejemplo de Xamarin.Forms (vídeo de la Universidad de Xamarin)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Cómo crear un inicio de sesión de flujo de pantalla de Xamarin.Forms (vídeo de la Universidad de Xamarin)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)
