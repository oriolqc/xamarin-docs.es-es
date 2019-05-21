---
title: Selección de Xamarin.Forms CollectionView
description: De forma predeterminada, la selección de CollectionView está deshabilitada. Sin embargo, se puede habilitar la selección única o múltiple.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: febd48f2ffad86ab8b00bafca8c296377f74a07b
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970695"
---
# <a name="xamarinforms-collectionview-selection"></a>Selección de Xamarin.Forms CollectionView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades que controlan la selección de elementos:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), del tipo [ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode), el modo de selección.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), del tipo `object`, el elemento seleccionado en la lista. Esta propiedad no tiene un `null` valor cuando se selecciona ningún elemento.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), del tipo `IList<object>`, los elementos seleccionados en la lista. Esta propiedad es de solo lectura y tiene un `null` valor cuando no hay elementos seleccionados.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), del tipo `ICommand`, que se ejecuta cuando cambia el elemento seleccionado.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), del tipo `object`, que es el parámetro que se pasa a la `SelectionChangedCommand`.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

De forma predeterminada, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) la selección está deshabilitada. Sin embargo, se puede cambiar este comportamiento estableciendo el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valor de propiedad en uno de los [ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode) miembros de enumeración:

- `None` : indica que no se pueden seleccionar elementos. Este es el valor predeterminado.
- `Single` : indica que se puede seleccionar un solo elemento con el elemento seleccionado se resalta.
- `Multiple` : indica que se pueden seleccionar varios elementos con los elementos seleccionados resaltados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define un [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento que se desencadena cuando el [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) los cambios de propiedad, ya sea debido a la selección de un elemento de la lista, o cuando una aplicación establece la propiedad del usuario. Además, este evento también se desencadena cuando el [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) los cambios de propiedad. El [ `SelectionChangedEventArgs` ](xref:Xamarin.Forms.SelectionChangedEventArgs) objeto que acompaña a la `SelectionChanged` eventos tiene dos propiedades, ambos de tipo `IReadOnlyList<object>`:

- `PreviousSelection` : la lista de elementos que se seleccionaron antes de cambia la selección.
- `CurrentSelection` : la lista de elementos que estén seleccionados, después del cambio de selección.

## <a name="single-selection"></a>Selección única

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Single`, un único elemento en el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) puede seleccionarse. Cuando se selecciona un elemento, el [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad se establecerá en el valor del elemento seleccionado. Cuando esta propiedad cambia, el [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) se ejecuta (con el valor de la [ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que se pasan a la `ICommand`) y el [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) desencadena el evento.

El siguiente ejemplo XAML se muestra un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) que puede responder a un solo elemento selección:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

En este ejemplo de código, el `OnCollectionViewSelectionChanged` se ejecuta el controlador de eventos cuando el [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) desencadena el evento, con el controlador de eventos recupera el elemento seleccionado previamente y el elemento seleccionado actualmente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> El [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) eventos pueden activar los cambios que se producen como resultado de cambiar el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad.

Las capturas de pantalla siguientes muestran la selección de elemento único en un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![Captura de pantalla de una lista vertical CollectionView con selección única, en iOS y Android](selection-images/single-selection.png "CollectionView lista vertical con selección única")](selection-images/single-selection-large.png#lightbox "CollectionView lista vertical con solo selección")

## <a name="multiple-selection"></a>Selección múltiple

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Multiple`, varios elementos en el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) puede seleccionarse. Cuando se seleccionan elementos, la [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad se establecerá en los elementos seleccionados. Cuando esta propiedad cambia, el [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) se ejecuta (con el valor de la [ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que se pasan a la `ICommand`) y el [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) desencadena el evento.

El siguiente ejemplo XAML se muestra un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) que puede responder a la selección de varios elementos:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

En este ejemplo de código, el `OnCollectionViewSelectionChanged` se ejecuta el controlador de eventos cuando el [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) desencadena el evento, con el controlador de eventos recupera los elementos seleccionados previamente y los elementos seleccionados actualmente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> El [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) eventos pueden activar los cambios que se producen como resultado de cambiar el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad.

Las capturas de pantalla siguientes muestran la selección de varios elementos en un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![Captura de pantalla de una lista vertical CollectionView con selección múltiple, en iOS y Android](selection-images/multiple-selection.png "CollectionView lista vertical con selección múltiple")](selection-images/multiple-selection-large.png#lightbox "CollectionView lista vertical con selección múltiple")

## <a name="single-pre-selection"></a>Selección previa única

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Single`, un único elemento en el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pueden seleccionarse previamente estableciendo el [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad al elemento. El siguiente ejemplo XAML se muestra un `CollectionView` que previamente selecciona un solo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey, Mode=TwoWay}">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey", BindingMode.TwoWay);
```

El [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) datos de la propiedad se enlaza a la `SelectedMonkey` propiedad del modelo de vista conectada, que es de tipo `Monkey`. Un `TwoWay` enlace se usa por lo que ese if el usuario cambia el elemento seleccionado, el valor de la `SelectedMonkey` se establecerá la propiedad seleccionada `Monkey` objeto. El `SelectedMonkey` propiedad está definida en el `MonkeysViewModel` clase y se establece en el cuarto elemento de la `Monkeys` colección:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

Por lo tanto, cuando el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) aparece, se preselecciona el cuarto elemento de la lista:

