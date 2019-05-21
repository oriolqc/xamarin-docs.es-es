---
title: Desplazamiento de Xamarin.Forms CollectionView
description: Cuando un Deslizamientos de usuario para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que se muestran por completo los elementos. Además, CollectionView define dos métodos ScrollTo, que se desplace mediante programación los elementos en la vista.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: b2f32f6695fffa27068fce9d8c12f4ecd9157bc2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970538"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Desplazamiento de Xamarin.Forms CollectionView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define dos [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que desplazar los elementos en la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado en la vista. Ambas sobrecargas tienen argumentos adicionales que pueden especificarse para indicar la posición exacta del elemento después de que el desplazamiento se ha completado y si se va a animar el desplazamiento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define un [ `ScrollToRequested` ](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento que se desencadena cuando uno de los [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos se invocan. El [ `ScrollToRequestedEventArgs` ](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objeto que acompaña a la `ScrollToRequested` eventos tiene muchas propiedades, como `IsAnimated`, `Index`, `Item`, y `ScrollToPosition`. Estas propiedades se establecen en los argumentos especificados en el `ScrollTo` llamadas al método.

Cuando un Deslizamientos de usuario para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que se muestran por completo los elementos. Esta característica se conoce como ajuste, porque los elementos de ajuste para colocar cuando se detiene el desplazamiento. Para obtener más información, consulte [ajustar puntos](#snap-points).

## <a name="scroll-an-item-at-an-index-into-view"></a>Desplazar un elemento en un índice en la vista

La primera [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga del método desplaza el elemento en el índice especificado en la vista. Dado un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) objeto denominado `collectionView`, en el ejemplo siguiente se muestra cómo desplazar el elemento en el índice 12 en la vista:

```csharp
collectionView.ScrollTo(12);
```

## <a name="scroll-an-item-into-view"></a>Desplazar un elemento en la vista

El segundo [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga del método desplaza el elemento especificado en la vista. Dado un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) objeto denominado `collectionView`, en el ejemplo siguiente se muestra cómo desplazar el elemento especificado en la vista:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

## <a name="control-scroll-position"></a>Posición de desplazamiento del control

Al desplazarse de un elemento en la vista, se puede especificar la posición exacta del elemento después de que el desplazamiento se ha completado con el `position` argumento de la [ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos. Este argumento acepta un [ `ScrollToPosition` ](xref:Xamarin.Forms.ScrollToPosition) miembro de enumeración.

### <a name="makevisible"></a>MakeVisible

El [ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition) miembro indica que se debe desplazar el elemento hasta que esté visible en la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de ejemplo genera el desplazamiento mínimo requerido para desplazar el elemento en la vista:

[![Captura de pantalla de una lista vertical CollectionView con un elemento desplaza en la vista, en iOS y Android](scrolling-images/scrolltoposition-makevisible.png "CollectionView lista vertical con el elemento desplazado")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "CollectionView lista vertical con puede desplazar elementos")

> [!NOTE]
> El [ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition) miembro se usa de forma predeterminada, si la `position` no se especifica un argumento al llamar a la `ScrollTo` método.

### <a name="start"></a>Iniciar

El [ `ScrollToPosition.Start` ](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento se debe desplazar al principio de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de ejemplo genera el elemento que se va a desplazar al principio de la vista:

[![Captura de pantalla de una lista vertical CollectionView con un elemento desplaza en la vista, en iOS y Android](scrolling-images/scrolltoposition-start.png "CollectionView lista vertical con el elemento desplazado")](scrolling-images/scrolltoposition-start-large.png#lightbox "CollectionView lista vertical con puede desplazar elementos")

### <a name="center"></a>Centrar

El [ `ScrollToPosition.Center` ](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento se debe desplazar al centro de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de ejemplo genera el elemento que se va a desplazar al centro de la vista:

[![Captura de pantalla de una lista vertical CollectionView con un elemento desplaza en la vista, en iOS y Android](scrolling-images/scrolltoposition-center.png "CollectionView lista vertical con el elemento desplazado")](scrolling-images/scrolltoposition-center-large.png#lightbox "CollectionView lista vertical con puede desplazar elementos")

### <a name="end"></a>Fin

El [ `ScrollToPosition.End` ](xref:Xamarin.Forms.ScrollToPosition) miembro indica que se debe desplazar el elemento al final de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de ejemplo genera el elemento que se desplaza hasta el final de la vista:

[![Captura de pantalla de una lista vertical CollectionView con un elemento desplaza en la vista, en iOS y Android](scrolling-images/scrolltoposition-end.png "CollectionView lista vertical con el elemento desplazado")](scrolling-images/scrolltoposition-end-large.png#lightbox "CollectionView lista vertical con puede desplazar elementos")

## <a name="disable-scroll-animation"></a>Deshabilitar la animación de desplazamiento

Se muestra una animación de desplazamiento al desplazarse a un elemento en la vista. Sin embargo, esta animación se puede deshabilitar estableciendo la `animate` argumento de la `ScrollTo` método `false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="snap-points"></a>Puntos de ajuste

Cuando un Deslizamientos de usuario para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que se muestran por completo los elementos. Esta característica se conoce como ajuste, porque los elementos de ajuste para colocar cuando el desplazamiento se detiene y se controla mediante las siguientes propiedades de la [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) clase:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), del tipo [ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType), especifica el comportamiento de puntos de acoplamiento al desplazarse.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), del tipo [ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment), especifica cómo se alinean los puntos de acoplamiento con elementos.

Estas propiedades están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> Cuando se produce el ajuste, se producirá en la dirección que genera la menor cantidad de movimiento.

### <a name="snap-points-type"></a>Tipo de puntos de ajuste

El [ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType) enumeración define los miembros siguientes:

- `None` indica que el desplazamiento no se ajusta a los elementos.
- `Mandatory` indica que el contenido siempre se ajusta a la más cercano complemento hacia donde el desplazamiento naturalmente detendría, a lo largo de la dirección de la inercia.
- `MandatorySingle` indica el mismo comportamiento que `Mandatory`, pero solo se desplaza a un elemento a la vez.

De forma predeterminada, el [ `SnapPointsType` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propiedad está establecida en `SnapPointsType.None`, lo que garantiza que el desplazamiento no ajusta los elementos, como se muestra en las capturas de pantalla siguiente:

[![Captura de pantalla de una lista vertical CollectionView sin puntos de ajuste, en iOS y Android](scrolling-images/snappoints-none.png "CollectionView lista vertical sin puntos de acoplamiento")](scrolling-images/snappoints-none-large.png#lightbox "CollectionView lista vertical sin ajuste puntos")

### <a name="snap-points-alignment"></a>Alineación de puntos de ajuste

El [ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment) enumeración define `Start`, `Center`, y `End` miembros.

> [!IMPORTANT]
> El valor de la [ `SnapPointsAlignment` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propiedad solo es cuando respeta el [ `SnapPointsType` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propiedad está establecida en `Mandatory`, o `MandatorySingle`.

#### <a name="start"></a>Iniciar

El `SnapPointsAlignment.Start` miembro indica que los puntos de acoplamiento se alinean con el borde inicial de elementos.

De forma predeterminada, el [ `SnapPointsAlignment` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propiedad está establecida en `SnapPointsAlignment.Start`. Sin embargo, por razones de integridad, en el siguiente ejemplo XAML se muestra cómo establecer a este miembro de enumeración:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Start">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Cuando un Deslizamientos de usuario para iniciar un desplazamiento, el elemento superior se alineará con la parte superior de la vista:

[![Captura de pantalla de una lista vertical CollectionView con puntos de ajuste de inicio, en iOS y Android](scrolling-images/snappoints-start.png "CollectionView lista vertical con puntos de ajuste inicio")](scrolling-images/snappoints-start-large.png#lightbox "CollectionView lista vertical con inicio puntos de ajuste")

#### <a name="center"></a>Centrar

El `SnapPointsAlignment.Center` miembro indica que los puntos de acoplamiento se alinean con el centro de elementos. En el siguiente ejemplo XAML se muestra cómo establecer a este miembro de enumeración:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Center">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Cuando un Deslizamientos de usuario para iniciar un desplazamiento, el elemento superior estará centrado en la parte superior de la vista:

[![Captura de pantalla de una lista vertical CollectionView con puntos de ajuste central, en iOS y Android](scrolling-images/snappoints-center.png "CollectionView lista vertical con puntos de acoplamiento center")](scrolling-images/snappoints-center-large.png#lightbox "CollectionView lista vertical con puntos centrales de complemento")

#### <a name="end"></a>Fin

El `SnapPointsAlignment.End` miembro indica que los puntos de acoplamiento se alinean con el borde final de elementos. En el siguiente ejemplo XAML se muestra cómo establecer a este miembro de enumeración:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="End">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Cuando un Deslizamientos de usuario para iniciar un desplazamiento, el elemento de la parte inferior se alineará con la parte inferior de la vista:

[![Captura de pantalla de una lista vertical CollectionView con puntos de acoplamiento final, en iOS y Android](scrolling-images/snappoints-end.png "CollectionView lista vertical con puntos de conexión de complemento")](scrolling-images/snappoints-end-large.png#lightbox "CollectionView lista vertical con el complemento de finalización puntos")

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
