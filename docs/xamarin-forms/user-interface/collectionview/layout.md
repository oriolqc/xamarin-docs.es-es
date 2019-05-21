---
title: Diseño de Xamarin.Forms CollectionView
description: De forma predeterminada, una colección mediante CollectionView mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar las cuadrículas y listas horizontales y verticales.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 74cfade0cd872ae107f74ca86d6bc5fffa22e1f6
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971279"
---
# <a name="xamarinforms-collectionview-layout"></a>Diseño de Xamarin.Forms CollectionView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades que controlan el diseño:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), del tipo [ `IItemsLayout` ](xref:Xamarin.Forms.IItemsLayout), especifica el diseño que se usará.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), del tipo [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy), especifica la estrategia de medida del elemento que se usará.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

De forma predeterminada, un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) mostrará sus elementos en una lista vertical. Sin embargo, cualquiera de los diseños siguientes pueden utilizarse:

- Lista vertical: una lista de columna única que aumenta de tamaño verticalmente conforme se agregan nuevos elementos.
- Lista horizontal: una lista de fila única que crece horizontalmente cuando se agregan nuevos elementos.
- Cuadrícula vertical: una cuadrícula de varias columna que aumenta de tamaño verticalmente conforme se agregan nuevos elementos.
- Cuadrícula horizontal: una cuadrícula de varias filas que crece horizontalmente cuando se agregan nuevos elementos.

Estos diseños se pueden especificar estableciendo el [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad a la clase que deriva el [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) clase. Esta clase define las siguientes propiedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), del tipo [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica la dirección en que el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) se expande cuando se agregan elementos.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), del tipo [ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment), especifica cómo se alinean los puntos de acoplamiento con elementos.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), del tipo [ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType), especifica el comportamiento de puntos de acoplamiento al desplazarse.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos. Para obtener más información acerca de los puntos de ajuste, vea [ajustar puntos](scrolling.md#snap-points) en el [Xamarin.Forms CollectionView desplazamiento](scrolling.md) guía.

El [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) enumeración define los miembros siguientes:

- `Vertical` indica que el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) expandirá verticalmente cuando se agregan elementos.
- `Horizontal` indica que el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) expandirá horizontalmente cuando se agregan elementos.

El [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) clase hereda de la [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) clase y define estático `VerticalList` y `HorizontalList` miembros. Estos miembros se pueden usar para crear listas horizontales o verticales, respectivamente. Como alternativa, un `ListItemsLayout` objeto puede crearse, especificando un [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de enumeración como argumento.

El [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) clase hereda de la [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) clase y define un `Span` propiedad de tipo `int`, que representa el número de columnas o filas para mostrar en la cuadrícula. El valor predeterminado de la `Span` propiedad es 1, y su valor debe ser siempre mayor o igual que 1.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) usa los motores de diseño nativo para realizar el diseño.

## <a name="vertical-list"></a>Lista vertical

