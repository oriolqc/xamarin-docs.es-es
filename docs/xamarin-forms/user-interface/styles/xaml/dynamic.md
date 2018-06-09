---
title: Estilos dinámicos de Xamarin.Forms
description: Este artículo explica cómo una aplicación de Xamarin.Forms puede responder a los cambios de estilo dinámicamente en tiempo de ejecución mediante el uso de recursos dinámicos.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 0f82e0cfde29921ea768000f17b93d04f8ad307e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245226"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Estilos dinámicos de Xamarin.Forms

_Estilos no responder a los cambios de propiedad y permanecen sin cambios para la duración de una aplicación. Por ejemplo, después de asignar un estilo a un elemento visual, si se modifica una de las instancias de establecedor, quitar, o una nueva instancia de establecedor agregado, los cambios no se aplicarán al elemento visual. Sin embargo, las aplicaciones pueden responder a los cambios de estilo dinámicamente en tiempo de ejecución mediante el uso de recursos dinámicos._

El `DynamicResource` extensión de marcado es similar a la `StaticResource` extensión de marcado en que ambos utilizan una clave de diccionario para capturar un valor de un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Sin embargo, mientras el `StaticResource` realiza una búsqueda de diccionario único, el `DynamicResource` mantiene un vínculo a la clave del diccionario. Por lo tanto, si se reemplaza la entrada del diccionario asociada a la clave, el cambio se aplica a los elementos visuales. Esto permite cambios de estilo de en tiempo de ejecución que se realizan en una aplicación.

En el ejemplo de código siguiente se muestra *dinámica* estilos en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) usarán las instancias del `DynamicResource` extensión de marcado para hacer referencia a un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) denominado `searchBarStyle`, que no está definido en el código XAML. Sin embargo, dado que la [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedades de la `SearchBar` instancias se establecen mediante una `DynamicResource`, la clave de diccionario que falta no se produce una excepción.

En su lugar, en el archivo de código subyacente, el constructor crea un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) entrada con la clave `searchBarStyle`, tal y como se muestra en el ejemplo de código siguiente:

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

Cuando el `OnButtonClicked` se ejecuta el controlador de eventos, `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](dynamic-images/dynamic-style-blue.png "Ejemplo de estilo dinámico de azul")](dynamic-images/dynamic-style-blue-large.png#lightbox "azul, ejemplo de estilo dinámico")
[![](dynamic-images/dynamic-style-green.png "verdes ejemplo de estilo dinámico") ] (dynamic-images/dynamic-style-green-large.png#lightbox "Verdes ejemplo de estilo dinámico")

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

En C#, la [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) usarán las instancias del [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/) método para hacer referencia a `searchBarStyle`. El `OnButtonClicked` código del controlador de eventos es idéntico al ejemplo XAML y cuando se ejecuta, `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`.

<a name="dynamic-style-inheritance">

## <a name="dynamic-style-inheritance"></a>Herencia de estilo dinámico

Derivar un estilo de un estilo dinámico no se puede alcanzar con la [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) propiedad. En su lugar, el [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) clase incluye el [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propiedad, que puede establecerse en una clave de diccionario cuyo valor puede cambiar dinámicamente.

En el ejemplo de código siguiente se muestra *dinámica* herencia de estilos en una página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

El [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) usarán las instancias del `StaticResource` extensión de marcado para hacer referencia a un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) denominado `tealSearchBarStyle`. Esto `Style` establece algunas propiedades adicionales y utiliza el [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propiedad para hacer referencia a `searchBarStyle`. El `DynamicResource` extensión de marcado no es necesario porque `tealSearchBarStyle` no va a cambiar, excepto para el `Style` se deriva. Por lo tanto, `tealSearchBarStyle` mantiene un vínculo a `searchBarStyle` y se modifica cuando cambia el estilo base.

En el archivo de código subyacente, el constructor crea un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) entrada con la clave `searchBarStyle`, como por el ejemplo anterior que muestran estilos dinámicos. Cuando el `OnButtonClicked` se ejecuta el controlador de eventos, `searchBarStyle` cambiará entre `blueSearchBarStyle` y `greenSearchBarStyle`. El resultado es el aspecto que se muestra en las capturas de pantalla siguiente:

[![](dynamic-images/dynamic-style-inheritance-blue.png "Ejemplo de la herencia de estilo dinámico de azul")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "azul, ejemplo de la herencia de estilo dinámico")
[![](dynamic-images/dynamic-style-inheritance-green.png "verdes estilo dinámico Ejemplo de la herencia")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "verdes de ejemplo de la herencia de estilo dinámico")

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

El `tealSearchBarStyle` se asigna directamente a la [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propiedad de la [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) instancias. Esto `Style` establece algunas propiedades adicionales y utiliza el [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propiedad para hacer referencia a `searchBarStyle`. El [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/) método no es necesario aquí porque `tealSearchBarStyle` no va a cambiar, excepto para el `Style` se deriva. Por lo tanto, `tealSearchBarStyle` mantiene un vínculo a `searchBarStyle` y se modifica cuando cambia el estilo base.

## <a name="summary"></a>Resumen

Estilos no responder a los cambios de propiedad y permanecen sin cambios para la duración de una aplicación. Sin embargo, las aplicaciones pueden responder a los cambios de estilo dinámicamente en tiempo de ejecución mediante el uso de recursos dinámicos. Además, *dinámica* pueden derivar de estilos con el [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) propiedad.



## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinámicos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Trabajar con estilos (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Establecedor](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
