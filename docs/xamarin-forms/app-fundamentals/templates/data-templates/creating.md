---
title: Crear un Xamarin.Forms DataTemplate
description: Plantillas de datos se pueden crear en línea, en un objeto ResourceDictionary o desde un tipo personalizado o un tipo de celda adecuado de Xamarin.Forms. En este artículo explora cada técnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 63f9bf82bc8e637aced1afa5d5699ac1e8dc3f8c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994620"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Crear un Xamarin.Forms DataTemplate

_Plantillas de datos se pueden crear en línea, en un objeto ResourceDictionary o desde un tipo personalizado o un tipo de celda adecuado de Xamarin.Forms. En este artículo explora cada técnica._

Un escenario de uso común para un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) muestra datos de una colección de objetos en un [ `ListView` ](xref:Xamarin.Forms.ListView). La apariencia de los datos para cada celda de la [ `ListView` ](xref:Xamarin.Forms.ListView) pueden administrarse mediante el establecimiento del [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propiedad a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Hay varias técnicas que puede usarse para realizar esta acción:

- [Creación de una plantilla de datos en línea](#inline).
- [Crear una plantilla de datos con un tipo](#type).
- [Creación de una plantilla de datos como un recurso](#resource).

Independientemente de la técnica que se va a usar, el resultado es que la apariencia de cada celda de la [ `ListView` ](xref:Xamarin.Forms.ListView) se define mediante un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), tal y como se muestra en las capturas de pantalla siguiente:

![](creating-images/data-template-appearance.png "ListView con un DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Creación de una plantilla de datos en línea

El [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propiedad puede establecerse en un elemento incorporado [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Una plantilla en línea, que es uno que se coloca como elemento secundario directo de una propiedad de control adecuado, debe usarse si no hay ninguna necesidad de volver a usar la plantilla de datos en otro lugar. Los elementos especificados en el `DataTemplate` definen la apariencia de cada celda, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ListView Margin="0,20,0,0">
    <ListView.ItemsSource>
        <x:Array Type="{x:Type local:Person}">
            <local:Person Name="Steve" Age="21" Location="USA" />
            <local:Person Name="John" Age="37" Location="USA" />
            <local:Person Name="Tom" Age="42" Location="UK" />
            <local:Person Name="Lucas" Age="29" Location="Germany" />
            <local:Person Name="Tariq" Age="39" Location="UK" />
            <local:Person Name="Jane" Age="30" Location="USA" />
        </x:Array>
    </ListView.ItemsSource>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

El elemento secundario de un elemento incorporado [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) debe ser de, o derivar de, escriba [ `ViewCell` ](xref:Xamarin.Forms.ViewCell). Diseño de la `ViewCell` aquí administrado por un [ `Grid` ](xref:Xamarin.Forms.Grid). El `Grid` contiene tres [ `Label` ](xref:Xamarin.Forms.Label) instancias que bind sus [ `Text` ](xref:Xamarin.Forms.Label.Text) propiedades a las propiedades adecuadas de cada `Person` objeto de la colección.

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

En C#, en la línea [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) se crea mediante una sobrecarga del constructor que especifica un `Func` argumento.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Crear una plantilla de datos con un tipo

El [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propiedad puede establecerse en un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) creado a partir de un tipo de celda. La ventaja de este enfoque es que la apariencia definida por el tipo de celda puede reutilizarse en varias plantillas de datos en toda la aplicación. El código XAML siguiente muestra un ejemplo de este enfoque:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

En este caso, el [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propiedad está establecida en un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) creado a partir de un tipo personalizado que define el aspecto de la celda. El tipo personalizado debe derivar del tipo [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), tal y como se muestra en el ejemplo de código siguiente:

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

Dentro de la [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), diseño administra aquí un [ `Grid` ](xref:Xamarin.Forms.Grid). El `Grid` contiene tres [ `Label` ](xref:Xamarin.Forms.Label) instancias que bind sus [ `Text` ](xref:Xamarin.Forms.Label.Text) propiedades a las propiedades adecuadas de cada `Person` objeto de la colección.

El código de C# equivalente se muestra en el ejemplo siguiente:

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

En C#, la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) se crea mediante una sobrecarga del constructor que especifica el tipo de celda como argumento. El tipo de celda debe derivar del tipo [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> Tenga en cuenta que Xamarin.Forms también incluye tipos de celdas que se pueden usar para mostrar datos simples en [ `ListView` ](xref:Xamarin.Forms.ListView) celdas. Para obtener más información, consulte [aspecto de la celda](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Creación de una plantilla de datos como un recurso

También se pueden crear plantillas de datos como objetos reutilizables en una [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Esto se consigue asignando un único cada declaración `x:Key` atributo, que le proporciona una clave descriptiva en el `ResourceDictionary`, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

El [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) se asigna a la [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propiedad utilizando el `StaticResource` extensión de marcado. Tenga en cuenta que mientras el `DataTemplate` se define en la página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), también se puede definir en el nivel de control o aplicación.

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

El [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) se agrega a la [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) utilizando el [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, que especifica un `Key` cadena que se usa para referencia de la `DataTemplate` al recuperarlos.

## <a name="summary"></a>Resumen

Este artículo explica cómo crear plantillas de datos, en línea, de un tipo personalizado, o en un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Si no hay ninguna necesidad de volver a usar la plantilla de datos en otro lugar, debe usarse una plantilla en línea. Como alternativa, se puede reutilizar una plantilla de datos si se define como un tipo personalizado, o como un recurso de nivel de página o el nivel de aplicación de nivel de control.


## <a name="related-links"></a>Vínculos relacionados

- [Apariencia de etiqueta](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Plantillas de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
