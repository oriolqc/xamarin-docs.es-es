---
title: Establecer la propiedad ItemsSource de un selector
description: La vista de selector es un control para seleccionar un elemento de texto en una lista de datos. En este artículo se explica cómo rellenar un selector de datos estableciendo la propiedad ItemsSource y cómo responder a la selección de elementos por el usuario.
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 102ff15ea0e0002b783f993c59eb50d03a5ece63
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051074"
---
# <a name="setting-a-pickers-itemssource-property"></a>Establecer la propiedad ItemsSource de un selector

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)

_La vista de selector es un control para seleccionar un elemento de texto en una lista de datos. En este artículo se explica cómo rellenar un selector de datos estableciendo la propiedad ItemsSource y cómo responder a la selección de elementos por el usuario._

Xamarin.Forms 2.3.4 mejoró la [ `Picker` ](xref:Xamarin.Forms.Picker) vista agregando la capacidad para rellenarlo con datos estableciendo sus [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propiedad y para recuperar el elemento seleccionado de la [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propiedad. Además, se puede cambiar el color del texto del elemento seleccionado estableciendo el [ `TextColor` ](xref:Xamarin.Forms.Picker.TextColor) propiedad a un [ `Color` ](xref:Xamarin.Forms.Color).

## <a name="populating-a-picker-with-data"></a>Rellenar un selector de datos

Un [ `Picker` ](xref:Xamarin.Forms.Picker) se pueden rellenar con datos estableciendo sus [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propiedad a un `IList` colección. Debe ser de cada elemento de la colección, o derivado de, escriba `object`. Se pueden agregar elementos en XAML, inicialice el `ItemsSource` propiedad desde una matriz de elementos:

```xaml
<Picker x:Name="picker" Title="Select a monkey">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> Tenga en cuenta que el `x:Array` elemento requiere un `Type` atributo que indica el tipo de los elementos de la matriz.

El código de C# equivalente se muestra a continuación:

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey" };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>Responder a la selección de elemento

Un [ `Picker` ](xref:Xamarin.Forms.Picker) admite la selección de un elemento a la vez. Cuando un usuario selecciona un elemento, el [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) desencadena el evento, el [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) se actualiza la propiedad en un entero que representa el índice del elemento seleccionado en la lista y el [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propiedad se actualiza a la `object` que representa el elemento seleccionado. El [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propiedad es un número de base cero que indica el elemento del usuario seleccionado. Si se selecciona ningún elemento, que es el caso cuando la [ `Picker` ](xref:Xamarin.Forms.Picker) en primer lugar se crea y se inicializa, `SelectedIndex` será -1.

> [!NOTE]
> Elemento de comportamiento de la selección en un [ `Picker` ](xref:Xamarin.Forms.Picker) puede personalizarse en iOS con una plataforma específica. Para obtener más información, consulte [selección de elementos de control de selector de](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

El ejemplo de código siguiente muestra cómo recuperar el [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) valor de propiedad de la [ `Picker` ](xref:Xamarin.Forms.Picker) en XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

El código de C# equivalente se muestra a continuación:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Además, puede ser un controlador de eventos se ejecuta cuando el [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) desencadena el evento:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

Este método obtiene la [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) valor de propiedad y el valor se utiliza para recuperar el elemento seleccionado de la [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) colección. Esto es funcionalmente equivalente a recuperar el elemento seleccionado de la [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propiedad. Tenga en cuenta que cada elemento de la `ItemsSource` colección es de tipo `object`por lo que debe convertirse a un `string` para su presentación.

> [!NOTE]
> Un [ `Picker` ](xref:Xamarin.Forms.Picker) puede inicializarse para mostrar un elemento específico mediante el establecimiento del [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) o [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propiedades. Sin embargo, estas propiedades deben establecerse después de inicializar el [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) colección.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Rellenar un selector de datos mediante el enlace de datos

Un [ `Picker` ](xref:Xamarin.Forms.Picker) puede también se rellena con datos mediante el uso de enlace de datos para enlazar su [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propiedad a un `IList` colección. En XAML Esto se logra con la [ `Binding` ](xref:Xamarin.Forms.Xaml.BindingExtension) extensión de marcado:

```xaml
<Picker Title="Select a monkey" ItemsSource="{Binding Monkeys}" ItemDisplayBinding="{Binding Name}" />
```

El código de C# equivalente se muestra a continuación:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

El [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) datos de la propiedad se enlaza a la `Monkeys` propiedad del modelo de vista conectada, que devuelve un `IList<Monkey>` colección. El siguiente ejemplo de código muestra la `Monkey` (clase), que contiene cuatro propiedades:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Cuando se enlaza a una lista de objetos, el [ `Picker` ](xref:Xamarin.Forms.Picker) se le debe indicar qué propiedad para mostrar de cada objeto. Esto se consigue estableciendo la [ `ItemDisplayBinding` ](xref:Xamarin.Forms.Picker.ItemDisplayBinding) propiedad a la propiedad necesaria de cada objeto. En los ejemplos de código anteriores, el `Picker` está establecido para mostrar cada `Monkey.Name` valor de propiedad.

### <a name="responding-to-item-selection"></a>Responder a la selección de elemento

Enlace de datos puede usarse para establecer un objeto con el [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) valor de propiedad cuando cambia:

```xaml
<Picker Title="Select a monkey"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

El código de C# equivalente se muestra a continuación:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

El [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) datos de la propiedad se enlaza a la `SelectedMonkey` propiedad del modelo de vista conectada, que es de tipo `Monkey`. Por lo tanto, cuando el usuario selecciona un elemento en el [ `Picker` ](xref:Xamarin.Forms.Picker), `SelectedMonkey` se establecerá la propiedad seleccionada `Monkey` objeto. El `SelectedMonkey` datos de objeto se muestran en la interfaz de usuario [ `Label` ](xref:Xamarin.Forms.Label) y [ `Image` ](xref:Xamarin.Forms.Image) vistas:

![](populating-itemssource-images/monkeys.png "Selección del selector de elemento")

> [!NOTE]
> Tenga en cuenta que el [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) y [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) ambas propiedades admiten enlaces bidireccionales de forma predeterminada.

## <a name="summary"></a>Resumen

El [ `Picker` ](xref:Xamarin.Forms.Picker) vista es un control para seleccionar un elemento de texto en una lista de datos. En este artículo se explica cómo rellenar un `Picker` con datos estableciendo el [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propiedad y cómo responder a la selección de elementos por el usuario. Este enfoque, que se introdujo en Xamarin.Forms 2.3.4, es el enfoque recomendado para interactuar con un `Picker`.


## <a name="related-links"></a>Vínculos relacionados

- [Demostración de selector (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Monkey App (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [Selector de enlazable (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [Selector](xref:Xamarin.Forms.Picker)
