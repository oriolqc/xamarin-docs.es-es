---
title: "Página con pestañas"
description: "El Xamarin.Forms TabbedPage consta de una lista de pestañas y un área más grande de detalle, con cada pestaña cargar contenido en el área de detalles. Este artículo demuestra cómo usar un TabbedPage para navegar por una colección de páginas."
ms.topic: article
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 2ec783b6963fc4ae14166ebf1e56bf8a802ba8b4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="tabbed-page"></a>Página con pestañas

_El Xamarin.Forms TabbedPage consta de una lista de pestañas y un área más grande de detalle, con cada pestaña cargar contenido en el área de detalles. Este artículo demuestra cómo usar un TabbedPage para navegar por una colección de páginas._

## <a name="overview"></a>Información general

El siguientes capturas de pantalla se muestra un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) en cada plataforma:

![](tabbed-page-images/tab1.png "Ejemplo de TabbedPage")

Las capturas de pantalla siguientes se centran en el formato de tabulación en cada plataforma:

![](tabbed-page-images/tabbedpage-components.png "Componentes de la ficha TabbedPage")

El diseño de un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)y sus pestañas, depende de la plataforma:

- En iOS, aparece la lista de pestañas en la parte inferior de la pantalla y el área de detalles está por encima. Cada ficha tiene también una imagen del icono que debe ser un 30 x 30 PNG con transparencia para la resolución normal, 60 x 60 para alta resolución y 90 x 90 para iPhone 6 Plus resolución. Si hay más de cinco pestañas, un *más* aparecerá la pestaña, que puede utilizarse para tener acceso a las fichas adicionales. Para obtener más información acerca de cómo cargar imágenes en una aplicación de Xamarin.Forms, consulte [trabajar con imágenes](~/xamarin-forms/user-interface/images.md). Para obtener más información acerca de los requisitos de icono, consulte [crear aplicaciones con fichas](~/ios/user-interface/controls/creating-tabbed-applications.md).

    > [!NOTE]
  > Tenga en cuenta que la `TabbedRenderer` para iOS tiene un reemplazable `GetIcon` método que puede utilizarse para cargar los iconos de la ficha desde un origen especificado. Esta invalidación permite usar las imágenes SVG como iconos en un `TabbedPage`. Además, se pueden proporcionar las versiones seleccionadas y no seleccionadas de un icono.

- En Android, aparece la lista de pestañas en la parte superior de la pantalla y el área de detalles está por debajo. Los nombres de las pestañas se pone en mayúscula automáticamente y el usuario puede desplazar la colección de fichas si hay demasiados como para caber en una pantalla.

    > [!NOTE]
  > Tenga en cuenta que al utilizar AppCompat en Android, cada ficha también mostrará un icono. Además, el `TabbedPageRenderer` para Android AppCompat tiene un reemplazable `SetTabIcon` método que puede utilizarse para cargar los iconos de pestaña a una personalizada `Drawable`. Esta invalidación permite usar las imágenes SVG como iconos en un `TabbedPage`.

- En Windows Phone, aparece la lista de pestañas en la parte superior de la pantalla y el área de detalles está por debajo. La pestaña nombres se convierten automáticamente en minúsculas y el usuario puede desplazarse a la colección de fichas si hay demasiados como para caber en una pantalla.
- En los factores de forma de Tablet PC de Windows, las fichas no siempre están visibles y los usuarios necesitan deslice el dedo en profundidad (o menú contextual, si dispone de un mouse conectado) para ver las fichas en un `TabbedPage` (tal y como se muestra a continuación).

![](tabbed-page-images/windows-tabs.png "Pestañas de TabbedPage en Windows")

## <a name="creating-a-tabbedpage"></a>Crear un TabbedPage

Pueden usar dos métodos para crear un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/):

- [Rellenar](#Populating_a_TabbedPage_with_a_Page_Collection) el [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) con una colección de elemento secundario [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) objetos, como una colección de [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancias.
- [Asignar](#Populating_a_TabbedPage_with_a_Template) una colección a la [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) propiedad y asigne un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) a la [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) propiedad para devolver las páginas para objetos de la colección.

Con ambos enfoques, la [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) mostrará cada página cuando el usuario selecciona cada pestaña.

> [!NOTE]
> Se recomienda que un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) debe rellenarse con [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) y [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)solo instancias. Esto le ayudará a garantizar una experiencia de usuario coherente en todas las plataformas.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Rellenar un TabbedPage con una colección de página

El ejemplo de código XAML siguiente muestra un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) construyendo rellenarlo con una colección de elemento secundario [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) objetos:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" Icon="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

En el ejemplo de código siguiente se muestra el equivalente [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) creado en C#:

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.Icon = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

El [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) se rellena con dos secundarios [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) objetos. El primer elemento secundario es un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancia y la segunda pestaña es un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) que contiene un `ContentPage` instancia.

> [!NOTE]
> El [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) no es compatible con la virtualización de la interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si la `TabbedPage` contiene demasiados elementos secundarios.

El siguientes capturas de pantalla se muestra la `TodayPage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancia, que se muestra en el *hoy* ficha:

![](tabbed-page-images/today-page.png "ContenidoPage en un TabbedPage")

Al seleccionar la *programación* ficha muestra la `SchedulePage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancia, que se incluye en un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) una instancia y se muestra en el captura de pantalla siguiente:

![](tabbed-page-images/schedule-page.png "NavigationPage en un TabbedPage")

Para obtener información sobre el diseño de un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), consulte [realizar exploración](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Aunque es aceptable para colocar un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) en un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), no se recomienda realizar una `TabbedPage` en un `NavigationPage`. Esto es porque, en iOS, un `UITabBarController` siempre actúa como contenedor para el `UINavigationController`. Para obtener más información, consulte [combinar Interfaces de controlador de vistas](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) en la biblioteca para desarrolladores de iOS.

#### <a name="navigation-inside-a-tab"></a>Navegación en una pestaña

Navegación puede realizarse desde la segunda pestaña invocando la [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) método en el [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propiedad de la [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancia, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Esto hace que la instancia de `UpcomingAppointmentsPage` se inserte en la pila de navegación, donde se convertirá en la página activa. Esto se muestra en las capturas de pantalla siguiente:

![](tabbed-page-images/navigationpage.png "Navegación en una pestaña")

Para obtener más información acerca de cómo realizar la exploración utilizando la [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) de clases, consulte [navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Rellenar un TabbedPage con una plantilla

El ejemplo de código XAML siguiente muestra un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) construido mediante la asignación de un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) a la [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) propiedad para devolver las páginas para objetos de la colección:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" Icon="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

El [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) se rellena con datos estableciendo el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) propiedad en el constructor para el archivo de código subyacente:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

En el ejemplo de código siguiente se muestra el equivalente [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) creado en C#:

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        Icon = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

Cada pestaña muestra una [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) que usa una serie de [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) y [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias para ver los datos de la pestaña. Las capturas de pantalla siguientes muestran el contenido de la *Tamarin* ficha:

![](tabbed-page-images/tab3.png "Rellenar un TabbedPage con una plantilla")

Seleccionar otra ficha, a continuación, muestra el contenido de esa pestaña.

> [!NOTE]
> El [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) no es compatible con la virtualización de la interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si la `TabbedPage` contiene demasiados elementos secundarios.

Para obtener más información sobre la [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), consulte [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) de libro de Xamarin.Forms de Petzold.

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar un TabbedPage para navegar por una colección de páginas. El Xamarin.Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) consta de una lista de pestañas y un área más grande de detalle, con cada pestaña cargar contenido en el área de detalles.


## <a name="related-links"></a>Vínculos relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)
