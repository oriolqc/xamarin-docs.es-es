---
title: Celdas de Xamarin.Forms
description: Las celdas de Xamarin.Forms se pueden agregar a ListView y TableViews.
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: dbc28f3d58b43bb53db39da2dcdf1ba722169264
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-cells"></a>Celdas de Xamarin.Forms

_Las celdas de Xamarin.Forms se pueden agregar a ListView y TableViews._

A *celda* es un elemento especializado utilizado para los elementos de una tabla y se describe cómo se debe representar cada elemento de una lista. El [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) clase se deriva de [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/), desde el que [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/) también se deriva. Una celda no es un elemento visual; en su lugar, es una plantilla para crear un elemento visual. 

`Cell` se utiliza exclusivamente con [ `ListView` ](views.md#listView) y [ `TableView` ](views.md#tableView) controles. Para obtener información sobre cómo usar y personalizar las celdas, consulte el [ `ListView` ](~/xamarin-forms/user-interface/listview/index.md) y [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) documentación.

## <a name="cells"></a>Celdas

Xamarin.Forms es compatible con los siguientes tipos de celda:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| A [ `TextCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell) muestra uno o dos cadenas de texto. Establecer el [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/) propiedad y, opcionalmente, el [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/) propiedad a estas cadenas de texto.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell) / [guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Ejemplo de TextCell](cells-images/TextCell.png "TextCell ejemplo")](cells-images/TextCell-Large.png#lightbox "TextCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| El [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell) muestra la misma información que [ `TextCell` ](#textCell) pero incluye un mapa de bits que establece con el [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) propiedad.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell) / [guía](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![En el ejemplo se ImageCell](cells-images/ImageCell.png "ejemplo ImageCell")](cells-images/ImageCell-Large.png#lightbox "ejemplo ImageCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| El [ `SwitchCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell) contiene texto establecido con la [ `Text`'](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCellText/) propiedad y y on/off de conmutador establecido inicialmente con el valor booleano [ `On` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCell.On/) propiedad. Controlar la [ `OnChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SwitchCell.OnChanged/) eventos para recibir notificaciones cuando el `On` cambios de propiedad.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell) / [guía](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Ejemplo de SwitchCell](cells-images/SwitchCell.png "SwitchCell ejemplo")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| El [ `EntryCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell) define un [ `Label` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Label/) propiedad que identifica la celda y una sola línea de texto editable en el [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Text/) propiedad. Controlar la [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.EntryCell.Completed/) eventos para recibir una notificación cuando el usuario ha completado la entrada de texto.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell) / [guía](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Ejemplo de EntryCell](cells-images/EntryCell.png "EntryCell ejemplo")](cells-images/EntryCell-Large.png#lightbox "EntryCell ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Ejemplo de Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Documentación de API de Xamarin.Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
