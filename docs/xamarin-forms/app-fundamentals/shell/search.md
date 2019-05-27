---
title: Búsqueda de Xamarin.Forms Shell
description: Las aplicaciones de Xamarin.Forms Shell pueden usar la funcionalidad de búsqueda integrada que se proporciona en un cuadro de búsqueda que se puede agregar a la parte superior de cada página.
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: e97dbb993cb108245636ae459a572e18b13d6817
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005197"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Xamarin.Forms Shell incluye la funcionalidad de búsqueda integrada que proporciona la clase `SearchHandler`. La funcionalidad de búsqueda se puede agregar a una página mediante el establecimiento de la propiedad adjunta `Shell.SearchHandler` en un objeto `SearchHandler` en subclase. El resultado es un cuadro de búsqueda que se agrega en la parte superior de la página:

[![Captura de pantalla de una clase SearchHandler de Shell en iOS y Android](search-images/searchhandler.png "SearchHandler de Shell")](search-images/searchhandler-large.png#lightbox "Shell SearchHandler")

Cuando se escribe una consulta en el cuadro de búsqueda, el área de sugerencias de búsqueda se puede rellenar con datos:

[![Captura de pantalla de unos resultados de búsqueda de una clase SearchHandler de Shell en iOS y Android](search-images/search-suggestions.png "Resultados de búsqueda de SearchHandler de Shell")](search-images/search-suggestions-large.png#lightbox "Shell SearchHandler search results")

Luego, cuando se selecciona un resultado de búsqueda, la aplicación puede responder de forma adecuada, por ejemplo, yendo a otra página.

## <a name="searchhandler-class"></a>Clase SearchHandler

La clase `SearchHandler` define las siguientes propiedades que controlan su apariencia y comportamiento:

- `ClearIcon`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), el icono que aparece para borrar el contenido del cuadro de búsqueda.
- `ClearIconHelpText`, de tipo `string`, el texto de ayuda accesible para el icono de borrar.
- `ClearIconName`, de tipo `string`, el nombre del icono de borrar para usar con los lectores de pantalla.
- `ClearPlaceholderCommand`, de tipo `ICommand`, que se ejecuta cuando se pulsa `ClearPlaceholderIcon`.
- `ClearPlaceholderCommandParameter`, de tipo `object`, que es el parámetro que se pasa a `ClearPlaceholderCommand`.
- `ClearPlaceholderEnabled`, de tipo `bool`, que determina si se puede ejecutar `ClearPlaceholderCommand`. El valor predeterminado es `true`.
- `ClearPlaceholderHelpText`, de tipo `string`, el texto de ayuda accesible para el icono de borrar marcador de posición.
- `ClearPlaceholderIcon`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), el icono de borrar marcador de posición que se muestra cuando el cuadro de búsqueda está vacío.
- `ClearPlaceholderName`, de tipo `string`, el nombre del icono de borrar marcador de posición para su uso con los lectores de pantalla.
- `Command`, de tipo `ICommand`, que se ejecuta cuando se confirma la consulta de búsqueda.
- `CommandParameter`, de tipo `object`, que es el parámetro que se pasa a `Command`.
- `DisplayMemberName`, de tipo `string`, que representa el nombre o la ruta de acceso de la propiedad que se muestra para cada elemento de datos de la colección `ItemsSource`.
- `IsSearchEnabled`, de tipo `bool`, que representa el estado habilitado del cuadro de búsqueda. El valor predeterminado es `true`.
- `ItemsSource`, de tipo `IEnumerable`, especifica la colección de elementos que se mostrarán en el área de sugerencias, y tiene un valor predeterminado de `null`.
- `ItemTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica la plantilla que se aplicará a cada elemento de la colección de elementos que se mostrará en el área de sugerencias.
- `Placeholder`, de tipo `string`, el texto que se muestra cuando el cuadro de búsqueda está vacío.
- `Query`, de tipo `string`, el texto especificado por el usuario en el cuadro de búsqueda.
- `QueryIcon`, de tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), el icono utilizado para indicar al usuario que la búsqueda está disponible.
- `QueryIconHelpText`, de tipo `string`, el texto de ayuda accesible para el icono de consulta.
- `QueryIconName`, de tipo `string`, el nombre del icono de consulta para su uso con los lectores de pantalla.
- `SearchBoxVisibility`, de tipo `SearchBoxVisibility`, la visibilidad del cuadro de búsqueda. De forma predeterminada, el cuadro de búsqueda está visible y totalmente expandido.
- `SelectedItem`, de tipo `object`, el elemento seleccionado en los resultados de búsqueda. Esta propiedad es de solo lectura y tiene un valor predeterminado de `null`.
- `ShowsResults`, de tipo `bool`, indica si se deben esperar resultados de búsqueda en el área de sugerencias, al escribir texto. El valor predeterminado es `false`.

Todas estas propiedades están respaldados por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que las propiedades pueden ser destinos de los enlaces de datos.

Además, la clase `SearchHandler` proporciona los métodos reemplazables siguientes:

- `OnClearPlaceholderClicked`, que se llama cada vez que se pulsa `ClearPlaceholderIcon`.
- `OnItemSelected`, que se llama cada vez que el usuario selecciona un resultado de búsqueda.
- `OnQueryChanged`, que se llama cuando cambia la propiedad `Query`.
- `OnQueryConfirmed`, que se llama cada vez que el usuario presiona Entrar o confirma su consulta en el cuadro de búsqueda.

Cuando un usuario escribe una consulta en el cuadro de búsqueda, la propiedad `Query` se actualiza y, en cada actualización, se ejecuta el método `OnQueryChanged`. Este método se puede usar para actualizar el área de sugerencias que aparece debajo del cuadro de búsqueda. Cuando un usuario selecciona un resultado del área de sugerencias, se ejecuta el método `OnItemSelected`.

## <a name="create-a-searchhandler"></a>Creación de una clase SearchHandler

La funcionalidad de búsqueda se puede agregar a una aplicación de Shell mediante la creación de subclases de la clase `SearchHandler` y la invalidación de los métodos `OnQueryChanged` y `OnItemSelected`:

```csharp
public class MonkeySearchHandler : SearchHandler
{
    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = MonkeyData.Monkeys
                .Where(monkey => monkey.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Note: strings will be URL encoded for navigation (e.g. "Blue Monkey" becomes "Blue%20Monkey"). Therefore, decode at the receiver.
        await (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"monkeydetails?name={((Animal)item).Name}");
    }
}
```

La invalidación `OnQueryChanged` tiene dos argumentos: `oldValue`, que contiene la consulta de búsqueda anterior, y `newValue`, que contiene la consulta de búsqueda actual. El área de sugerencias de búsqueda se puede actualizar mediante el establecimiento de la propiedad `SearchHandler.ItemsSource` en una colección `IEnumerable` que contiene elementos que coinciden con la consulta de búsqueda actual.

Cuando el usuario selecciona un resultado de búsqueda, se ejecuta la invalidación `OnItemSelected` y se establece la propiedad `SelectedItem`. En este ejemplo, el método dirige a otra página que muestra datos sobre el elemento `Animal` seleccionado. Para más información sobre la navegación, consulte [Navegación en Xamarin.Forms Shell](navigation.md).

> [!NOTE]
> Se pueden establecer propiedades `SearchHandler` adicionales para controlar la apariencia del cuadro de búsqueda.

## <a name="consume-a-searchhandler"></a>Consumo de una clase SearchHandler

La clase `SearchHandler` en subclase se puede consumir mediante el establecimiento de la propiedad adjunta `Shell.SearchHandler` en un objeto del tipo en subclase:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

El método `MonkeySearchHandler.OnQueryChanged` devuelve un elemento `List` de objetos `Animal`. La propiedad `DisplayMemberName` se establece en la propiedad `Name` de cada objeto `Animal`, por lo que los datos mostrados en el área de sugerencias serán el nombre de cada animal.

La propiedad `ShowsResults` está establecida en `true`, de modo que se muestran sugerencias de búsqueda cuando el usuario escribe una consulta de búsqueda:

[![Captura de pantalla de resultados de búsqueda en una clase SearchHandler de Shell en iOS y Android](search-images/search-results.png "Resultados de búsqueda de SearchHandler de Shell")](search-images/search-results-large.png#lightbox "Shell SearchHandler search results")

A medida que cambia la consulta de búsqueda, se actualiza el área de sugerencias de búsqueda:

[![Captura de pantalla de resultados de búsqueda en una clase SearchHandler de Shell en iOS y Android](search-images/search-results-change.png "Resultados de búsqueda de SearchHandler de Shell")](search-images/search-results-change-large.png#lightbox "Shell SearchHandler search results")

Cuando se selecciona un resultado de búsqueda, hay un desplazamiento hasta `MonkeyDetailPage` y se muestran datos sobre el mono seleccionado:

[![Captura de pantalla de detalles de mono en iOS y Android](search-images/detailpage.png "Detalles de mono")](search-images/detailpage-large.png#lightbox "Monkey details")

## <a name="define-search-results-item-appearance"></a>Definición de la apariencia de los elemento de los resultados de búsqueda

Además de mostrar datos de `string` en los resultados de búsqueda, se puede definir la apariencia de cada uno de los elementos de los resultados de búsqueda mediante el establecimiento de la propiedad `SearchHandler.ItemTemplate` en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:MonkeySearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="0.15*" />
                            <ColumnDefinition Width="0.85*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                    </Grid>
                </DataTemplate>
            </controls:MonkeySearchHandler.ItemTemplate>
       </controls:MonkeySearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

El código de C# equivalente es el siguiente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name",
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

Los elementos especificados en [ `DataTemplate`](xref:Xamarin.Forms.DataTemplate) definen la apariencia de cada elemento en el área de sugerencias. En este ejemplo, el diseño dentro de `DataTemplate` se administra mediante un objeto [`Grid`](xref:Xamarin.Forms.Grid). El objeto `Grid` contiene un objeto [`Image`](xref:Xamarin.Forms.Image) y un objeto [`Label`](xref:Xamarin.Forms.Label), que enlazan ambos con las propiedades de cada objeto `Monkey`.

Las capturas de pantalla siguientes muestran el resultado de crear plantillas para cada elemento del área de sugerencias:

[![Captura de pantalla de resultados de búsqueda con plantilla en una clase SearchHandler de Shell en iOS y Android](search-images/search-results-template.png "Resultados de búsqueda con plantilla de SearchHandler de Shell")](search-images/search-results-template-large.png#lightbox "Shell SearchHandler templated search results")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="searchbox-visibility"></a>Visibilidad de SearchBox

Cuando se agrega un cuadro de búsqueda en la parte superior de una página, de forma predeterminada el cuadro de búsqueda está visible y totalmente expandido. Pero este comportamiento se puede cambiar estableciendo la propiedad `SearchHandler.SearchBoxVisibility` en uno de los miembros de la enumeración `SearchBoxVisibility`:

- `Hidden`: el cuadro de búsqueda no es visible ni accesible.
- `Collapsible`: el cuadro de búsqueda está oculto hasta que el usuario realiza una acción para mostrarlo.
- `Expanded`: el cuadro de búsqueda está visible y totalmente expandido.

En el ejemplo siguiente se muestra cómo ocultar el cuadro de búsqueda:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="related-links"></a>Vínculos relacionados

- [Xaminals (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Navegación en Xamarin.Forms Shell](navigation.md)
