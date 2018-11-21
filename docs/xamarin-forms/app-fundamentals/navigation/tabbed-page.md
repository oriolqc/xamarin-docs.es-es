---
title: Página con pestañas de Xamarin.Forms
description: El Xamarin.Forms TabbedPage consta de una lista de las pestañas y un área más grande de detalle, con cada pestaña cargar contenido en el área de detalles. En este artículo se muestra cómo usar una TabbedPage para navegar por una colección de páginas.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 85a6bce8a1021c75064ba06f3a5daf69b7fe3e57
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171383"
---
# <a name="xamarinforms-tabbed-page"></a>Página con pestañas de Xamarin.Forms

_El Xamarin.Forms TabbedPage consta de una lista de las pestañas y un área más grande de detalle, con cada pestaña cargar contenido en el área de detalles. En este artículo se muestra cómo usar una TabbedPage para navegar por una colección de páginas._

## <a name="overview"></a>Información general

El siguientes capturas de pantalla se muestra un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) en cada plataforma:

![](tabbed-page-images/tab1.png "Ejemplo de TabbedPage")

Las capturas de pantalla siguientes se centran en el formato de la pestaña en cada plataforma:

![](tabbed-page-images/tabbedpage-components.png "TabbedPage ficha componentes")

El diseño de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)y sus pestañas, depende de la plataforma:

- En iOS, aparece la lista de pestañas en la parte inferior de la pantalla y el área de detalles está por encima. Cada pestaña tiene también una imagen de icono que debe ser un 30 x 30 PNG con transparencia para la resolución normal, 60 x 60 para alta resolución y 90 x 90 para iPhone 6 Plus resolución. Si hay más de cinco pestañas, un *más* aparecerá la ficha, que puede utilizarse para tener acceso a las fichas adicionales. Para obtener más información acerca de cómo cargar imágenes en una aplicación de Xamarin.Forms, consulte [trabajar con imágenes](~/xamarin-forms/user-interface/images.md). Para obtener más información acerca de los requisitos de icono, consulte [crear aplicaciones con fichas](~/ios/user-interface/controls/creating-tabbed-applications.md).

  > [!NOTE]
  > Tenga en cuenta que el `TabbedRenderer` para iOS tiene un reemplazable `GetIcon` método que se puede usar para cargar los iconos de pestaña de un origen especificado. Esta invalidación permite usar las imágenes SVG como iconos en un `TabbedPage`. Además, se pueden proporcionar las versiones seleccionadas y no seleccionadas de un icono.

- En Android, aparece la lista de pestañas en la parte superior de la pantalla de forma predeterminada y el área de detalles está por debajo. Sin embargo, se puede mover la lista de pestañas a la parte inferior de la pantalla con una plataforma específica. Para obtener más información, consulte [configuración TabbedPage barra de herramientas de selección de ubicación y Color](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar).

  > [!NOTE]
  > Tenga en cuenta que al utilizar AppCompat en Android, cada ficha también mostrará un icono. Además, el `TabbedPageRenderer` para AppCompat Android tiene una reemplazable `GetIconDrawable` método que se puede usar para cargar los iconos de pestaña de personalizada `Drawable`. Esta invalidación permite usar las imágenes SVG como iconos en un `TabbedPage`, y funciona con ambos superior e inferior barras de pestañas. Como alternativa, reemplazable `SetTabIcon` método puede utilizarse para cargar los iconos de pestaña de personalizada `Drawable` para barras de pestañas principales.

- En los factores de forma de Tablet PC de Windows, las fichas no están siempre visibles y los usuarios necesitan deslice el dedo hacia abajo (o el botón derecho, si tienen un mouse conectado) para ver las fichas en un `TabbedPage` (como se muestra a continuación).

![](tabbed-page-images/windows-tabs.png "TabbedPage pestañas en Windows")

## <a name="creating-a-tabbedpage"></a>Creación de una TabbedPage

Se pueden usar dos enfoques para crear un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

