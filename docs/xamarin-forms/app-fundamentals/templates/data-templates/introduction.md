---
title: Introducción a las plantillas de datos de Xamarin.Forms
description: Las plantillas de datos de Xamarin.Forms proporcionan la capacidad para definir la presentación de datos en los controles admitidos. En este artículo se proporciona una introducción a las plantillas de datos, Examinar por qué son necesarios.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 129ce7a04b93bfb3cb1b9a1639aee61cd56d09d5
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998926"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introducción a las plantillas de datos de Xamarin.Forms

_Las plantillas de datos de Xamarin.Forms proporcionan la capacidad para definir la presentación de datos en los controles admitidos. En este artículo se proporciona una introducción a las plantillas de datos, Examinar por qué son necesarios._

Considere la posibilidad de un [ `ListView` ](xref:Xamarin.Forms.ListView) que muestra una colección de `Person` objetos. En el ejemplo de código siguiente se muestra la definición de la `Person` clase:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

El `Person` define la clase `Name`, `Age`, y `Location` propiedades, que pueden establecerse cuando un `Person` se crea el objeto. El [ `ListView` ](xref:Xamarin.Forms.ListView) se usa para mostrar la colección de `Person` objetos, como se muestra en el ejemplo de código XAML siguiente:

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

Se agregan elementos a la [ `ListView` ](xref:Xamarin.Forms.ListView) en XAML, inicialice la [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propiedad desde una matriz de `Person` instancias.

> [!NOTE]
> Tenga en cuenta que el `x:Array` elemento requiere un `Type` atributo que indica el tipo de los elementos de la matriz.

La página de C# equivalente se muestra en el siguiente ejemplo de código que inicializa el [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propiedad a un `List` de `Person` instancias:

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

El [ `ListView` ](xref:Xamarin.Forms.ListView) llamadas `ToString` al mostrar los objetos en la colección. Porque no hay ningún `Person.ToString` invalidar, `ToString` devuelve el nombre de tipo de cada objeto, como se muestra en las capturas de pantalla siguiente:

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

Esto da como resultado la [ `ListView` ](xref:Xamarin.Forms.ListView) mostrando el `Person.Name` valor de propiedad para cada objeto de la colección, como se muestra en las capturas de pantalla siguiente:

![](introduction-images/override-tostring.png "ListView con una plantilla de datos")

El `Person.ToString` invalidación podría devolver una cadena con formato que consta de los `Name`, `Age`, y `Location` propiedades. Sin embargo, este enfoque ofrece solo un control limitado sobre la apariencia de cada elemento de datos. Para obtener más flexibilidad, un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) pueden crearse que define la apariencia de los datos.

## <a name="creating-a-datatemplate"></a>Crear un DataTemplate

Un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) se usa para especificar la apariencia de los datos y normalmente se usa el enlace de datos para mostrar los datos. Su escenario de uso común es mostrar datos de una colección de objetos en un [ `ListView` ](xref:Xamarin.Forms.ListView). Por ejemplo, cuando un `ListView` está enlazado a una colección de `Person` objetos, el `ListView.ItemTemplate` propiedad se establecerá en un `DataTemplate` que define la apariencia de cada `Person` objeto en el `ListView`. El `DataTemplate` contendrá los elementos que se enlazan a los valores de propiedad de cada `Person` objeto. Para más información sobre el enlace de datos, consulte [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Aspectos básicos del enlace de datos).

Un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) puede usarse como un valor de las siguientes propiedades:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), que es heredado por [ `ListView` ](xref:Xamarin.Forms.ListView).
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), que es heredado por [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage), [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage), y [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage).

> [!NOTE]
> Tenga en cuenta que aunque la [ `TableView` ](xref:Xamarin.Forms.TableView) hace que los usos de [ `Cell` ](xref:Xamarin.Forms.Cell) objetos, no utiliza un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Esto es porque los enlaces de datos siempre se establecen directamente en `Cell` objetos.

Un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se coloca como elemento secundario directo de las propiedades enumeradas anteriormente se conoce como un *plantilla en línea*. Como alternativa, un `DataTemplate` puede definirse como un recurso de nivel de control, el nivel de página o nivel de aplicación. Elegir dónde se puede definir un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) impactos en el que se puede usar:

- Un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definido en el control de nivel solo puede aplicarse al control.
- Un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definido en la página de nivel se puede aplicar a varios controles válidos en la página.
- Un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definido en el nivel de aplicación pueden ser controles aplicados a válido en toda la aplicación.

Plantillas de datos más abajo en la jerarquía de vistas tienen prioridad sobre aquellas definidas mayor seguridad cuando comparten `x:Key` atributos. Por ejemplo, una plantilla de datos de nivel de aplicación serán reemplazada por una plantilla de datos de nivel de página y una plantilla de datos de nivel de página se reemplazará por una plantilla de datos de nivel de control o una plantilla de datos en línea.


## <a name="related-links"></a>Vínculos relacionados

- [Apariencia de etiqueta](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Plantillas de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
