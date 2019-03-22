---
title: Mostrar un EmptyView cuando los datos no están disponible
description: En CollectionView, se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para su presentación. La vista vacía puede ser una cadena, una vista o varias vistas.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/19/2019
ms.openlocfilehash: a430387bba83887045e5687c99d9295d4be373e4
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58330064"
---
# <a name="display-an-emptyview-when-data-is-unavailable"></a>Mostrar un EmptyView cuando los datos no están disponible

![Vista previa](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> El `CollectionView` es actualmente una versión preliminar y carece de algunas de sus funciones previstas. Además, la API puede cambiar cuando se completa la implementación.

`CollectionView` define las siguientes propiedades que pueden usarse para proporcionar comentarios de los usuarios cuando no hay ningún dato para mostrar:

- `EmptyView`, del tipo `object`, la cadena, el enlace o la vista que será que se muestra cuando el `ItemsSource` propiedad es `null`, o cuando la colección especificado por el `ItemsSource` propiedad es `null` o está vacío. El valor predeterminado es `null`.
- `EmptyViewTemplate`, del tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), la plantilla para dar formato especificado a `EmptyView`. El valor predeterminado es `null`.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Los escenarios de uso principal para la configuración de la `EmptyView` propiedad se muestran los comentarios de los usuarios cuando una operación de filtrado en un `CollectionView` produce no tiene datos y mostrar comentarios de los usuarios mientras se recuperan los datos de un servicio web.

> [!NOTE]
> El `EmptyView` propiedad puede establecerse en una vista que incluya contenido interactivo si es necesario.

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Mostrar una cadena cuando los datos no están disponibles

El `EmptyView` propiedad puede establecerse en una cadena, que será que se muestra cuando el `ItemsSource` propiedad es `null`, o cuando la colección especificado por el `ItemsSource` propiedad es `null` o está vacío. El XAML siguiente muestra un ejemplo de este escenario:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

El resultado es que, dado que los datos de colección enlazan es `null`, Establece la cadena como el `EmptyView` se muestra el valor de propiedad:

[![Captura de pantalla de una lista vertical CollectionView con una vista de texto vacío, en iOS y Android](emptyview-images/null-itemssource.png "CollectionView lista vertical con la vista de texto vacío")](emptyview-images/null-itemssource-large.png#lightbox "CollectionView lista vertical con texto vacío vista")

## <a name="display-views-when-data-is-unavailable"></a>Mostrar las vistas cuando los datos no están disponibles

El `EmptyView` propiedad puede establecerse en una vista, que será que se muestra cuando el `ItemsSource` propiedad es `null`, o cuando la colección especificado por el `ItemsSource` propiedad es `null` o está vacío. Puede tratarse de una vista única o una vista que contiene varias vistas secundarias. El siguiente ejemplo XAML muestra el `EmptyView` propiedad establecida en una vista que contiene varias vistas secundarias:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </CollectionView.EmptyView>
    </CollectionView>
</StackLayout>
```

El código de C# equivalente es:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new StackLayout
    {
        Children =
        {
            new Label { Text = "No results matched your filter.", ... },
            new Label { Text = "Try a broader filter?", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Cuando el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el `CollectionView` se filtra para el término de búsqueda se almacena en el [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propiedad. Si el resultado de la operación de filtrado ningún dato, la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) establecer como el `EmptyView` se muestra el valor de propiedad:

[![Captura de pantalla de una lista vertical CollectionView con vista personalizada de vacía, en iOS y Android](emptyview-images/filter-multiple-views.png "CollectionView lista vertical con vista vacía personalizada")](emptyview-images/filter-multiple-views-large.png#lightbox "CollectionView lista vertical con personalizado Vista vacía")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Mostrar el tipo de un plantilla personalizado cuando los datos no están disponibles

El `EmptyView` propiedad puede establecerse en un tipo personalizado, cuya plantilla es que se muestra cuando el `ItemsSource` propiedad es `null`, o cuando la colección especificado por el `ItemsSource` propiedad es `null` o está vacío. El `EmptyViewTemplate` propiedad puede establecerse en un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que define la apariencia de la `EmptyView`. El XAML siguiente muestra un ejemplo de este escenario:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <views:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CollectionView.EmptyView>
        <CollectionView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CollectionView.EmptyViewTemplate>
    </CollectionView>
</StackLayout>
```

El código de C# equivalente es:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

El `FilterData` tipo define un `Filter` propiedad y su correspondiente [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty):

```csharp
public class FilterData : BindableObject
{
    public static readonly BindableProperty FilterProperty = BindableProperty.Create(nameof(Filter), typeof(string), typeof(FilterData), null);

    public string Filter
    {
        get { return (string)GetValue(FilterProperty); }
        set { SetValue(FilterProperty, value); }
    }
}
```

El `EmptyView` propiedad está establecida en un `FilterData` objeto y el `Filter` datos de la propiedad se enlaza a la [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propiedad. Cuando el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el `CollectionView` se filtra para el término de búsqueda se almacena en el `Filter` propiedad. Si el resultado de la operación de filtrado ningún dato, la [ `Label` ](xref:Xamarin.Forms.Label) definido en el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), que se establece como el `EmptyViewTemplate` se muestra el valor de propiedad:

[![Captura de pantalla de una lista vertical CollectionView con una plantilla de vista vacía, en iOS y Android](emptyview-images/emptyviewtemplate.png "CollectionView lista vertical con la plantilla de vista vacía")](emptyview-images/emptyviewtemplate-large.png#lightbox "CollectionView lista vertical con plantilla de vista vacía")

> [!NOTE]
> Cuando se muestra el tipo de un plantilla personalizado cuando los datos no están disponibles, el `EmptyViewTemplate` propiedad puede establecerse en una vista que contiene varias vistas secundarias.

## <a name="choose-an-emptyview-at-runtime"></a>Elija un EmptyView en tiempo de ejecución

Las vistas que se mostrará como un `EmptyView` cuando los datos no están disponibles, puede definirse como [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). El `EmptyView` , a continuación, se puede establecer la propiedad a un determinado `ContentView`, en función de alguna lógica de negocios, en tiempo de ejecución. En el siguiente ejemplo XAML se muestra un ejemplo de este escenario:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.Resources>
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No items to display."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CollectionView x:Name="collectionView"
                        ItemsSource="{Binding Monkeys}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    ...
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

Este XAML define dos [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos en el nivel de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), con el [ `Switch` ](xref:Xamarin.Forms.Switch) controlar que el objeto `ContentView` objeto que se establecerá como el `EmptyView` valor de propiedad. Cuando el [ `Switch` ](xref:Xamarin.Forms.Switch) se activa, el `OnEmptyViewSwitchToggled` ejecuta el controlador de eventos el `ToggleEmptyView` método:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

El `ToggleEmptyView` método establece el `EmptyView` propiedad de la `collectionView` objeto en uno de los dos [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos almacenados en el [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), según el valor de la [ `Switch.IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled) propiedad. Cuando el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el `CollectionView` se filtra para el término de búsqueda se almacena en el [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propiedad. Si el resultado de la operación de filtrado ningún dato, la `ContentView` objeto establecido como el `EmptyView` propiedad se muestra:

[![Captura de pantalla de una lista vertical CollectionView con vistas vacías se intercambiaron en iOS y Android](emptyview-images/swap.png "CollectionView lista vertical con vistas vacías se intercambiaron")](emptyview-images/swap-large.png#lightbox "CollectionView lista vertical con intercambiar las vistas vacías")

Para obtener más información acerca de los diccionarios de recursos, consulte [diccionarios de recursos de Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Diccionarios de recursos de Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
