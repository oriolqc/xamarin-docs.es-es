---
title: Xamarin.Forms StackLayout
description: Este artículo explica cómo usar la clase Xamarin.Forms StackLayout para presentar las colecciones de vistas a través de una dimensión.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 6e278c466c352ad19575cd3a84d6e38e14ec2587
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244602"
---
# <a name="xamarinforms-stacklayout"></a>Xamarin.Forms StackLayout

`StackLayout` Organiza las vistas en una línea unidimensional ("pila"), ya sea horizontal o verticalmente. Vistas en un `StackLayout` se puede cambiar en función del espacio en el diseño usando las opciones de diseño. Posición se determina por el orden en que se agregaron vistas en el diseño y las opciones de diseño de las vistas.

[![](stack-layout-images/layouts-sml.png "Diseños de Xamarin.Forms")](stack-layout-images/layouts.png#lightbox "diseños de Xamarin.Forms")

## <a name="purpose"></a>Propósito

`StackLayout` es menos complejo que otras vistas. Interfaces lineales simples pueden crearse con solo agregar vistas para un `StackLayout`y las interfaces más complejas creadas anidarlos.

## <a name="usage--behavior"></a>Uso de & comportamiento

### <a name="spacing"></a>Espaciado

De forma predeterminada, `StackLayout` agregará un margen de 6px entre las vistas. Esto puede ser controlado o define para no tener ningún margen estableciendo la `Spacing` propiedad StackLayout. A continuación, muestra cómo establecer el espaciado y el efecto de las opciones de espaciado diferente:

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout Spacing="10" x:Name="layout">
      <Button Text="StackLayout" VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView Color="Yellow" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView Color="Green" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" Color="Blue" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En C#:

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { Text = "StackLayout", VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var yellowBox = new BoxView { Color = Color.Yellow, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var greenBox = new BoxView { Color = Color.Green, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var blueBox = new BoxView { Color = Color.Blue, VerticalOptions = LayoutOptions.FillAndExpand,
      HorizontalOptions = LayoutOptions.FillAndExpand, HeightRequest = 75 };

    layout.Children.Add(button);
    layout.Children.Add(yellowBox);
    layout.Children.Add(greenBox);
    layout.Children.Add(blueBox);
    layout.Spacing = 10;
    Content = layout;
  }
}
```

Espaciado = 0:

![](stack-layout-images/spacing-zero.png "StackLayout con espaciado = 0")

Espaciado de diez:

![](stack-layout-images/spacing-ten.png "StackLayout con espaciado = 10")

### <a name="sizing"></a>Ajuste de tamaño

El tamaño de una vista en un StackLayout depende de las solicitudes de alto y el ancho y las opciones de diseño. `StackLayout` aplicará el relleno. El siguiente `LayoutOption`s hará que vistas para ocupar el espacio disponible del diseño:

- **CenterAndExpand** &ndash; centra la vista en el diseño y se expande para ocupar espacio le asignará el diseño.
- **EndAndExpand** &ndash; coloca la vista al final del diseño (inferior o límite de más a la derecha) y se expande para ocupar espacio le asignará el diseño.
- **FillAndExpand** &ndash; coloca la vista para que no tiene ningún relleno y ocupa espacio le asignará el diseño.
- **StartAndExpand** &ndash; coloca la vista al principio del diseño y ocupa espacio le dará el elemento primario.

Para obtener más información, consulte [expansión](~/xamarin-forms/user-interface/layouts/layout-options.md#expansion).

### <a name="positioning"></a>Posición

Vistas en un StackLayout puede colocarse y tamaño utilizando `LayoutOptions`. Cada vista puede indicarse `VerticalOptions` y `HorizontalOptions`, definir cómo las vistas se posicionan en relación con el diseño. Los siguientes predefinidos `LayoutOptions` están disponibles:

- **Centro de** &ndash; centra la vista en el diseño.
- **End** &ndash; coloca la vista al final del diseño (inferior o límite de más a la derecha).
- **Rellenar** &ndash; coloca la vista para que no tiene ningún relleno.
- **Iniciar** &ndash; coloca la vista al principio del diseño.

El código siguiente muestra cómo establecer las opciones del diseño:

En XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout x:Name="layout">
      <Button VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

En C#:

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var oneBox = new BoxView { VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var twoBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var threeBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };

    layout.Children.Add(button);
    layout.Children.Add(oneBox);
    layout.Children.Add(twoBox);
    layout.Children.Add(threeBox);
    Content = layout;
  }
}
```

Para obtener más información, consulte [alineación](~/xamarin-forms/user-interface/layouts/layout-options.md#alignment).

## <a name="exploring-a-complex-layout"></a>Explorar un diseño complejo

Cada uno de los diseños tiene ventajas y desventajas de la creación de diseños determinados. A lo largo de esta serie de artículos de diseño, se ha creado una aplicación de ejemplo con el mismo diseño de página que se implementa mediante tres diseños diferentes.

Tenga en cuenta el siguiente código XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.StackLayoutPage"
BackgroundColor="Maroon"
Title="StackLayouts">
    <ContentPage.Content>
    <ScrollView>
      <StackLayout Spacing="0" Padding="0" BackgroundColor="Maroon">
        <BoxView HorizontalOptions="FillAndExpand" HeightRequest="100"
          VerticalOptions="Start" Color="Gray" />
        <Button BorderRadius="30" HeightRequest="60" WidthRequest="60"
          BackgroundColor="Red" HorizontalOptions="Center" VerticalOptions="Start" />
        <StackLayout HeightRequest="100" VerticalOptions="Start" HorizontalOptions="FillAndExpand" Spacing="20" BackgroundColor="Maroon">
          <Label Text="User Name" FontSize="28" HorizontalOptions="Center"
            VerticalOptions="Center" FontAttributes="Bold" />
          <Entry Text="Bio + Hashtags" TextColor="White"
            BackgroundColor="Maroon" HorizontalOptions="FillAndExpand" VerticalOptions="CenterAndExpand" />
        </StackLayout>
        <StackLayout Orientation="Horizontal" HeightRequest="50" BackgroundColor="White" Padding="5">
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="Start">
            <BoxView BackgroundColor="Black" WidthRequest="40" HeightRequest="40"  HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="EndAndExpand">
            <BoxView BackgroundColor="Maroon" WidthRequest="40" HeightRequest="40" HorizontalOptions="Start" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Age:" TextColor="White" HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="35" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Interests:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100"/>
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin, C#, .NET, Mono..." TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
      </StackLayout>
    </ScrollView>
    </ContentPage.Content>
</ContentPage>

```

El código anterior genera el siguiente diseño:

![](stack-layout-images/stack.png "StackLayout compleja")

Tenga en cuenta que `StackLayouts`s están anidados, porque en algunos casos diseños de anidamiento puede ser más sencillo que presentar todos los elementos en el mismo diseño. Observe también que, dado que `StackLayout` no es compatible con los elementos superpuestos, la página no haya algunas de las sutiles de características de diseño encontrado en las páginas de los diseños de otros.



## <a name="related-links"></a>Vínculos relacionados

- [LayoutOptions](~/xamarin-forms/user-interface/layouts/layout-options.md)
- [Diseño (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Ejemplo de BusinessTumble (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
