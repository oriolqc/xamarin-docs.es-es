---
title: Puede enlazables diseños de Xamarin.Forms
description: Diseños enlazables habilitar clases de diseño generar su contenido mediante un enlace a una colección de elementos, con la opción para establecer la apariencia de cada elemento con un DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: b0e2d5e3c7923e5c3cf2adcc1dd104a97b78e727
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/07/2019
ms.locfileid: "55832256"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Diseños enlazables en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)

Los Diseños enlazables permiten que cualquier clase de diseño que deriva de la clase [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) genere su contenido al enlazarse a una colección de elementos, con la opción de establecer la apariencia de cada elemento con un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Los diseños enlazables son proporcionados por la clase `BindableLayout`, la cual expone las siguientes propiedades adjuntas:

- `ItemsSource` – especifica la colección de elementos `IEnumerable` que va a mostrar el diseño.
- `ItemTemplate` – especifica el [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se aplicará a cada elemento de la colección de elementos que muestra el diseño.
- `ItemTemplateSelector` – especifica el [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.

Estas propiedades se pueden conectar a las clases [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), y [`StackLayout`](xref:Xamarin.Forms.StackLayout), las cuales derivan todas de la clase [`Layout<T>`](xref:Xamarin.Forms.Layout`1).

> [!NOTE]
> La propiedad `ItemTemplate` tiene prioridad incluso cuando ambas propiedades `ItemTemplate` y `ItemTemplateSelector` estén asignadas.

La clase `Layout<T>` expone una colección [ `Children` ](xref:Xamarin.Forms.Layout`1.Children), al que se agregan los elementos secundarios de un diseño. Cuando la propiedad `BinableLayout.ItemsSource` se establece en una colección de elementos y se adjunta a una clase derivada de[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1), cada elemento de la colección se agrega a la colección `Layout<T>.Children` para ser mostrado por el diseño. Por lo tanto, la clase derivada de `Layout<T>` actualizará sus vistas secundarias cuando existan cambios en la colección subyacente. Para obtener más información sobre el ciclo de diseño de Xamarin.Forms, consulte [crear un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

> [!IMPORTANT]
> Los Diseños enlazables sólo deben usarse cuando la colección de elementos para mostrar es pequeña y el desplazamiento y la selección no sean necesarios. Aunque el desplazamiento puede proporcionarse ajustando un diseño enlazable en un [ `ScrollView`](xref:Xamarin.Forms.ScrollView), no es recomendado debido a que los Diseños enlazables carecen de virtualización de interfaz de usuario. Cuando se requiera desplazamiento, se debe usar una vista desplazable que incluya virtualización de interfaz de usuario, como [ `ListView` ](xref:Xamarin.Forms.ListView) o `CollectionView`. Ignorar esta recomendación puede provocar problemas de rendimiento.

## <a name="populating-a-bindable-layout-with-data"></a>Rellenar un diseño enlazable con datos

Un diseño enlazable se rellena con datos estableciendo la propiedad `ItemsSource` a cualquier colección que implementa `IEnumerable`y adjuntarlo a una clase derivada de [`Layout<T>`](xref:Xamarin.Forms.Layout`1):

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

El código de C# equivalente es:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Cuando la propiedad adjunta `BindableLayout.ItemsSource` se establece en un diseño, pero la propiedad adjunta `BindableLayout.ItemTemplate` no se ha establecido, todos los elementos en el `IEnumerable` colección serán mostrados por un [ `Label` ](xref:Xamarin.Forms.Label) creado por la clase `BindableLayout`.

## <a name="defining-item-appearance"></a>Definir la apariencia del elemento

Se puede definir la apariencia de cada elemento en el diseño enlazable estableciendo la propiedad adjunta `BindableLayout.ItemTemplate` a un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

El código de C# equivalente es:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

En este ejemplo, todos los elementos de la colección `TopFollowers` será mostrados por una vista `CircleImage`s definida en el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

![El diseño se puede enlazar con un DataTemplate](bindable-layouts-images/top-followers.png "enlazable diseño con una plantilla de datos")

Para obtener más información acerca de las plantillas de datos, véase [plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Elegir la apariencia del elemento en tiempo de ejecución

Se puede elegir la apariencia de cada elemento en el diseño enlazable en tiempo de ejecución, según el valor del elemento, estableciendo la propiedad adjunta `BindableLayout.ItemTemplateSelector` a un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

El código de C# equivalente es:

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

El [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) utilizado en el ejemplo de aplicación se muestra en el ejemplo siguiente:

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

La clase `TechItemTemplateSelector` define las propiedades `DefaultTemplate` y `XamarinFormsTemplate` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. El método `OnSelectTemplate`  devuelve el `XamarinFormsTemplate`, que muestra un elemento en rojo oscuro con un corazón junto a él, cuando el elemento es igual a "Xamarin.Forms". Cuando el elemento no es igual a "Xamarin.Forms", la `OnSelectTemplate` método devuelve el `DefaultTemplate`, que muestra un elemento usando el color predeterminado de un [ `Label` ](xref:Xamarin.Forms.Label):

![El diseño se puede enlazar con un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Diseño enlazable con un selector de plantillas de datos")

Para obtener más información acerca de los selectores de plantilla de datos, véase [creando un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de diseño enlazable (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)
- [Creación de un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creación de un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
