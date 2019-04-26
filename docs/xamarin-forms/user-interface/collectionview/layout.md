---
title: Especificar el diseño de Xamarin.Forms CollectionView
description: De forma predeterminada, una colección mediante CollectionView mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar las cuadrículas y listas horizontales y verticales.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 8ed365ed41ac31c66d41f1a32a7a16929cdc6770
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61367689"
---
# <a name="specify-xamarinforms-collectionview-layout"></a>Especificar el diseño de Xamarin.Forms CollectionView

![Vista previa](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> El `CollectionView` es actualmente una versión preliminar y carece de algunas de sus funciones previstas. Además, la API puede cambiar cuando se completa la implementación.

`CollectionView` define las siguientes propiedades que controlan el diseño:

- `ItemsLayout`, del tipo `IItemsLayout`, especifica el diseño que se usará.
- `ItemSizingStrategy`, del tipo `ItemSizingStrategy`, especifica la estrategia de medida del elemento que se usará.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

De forma predeterminada, un `CollectionView` mostrará sus elementos en una lista vertical. Sin embargo, cualquiera de los diseños siguientes pueden utilizarse:

- Lista vertical: una lista de columna única que aumenta de tamaño verticalmente conforme se agregan nuevos elementos.
- Lista horizontal: una lista de fila única que crece horizontalmente cuando se agregan nuevos elementos.
- Cuadrícula vertical: una cuadrícula de varias columna que aumenta de tamaño verticalmente conforme se agregan nuevos elementos.
- Cuadrícula horizontal: una cuadrícula de varias filas que crece horizontalmente cuando se agregan nuevos elementos.

Estos diseños se pueden especificar estableciendo el `ItemsLayout` propiedad a la clase que deriva la `ItemsLayout` clase. Esta clase define las siguientes propiedades:

- `Orientation`, del tipo `ItemsLayoutOrientation`, especifica la dirección en que el `CollectionView` se expande cuando se agregan elementos.
- `SnapPointsAlignment`, del tipo `SnapPointsAlignment`, especifica cómo se alinean los puntos de acoplamiento con elementos.
- `SnapPointsType`, del tipo `SnapPointsType`, especifica el comportamiento de puntos de acoplamiento al desplazarse.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos. Para obtener más información acerca de los puntos de ajuste, vea [ajustar puntos](scrolling.md#snap-points) en el [desplazar un elemento en la vista](scrolling.md) guía.

El `ItemsLayoutOrientation` enumeración define los miembros siguientes:

- `Vertical` indica que el `CollectionView` expandirá verticalmente cuando se agregan elementos.
- `Horizontal` indica que el `CollectionView` expandirá horizontalmente cuando se agregan elementos.

El `ListItemsLayout` clase hereda de la `ItemsLayout` clase y define estático `VerticalList` y `HorizontalList` miembros. Estos miembros se pueden usar para crear listas horizontales o verticales, respectivamente. Como alternativa, un `ListItemsLayout` objeto puede crearse, especificando un `ItemsLayoutOrientation` miembro de enumeración como argumento.

El `GridItemsLayout` clase hereda de la `ItemsLayout` clase y define un `Span` propiedad de tipo `int`, que representa el número de columnas o filas que se muestran en la cuadrícula. El valor predeterminado de la `Span` propiedad es 1, y su valor debe ser siempre mayor o igual que 1.

> [!NOTE]
> `CollectionView` usa los motores de diseño nativo para realizar el diseño.

## <a name="vertical-list"></a>Lista vertical

De forma predeterminada, `CollectionView` mostrará sus elementos en un diseño de la lista vertical. Por lo tanto, no es necesario establecer el `ItemsLayout` propiedad que se utilizará este diseño:

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

Sin embargo, para proporcionar información completa, una `CollectionView` puede configurarse para mostrar sus elementos en una lista vertical estableciendo su `ItemsLayout` a estático `ListItemsLayout.VerticalList` miembro:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.VerticalList}">
    ...
</CollectionView>
```

Como alternativa, esto también puede realizarse estableciendo el `ItemsLayout` propiedad a un objeto de la `ListItemsLayout` clase especificando el `Vertical` `ItemsLayoutOrientation` miembro de enumeración como argumento:

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

`CollectionView` puede mostrar sus elementos en una lista horizontal estableciendo su `ItemsLayout` propiedad estático `ListItemsLayout.HorizontalList` miembro:

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

Como alternativa, esto también puede realizarse estableciendo el `ItemsLayout` propiedad a un `ListItemsLayout` objeto, especificando el `Horizontal` `ItemsLayoutOrientation` miembro de enumeración como argumento:

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

`CollectionView` puede mostrar sus elementos en una cuadrícula vertical estableciendo su `ItemsLayout` propiedad a un `GridItemsLayout` cuyo `Orientation` propiedad está establecida en `Vertical`:

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

De forma predeterminada, una vertical `GridItemsLayout` mostrará los elementos en una sola columna. Sin embargo, en este ejemplo se establece la `GridItemsLayout.Span` propiedad en 2. Esto da como resultado una cuadrícula de dos columnas, lo que aumenta de tamaño verticalmente conforme se agregan nuevos elementos:

[![Captura de pantalla de un diseño de cuadrícula vertical CollectionView, en iOS y Android](layout-images/vertical-grid.png "diseño de cuadrícula vertical CollectionView")](layout-images/vertical-grid-large.png#lightbox "CollectionView diseño de cuadrícula vertical")

## <a name="horizontal-grid"></a>Cuadrícula horizontal

`CollectionView` puede mostrar sus elementos en una cuadrícula horizontal estableciendo su `ItemsLayout` propiedad a un `GridItemsLayout` cuyo `Orientation` propiedad está establecida en `Horizontal`:

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

De forma predeterminada, una horizontal `GridItemsLayout` mostrará los elementos en una sola fila. Sin embargo, en este ejemplo se establece la `GridItemsLayout.Span` propiedad a 4. Esto da como resultado una cuadrícula de cuatro filas, lo que aumenta de tamaño horizontalmente conforme se agregan nuevos elementos:

[![Captura de pantalla de un diseño de cuadrícula horizontales CollectionView, en iOS y Android](layout-images/horizontal-grid.png "diseño de cuadrícula horizontales CollectionView")](layout-images/horizontal-grid-large.png#lightbox "CollectionView diseño de cuadrícula horizontal")

## <a name="right-to-left-layout"></a>Diseño de derecha a izquierda

`CollectionView` Puede diseño su contenido en una dirección de flujo de derecha a izquierda estableciendo su [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft). Sin embargo, el `FlowDirection` propiedad lo ideal es que debe establecerse en un diseño de página o raíz, lo que hace que todos los elementos dentro de la página o el diseño de la raíz, para responder a la dirección de flujo:

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

El valor predeterminado [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) para un elemento con un elemento primario es [ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent). Por lo tanto, el `CollectionView` hereda el `FlowDirection` valor de propiedad de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), que a su vez hereda el `FlowDirection` valor de propiedad de la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) . Esto da como resultado el diseño de derecha a izquierda que se muestra en las capturas de pantalla siguiente:

[![Captura de pantalla de un diseño de CollectionView lista vertical de derecha a izquierda, en iOS y Android](layout-images/vertical-list-rtl.png "diseño de lista vertical de derecha a izquierda CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView vertical de derecha a izquierda diseño de lista")

Para obtener más información acerca de la dirección del flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="item-sizing"></a>Ajuste de tamaño de elemento

De forma predeterminada, cada elemento en un `CollectionView` es individualmente mide y tamaño, siempre que los elementos de interfaz de usuario en el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) no especificar tamaños fijos. Este comportamiento, que se puede cambiar, especificado por el `CollectionView.ItemSizingStrategy` valor de propiedad. Este valor de propiedad puede establecerse en uno de los `ItemSizingStrategy` miembros de enumeración:

- `MeasureAllItems` – individualmente se mide cada elemento. Este es el valor predeterminado.
- `MeasureFirstItem` : se mide solo el primer elemento, con todos los elementos subsiguientes del mismo tamaño que el primer elemento determinados.

> [!IMPORTANT]
> El `MeasureFirstItem` estrategia de ajuste de tamaño que debe usarse en situaciones donde el tamaño del elemento está pensado para ser uniforme en todos los elementos y dará como resultado un aumento del rendimiento.

En el ejemplo de código siguiente se muestra cómo establecer el `ItemSizingStrategy` propiedad:

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

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Desplazar un elemento en la vista](scrolling.md)
