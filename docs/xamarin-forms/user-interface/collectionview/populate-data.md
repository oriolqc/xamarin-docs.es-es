---
title: Rellenar Xamarin.Forms CollectionView con datos
description: Una colección mediante CollectionView se rellena con datos estableciendo su propiedad ItemsSource a cualquier colección que implementa IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 70b241944376782ec4c9446878ee2a19dcee2bbd
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58330034"
---
# <a name="populate-xamarinforms-collectionview-with-data"></a>Rellenar Xamarin.Forms CollectionView con datos

![Vista previa](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> El `CollectionView` es actualmente una versión preliminar y carece de algunas de sus funciones previstas. Además, la API puede cambiar cuando se completa la implementación.

`CollectionView` define las siguientes propiedades que definen los datos que se mostrarán y su apariencia:

- `ItemsSource`, del tipo `IEnumerable`, especifica la colección de elementos que se mostrarán, y tiene un valor predeterminado de `null`.
- `ItemTemplate`, del tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), especifica la plantilla para aplicar a cada elemento de la colección de elementos que se mostrará.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

## <a name="populate-a-collectionview-with-data"></a>Rellenar una colección mediante CollectionView con datos

Un `CollectionView` se rellena con datos estableciendo sus `ItemsSource` propiedad a cualquier colección que implementa `IEnumerable`. Se pueden agregar elementos en XAML, inicialice el `ItemsSource` propiedad desde una matriz de cadenas:

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> Tenga en cuenta que el elemento `x:Array` requiere un atributo `Type` que indica el tipo de los elementos de la matriz.

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!IMPORTANT]
> Si el `CollectionView` es necesario actualizar como elementos se agregan, quitados o cambiados en la colección subyacente, la colección subyacente debe ser un `IEnumerable` que envía la propiedad de colección de notificaciones de cambios, como `ObservableCollection`.

De forma predeterminada, `CollectionView` muestra elementos en una lista vertical, como se muestra en las capturas de pantalla siguiente:

[![Captura de pantalla de CollectionView que contiene los elementos de texto, en iOS y Android](populate-data-images/text.png "elementos de texto en una colección mediante CollectionView")](populate-data-images/text-large.png#lightbox "elementos de texto en una colección mediante CollectionView")

Para obtener información sobre cómo cambiar la `CollectionView` diseño, vea [especificar un diseño](layout.md). Para obtener información sobre cómo definir la apariencia de cada elemento en el `CollectionView`, consulte [definen la apariencia del elemento de lista](#define-list-item-appearance).

### <a name="data-binding"></a>Enlace de datos

`CollectionView` se pueden rellenar con datos mediante el uso de enlace de datos para enlazar su `ItemsSource` propiedad a un `IEnumerable` colección. En XAML, esto se logra con la `Binding` extensión de marcado:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

En este ejemplo, el `ItemsSource` datos de la propiedad se enlaza a la `Monkeys` propiedad del modelo de vista conectada.

> [!NOTE]
> Enlaces compilados se pueden habilitar para mejorar el rendimiento de enlace de datos en las aplicaciones de Xamarin.Forms. Para obtener más información, consulte [compilado enlaces](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Para obtener más información sobre el enlace de datos, vea [Enlace de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definir la apariencia del elemento

La apariencia de cada elemento de la `CollectionView` puede definirse estableciendo el `CollectionView.ItemTemplate` propiedad a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

Los elementos especificados en el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada elemento en la lista. En el ejemplo, la disposición dentro de la `DataTemplate` está administrado por un [ `Grid` ](xref:Xamarin.Forms.Grid). El `Grid` contiene un [ `Image` ](xref:Xamarin.Forms.Image) objeto y dos [ `Label` ](xref:Xamarin.Forms.Label) objetos, que todos se enlazan a las propiedades de la `Monkey` clase:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

Las capturas de pantalla siguientes muestran el resultado de las plantillas cada elemento en la lista:

[![Captura de pantalla de CollectionView donde cada elemento es con plantilla, en iOS y Android](populate-data-images/datatemplate.png "elementos con plantilla en una colección mediante CollectionView")](populate-data-images/datatemplate-large.png#lightbox "elementos con plantilla en una colección mediante CollectionView")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Enlace de datos en Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
