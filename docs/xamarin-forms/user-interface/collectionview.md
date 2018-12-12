---
title: Xamarin.Forms CollectionView
description: El elemento CollectionView es una vista flexible y eficaz para presentar las listas de datos con las especificaciones de diseño diferente.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: 46ab8169b31624e3862cf14f477bcd6cf8c8f3a3
ms.sourcegitcommit: 408b78dd6eded4696469e316af7922a5991f2211
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2018
ms.locfileid: "53246313"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![Vista previa](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` es una vista para presentar las listas de datos mediante las especificaciones de diseño diferente. Su objetivo es proporcionar una forma más flexible y una alternativa de alto rendimiento `ListView`. Mientras el `CollectionView` y `ListView` API son similares, existen algunas diferencias importantes:

- `CollectionView` no tiene ningún concepto de celdas. En su lugar, las plantillas de datos se usan para definir la apariencia de cada elemento de datos en la lista.
- `CollectionView` reduce la superficie de API de `ListView`. Muchas propiedades y eventos de `ListView` no están presentes en `CollectionView`.
- `CollectionView` tiene un modelo de diseño flexible que permite que los datos se presentan vertical u horizontalmente, en una lista o una cuadrícula.

Un `CollectionView` consume estableciendo su `ItemsSource` propiedad a cualquier colección que implementa `IEnumerable`y su `ItemTemplate` propiedad a un `DataTemplate` que define la apariencia de cada elemento de lista. Además, su `ItemsLayout` propiedad debe establecerse en un `ItemsLayout` (clase), para definir la especificación de diseño de la lista.

> [!IMPORTANT]
> El `CollectionView` es actualmente una versión preliminar y no tiene gran parte de su funcionalidad planeada. Además, la API cambiará cuando se completa la implementación.

`CollectionView` está disponible en Xamarin.Forms 4.0-pre1. Sin embargo, es experimental actualmente y solo se puede usar agregando la siguiente línea de código para su `AppDelegate` clase en iOS, así como su `MainActivity` clase en Android, antes de llamar a `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

## <a name="populating-a-collectionview-with-data"></a>Rellenar una colección mediante CollectionView con datos

Un `CollectionView` se rellena con datos estableciendo sus `ItemSource` propiedad a cualquier colección que implementa `IEnumerable`. Se pueden agregar elementos en XAML, inicialice el `ItemsSource` propiedad desde una matriz de elementos:

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
> Tenga en cuenta que el `x:Array` elemento requiere un `Type` atributo que indica el tipo de los elementos de la matriz.

Además, un `CollectionView` se pueden rellenar con datos mediante el uso de enlace de datos para enlazar su `ItemsSource` propiedad a un `IEnumerable` colección. En XAML Esto se logra con la `Binding` extensión de marcado:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

En este ejemplo, el `ItemsSource` datos de la propiedad se enlaza a la `Monkeys` propiedad del modelo de vista conectada, que devuelve un `IList<Monkey>` colección. El siguiente ejemplo de código muestra la `Monkey` (clase), que contiene cuatro propiedades:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

## <a name="providing-a-view-for-the-empty-state"></a>Proporcionar una vista para el estado vacío

Cuando el `CollectionView` no tiene ningún dato que mostrar, se puede mostrar un mensaje adecuado al usuario. Esto se consigue estableciendo la `CollectionView.EmptyView` propiedad a un `object` que se mostrará cuando la colección especificada por el `ItemSource` propiedad está vacía:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}">
    <CollectionView.EmptyView>
        <Label Text="No items to display" />
    </CollectionView.EmptyView>
