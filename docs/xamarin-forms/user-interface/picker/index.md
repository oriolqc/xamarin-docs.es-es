---
title: Selector de Xamarin.Forms
description: El selector de Xamarin.Forms muestra una breve lista de elementos, desde el que el usuario puede seleccionar un elemento. En este artículo se explica cómo usar la clase del selector para seleccionar un elemento de texto de una lista de datos.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: dc39fd9c129fb63fa4a3a73b15aea4204a38cdbd
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557274"
---
# <a name="xamarinforms-picker"></a>Selector de Xamarin.Forms

_La vista de selector es un control para seleccionar un elemento de texto en una lista de datos._

Xamarin.Forms [ `Picker` ](xref:Xamarin.Forms.Picker) muestra una breve lista de elementos, desde el que el usuario puede seleccionar un elemento. `Picker` define las siguientes propiedades:

- [`Title`](xref:Xamarin.Forms.Picker.Title) de tipo `string`, cuyo valor predeterminado es `null`.
- `TitleColor` de tipo [ `Color` ](xref:Xamarin.Forms.Color), el color utilizado para mostrar el `Title` texto.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) de tipo `IList`, la lista de origen de elementos para mostrar, cuyo valor predeterminado es `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) de tipo `int`, el índice del elemento seleccionado, cuyo valor predeterminado es -1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) de tipo `object`, el elemento seleccionado, cuyo valor predeterminado es `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) de tipo [ `Color` ](xref:Xamarin.Forms.Color), el color utilizado para mostrar el texto, cuyo valor predeterminado es [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) de tipo [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), cuyo valor predeterminado es [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) de tipo `string`, cuyo valor predeterminado es `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) de tipo `double`, que de forma predeterminada va de -1,0.

Todas las propiedades están respaldados por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que puede cambiar el estilo y las propiedades pueden ser destinos de enlaces de datos. El [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) y [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propiedades tienen un modo de enlace predeterminada de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), lo que significa que pueden ser destinos de enlaces de datos en una aplicación que utiliza el [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitectura. Para obtener información sobre cómo establecer las propiedades de fuente, consulte [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

Un [ `Picker` ](xref:Xamarin.Forms.Picker) no muestra ningún dato al que se muestra por primera vez. En su lugar, el valor de su [ `Title` ](xref:Xamarin.Forms.Picker.Title) propiedad se muestra como un marcador de posición en las plataformas iOS y Android:

[![](images/picker-initial.png "Inicial para mostrar selector")](images/picker-initial-large.png#lightbox "inicial para mostrar selector")

Cuando el [ `Picker` ](xref:Xamarin.Forms.Picker) se muestra el enfoque de ganancias, sus datos y el usuario puede seleccionar un elemento:

[![](images/picker-selection.png "Selector de selección de un elemento")](images/picker-selection-large.png#lightbox "selector de selección de un elemento")

El [ `Picker` ](xref:Xamarin.Forms.Picker) se activa un [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) eventos cuando el usuario selecciona un elemento. Después de selección, se muestra el elemento seleccionado por el `Picker`:

![](images/picker-after-selection.png "Selector de después de la selección")

Hay dos técnicas para rellenar un [ `Picker` ](xref:Xamarin.Forms.Picker) con datos:

- Establecer el [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propiedad a los datos que se mostrará. Esta es la técnica recomendada. Para obtener más información, consulte [establecer la propiedad ItemsSource de un selector](populating-itemssource.md).
- Agregar los datos que se mostrará a los [ `Items` ](xref:Xamarin.Forms.Picker.Items) colección. Esta técnica era el proceso original para rellenar un [ `Picker` ](xref:Xamarin.Forms.Picker) con datos. Para obtener más información, consulte [agregar datos a la colección de elementos de un selector](populating-items.md).

## <a name="related-links"></a>Vínculos relacionados

- [Selector](xref:Xamarin.Forms.Picker)
