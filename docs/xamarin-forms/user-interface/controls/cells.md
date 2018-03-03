---
title: Celdas de Xamarin.Forms
description: Las celdas de Xamarin.Forms se pueden agregar a ListView y TableViews.
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 509ecc509754bba544115c140e619f634bd64eae
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-cells"></a>Celdas de Xamarin.Forms

_Las celdas de Xamarin.Forms se pueden agregar a ListView y TableViews._

<style>.tableimg {ancho máximo: ninguno! importante;}</style>

## <a name="cells"></a>Celdas

Una celda es un elemento especializado utilizado para los elementos de una tabla y describe cómo se debe dibujar cada elemento en una lista. Celda se deriva de [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/), del que deriva también VisualElement. Sin embargo, celda no es un elemento visual, simplemente describe una plantilla para crear un elemento visual. [`Cell`](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) proporciona capacidades y la clase base para todas las celdas de Xamarin.Forms. Las celdas son elementos diseñados para agregarse a [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) o [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) controles.

Para obtener información sobre cómo usar y personalizar las celdas, consulte el [ListView](~/xamarin-forms/user-interface/listview/index.md) y [TableView](~/xamarin-forms/user-interface/tableview.md) documentación.

<table align="center" border="1" cellpadding="1" cellspacing="1">
<thead>
    <th>
      <strong>tipo de</strong>
    </th>
    <th>
      <strong>Descripción</strong>
    </th>
    <th style="min-width:400px">
      <strong>captura de pantalla</strong>
    </th>
  </thead>
  <tbody>
    <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/">EntryCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> con una etiqueta y un campo de entrada de texto de línea única.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="cells-images/EntryCell.png" title="Ejemplo de EntryCell" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/">SwitchCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> con una etiqueta y un conmutador de encendido/apagado.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchCellDemoPage.cs"><img src="cells-images/SwitchCell.png" title="Ejemplo de SwitchCell" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">TextCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> con texto principal y secundario.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TextCellDemoPage.cs"><img src="cells-images/TextCell.png" title="Ejemplo de TextCell" class="tableimg">
    </a></td>
  </tr>
      <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/">ImageCell</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">texto de la celda</a> que también incluye una imagen.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageCellDemoPage.cs"><img src="cells-images/ImageCell.png" title="En el ejemplo se ImageCell" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Galería de Xamarin.Forms (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Ejemplos de Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentación de API de Xamarin.Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
