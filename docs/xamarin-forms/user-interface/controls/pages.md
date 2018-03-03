---
title: "Páginas de Xamarin.Forms"
description: "Xamarin.Forms páginas representan las pantallas de aplicaciones móviles multiplataforma."
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 35822dbbb7d5694e7f1f0a3f35f10df404206af9
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-pages"></a>Páginas de Xamarin.Forms

_Xamarin.Forms páginas representan las pantallas de aplicaciones móviles multiplataforma._

<style>.tableimg {ancho máximo: ninguno! importante;}</style>

## <a name="pages"></a>Páginas

El [ `Page` ](http://iosapi.xamarin.com/?link=T%3aXamarin.Forms.Page) clase es un elemento visual que ocupa la mayor parte o la totalidad de la pantalla y contiene un único elemento secundario. Un `Xamarin.Forms.Page` representa un controlador de vista de iOS o una página en Windows Phone. En Android cada página ocupa la pantalla como una actividad, pero son de páginas de Xamarin.Forms *no* actividades.

 [ ![](pages-images/pages-sml.png "Tipos de páginas de Xamarin.Forms")](pages-images/pages.png "tipos de página de Xamarin.Forms")

<br clear="all" />

Xamarin.Forms es compatible con:

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <tr>
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
  </thead></tr>
  <tbody>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>
    </td>
    <td align="center" valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">contenidoPage</a> muestra una sola <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a>, a menudo un contenedor, como un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a> o un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentPageDemoPage.cs"><img src="pages-images/ContentPage.png" title="Ejemplo de contenidoPage" class="tableimg">
    </a></td>
  </tr><tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/">MasterDetailPage</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que administra dos paneles de información.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/MasterDetailPageDemoPage.cs"><img src="pages-images/MasterDetailPage.png" title="Ejemplo de MasterDetailPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/">NavigationPage</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que administra la navegación y la experiencia del usuario de una pila de otras páginas.  
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/NavigationPageDemoPage.cs"><img src="pages-images/NavigationPage.png" title="Ejemplo de NavigationPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/">TabbedPage</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que permite la navegación entre los elementos secundarios páginas, utilizando las pestañas.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TabbedPageDemoPage.cs"><img src="pages-images/TabbedPage.png" title="Ejemplo de TabbedPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/">TemplatedPage</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que muestra el contenido de pantalla completa con una plantilla de control y la clase base para <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">contenidoPage</a>.
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="pages-images/TemplatedPage.png" title="Ejemplo de TemplatedPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/">CarouselPage</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que permite movimientos deslice el dedo entre subpáginas, como una galería.
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CarouselPageDemoPage.cs"><img src="pages-images/CarouselPage.png" title="Ejemplo de CarouselPage" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Galería de Xamarin.Forms (ejemplo)](https://developer.xamarin.com/samples/FormsGallery/)
- [Ejemplos de Xamarin.Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentación de API de Xamarin.Forms](http://iosapi.xamarin.com/?link=N%3aXamarin.Forms)
