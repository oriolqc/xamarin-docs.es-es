---
title: Agregar datos a la colección de elementos de un selector
description: La vista de selector es un control para seleccionar un elemento de texto de una lista de datos. Este artículo explica cómo rellenar un selector de datos, éste se agrega a la colección de elementos y cómo responder a la selección de elementos por el usuario.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 63a72861895f79d2d0154297f88610ddb8bb8beb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Agregar datos a la colección de elementos de un selector

_La vista de selector es un control para seleccionar un elemento de texto de una lista de datos. Este artículo explica cómo rellenar un selector de datos, éste se agrega a la colección de elementos y cómo responder a la selección de elementos por el usuario._

## <a name="populating-a-picker-with-data"></a>Rellenar un selector con datos

Antes de Xamarin.Forms 2.3.4, el proceso para rellenar un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con datos era agregar los datos que se mostrará a solo lectura [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) colección, que es de tipo `IList<string>`. Cada elemento de la colección debe ser del tipo `string`. Se pueden agregar elementos en XAML mediante la inicialización de la `Items` propiedad con una lista de `x:String` elementos:

```xaml
<Picker Title="Select a monkey">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

El código equivalente en C# se muestra a continuación:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

Además de agregar datos utilizando la `Items.Add` método, datos también se pueden insertar en la colección utilizando la `Items.Insert` método.

## <a name="responding-to-item-selection"></a>Responder a la selección de elementos

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) admite la selección de un elemento a la vez. Cuando un usuario selecciona un elemento, el [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) desencadena el evento y el [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) propiedad se actualiza a un entero que representa el índice del elemento seleccionado en la lista. El `SelectedIndex` propiedad es un número de base cero que indica el elemento que el usuario seleccionado. Si se selecciona ningún elemento, que es el caso cuando la `Picker` en primer lugar se crea y se inicializa, `SelectedIndex` será -1.

> [!NOTE]
> Elemento de comportamiento de la selección en un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) se pueden personalizar en iOS con una específica de la plataforma. Para obtener más información, consulte [controlar la selección de elementos de selector](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

El siguiente ejemplo de código muestra la `OnPickerSelectedIndexChanged` método de controlador de eventos, que es ejecutado cuando la [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) desencadena el evento:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

Este método obtiene el [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) valor de propiedad y el valor se utiliza para recuperar el elemento seleccionado de la [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) colección. Dado que cada elemento de la `Items` colección es un `string`, se pueden mostrar mediante un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) sin necesidad de realizar una conversión de tipos.

> [!NOTE]
> A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) puede inicializarse para mostrar un elemento específico estableciendo la [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) propiedad. Sin embargo, el `SelectedIndex` propiedad debe establecerse después de inicializar el [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) colección.

## <a name="summary"></a>Resumen

El [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) vista es un control para seleccionar un elemento de texto de una lista de datos. Este artículo explica cómo rellenar un `Picker` con datos agregándolo a la [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) colección y cómo responder a la selección de elementos por el usuario. Esto era el proceso para utilizar un `Picker` antes de Xamarin.Forms 2.3.4.


## <a name="related-links"></a>Vínculos relacionados

- [Demostración de selector (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Selector](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
