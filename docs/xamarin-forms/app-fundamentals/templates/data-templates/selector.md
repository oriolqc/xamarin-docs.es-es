---
title: Crear un Xamarin.Forms DataTemplateSelector
description: Este artículo demuestra cómo crear y consumir un DataTemplateSelector, que se puede usar para elegir una plantilla de datos en tiempo de ejecución en función del valor de una propiedad enlazada a datos.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 5e1f9e4ef3aa7f9c415427e280d9bdd05dfbd13c
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240657"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Crear un Xamarin.Forms DataTemplateSelector

_Un DataTemplateSelector puede utilizarse para elegir una plantilla de datos en tiempo de ejecución en función del valor de una propiedad enlazada a datos. Esto permite que varios DataTemplates aplicarse en el mismo tipo de objeto, para personalizar la apariencia de los objetos concretos. Este artículo demuestra cómo crear y consumir un DataTemplateSelector._

Un selector de plantilla de datos habilita escenarios como un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) enlace a una colección de objetos, donde la apariencia de cada objeto de la `ListView` puede seleccionarse en tiempo de ejecución por el selector de plantilla de datos devuelve una determinado [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/).

## <a name="creating-a-datatemplateselector"></a>Crear un DataTemplateSelector

Un selector de plantillas de datos se implementa mediante la creación de una clase que hereda de [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/). El `OnSelectTemplate` , a continuación, se reemplaza el método para devolver un determinado [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), tal y como se muestra en el ejemplo de código siguiente:

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

El `OnSelectTemplate` método devuelve la plantilla adecuada en función del valor de la `DateOfBirth` propiedad. La plantilla que se devuelve es el valor de la `ValidTemplate` propiedad o el `InvalidTemplate` propiedad, que se establecen al consumir el `PersonDataTemplateSelector`.

Una instancia de la clase de selector de plantilla de datos, a continuación, se asignan a las propiedades de control de Xamarin.Forms como [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/). Para obtener una lista de propiedades válidas, vea [crear una plantilla de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitaciones

[`DataTemplateSelector`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) instancias tienen las siguientes limitaciones:

- El `DataTemplateSelector` subclase siempre debe devolver la misma plantilla para los mismos datos si se consultan varias veces.
- El `DataTemplateSelector` subclase no debe devolver otra `DataTemplateSelector` subclase.
- El `DataTemplateSelector` subclase no debe devolver nuevas instancias de un `DataTemplate` en cada llamada. En su lugar, se debe devolver la misma instancia. Si no lo hace, se creará una fuga de memoria y se deshabilitará la virtualización.
- En Android, puede ser no más de 20 plantillas de datos diferente por `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Consumir un DataTemplateSelector en XAML

En XAML, el `PersonDataTemplateSelector` se pueden crear instancias mediante la declaración como un recurso, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

Este nivel de página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define dos [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) instancias y un `PersonDataTemplateSelector` instancia. El `PersonDataTemplateSelector` instancia conjuntos su `ValidTemplate` y `InvalidTemplate` propiedades correspondientes `DataTemplate` instancias utilizando el `StaticResource` extensión de marcado. Tenga en cuenta que, si bien los recursos, se definen en la página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), también se podría definir en el nivel de aplicación o el nivel de control.

El `PersonDataTemplateSelector` instancia se consume asignándolo a la [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propiedad, como se muestra en el ejemplo de código siguiente:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

En tiempo de ejecución, el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) llamadas el `PersonDataTemplateSelector.OnSelectTemplate` método para cada uno de los elementos de la colección subyacente, con la llamada pasa el objeto de datos como el `item` parámetro. El [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) devuelto por el método, a continuación, se aplica a ese objeto.

Las capturas de pantalla siguientes muestran el resultado de la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) aplicar el `PersonDataTemplateSelector` a cada objeto de la colección subyacente:

![](selector-images/data-template-selector.png "ListView con un Selector de plantilla de datos")

Cualquier `Person` objeto que tiene un `DateOfBirth` valor de propiedad mayor que o igual que 1980 se muestra en verde, con los demás objetos que se muestra en rojo.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consumir un DataTemplateSelector en C&num;

En C#, la `PersonDataTemplateSelector` puede crear una instancia y asigna a la [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) propiedad, como se muestra en el ejemplo de código siguiente:

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

El `PersonDataTemplateSelector` instancia conjuntos su `ValidTemplate` y `InvalidTemplate` propiedades correspondientes [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) instancias creadas por el `SetupDataTemplates` método. En tiempo de ejecución, el [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) llamadas el `PersonDataTemplateSelector.OnSelectTemplate` método para cada uno de los elementos de la colección subyacente, con la llamada pasa el objeto de datos como el `item` parámetro. El `DataTemplate` devuelto por el método, a continuación, se aplica a ese objeto.

## <a name="summary"></a>Resumen

Este artículo demuestra cómo crear y consumir un [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/). A `DataTemplateSelector` puede usarse para elegir un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) en tiempo de ejecución en función del valor de una propiedad enlazada a datos. Esto permite que varios `DataTemplate` instancias que se aplicará al mismo tipo de objeto, para personalizar la apariencia de los objetos concretos.


## <a name="related-links"></a>Vínculos relacionados

- [Selector de plantilla de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)
