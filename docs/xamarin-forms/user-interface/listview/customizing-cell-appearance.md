---
title: Aspecto de la celda
description: Explorar las opciones para presentar los datos mientras se aprovecha de la comodidad de ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 37ecc76d9774b3f375af92f2a00c6c687358f065
ms.sourcegitcommit: a69439ad4c9fd0abe759143687d3b23582573d90
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="cell-appearance"></a>Aspecto de la celda

ListView presenta listas desplazables, que se pueden personalizar mediante el uso de `ViewCell`s. `ViewCells` puede utilizarse para mostrar texto e imágenes, que indica un estado verdadero/falso y recibir proporcionados por el usuario.

Hay dos métodos para obtener la apariencia deseada de las celdas de ListView:

- **[Personalizar celdas integradas](#Built_in_Cells)**  &ndash; una implementación más sencilla y mejorar el rendimiento a costa de la capacidad de personalización.
- **[Crear celdas personalizadas](#customcells)**  &ndash; más control sobre el resultado final, pero tienen la posibilidad de problemas de rendimiento si no se implementa correctamente.

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>Integrados en celdas
Xamarin.Forms incluye celdas integradas que funcionan para muchas aplicaciones simples:

- **TextCell** &ndash; para mostrar texto
- **ImageCell** &ndash; para mostrar una imagen con texto.

Otras dos celdas, [ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell) y [ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell) están disponibles, pero que no se utilizan habitualmente con `ListView`. Vea [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) para obtener más información acerca de estas celdas.

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) es una celda para mostrar el texto, opcionalmente con una segunda línea como texto de detalle.

TextCells se representan como controles nativos en tiempo de ejecución, por lo que el rendimiento es muy bueno en comparación con un personalizado `ViewCell`. TextCells son personalizables, lo que le permite establecer:

- `Text` &ndash; el texto que se muestra en la primera línea, en fuente grande.
- `Detail` &ndash; el texto que se muestra debajo de la primera línea, en una fuente menor.
- `TextColor` &ndash; el color del texto.
- `DetailColor` &ndash; el color del texto de detalle

![](customizing-cell-appearance-images/text-cell-default.png "Ejemplo de TextCell predeterminado")

![](customizing-cell-appearance-images/text-cell-custom.png "En el ejemplo se TextCell personalizada")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/), como `TextCell`, puede utilizarse para mostrar texto y texto de detalle secundaria y ofrece un gran rendimiento mediante el uso de los controles nativos de cada plataforma. `ImageCell` difiere de `TextCell` en que muestra una imagen a la izquierda del texto.

`ImageCell` es útil cuando se necesitan para mostrar una lista de datos con un aspecto visual, como una lista de contactos o películas. ImageCells son personalizables, lo que le permite establecer:

- `Text` &ndash; el texto que se muestra en la primera línea, en fuente grande
- `Detail` &ndash; el texto que se muestra debajo de la primera línea, en una fuente más pequeña
- `TextColor` &ndash; el color del texto
- `DetailColor` &ndash; el color del texto de detalle
- `ImageSource` &ndash; la imagen que se muestra junto al texto

![](customizing-cell-appearance-images/image-cell-default.png "Ejemplo de ImageCell predeterminado")

![](customizing-cell-appearance-images/image-cell-custom.png "En el ejemplo se ImageCell personalizada")

<a name="customcells" />

## <a name="custom-cells"></a>Celdas personalizadas
Cuando las celdas integradas no proporcionan el diseño necesarias, celdas personalizadas implementan el diseño necesario. Por ejemplo, puede que desee presentar una celda con dos etiquetas que tienen el mismo peso. A `TextCell` sería suficiente porque la `TextCell` tiene una etiqueta que sea menor. La mayoría de las personalizaciones de celda agregan datos de solo lectura adicionales (por ejemplo, etiquetas adicionales, imágenes u otra información de presentación).

Todas las celdas personalizadas deben derivarse de [ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), la misma clase base que todos los de la celda integrada tipos de uso.

