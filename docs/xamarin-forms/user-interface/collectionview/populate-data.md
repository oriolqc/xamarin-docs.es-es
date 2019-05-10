---
title: Xamarin.Forms CollectionView datos
description: Una colección mediante CollectionView se rellena con datos estableciendo su propiedad ItemsSource a cualquier colección que implementa IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 1350d5a5a0845029b7ef6a06647ad4c56f0f8135
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65048271"
---
# <a name="xamarinforms-collectionview-data"></a>Xamarin.Forms CollectionView datos

![](~/media/shared/preview.png "Esta API es actualmente una versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

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

Para obtener información sobre cómo cambiar la `CollectionView` diseño, vea [especificar un diseño](layout.md). Para obtener información sobre cómo definir la apariencia de cada elemento en el `CollectionView`, consulte [definen la apariencia del elemento](#define-item-appearance).

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

## <a name="choose-item-appearance-at-runtime"></a>Elija la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento en el `CollectionView` se puede elegir en tiempo de ejecución, según el valor del elemento, estableciendo el `CollectionView.ItemTemplate` propiedad a un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) objeto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

El `ItemTemplate` propiedad está establecida en un `MonkeyDataTemplateSelector` objeto. El ejemplo siguiente se muestra la `MonkeyDataTemplateSelector` clase:

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

La clase `MonkeyDataTemplateSelector` define las propiedades `AmericanMonkey` y `OtherMonkey` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. El `OnSelectTemplate` invalidar devuelve el `AmericanMonkey` plantilla, que muestra la ubicación y el nombre del objeto monkey en verde azulado, cuando el nombre de objeto monkey contiene "America". Cuando el nombre de objeto monkey no contiene "Estados Unidos", la `OnSelectTemplate` invalidar devuelve el `OtherMonkey` plantilla, que muestra el nombre del objeto monkey y la ubicación de plata:

[![Selección de plantilla de elemento de captura de pantalla de CollectionView en tiempo de ejecución, en iOS y Android](populate-data-images/datatemplateselector.png "selección de plantilla de elementos en tiempo de ejecución en una colección mediante CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "selección de plantilla de elementos en tiempo de ejecución en un CollectionView")

Para obtener más información acerca de los selectores de plantilla de datos, vea [crear un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Enlace de datos en Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Crear un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
