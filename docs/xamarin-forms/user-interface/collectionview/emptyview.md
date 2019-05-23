---
title: Xamarin.Forms CollectionView EmptyView
description: En CollectionView, se puede especificar una vista vacía que proporciona comentarios al usuario cuando no hay datos disponibles para su presentación. La vista vacía puede ser una cadena, una vista o varias vistas.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 6bf24cb81bbd40c7e3f5b0f65ed2a2af7cbbe98b
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005321"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin.Forms CollectionView EmptyView

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades que pueden usarse para proporcionar comentarios de los usuarios cuando no hay ningún dato para mostrar:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), del tipo `object`, la cadena, el enlace o la vista que será que se muestra cuando el [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad es `null`, o cuando la colección especificado por el `ItemsSource` propiedad es `null` o está vacío. El valor predeterminado es `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), del tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), la plantilla para dar formato especificado a `EmptyView`. El valor predeterminado es `null`.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Los escenarios de uso principal para la configuración de la [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad se muestran los comentarios de los usuarios cuando una operación de filtrado en un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) produce no tiene datos y mostrar comentarios de los usuarios mientras se recuperan datos de un servicio web.

> [!NOTE]
> El [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad puede establecerse en una vista que incluya contenido interactivo si es necesario.

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Mostrar una cadena cuando los datos no están disponibles

El [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad puede establecerse en una cadena, que será que se muestra cuando el [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad es `null`, o cuando la colección especificado por el `ItemsSource` propiedad es `null` o está vacío. El XAML siguiente muestra un ejemplo de este escenario:

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

El resultado es que, dado que los datos de colección enlazan es `null`, Establece la cadena como el [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) se muestra el valor de propiedad:

[![Captura de pantalla de una lista vertical CollectionView con una vista de texto vacío, en iOS y Android](emptyview-images/null-itemssource.png "CollectionView lista vertical con la vista de texto vacío")](emptyview-images/null-itemssource-large.png#lightbox "CollectionView lista vertical con texto vacío vista")

## <a name="display-views-when-data-is-unavailable"></a>Mostrar las vistas cuando los datos no están disponibles

El [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad puede establecerse en una vista, que será que se muestra cuando el [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad es `null`, o cuando la colección especificado por el `ItemsSource` propiedad es `null` o está vacío. Puede tratarse de una vista única o una vista que contiene varias vistas secundarias. El siguiente ejemplo XAML muestra el `EmptyView` propiedad establecida en una vista que contiene varias vistas secundarias:

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

Cuando el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda se almacena en el [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propiedad. Si el resultado de la operación de filtrado ningún dato, la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) establecer como el [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) se muestra el valor de propiedad:

[![Captura de pantalla de una lista vertical CollectionView con vista personalizada de vacía, en iOS y Android](emptyview-images/filter-multiple-views.png "CollectionView lista vertical con vista vacía personalizada")](emptyview-images/filter-multiple-views-large.png#lightbox "CollectionView lista vertical con personalizado Vista vacía")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Mostrar el tipo de un plantilla personalizado cuando los datos no están disponibles

El [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad puede establecerse en un tipo personalizado, cuya plantilla es que se muestra cuando el [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource) propiedad es `null`, o cuando la colección especificada por el `ItemsSource`propiedad es `null` o está vacío. El [ `EmptyViewTemplate` ](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad puede establecerse en un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que define la apariencia de la `EmptyView`. El XAML siguiente muestra un ejemplo de este escenario:

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

El [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad está establecida en un `FilterData` objeto y el `Filter` datos de la propiedad se enlaza a la [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propiedad. Cuando el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda se almacena en el `Filter` propiedad. Si el resultado de la operación de filtrado ningún dato, la [ `Label` ](xref:Xamarin.Forms.Label) definido en el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), que se establece como el [ `EmptyViewTemplate` ](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) es el valor de propiedad muestra:

[![Captura de pantalla de una lista vertical CollectionView con una plantilla de vista vacía, en iOS y Android](emptyview-images/emptyviewtemplate.png "CollectionView lista vertical con la plantilla de vista vacía")](emptyview-images/emptyviewtemplate-large.png#lightbox "CollectionView lista vertical con plantilla de vista vacía")

> [!NOTE]
> Cuando se muestra el tipo de un plantilla personalizado cuando los datos no están disponibles, el [ `EmptyViewTemplate` ](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad puede establecerse en una vista que contiene varias vistas secundarias.

## <a name="choose-an-emptyview-at-runtime"></a>Elija un EmptyView en tiempo de ejecución

Las vistas que se mostrará como un [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) cuando los datos no están disponibles, puede definirse como [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). El `EmptyView` , a continuación, se puede establecer la propiedad a un determinado `ContentView`, en función de alguna lógica de negocios, en tiempo de ejecución. En el siguiente ejemplo XAML se muestra un ejemplo de este escenario:

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

Este XAML define dos [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos en el nivel de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), con el [ `Switch` ](xref:Xamarin.Forms.Switch) controlar que el objeto `ContentView` objeto que se establecerá como el [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) valor de propiedad. Cuando el [ `Switch` ](xref:Xamarin.Forms.Switch) se activa, el `OnEmptyViewSwitchToggled` ejecuta el controlador de eventos el `ToggleEmptyView` método:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

El `ToggleEmptyView` método establece el [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad de la `collectionView` objeto en uno de los dos [ `ContentView` ](xref:Xamarin.Forms.ContentView) objetos almacenados en el [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), según el valor de la [ `Switch.IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled) propiedad. Cuando el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda se almacena en el [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propiedad. Si el resultado de la operación de filtrado ningún dato, la `ContentView` objeto establecido como el `EmptyView` propiedad se muestra:

[![Captura de pantalla de una lista vertical CollectionView con vistas vacías se intercambiaron en iOS y Android](emptyview-images/swap.png "CollectionView lista vertical con vistas vacías se intercambiaron")](emptyview-images/swap-large.png#lightbox "CollectionView lista vertical con intercambiar las vistas vacías")

Para obtener más información acerca de los diccionarios de recursos, consulte [diccionarios de recursos de Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Elija un EmptyViewTemplate en tiempo de ejecución

La apariencia de la [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) se puede elegir en tiempo de ejecución, según su valor, estableciendo el [ `CollectionView.EmptyViewTemplate` ](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad a un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) objeto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AdvancedTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="BasicTemplate">
            ...
        </DataTemplate>

        <controls:SearchTermDataTemplateSelector x:Key="SearchSelector"
                                                 DefaultTemplate="{StaticResource AdvancedTemplate}"
                                                 OtherTemplate="{StaticResource BasicTemplate}" />
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <CollectionView ItemsSource="{Binding Monkeys}"
                        EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                        EmptyViewTemplate="{StaticResource SearchSelector}" />
    </StackLayout>
</ContentPage>
```

El código de C# equivalente es:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

El [ `EmptyView` ](xref:Xamarin.Forms.ItemsView.EmptyView) propiedad está establecida en el [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propiedad y el [ `EmptyViewTemplate` ](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad está establecida en un `SearchTermDataTemplateSelector` objeto.

Cuando el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) ejecuta el `FilterCommand`, la colección mostrada por el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) se filtra para el término de búsqueda se almacena en el [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) propiedad. Si el resultado de la operación de filtrado ningún dato, la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) elegido por el `SearchTermDataTemplateSelector` objeto está establecido como el [ `EmptyViewTemplate` ](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propiedad y se muestran.

El ejemplo siguiente se muestra la `SearchTermDataTemplateSelector` clase:

```csharp
public class SearchTermDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate OtherTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        string query = (string)item;
        return query.ToLower().Equals("xamarin") ? OtherTemplate : DefaultTemplate;
    }
}
```

La clase `SearchTermTemplateSelector` define las propiedades `DefaultTemplate` y `OtherTemplate` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. El `OnSelectTemplate` invalidar devuelve `DefaultTemplate`, que muestra un mensaje al usuario, cuando la consulta de búsqueda no es igual a "xamarin". Cuando la consulta de búsqueda es igual a "xamarin", la `OnSelectTemplate` invalidar devuelve `OtherTemplate`, que muestra un mensaje básico para el usuario:

[![Captura de pantalla de una selección de plantilla vacía de la vista de CollectionView en tiempo de ejecución, en iOS y Android](emptyview-images/datatemplateselector.png "selección de plantilla de vista vacía en tiempo de ejecución en una colección mediante CollectionView")](emptyview-images/datatemplateselector-large.png#lightbox "plantilla de vista vacía en tiempo de ejecución selección de una colección mediante CollectionView")

Para obtener más información acerca de los selectores de plantilla de datos, vea [crear un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Diccionarios de recursos de Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Crear un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
