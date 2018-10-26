---
title: Página de carrusel de Xamarin.Forms
description: El Xamarin.Forms CarouselPage es una página que los usuarios pueden pasar el dedo de lado a lado para navegar por las páginas de contenido, como una galería. En este artículo se muestra cómo usar un CarouselPage para navegar por una colección de páginas.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 48c009b836ac109e0d54cd2fdb036c46e17c4387
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121508"
---
# <a name="xamarinforms-carousel-page"></a>Página de carrusel de Xamarin.Forms

_El Xamarin.Forms CarouselPage es una página que los usuarios pueden pasar el dedo de lado a lado para navegar por las páginas de contenido, como una galería. En este artículo se muestra cómo usar un CarouselPage para navegar por una colección de páginas._

## <a name="overview"></a>Información general

El siguientes capturas de pantalla se muestra un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) en cada plataforma:

![](carousel-page-images/thirdpage.png "Elemento de la tercera CarouselPage")

El diseño de un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) es idéntico en cada plataforma. Las páginas se pueden navegar a través de deslizándose hacia la derecha a izquierda para navegar hacia delante a través de la colección y deslizándose hacia la izquierda a derecha para navegar hacia atrás a través de la colección. Las capturas de pantalla siguientes muestran la primera página en un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) instancia:

![](carousel-page-images/firstpage.png "Elemento CarouselPage primero")

Pasar el dedo de derecha a izquierda mueve hasta la segunda página, como se muestra en las capturas de pantalla siguiente:

![](carousel-page-images/secondpage.png "Elemento del segundo CarouselPage")

Pasar de nuevo de derecha a izquierda se mueve a la tercera página, mientras que el gesto de deslizar rápidamente de izquierda a derecha devuelve a la página anterior.

<!--
> [!NOTE]
> The [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](xref:Xamarin.Forms.CarouselView) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>Creación de un CarouselPage

Se pueden usar dos enfoques para crear un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage):

- [Rellenar](#Populating_a_CarouselPage_with_a_Page_Collection) el `CarouselPage` con una colección de secundarios [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancias.
- [Asignar](#Populating_a_CarouselPage_with_a_Template) una colección a la [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propiedad y asignar un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) a la [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propiedad para devolver [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancias de objetos de la colección.

Con ambos enfoques, la `CarouselPage` mostrará a continuación, cada página a su vez, con una interacción de deslizar rápidamente moverse a la página siguiente que se mostrará.

> [!NOTE]
> Un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) solo se pueden rellenar con [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancias, o `ContentPage` derivados.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>Rellenar un CarouselPage con una colección de páginas

El ejemplo de código XAML siguiente muestra un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) que muestra tres [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instancias:

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

Cada [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) muestra simplemente un [ `Label` ](xref:Xamarin.Forms.Label) para un color determinado y un [ `BoxView` ](xref:Xamarin.Forms.BoxView) de ese color.

> [!NOTE]
> El [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) no admite la virtualización de interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si la `CarouselPage` contiene demasiados elementos secundarios.

Si un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) se incrusta en el [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) página de un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage), [ `MasterDetailPage.IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) propiedad debe establecerse en `false` para evitar conflictos de gesto entre el `CarouselPage` y `MasterDetailPage`.

Para obtener más información sobre la [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage), consulte [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro de Petzold Xamarin.Forms.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>Rellenar un CarouselPage con una plantilla

El ejemplo de código XAML siguiente muestra un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) construido mediante la asignación de un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) a la [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) páginas para esta propiedad objetos de la colección:

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

El [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) se rellena con datos estableciendo el [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propiedad en el constructor para el archivo de código subyacente:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

En el ejemplo de código siguiente se muestra el equivalente [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) creado en C#:

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

Cada [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) muestra simplemente un [ `Label` ](xref:Xamarin.Forms.Label) para un color determinado y un [ `BoxView` ](xref:Xamarin.Forms.BoxView) de ese color.

> [!NOTE]
> El [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) no admite la virtualización de interfaz de usuario. Por lo tanto, el rendimiento puede verse afectado si la `CarouselPage` contiene demasiados elementos secundarios.

Si un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) se incrusta en el [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) página de un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage), [ `MasterDetailPage.IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) propiedad debe establecerse en `false` para evitar conflictos de gesto entre el `CarouselPage` y `MasterDetailPage`.

Para obtener más información sobre la [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage), consulte [capítulo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro de Petzold Xamarin.Forms.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo usar un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) para navegar por una colección de páginas. El `CarouselPage` es una página que los usuarios pueden pasar el dedo de lado a lado para navegar por las páginas de contenido, muy similar a una galería.


## <a name="related-links"></a>Vínculos relacionados

- [Variedades de página](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
