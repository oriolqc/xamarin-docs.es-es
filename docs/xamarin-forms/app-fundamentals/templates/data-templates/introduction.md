---
title: Introducción
description: Plantillas de Xamarin.Forms de datos proporcionan la capacidad para definir la presentación de datos en controles que se admiten. Este artículo proporciona una introducción a las plantillas de datos, Examinar por qué son necesarios.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 6b71e1cb5f45a580aaf04c267028af1740cb560e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="introduction"></a>Introducción

_Plantillas de Xamarin.Forms de datos proporcionan la capacidad para definir la presentación de datos en controles que se admiten. Este artículo proporciona una introducción a las plantillas de datos, Examinar por qué son necesarios._

Considere la posibilidad de un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) que muestra una colección de `Person` objetos. En el ejemplo de código siguiente se muestra la definición de la `Person` clase:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

El `Person` clase define `Name`, `Age`, y `Location` propiedades, que se pueden establecer cuando un `Person` se crea el objeto. El [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) se usa para mostrar la colección de `Person` objetos, como se muestra en el siguiente ejemplo de código XAML:

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
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

Los elementos se agregan a la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) en XAML mediante la inicialización de la [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) propiedad de una matriz de `Person` instancias.

> [!NOTE]
> Tenga en cuenta que la `x:Array` elemento requiere un `Type` atributo que indica el tipo de los elementos de la matriz.

La página de C# equivalente se muestra en el ejemplo de código siguiente, que inicializa el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) propiedad a una `List` de `Person` instancias:

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

El [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) llamadas `ToString` al mostrar los objetos de la colección. Porque no hay ningún `Person.ToString` invalidar, `ToString` devuelve el nombre de tipo de cada objeto, como se muestra en las capturas de pantalla siguiente:

![](introduction-images/no-data-template.png "ListView sin una plantilla de datos")

El `Person` objeto puede invalidar el `ToString` método para mostrar datos significativos, como se muestra en el ejemplo de código siguiente:

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

Esto da como resultado la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) mostrar la `Person.Name` valor de propiedad para cada objeto de la colección, como se muestra en las capturas de pantalla siguiente:

![](introduction-images/override-tostring.png "ListView con una plantilla de datos")

El `Person.ToString` invalidación podría devolver una cadena con formato que consta de la `Name`, `Age`, y `Location` propiedades. Sin embargo, este enfoque ofrece solo un control limitado sobre el aspecto de cada elemento de datos. Para obtener mayor flexibilidad, un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) pueden crearse que define la apariencia de los datos.

## <a name="creating-a-datatemplate"></a>Crear una plantilla de datos

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) se utiliza para especificar la apariencia de los datos y normalmente se usa el enlace de datos para mostrar los datos. Su escenario de uso común es cuando se visualizan los datos de una colección de objetos en un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Por ejemplo, cuando un `ListView` está enlazado a una colección de `Person` objetos, el `ListView.ItemTemplate` propiedad se establecerá en un `DataTemplate` que define la apariencia de cada `Person` objeto en el `ListView`. El `DataTemplate` contendrá los elementos que se enlazan a los valores de propiedad de cada `Person` objeto. Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) puede usarse como un valor para las propiedades siguientes:

- [`ListView.HeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HeaderTemplate/)
- [`ListView.FooterTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.FooterTemplate/)
- [`ListView.GroupHeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/)
- [`ItemsView.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/), que es heredado por [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/).
- [`MultiPage.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/), que es heredado por [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), y [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/).

> [!NOTE]
> Tenga en cuenta que aunque la [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) hace que los usos de [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) objetos, no use un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/). Esto es porque siempre se establecen los enlaces de datos directamente en `Cell` objetos.

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que se coloca como un elemento secundario directo de las propiedades enumeradas anteriormente se conoce como un *plantilla en línea*. Como alternativa, un `DataTemplate` puede definirse como un recurso de nivel de control, el nivel de página o el nivel de aplicación. Elegir dónde se defina un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) impactos dónde pueden utilizarse:

- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definido en el control de nivel solo puede aplicarse al control.
- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definido en la página de nivel se puede aplicar a varios controles válidos en la página.
- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) definido en el nivel de aplicación pueden ser controles aplicados a válido a lo largo de la aplicación.

Plantillas de datos inferiores en la jerarquía de vista tienen prioridad sobre los definidos mayor seguridad cuando comparten `x:Key` atributos. Por ejemplo, una plantilla de datos en el nivel de aplicación serán reemplazada por una plantilla de datos de nivel de página y una plantilla de datos de nivel de página se reemplazará por una plantilla de datos en el nivel de control o una plantilla de datos en línea.


## <a name="related-links"></a>Vínculos relacionados

- [Apariencia de etiqueta](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Plantillas de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [Plantilla de datos](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