</CollectionView>
```

El `EmptyView` propiedad puede establecerse en un `string`, un enlace o una vista por lo que puede incluir contenido interactivo si es necesario.

Además, el `EmptyViewTemplate` propiedad puede establecerse en un `DataTemplate` que se usará para dar formato a la `EmptyView`.

## <a name="defining-list-item-appearance"></a>Definir la apariencia del elemento de lista

La apariencia de los datos para cada elemento de la `CollectionView` puede definirse estableciendo el `CollectionView.ItemTemplate` propiedad a un `DataTemplate`:

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

En este ejemplo, el `DataTemplate` contiene un `Grid` con un `Image`y dos `Label` instancias, que todos se enlazan a las propiedades de la `Monkey` objeto.

Para obtener más información acerca de las plantillas de datos, vea [plantillas de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="specifying-a-layout"></a>Especificación de un diseño

De forma predeterminada, un `CollectionView` mostrará sus elementos en una lista vertical. Sin embargo, cualquiera de las siguientes especificaciones de diseño se puede usar:

- Lista vertical: una lista de columna única que aumenta de tamaño verticalmente conforme se agregan nuevos elementos.
- Lista horizontal: una lista de fila única que crece horizontalmente cuando se agregan nuevos elementos.
- Cuadrícula vertical: una cuadrícula de varias columna que aumenta de tamaño verticalmente conforme se agregan nuevos elementos.
- Cuadrícula horizontal: una cuadrícula de varias filas que crece horizontalmente cuando se agregan nuevos elementos.

Estos diseños se pueden especificar estableciendo el `CollectionView.ItemsLayout` propiedad a un `ItemsLayout` (clase), con el `ListItemsLayout` clase que proporciona un diseño de la lista y el `GridItemsLayout` clase que proporciona un diseño de cuadrícula.

El `ItemsLayout` clase define la `Orientation` propiedad de tipo `ItemsLayoutOrientation`. El `ItemsLayoutOrientation` enumeración define `Vertical` y `Horizontal` valores de miembro.

El `ListItemsLayout` clase hereda de la `ItemsLayout` clase y define estático `VerticalList` y `HorizontalList` miembros. Estos miembros se pueden usar para crear listas horizontales o verticales, respectivamente. Como alternativa, un `ListItemsLayout` instancia puede crearse, especificando un `ItemsLayoutOrientation` miembro de enumeración como argumento.

El `GridItemsLayout` clase hereda de la `ItemsLayout` clase y define un `Span` propiedad de tipo `int`, que representa el número de columnas o filas que se muestran en la cuadrícula. El valor predeterminado de la `Span` propiedad es 1, y su valor debe ser siempre mayor o igual que 1.

### <a name="vertical-list"></a>Lista vertical

De forma predeterminada, un `CollectionView` mostrará sus elementos en un diseño de la lista vertical. Por lo tanto, no es necesario establecer el `ItemsLayout` propiedad que se utilizará este diseño:

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

Como alternativa, esto también puede realizarse estableciendo el `ItemsLayout` propiedad a una instancia de la `ListItemsLayout` clase especificando el `Vertical` `ItemsLayoutOrientation` miembro de enumeración como argumento:

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

Esto da como resultado una lista de columna única, lo que aumenta de tamaño verticalmente conforme se agregan nuevos elementos:

[![Diseño de la lista vertical de CollectionView](collectionview-images/vertical-list.png "diseño de la lista vertical de CollectionView")](collectionview-images/vertical-list-large.png#lightbox)

### <a name="horizontal-list"></a>Lista horizontal

Un `CollectionView` puede mostrar sus elementos en una lista horizontal estableciendo su `ItemsLayout` propiedad estático `ListItemsLayout.HorizontalList` miembro:

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

Como alternativa, esto también puede realizarse estableciendo el `ItemsLayout` propiedad a una instancia de la `ListItemsLayout` clase especificando el `Horizontal` `ItemsLayoutOrientation` miembro de enumeración como argumento:

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

Esto da como resultado una lista de fila única, lo que aumenta de tamaño horizontalmente conforme se agregan nuevos elementos:

[![Diseño de lista horizontal CollectionView](collectionview-images/horizontal-list.png "CollectionView diseño de lista horizontal")](collectionview-images/horizontal-list-large.png#lightbox)

### <a name="vertical-grid"></a>Cuadrícula vertical

Un `CollectionView` puede mostrar sus elementos en una cuadrícula vertical estableciendo su `ItemsLayout` propiedad a un `GridItemsLayout` cuya instancia `Orientation` propiedad está establecida en `Vertical`:

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

De forma predeterminada, una vertical `GridItemsLayout` mostrará los elementos en una sola columna. Sin embargo, en este ejemplo se establece la `GridItemsLayout.Span` propiedad en 2. Esto da como resultado una cuadrícula de dos columnas, lo que aumenta de tamaño verticalmente conforme se agregan nuevos elementos:

[![Diseño de cuadrícula vertical CollectionView](collectionview-images/vertical-grid.png "CollectionView diseño de cuadrícula vertical")](collectionview-images/vertical-grid-large.png#lightbox)

### <a name="horizontal-grid"></a>Cuadrícula horizontal

Un `CollectionView` puede mostrar sus elementos en una cuadrícula horizontal estableciendo su `ItemsLayout` propiedad a un `GridItemsLayout` cuya instancia `Orientation` propiedad está establecida en `Horizontal`:

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

De forma predeterminada, una horizontal `GridItemsLayout` mostrará los elementos en una sola fila. Sin embargo, en este ejemplo se establece la `GridItemsLayout.Span` propiedad a 4. Esto da como resultado una cuadrícula de cuatro filas, lo que aumenta de tamaño horizontalmente conforme se agregan nuevos elementos:

[![Diseño de cuadrícula horizontales CollectionView](collectionview-images/horizontal-grid.png "CollectionView diseño de cuadrícula horizontal")](collectionview-images/horizontal-grid-large.png#lightbox)

## <a name="scrolling"></a>El desplazamiento

`CollectionView` puede desplazar elementos solicitados en la vista con el `ScrollTo` método. Este método desplaza el elemento en el índice especificado en la vista:

```csharp
_collectionView.ScrollTo(12);
```

Como alternativa, una sobrecarga de este método puede utilizarse para desplazar el elemento especificado en la vista:

```csharp
var viewModel = BindingContext as MonkeysViewModel;
var monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
_collectionView.ScrollTo(monkey);
```

Ambas sobrecargas permiten especificar que indican que el grupo en el elemento, la posición exacta del elemento después de que el desplazamiento tiene completado (inicio), center, terminar y si se va a animar el desplazamiento de los argumentos adicionales.

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
