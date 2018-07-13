---
title: Celdas de Xamarin.Forms
description: Las celdas de Xamarin.Forms pueden agregarse a ListView y TableViews. En este artículo se enumera las celdas incluidas en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 3b69aaf0a10468a5950e0ccf5a61ab6ecbbc110f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995925"
---
# <a name="xamarinforms-cells"></a>Celdas de Xamarin.Forms

_Las celdas de Xamarin.Forms pueden agregarse a ListView y TableViews._

Un *celda* es un elemento especializado utilizado para los elementos de una tabla y se describe cómo se debe representar cada elemento en una lista. El [ `Cell` ](xref:Xamarin.Forms.Cell) clase se deriva de [ `Element` ](xref:Xamarin.Forms.Element), desde el que [ `VisualElement` ](xref:Xamarin.Forms.Element) también se deriva. Una celda no es un elemento visual; en realidad es una plantilla para crear un elemento visual.

`Cell` se utiliza exclusivamente con [ `ListView` ](views.md#listView) y [ `TableView` ](views.md#tableView) controles. Para obtener información sobre cómo usar y personalizar celdas, consulte el [ `ListView` ](~/xamarin-forms/user-interface/listview/index.md) y [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) documentación.

## <a name="cells"></a>Celdas

Xamarin.Forms es compatible con los siguientes tipos de celda:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Un [ `TextCell` ](xref:Xamarin.Forms.TextCell) muestra uno o dos cadenas de texto. Establecer el [ `Text` ](xref:Xamarin.Forms.TextCell.Text) propiedad y, opcionalmente, el [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) propiedad a estas cadenas de texto.<br /><br />[Documentación de API](xref:Xamarin.Forms.TextCell) / [guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Ejemplo de TextCell](cells-images/TextCell.png "TextCell ejemplo")](cells-images/TextCell-Large.png#lightbox "TextCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| El [ `ImageCell` ](xref:Xamarin.Forms.ImageCell) muestra la misma información que [ `TextCell` ](#textCell) pero incluye un mapa de bits que establece con el [ `Source` ](xref:Xamarin.Forms.Image.Source) propiedad.<br /><br />[Documentación de API](xref:Xamarin.Forms.ImageCell) / [guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![Ejemplo de ImageCell](cells-images/ImageCell.png "ejemplo ImageCell")](cells-images/ImageCell-Large.png#lightbox "ImageCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| El [ `SwitchCell` ](xref:Xamarin.Forms.SwitchCell) contiene texto establecido con el [ `Text`'](xref:Xamarin.Forms.SwitchCell.Text) propiedad y y on/off se establecen inicialmente con el valor booleano del modificador [ `On` ](xref:Xamarin.Forms.SwitchCell.On) propiedad. Controlar la [ `OnChanged` ](xref:Xamarin.Forms.SwitchCell.OnChanged) eventos para recibir una notificación cuando la `On` los cambios de propiedad.<br /><br />[Documentación de API](xref:Xamarin.Forms.SwitchCell) / [guía](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Ejemplo de SwitchCell](cells-images/SwitchCell.png "SwitchCell ejemplo")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| El [ `EntryCell` ](xref:Xamarin.Forms.EntryCell) define un [ `Label` ](xref:Xamarin.Forms.EntryCell.Label) propiedad que identifica la celda y una sola línea de texto editable en el [ `Text` ](xref:Xamarin.Forms.EntryCell.Text) propiedad. Controlar la [ `Completed` ](xref:Xamarin.Forms.EntryCell.Completed) eventos para recibir una notificación cuando el usuario ha completado la entrada de texto.<br /><br />[Documentación de API](xref:Xamarin.Forms.EntryCell) / [guía](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Ejemplo de EntryCell](cells-images/EntryCell.png "EntryCell ejemplo")](cells-images/EntryCell-Large.png#lightbox "EntryCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Ejemplo de Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Documentación de API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
