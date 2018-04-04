---
title: Páginas modales
description: Xamarin.Forms es compatible con las páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea. En este artículo se muestra cómo navegar a páginas modales.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 909a04398043a3c2f0c30e4da82d174a6bfaf148
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="modal-pages"></a>Páginas modales

_Xamarin.Forms ofrece compatibilidad con páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea. En este artículo se muestra cómo navegar a páginas modales._

En este artículo se trata los temas siguientes:

- [Realizar exploración](#Performing_Navigation) : insertar páginas a la pila modal, extraer páginas de la pila modal, deshabilitar el botón Atrás y animación de transiciones de página.
- [Pasar datos al navegar por](#Passing_Data_when_Navigating) : pasar datos a través de un constructor de la página y, a través de un `BindingContext`.

## <a name="overview"></a>Información general

Una página modal puede ser cualquiera de los [página](~/xamarin-forms/user-interface/controls/pages.md) tipos compatibles con Xamarin.Forms. Para mostrar una página modal la aplicación insertarlo en la pila modal, donde se convertirá en la página activa, como se muestra en el diagrama siguiente:

![](modal-images/pushing.png "Insertar una página en la pila Modal")

Para volver a la página anterior la aplicación mostrará la página actual de la pila modal y la nueva página de nivel superior se convierte en la página activa, como se muestra en el diagrama siguiente:

![](modal-images/popping.png "Retirar una página de la pila Modal")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Realización de navegación

Los métodos de navegación modal se exponen mediante la propiedad [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) en cualquier tipo [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) derivado. Estos métodos proporcionan la capacidad de [Insertar páginas modales](#Pushing_Pages_to_the_Modal_Stack) en la pila de modal y [emergente modales páginas](#Popping_Pages_from_the_Modal_Stack) de la pila modal.

El [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad también expone un [ `ModalStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) propiedad desde el que se pueden obtener las páginas en la pila modal modales. Pero no existe el concepto de realizar una manipulación de pila modal o extraer la página raíz de la navegación modal. Esto se debe a que estas operaciones no se admiten de forma universal en las plataformas subyacentes.

> [!NOTE]
> No es necesaria una instancia de [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) para realizar la navegación de páginas modal.

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Insertar páginas en la pila Modal

Para navegar hasta la `ModalPage` es necesario invocar la [ `PushModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) método en el [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad de la página actual, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Esto hace que la `ModalPage` proporciona de instancia que se inserta en la pila modal, donde se convierte en la página activa, que se ha seleccionado un elemento en el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) en el `MainPage` instancia. El `ModalPage` instancia se muestra en las capturas de pantalla siguiente:

![](modal-images/modalpage.png "Ejemplo de la página modal")

Cuando [ `PushModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) se invoca, ocurre lo siguiente:

- La llamada a página `PushModalAsync` tiene su [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) invalidación que se invoca, siempre que la plataforma subyacente no Android.
- La página que se navega tiene su [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) invalidación invocada.
- La `PushAsync` tarea se complete.

Sin embargo, el orden exacto que se producen estos eventos es depende de la plataforma. Para obtener más información, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de libro de Xamarin.Forms de Petzold.

> [!NOTE]
> Llamadas a la [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) y [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) invalidaciones no se puede tratar como indicaciones garantizadas de navegación en páginas. Por ejemplo, en iOS, el `OnDisappearing` invalidación se llama en la página activa cuando la aplicación finaliza.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Extraer páginas de la pila Modal

La página activa puede se extrae de la pila modal presionando el *volver* situado en el dispositivo, independientemente de si se trata de un botón físico en el dispositivo o un botón en la pantalla.

Para volver mediante programación a la página original, la instancia `ModalPage` debe invocar el método [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/), como se muestra en el ejemplo de código siguiente:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Esto hace que el `ModalPage` instancia va a quitar de la pila modal, con la nueva página de nivel superior se convierta en la página activa. Cuando [ `PopModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/) se invoca, ocurre lo siguiente:

- La llamada a página `PopModalAsync` tiene su [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) invalidación invocada.
- La página que se devuelve al tiene su [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) invalidación que se invoca, siempre que la plataforma subyacente no Android.
- El `PopModalAsync` devuelve la tarea.

Sin embargo, el orden exacto que se producen estos eventos es depende de la plataforma. Para obtener más información, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de libro de Xamarin.Forms de Petzold.

### <a name="disabling-the-back-button"></a>Deshabilitar el botón Atrás

En Android y Windows Phone, el usuario siempre puede volver a la página anterior presionando el estándar *volver* botón en el dispositivo. Si la página modal requiere que el usuario completar una tarea independiente antes de abandonar la página, la aplicación debe deshabilitar la *volver* botón. Esto puede realizarse al invalidar la [ `Page.OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed/) método en la página modal. Para obtener más información, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de libro de Xamarin.Forms de Petzold.

### <a name="animating-page-transitions"></a>Animación de transiciones de página

El [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad de cada página también proporciona inserción invalidado y métodos de confirmación que incluyen un `boolean` parámetro que controla si se muestra una animación de página durante la navegación, como se muestra en el código siguiente ejemplo:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
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
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Este código crea un `MainPage` instancia pasando la fecha y hora actuales en formato ISO8601.

El `MainPage` instancia recibe los datos a través de un parámetro de constructor, tal como se muestra en el ejemplo de código siguiente:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Los datos se muestran a continuación, en la página estableciendo la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedad.

### <a name="passing-data-through-a-bindingcontext"></a>Pasar datos a través de un objeto BindingContext

Un enfoque alternativo para pasar datos a otra página durante la navegación está definiendo la nueva página [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) a los datos, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Este código establece la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) de la `DetailPage` de instancia para la `Contact` una instancia y, a continuación, se desplaza a la `DetailPage`.

El `DetailPage` , a continuación, usa el enlace de datos para mostrar el `Contact` instancia datos, como se muestra en el siguiente ejemplo de código XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En el ejemplo de código siguiente se muestra cómo se puede lograr el enlace de datos en C#:

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
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
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

Los datos se muestran a continuación, en la página mediante una serie de [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controles.

Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/index.md) (Aspectos básicos del enlace de datos).

## <a name="summary"></a>Resumen

Este artículo muestra cómo navegar a páginas modales. Una página modal anima a los usuarios a completar una tarea autocontenida que no se puede abandonar mientras no se complete o se cancele la tarea.


## <a name="related-links"></a>Vínculos relacionados

- [Navegación de páginas](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