[![Captura de pantalla de una lista vertical CollectionView con preselección solo en iOS y Android](selection-images/single-pre-selection.png "CollectionView lista vertical con preselección único")](selection-images/single-pre-selection-large.png#lightbox "lista vertical CollectionView con la única selección previa")

## <a name="multiple-pre-selection"></a>Selección múltiple anterior

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `Multiple`, varios elementos en el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pueden seleccionarse previamente. El siguiente ejemplo XAML se muestra un `CollectionView` que permitirá a la selección previa de varios elementos:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Varios elementos en el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pueden seleccionarse previamente agregándolos a la [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad:

```csharp
collectionView.SelectedItems.Add(viewModel.Monkeys.Skip(1).FirstOrDefault());
collectionView.SelectedItems.Add(viewModel.Monkeys.Skip(3).FirstOrDefault());
collectionView.SelectedItems.Add(viewModel.Monkeys.Skip(4).FirstOrDefault());
```

> [!NOTE]
> El [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propiedad es de solo lectura y, por lo que no es posible utilizar un enlace debe para preseleccionar elementos de datos bidireccional.

Por lo tanto, cuando el [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) aparece, el segundo, cuarto, y están preseleccionados quinto elementos de la lista:

[![Captura de pantalla de una lista vertical CollectionView con varios preselección, en iOS y Android](selection-images/multiple-pre-selection.png "CollectionView lista vertical con varios preselección")](selection-images/multiple-pre-selection-large.png#lightbox "CollectionView vertical lista con varios preselección")

## <a name="change-selected-item-color"></a>Cambiar el color del elemento seleccionado

[`CollectionView`](xref:Xamarin.Forms.CollectionView) tiene un `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que puede utilizarse para iniciar un cambio visual en el elemento seleccionado en el `CollectionView`. Caso de uso común para esto `VisualState` consiste en cambiar el color de fondo del elemento seleccionado, que se muestra en el siguiente ejemplo XAML:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> El [ `Style` ](xref:Xamarin.Forms.Style) que contiene el `Selected` `VisualState` debe tener un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) valor de propiedad que es el tipo del elemento raíz de la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), que se establece como el `ItemTemplate` valor de propiedad.

En este ejemplo, el [ `Style.TargetType` ](xref:Xamarin.Forms.Style.TargetType) el valor de propiedad se establece en `Grid` porque el elemento raíz de la [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) es un [ `Grid` ](xref:Xamarin.Forms.Grid). El `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) especifica que cuando un elemento de la [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) está activada, el [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) del elemento se establecerá en `LightSkyBlue`:

[![Captura de pantalla de una lista vertical CollectionView con un color personalizado de selección única, en iOS y Android](selection-images/single-selection-color.png "CollectionView lista vertical con un color personalizado de selección única") ] (selection-images/single-selection-color-large.png#lightbox " CollectionView lista vertical con un color personalizado de selección única")

Para obtener más información acerca de los estados visuales, vea [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Deshabilitar la selección

[`CollectionView`](xref:Xamarin.Forms.CollectionView) selección está deshabilitada de forma predeterminada. Sin embargo, si un `CollectionView` tiene selección habilitada, se puede deshabilitar estableciendo la [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad `None`:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Cuando el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propiedad está establecida en `None`, los elementos de la [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) no puede seleccionarse el [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad permanecer `null`y el [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) no se desencadenará el evento.

> [!NOTE]
> Cuando se selecciona un elemento y el [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) se cambia la propiedad de `Single` a `None`, [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propiedad se establecerá en `null` y el [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) se desencadenará el evento con un valor vacío `CurrentSelection` propiedad.

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Administrador de estado Visual de Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