- [Rellenar](#Populating_a_TabbedPage_with_a_Page_Collection) el [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) con una colección de secundarios [ `Page` ](xref:Xamarin.Forms.Page) objetos, como una colección de [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancias.
- [Asignar](#Populating_a_TabbedPage_with_a_Template) una colección a la [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propiedad y asignar un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) a la [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) páginas para esta propiedad objetos de la colección.

Con ambos enfoques, la [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) mostrará cada página cuando el usuario selecciona cada pestaña.

> [!NOTE]
> Se recomienda que un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) debe rellenarse con [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) y [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)solo instancias. Esto le ayudará a garantizar una experiencia de usuario coherente en todas las plataformas.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Rellenar una TabbedPage con una colección de páginas

El ejemplo de código XAML siguiente muestra un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) construido rellenando una colección del elemento secundario [ `Page` ](xref:Xamarin.Forms.Page) objetos:

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

En el ejemplo de código siguiente se muestra el equivalente [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) creado en C#:

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

El [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) se rellena con dos secundarios [ `Page` ](xref:Xamarin.Forms.Page) objetos. El primer elemento secundario es un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancia y la segunda pestaña es un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) que contiene un `ContentPage` instancia.

> [!NOTE]
> El [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) no admite la virtualización de interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si la `TabbedPage` contiene demasiados elementos secundarios.

Capturas de pantalla siguientes se muestra el `TodayPage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancia, que se muestra en el *hoy* pestaña:

![](tabbed-page-images/today-page.png "ContentPage en una TabbedPage")

Seleccionar el *programación* ficha muestra el `SchedulePage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancia, que se encapsula en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) de instancia y se muestra en el captura de pantalla siguiente:

![](tabbed-page-images/schedule-page.png "NavigationPage en una TabbedPage")

Para obtener información sobre el diseño de un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), consulte [realizar exploración](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Aunque es aceptable para colocar un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), se recomienda no colocar un `TabbedPage` en un `NavigationPage`. Esto es porque, en iOS, un `UITabBarController` siempre actúa como un contenedor para el `UINavigationController`. Para obtener más información, consulte [combinar Interfaces de controlador de vista](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) en la biblioteca de desarrolladores de iOS.

#### <a name="navigation-inside-a-tab"></a>Navegación dentro de una pestaña

Navegación se puede realizar desde la segunda pestaña invocando el [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) método en el [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propiedad de la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancia, como se muestra en el ejemplo de código siguiente:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Esto hace que la instancia de `UpcomingAppointmentsPage` se inserte en la pila de navegación, donde se convertirá en la página activa. Esto se muestra en las capturas de pantalla siguiente:

![](tabbed-page-images/navigationpage.png "Navegación dentro de una pestaña")

Para obtener más información acerca de cómo realizar la navegación utilizando la [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) de clases, vea [navegación jerárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Rellenar una TabbedPage con una plantilla

El ejemplo de código XAML siguiente muestra un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) construido mediante la asignación de un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) a la [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) páginas para esta propiedad objetos de la colección:

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

El [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) se rellena con datos estableciendo el [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propiedad en el constructor para el archivo de código subyacente:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

En el ejemplo de código siguiente se muestra el equivalente [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) creado en C#:

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

Cada pestaña muestra una [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) que usa una serie de [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) y [ `Label` ](xref:Xamarin.Forms.Label) instancias para mostrar los datos de la pestaña. Las capturas de pantalla siguientes muestran el contenido de la *Tamarin* pestaña:

![](tabbed-page-images/tab3.png "Rellenar una TabbedPage con una plantilla")

Seleccionar otra ficha, a continuación, muestra el contenido de esa pestaña.

> [!NOTE]
> El [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) no admite la virtualización de interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si la `TabbedPage` contiene demasiados elementos secundarios.

Para obtener más información sobre la [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), consulte [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro de Petzold Xamarin.Forms.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar una TabbedPage para navegar por una colección de páginas. Xamarin.Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) consta de una lista de las pestañas y un área más grande de detalle, con cada pestaña cargar contenido en el área de detalles.


## <a name="related-links"></a>Vínculos relacionados

- [Variedades de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
