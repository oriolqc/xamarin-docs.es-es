---
title: Página de carrusel de Xamarin.Forms
description: El Xamarin.Forms CarouselPage es una página que los usuarios pueden pasar el dedo de lado a lado para navegar por las páginas de contenido, como una galería. En este artículo se muestra cómo usar un CarouselPage para navegar por una colección de páginas.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: b190498911867d29b63d839f56613fb1b80fe56f
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935152"
---
# <a name="xamarinforms-carousel-page"></a>Página de carrusel de Xamarin.Forms

_El Xamarin.Forms CarouselPage es una página que los usuarios pueden pasar el dedo de lado a lado para navegar por las páginas de contenido, como una galería. En este artículo se muestra cómo usar un CarouselPage para navegar por una colección de páginas._

## <a name="overview"></a>Información general

El siguientes capturas de pantalla se muestra un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) en cada plataforma:

![](carousel-page-images/thirdpage.png "CarouselPage Thid elemento")

El diseño de un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) es idéntico en cada plataforma. Las páginas se pueden navegar a través de deslizándose hacia la derecha a izquierda para navegar hacia delante a través de la colección y deslizándose hacia la izquierda a derecha para navegar hacia atrás a través de la colección. Las capturas de pantalla siguientes muestran la primera página en un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) instancia:

![](carousel-page-images/firstpage.png "Elemento CarouselPage primero")

Pasar el dedo de derecha a izquierda mueve hasta la segunda página, como se muestra en las capturas de pantalla siguiente:

![](carousel-page-images/secondpage.png "Elemento del segundo CarouselPage")

Pasar de nuevo de derecha a izquierda se mueve a la tercera página, mientras que el gesto de deslizar rápidamente de izquierda a derecha devuelve a la página anterior.

<!--
> [!NOTE]
> The [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>Creación de un CarouselPage

Se pueden usar dos enfoques para crear un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/):

- [Rellenar](#Populating_a_CarouselPage_with_a_Page_Collection) el `CarouselPage` con una colección de secundarios [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancias.
- [Asignar](#Populating_a_CarouselPage_with_a_Template) una colección a la [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) propiedad y asignar un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) a la [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) propiedad para devolver [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancias de objetos de la colección.

Con ambos enfoques, la `CarouselPage` mostrará a continuación, cada página a su vez, con una interacción de deslizar rápidamente moverse a la página siguiente que se mostrará.

> [!NOTE]
> Un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) solo se pueden rellenar con [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancias, o `ContentPage` derivados.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>Rellenar un CarouselPage con una colección de páginas

El ejemplo de código XAML siguiente muestra un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) que muestra tres [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instancias:

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <ContentPage>
        <ContentPage.Padding>
          <OnPlatform x:TypeArguments="Thickness">
              <On Platform="iOS, Android" Value="0,40,0,0" />
          </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
            <Label Text="Red" FontSize="Medium" HorizontalOptions="Center" />
            <BoxView Color="Red" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
        </StackLayout>
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
</CarouselPage>
```

El ejemplo de código siguiente muestra la interfaz de usuario equivalente en C#:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        var redContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                Children = {
                    new Label {
                        Text = "Red",
                        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                        HorizontalOptions = LayoutOptions.Center
                    },
                    new BoxView {
                        Color = Color.Red,
                        WidthRequest = 200,
                        HeightRequest = 200,
                        HorizontalOptions = LayoutOptions.Center,
                        VerticalOptions = LayoutOptions.CenterAndExpand
                    }
                }
            }
        };
        var greenContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };
        var blueContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };

        Children.Add (redContentPage);
        Children.Add (greenContentPage);
        Children.Add (blueContentPage);
    }
}
```

Cada [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) muestra simplemente un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) para un color determinado y un [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) de ese color.

> [!NOTE]
> El [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) no admite la virtualización de interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si la `CarouselPage` contiene demasiados elementos secundarios.

Si un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) se incrusta en el [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) página de un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), [ `MasterDetailPage.IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) propiedad debe establecerse en `false` para evitar conflictos de gesto entre el `CarouselPage` y `MasterDetailPage`.

Para obtener más información sobre la [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), consulte [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro de Petzold Xamarin.Forms.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>Rellenar un CarouselPage con una plantilla

El ejemplo de código XAML siguiente muestra un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) construido mediante la asignación de un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) a la [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) páginas para esta propiedad objetos de la colección:

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <CarouselPage.ItemTemplate>
        <DataTemplate>
            <ContentPage>
                <ContentPage.Padding>
                  <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS, Android" Value="0,40,0,0" />
                  </OnPlatform>
                </ContentPage.Padding>
                <StackLayout>
                    <Label Text="{Binding Name}" FontSize="Medium" HorizontalOptions="Center" />
                    <BoxView Color="{Binding Color}" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
                </StackLayout>
            </ContentPage>
        </DataTemplate>
    </CarouselPage.ItemTemplate>
</CarouselPage>
```

El [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) se rellena con datos estableciendo el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) propiedad en el constructor para el archivo de código subyacente:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

En el ejemplo de código siguiente se muestra el equivalente [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) creado en C#:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        ItemTemplate = new DataTemplate (() => {
            var nameLabel = new Label {
                FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                HorizontalOptions = LayoutOptions.Center
            };
            nameLabel.SetBinding (Label.TextProperty, "Name");

            var colorBoxView = new BoxView {
                WidthRequest = 200,
                HeightRequest = 200,
                HorizontalOptions = LayoutOptions.Center,
                VerticalOptions = LayoutOptions.CenterAndExpand
            };
            colorBoxView.SetBinding (BoxView.ColorProperty, "Color");

            return new ContentPage {
                Padding = padding,
                Content = new StackLayout {
                    Children = {
                        nameLabel,
                        colorBoxView
                    }
                }
            };
        });

        ItemsSource = ColorsDataModel.All;
    }
}
```

Cada [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) muestra simplemente un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) para un color determinado y un [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) de ese color.

> [!NOTE]
> El [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) no admite la virtualización de interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si la `CarouselPage` contiene demasiados elementos secundarios.

Si un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) se incrusta en el [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) página de un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), [ `MasterDetailPage.IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) propiedad debe establecerse en `false` para evitar conflictos de gesto entre el `CarouselPage` y `MasterDetailPage`.

Para obtener más información sobre la [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), consulte [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro de Petzold Xamarin.Forms.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) para navegar por una colección de páginas. El `CarouselPage` es una página que los usuarios pueden pasar el dedo de lado a lado para navegar por las páginas de contenido, muy similar a una galería.


## <a name="related-links"></a>Vínculos relacionados

- [Variedades de página](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)