De forma predeterminada, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) mostrará sus elementos en un diseño de la lista vertical. Por lo tanto, no es necesario establecer la [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad que se utilizará este diseño:

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
</CollectionView>
```

Sin embargo, para proporcionar información completa, una [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) puede configurarse para mostrar sus elementos en una lista vertical estableciendo su [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad estático `ListItemsLayout.VerticalList` miembro:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.VerticalList}">
    ...
</CollectionView>
```

Como alternativa, esto también puede realizarse estableciendo el [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad a un objeto de la [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) clase especificando el `Vertical` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de enumeración como argumento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.VerticalList
};
```

Esto da como resultado una lista de columna única, lo que aumenta de tamaño verticalmente conforme se agregan nuevos elementos:

[![Captura de pantalla de un diseño de lista vertical de CollectionView en iOS y Android](layout-images/vertical-list.png "diseño de la lista vertical de CollectionView")](layout-images/vertical-list-large.png#lightbox "diseño de la lista vertical de CollectionView")

## <a name="horizontal-list"></a>Lista horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede mostrar sus elementos en una lista horizontal estableciendo su [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad estático `ListItemsLayout.HorizontalList` miembro:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.HorizontalList}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Como alternativa, esto también puede realizarse estableciendo el [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad a un [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) objeto, especificando el `Horizontal` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de enumeración como argumento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Horizontal</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.HorizontalList
};
```

Esto da como resultado una lista de fila única, lo que aumenta de tamaño horizontalmente conforme se agregan nuevos elementos:

[![Captura de pantalla de un diseño de lista horizontal CollectionView, en iOS y Android](layout-images/horizontal-list.png "diseño de lista horizontal CollectionView")](layout-images/horizontal-list-large.png#lightbox "CollectionView diseño de lista horizontal")

## <a name="vertical-grid"></a>Cuadrícula vertical

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede mostrar sus elementos en una cuadrícula vertical estableciendo su [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad a un [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) cuyo [ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation) propiedad está establecida en `Vertical`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

De forma predeterminada, una vertical [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) mostrará los elementos en una sola columna. Sin embargo, en este ejemplo se establece la `GridItemsLayout.Span` propiedad en 2. Esto da como resultado una cuadrícula de dos columnas, lo que aumenta de tamaño verticalmente conforme se agregan nuevos elementos:

[![Captura de pantalla de un diseño de cuadrícula vertical CollectionView, en iOS y Android](layout-images/vertical-grid.png "diseño de cuadrícula vertical CollectionView")](layout-images/vertical-grid-large.png#lightbox "CollectionView diseño de cuadrícula vertical")

## <a name="horizontal-grid"></a>Cuadrícula horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede mostrar sus elementos en una cuadrícula horizontal estableciendo su [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad a un [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) cuyo[ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation) propiedad está establecida en `Horizontal`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

De forma predeterminada, una horizontal [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) mostrará los elementos en una sola fila. Sin embargo, en este ejemplo se establece la `GridItemsLayout.Span` propiedad a 4. Esto da como resultado una cuadrícula de cuatro filas, lo que aumenta de tamaño horizontalmente conforme se agregan nuevos elementos:

[![Captura de pantalla de un diseño de cuadrícula horizontales CollectionView, en iOS y Android](layout-images/horizontal-grid.png "diseño de cuadrícula horizontales CollectionView")](layout-images/horizontal-grid-large.png#lightbox "CollectionView diseño de cuadrícula horizontal")

## <a name="item-sizing"></a>Ajuste de tamaño de elemento

De forma predeterminada, cada elemento en un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) es individualmente mide y tamaño, siempre que los elementos de interfaz de usuario en el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) no especificar tamaños fijos. Este comportamiento, que se puede cambiar, especificado por el [ `CollectionView.ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) valor de propiedad. Este valor de propiedad puede establecerse en uno de los [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy) miembros de enumeración:

- `MeasureAllItems` – individualmente se mide cada elemento. Este es el valor predeterminado.
- `MeasureFirstItem` : se mide solo el primer elemento, con todos los elementos subsiguientes del mismo tamaño que el primer elemento determinados.

> [!IMPORTANT]
> El `MeasureFirstItem` estrategia de ajuste de tamaño dará como resultado un aumento del rendimiento cuando se utiliza en situaciones donde el tamaño del elemento debe ser uniforme en todos los elementos.

En el ejemplo de código siguiente se muestra cómo establecer el [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) propiedad:

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

> [!NOTE]
> La estrategia de ajuste de tamaño de elemento está actualmente sólo implementada en iOS.

## <a name="dynamic-resizing-of-items"></a>Cambio de tamaño dinámico de elementos

Los elementos de un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) puede cambiarse dinámicamente en tiempo de ejecución cambiando diseño relacionados con las propiedades de elementos dentro de la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Por ejemplo, el código siguiente ejemplo se cambia el [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) y [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) las propiedades de un [ `Image` ](xref:Xamarin.Forms.Image) objeto:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

El `OnImageTapped` controlador de eventos se ejecuta en respuesta a una [ `Image` ](xref:Xamarin.Forms.Image) de objetos que se pulsa y cambia las dimensiones de la imagen para que lo esté viendo más fácilmente:

[![Captura de pantalla de una colección mediante CollectionView con ajuste de tamaño de elemento dinámico en iOS y Android](layout-images/runtime-resizing.png "ajuste de tamaño de elemento dinámico CollectionView")](layout-images/runtime-resizing-large.png#lightbox "ajuste de tamaño de elemento dinámico CollectionView")

## <a name="right-to-left-layout"></a>Diseño de derecha a izquierda

[`CollectionView`](xref:Xamarin.Forms.CollectionView) Puede diseño su contenido en una dirección de flujo de derecha a izquierda estableciendo su [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft). Sin embargo, el `FlowDirection` propiedad lo ideal es que debe establecerse en un diseño de página o raíz, lo que hace que todos los elementos dentro de la página o el diseño de la raíz, para responder a la dirección de flujo:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

El valor predeterminado [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) para un elemento con un elemento primario es [ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent). Por lo tanto, el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) hereda el `FlowDirection` valor de propiedad de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), que a su vez hereda el `FlowDirection` valor de propiedad de la [ `ContentPage`](xref:Xamarin.Forms.ContentPage). Esto da como resultado el diseño de derecha a izquierda que se muestra en las capturas de pantalla siguiente:

[![Captura de pantalla de un diseño de CollectionView lista vertical de derecha a izquierda, en iOS y Android](layout-images/vertical-list-rtl.png "diseño de lista vertical de derecha a izquierda CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView vertical de derecha a izquierda diseño de lista")

Para obtener más información acerca de la dirección del flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Desplazamiento de Xamarin.Forms CollectionView](scrolling.md)
