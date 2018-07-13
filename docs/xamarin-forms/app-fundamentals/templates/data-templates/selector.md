---
title: Creación de un Xamarin.Forms DataTemplateSelector
description: En este artículo se muestra cómo crear y consumir un DataTemplateSelector, que se puede usar para elegir un DataTemplate en tiempo de ejecución según el valor de una propiedad enlazada a datos.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a72777c7e51e96a8e123ecd85ad0aa24fc60fc6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994522"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Creación de un Xamarin.Forms DataTemplateSelector

_Puede usarse un DataTemplateSelector para elegir un DataTemplate en tiempo de ejecución según el valor de una propiedad enlazada a datos. Esto permite que varios DataTemplates para aplicarse en el mismo tipo de objeto, para personalizar la apariencia de los objetos concretos. En este artículo se muestra cómo crear y consumir un DataTemplateSelector._

Un selector de plantillas de datos habilita escenarios como un [ `ListView` ](xref:Xamarin.Forms.ListView) enlaza a una colección de objetos, donde la apariencia de cada objeto en el `ListView` se puede elegir en tiempo de ejecución mediante el selector de plantillas de datos devolviendo un determinado [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="creating-a-datatemplateselector"></a>Creación de un DataTemplateSelector

Un selector de plantillas de datos se implementa mediante la creación de una clase que hereda de [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector). El `OnSelectTemplate` , a continuación, se reemplaza el método para devolver un determinado [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), tal y como se muestra en el ejemplo de código siguiente:

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

Una instancia de la clase de selector de plantilla de datos, a continuación, se asigna a las propiedades de control de Xamarin.Forms como [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1). Para obtener una lista de propiedades válidas, vea [crear un DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitaciones

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) las instancias tienen las siguientes limitaciones:

- El `DataTemplateSelector` subclase siempre debe devolver la misma plantilla para los mismos datos si se consultan varias veces.
- El `DataTemplateSelector` subclase no debe devolver otro `DataTemplateSelector` subclase.
- El `DataTemplateSelector` subclase no debe devolver nuevas instancias de un `DataTemplate` en cada llamada. En su lugar, se debe devolver la misma instancia. Si no lo hace se creará una fuga de memoria y se deshabilitará la virtualización.
- En Android, puede ser no más de 20 plantillas de datos diferente por `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Consumir un DataTemplateSelector en XAML

En XAML, el `PersonDataTemplateSelector` se pueden crear instancias declarando como un recurso, como se muestra en el ejemplo de código siguiente:

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

Este nivel de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) define dos [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) instancias y un `PersonDataTemplateSelector` instancia. El `PersonDataTemplateSelector` instancia conjuntos su `ValidTemplate` y `InvalidTemplate` propiedades correspondientes `DataTemplate` instancias utilizando el `StaticResource` extensión de marcado. Tenga en cuenta que mientras los recursos se definen en la página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), también se puede definir en el nivel de control o aplicación.

El `PersonDataTemplateSelector` instancia consume asignándola a la [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propiedad, como se muestra en el ejemplo de código siguiente:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

En tiempo de ejecución, el [ `ListView` ](xref:Xamarin.Forms.ListView) llamadas la `PersonDataTemplateSelector.OnSelectTemplate` método para cada uno de los elementos de la colección subyacente, con la llamada pasa el objeto de datos como el `item` parámetro. El [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) devuelto por el método, a continuación, se aplica a ese objeto.

Las capturas de pantalla siguientes muestran el resultado de la [ `ListView` ](xref:Xamarin.Forms.ListView) aplicando el `PersonDataTemplateSelector` a cada objeto de la colección subyacente:

![](selector-images/data-template-selector.png "ListView con un Selector de plantillas de datos")

Cualquier `Person` objeto que tiene un `DateOfBirth` valor de propiedad mayor o igual a 1980 se muestra en verde, con los demás objetos que se muestra en rojo.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consumir un DataTemplateSelector en C&num;

En C#, la `PersonDataTemplateSelector` se pueden crear instancias y asignar a la [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) propiedad, como se muestra en el ejemplo de código siguiente:

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

El `PersonDataTemplateSelector` instancia conjuntos su `ValidTemplate` y `InvalidTemplate` propiedades correspondientes [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) instancias creadas por el `SetupDataTemplates` método. En tiempo de ejecución, el [ `ListView` ](xref:Xamarin.Forms.ListView) llamadas la `PersonDataTemplateSelector.OnSelectTemplate` método para cada uno de los elementos de la colección subyacente, con la llamada pasa el objeto de datos como el `item` parámetro. El `DataTemplate` devuelta por el método, a continuación, se aplica a ese objeto.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo crear y consumir un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector). Un `DataTemplateSelector` se puede usar para elegir un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) en tiempo de ejecución según el valor de una propiedad enlazada a datos. Esto permite que varios `DataTemplate` instancias al aplicarse en el mismo tipo de objeto, para personalizar la apariencia de los objetos concretos.


## <a name="related-links"></a>Vínculos relacionados

- [Selector de plantillas de datos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
