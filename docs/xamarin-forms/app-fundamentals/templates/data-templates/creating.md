---
title: Crear una plantilla de datos
description: Plantillas de datos pueden crearse en línea, en un ResourceDictionary, o desde un tipo personalizado o un tipo de celda Xamarin.Forms adecuado. Este artículo exploran cada técnica.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: cd4266fb8e7d68a9f93bd169ca70c6a0f516a357
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-datatemplate"></a>Crear una plantilla de datos

_Plantillas de datos pueden crearse en línea, en un ResourceDictionary, o desde un tipo personalizado o un tipo de celda Xamarin.Forms adecuado. Este artículo exploran cada técnica._

Un escenario de uso común para un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) se visualizan los datos de una colección de objetos en un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). La apariencia de los datos para cada celda de la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) pueden administrarse mediante el establecimiento del [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propiedad a una [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/). Hay varias técnicas que puede usar para lograr esto:

- [Crear una plantilla de datos en línea](#inline).
- [Crear una plantilla de datos con un tipo](#type).
- [Crear una plantilla de datos como un recurso](#resource).

Independientemente de la técnica que se va a utilizar, el resultado es que la apariencia de cada celda de la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) se define mediante una [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), tal y como se muestra en las capturas de pantalla siguiente:

![](creating-images/data-template-appearance.png "ListView con una plantilla de datos")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Crear una plantilla de datos en línea

El [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propiedad puede establecerse en un insertado [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/). Una plantilla en línea, que es uno que se coloca como un elemento secundario directo de una propiedad de control adecuado, debe usarse si no hay ninguna necesidad de volver a usar la plantilla de datos en otro lugar. Los elementos especificados en la `DataTemplate` definen la apariencia de cada celda, como se muestra en el siguiente ejemplo de código XAML:

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

El elemento secundario de una en línea [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) debe ser de, o que se derivan de, escriba [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/). Diseño dentro de la `ViewCell` administra aquí una [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/). El `Grid` contiene tres [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias ese enlace sus [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedades para las propiedades adecuadas de cada `Person` objeto de la colección.

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

En C#, en la línea [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) se crea mediante una sobrecarga del constructor que especifica un `Func` argumento.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Crear una plantilla de datos con un tipo

El [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propiedad también puede establecerse en un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) creado a partir de un tipo de celda. La ventaja de este enfoque es que la apariencia definida por el tipo de celda puede ser reutilizada por varias plantillas de datos a lo largo de la aplicación. El código XAML siguiente muestra un ejemplo de este enfoque:

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

En este caso, el [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propiedad está establecida en un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) creado a partir de un tipo personalizado que define el aspecto de la celda. El tipo personalizado debe derivar del tipo [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), tal y como se muestra en el ejemplo de código siguiente:

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

En el [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), diseño administra aquí una [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/). El `Grid` contiene tres [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instancias ese enlace sus [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedades para las propiedades adecuadas de cada `Person` objeto de la colección.

El código equivalente en C# se muestra en el ejemplo siguiente:

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

En C#, la [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) se crea mediante una sobrecarga del constructor que especifica el tipo de celda como argumento. El tipo de celda debe derivar del tipo [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), tal y como se muestra en el ejemplo de código siguiente:

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
> Tenga en cuenta que Xamarin.Forms también incluye tipos de celdas que pueden utilizarse para mostrar datos simples en [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) celdas. Para obtener más información, consulte [aspecto de la celda](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Crear una plantilla de datos como un recurso

Plantillas de datos también se pueden crear como objetos reutilizables en una [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Esto se logra dando a cada declaración de un único `x:Key` atributo, que le proporciona una clave descriptiva en el `ResourceDictionary`, tal y como se muestra en el siguiente ejemplo de código XAML:

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

El [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) se asigna a la [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propiedad utilizando el `StaticResource` extensión de marcado. Tenga en cuenta que mientras el `DataTemplate` se define en la página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), también se podría definir en el nivel de aplicación o el nivel de control.

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

El [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) se agrega a la [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) mediante la [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/) método, que especifica un `Key` cadena que se utiliza para referencia de la `DataTemplate` al recuperarlos.

## <a name="summary"></a>Resumen

En este artículo se explica cómo crear plantillas de datos, en línea, un tipo personalizado, o en un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Si no hay ninguna necesidad de volver a usar la plantilla de datos en otra parte, debe usarse una plantilla en línea. Como alternativa, se puede reutilizar una plantilla de datos definiendo como un tipo personalizado, o como un recurso de nivel de página o el nivel de aplicación de nivel de control.


## <a name="related-links"></a>Vínculos relacionados

- [Apariencia de etiqueta](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Plantillas de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [Plantilla de datos](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