Xamarin.Forms 2 introdujo una nueva [comportamiento almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy) en el `ListView` control que se puede establecer para mejorar el rendimiento de desplazamiento para algunos tipos de celdas personalizadas.

Este es un ejemplo de una celda personalizado:

![](customizing-cell-appearance-images/custom-cell.png "Ejemplo de celda personalizado")

### <a name="xaml"></a>XAML
El código XAML para crear el diseño anterior es menor que:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

El XAML anterior realiza mucho. Vamos a interrumpir hacia abajo:

- La celda personalizada está anidada dentro de un `DataTemplate`, que está dentro de `ListView.ItemTemplate`. Este es el mismo proceso que con cualquier otra celda.
- `ViewCell` es el tipo de celda personalizado. El elemento secundario de la `DataTemplate` elemento debe ser de o derivan del tipo `ViewCell`.
- Tenga en cuenta que dentro del `ViewCell`, diseño está administrado por un `StackLayout`. Este diseño permite personalizar el color de fondo. Tenga en cuenta que cualquier propiedad de `StackLayout` decir enlazable puede enlazarse dentro de una celda personalizada, aunque no se muestra aquí.

### <a name="cnum"></a>C&num;

La especificación de una celda personalizada en C# es un poco más detallado que el equivalente XAML. Observemos lo siguiente:

En primer lugar, defina una clase de celda personalizada, con `ViewCell` como la clase base:

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

En el constructor de la página con el `ListView`, establezca el ListView `ItemTemplate` propiedad a una nueva `DataTemplate`:

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

Tenga en cuenta que el constructor de `DataTemplate` toma un tipo. El uso del operador typeof Obtiene el tipo CLR para `CustomCell`.

<a name="binding-context-changes" />

### <a name="binding-context-changes"></a>Cambios de contexto de enlace

Cuando se enlaza a un tipo de celda personalizado [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instancias, los controles de interfaz de usuario muestra la `BindableProperty` valores deben usar el [ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/) invalidación para establecer los datos que se mostrará en cada celda, en lugar de al constructor de celda, como se muestra en el ejemplo de código siguiente:

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

El [ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/) se invocará invalidación cuando la [ `BindingContextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.BindingContextChanged/) se desencadene el evento, en respuesta al valor de la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) propiedad modificada. Por lo tanto, cuando la `BindingContext` cambia, los controles de interfaz de usuario muestra la [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) valores deben establecer sus datos. Tenga en cuenta que la `BindingContext` debe comprobarse para un `null` valor, tal como se puede establecer mediante Xamarin.Forms para la recolección, lo que dará lugar a su vez en el `OnBindingContextChanged` invalidación que se la llame.

Como alternativa, pueden enlazar los controles de interfaz de usuario a la [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instancias para mostrar sus valores, lo que elimina la necesidad de reemplazar el `OnBindingContextChanged` método.

> [!NOTE]
> Al reemplazar `OnBindingContextChanged`, asegúrese de que la clase base `OnBindingContextChanged` método se llama para que los delegados registrados reciban el `BindingContextChanged` eventos.

En XAML, el tipo de celda personalizado de enlace a datos se puede lograr tal como se muestra en el ejemplo de código siguiente:

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Esto enlaza el `Name`, `Age`, y `Location` propiedades enlazables en el `CustomCell` instancia, a la `Name`, `Age`, y `Location` propiedades de cada objeto en la colección subyacente.

El enlace equivalente en C# se muestra en el ejemplo de código siguiente:

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

En iOS y Android, si la [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) ha entrado en bucle los elementos y la celda personalizada utiliza un representador personalizado, el representador personalizado debe implementar correctamente la notificación de cambio de propiedad. Cuando las celdas se reutilizan sus valores de propiedad cambiará cuando se actualiza el contexto de enlace a la de una celda disponible, con `PropertyChanged` que se generen eventos. Para obtener más información, consulte [personalizar un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Para obtener más información acerca del reciclaje de celda, vea [estrategia de almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

## <a name="related-links"></a>Vínculos relacionados

- [Integrada en las celdas (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [Celdas personalizadas (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [Cambiar de contexto de enlace (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BindingContextChanged)
