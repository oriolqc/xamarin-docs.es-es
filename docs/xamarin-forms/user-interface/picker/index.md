---
title: Selector de
description: La vista de selector es un control para seleccionar un elemento de texto de una lista de datos.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 7f0050351ca28d7f8afeb82a85e82e51d399824b
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847503"
---
# <a name="picker"></a>Selector de

_La vista de selector es un control para seleccionar un elemento de texto de una lista de datos._

El Xamarin.Forms [ `Picker` ](xref:Xamarin.Forms.Picker) muestra una breve lista de elementos, desde el que el usuario puede seleccionar un elemento. `Picker` define ocho propiedades:

- [`Title`](xref:Xamarin.Forms.Picker.Title) de tipo `string`, cuyo valor predeterminado es `null`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) de tipo `IList`, la lista de origen de elementos para mostrar, cuyo valor predeterminado es `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) de tipo `int`, el índice del elemento seleccionado, cuyo valor predeterminado es-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) de tipo `object`, el elemento seleccionado, que tiene como valor predeterminado `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) de tipo [ `Color` ](xref:Xamarin.Forms.Color), el color utilizado para mostrar el texto, que tiene como valor predeterminado [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) de tipo [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), cuyo valor predeterminado es [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) de tipo `string`, cuyo valor predeterminado es `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) de tipo `double`, cuyo valor predeterminado es -1,0.

Todas las propiedades de ocho están respaldadas por [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden aplicar los estilos y las propiedades pueden ser destinos de enlaces de datos. El [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) y [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propiedades tienen un modo de enlace predeterminado de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), lo que significa que pueden ser destinos de enlaces de datos en una aplicación que utiliza el [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitectura. Para obtener información acerca de cómo establecer las propiedades de fuente, consulte [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) no muestre datos cuando se muestra por primera vez. En su lugar, el valor de su [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) propiedad se muestra como un marcador de posición en las plataformas iOS y Android:

[![](images/picker-initial.png "Inicial selector mostrar")](images/picker-initial-large.png#lightbox "inicial de la presentación de selector")

Cuando el [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) mejoras en el foco, sus datos se muestra y el usuario puede seleccionar un elemento:

[![](images/picker-selection.png "Selector de selección de un elemento")](images/picker-selection-large.png#lightbox "selector de selección de un elemento")

El [ `Picker` ](xref:Xamarin.Forms.Picker) se activa un [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) eventos cuando el usuario selecciona un elemento. Después de selección, se muestra el elemento seleccionado por el `Picker`:

![](images/picker-after-selection.png "Selector de después de la selección")

Hay dos técnicas para rellenar un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con datos:

- Establecer el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propiedad para que se muestren los datos. Esta es la técnica recomendada, que se introdujo en Xamarin.Forms 2.3.4. Para obtener más información, consulte [configuración ItemsSource propiedad de un selector](populating-itemssource.md).
- Agregar los datos que se mostrará a los [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) colección. Esta técnica era el proceso original para rellenar un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con datos. Para obtener más información, consulte [agregar datos de la colección de elementos de un selector](populating-items.md).

## <a name="related-links"></a>Vínculos relacionados

- [Selector](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
