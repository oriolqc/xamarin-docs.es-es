---
title: Selector de
description: La vista de selector es un control para seleccionar un elemento de texto de una lista de datos.
ms.topic: article
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: fc1aaffe4e31b596d57b5de30c87217ffba3772e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="picker"></a>Selector de

_La vista de selector es un control para seleccionar un elemento de texto de una lista de datos._

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) muestra una breve lista de elementos, desde donde puede seleccionar el usuario. Sin embargo, un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) no muestre datos cuando se muestra por primera vez. En su lugar, el valor de su [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/) propiedad se muestra como un marcador de posición en las plataformas iOS y Android:

[![](images/picker-initial.png "Inicial selector mostrar")](images/picker-initial-large.png#lightbox "inicial de la presentación de selector")

Cuando el [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) mejoras en el foco, sus datos se muestra y el usuario puede seleccionar un elemento:

[![](images/picker-selection.png "Selector de selección de un elemento")](images/picker-selection-large.png#lightbox "selector de selección de un elemento")

Después de selección, se muestra el elemento seleccionado por el [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/):

![](images/picker-after-selection.png "Selector de después de la selección")

Hay dos técnicas para rellenar un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con datos:

- Establecer el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propiedad para que se muestren los datos. Esta es la técnica recomendada, que se introdujo en Xamarin.Forms 2.3.4. Para obtener más información, consulte [configuración ItemsSource propiedad de un selector](populating-itemssource.md).
- Agregar los datos que se mostrará a los [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) colección. Esta técnica era el proceso original para rellenar un [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) con datos. Para obtener más información, consulte [agregar datos de la colección de elementos de un selector](populating-items.md).


## <a name="related-links"></a>Vínculos relacionados

- [Selector](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
