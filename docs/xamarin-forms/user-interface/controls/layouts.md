---
title: "Diseños de Xamarin.Forms"
description: "Diseños de Xamarin.Forms se utilizan para crear controles de interfaz de usuario en estructuras lógicas."
ms.topic: article
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: ecea0f55360fcde7a50c52bb33c45a2c5fff5eeb
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-layouts"></a>Diseños de Xamarin.Forms

_Diseños de Xamarin.Forms se utilizan para crear controles de interfaz de usuario en estructuras lógicas._

<style>.tableimg {ancho máximo: ninguno! importante;}</style>

## <a name="layouts"></a>Diseños de

El [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout) clase de Xamarin.Forms es un subtipo de vista, que actúa como contenedor para otros diseños o vistas especializado. Normalmente contiene la lógica para establecer la posición y el tamaño de los elementos secundarios en las aplicaciones de Xamarin.Forms.

 [ ![](layouts-images/layouts-sml.png "Tipos de diseño de Xamarin.Forms")](layouts-images/layouts.png "tipos de diseño de Xamarin.Forms")

<br clear="all" />

Xamarin.Forms es compatible con:

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
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/">ContentPresenter</a>
    </td>
    <td valign="top">
Un administrador de diseño para las vistas con plantilla. Usar dentro de un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/">ControlTemplate</a> para marcar dónde aparece el contenido que se van a presentar.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/Templates/ControlTemplates/SimpleTheme/SimpleTheme/App.xaml"><img src="layouts-images/ContentPresenter.png" title="Ejemplo de ContentPresenter" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/">ContentView</a>
    </td>
    <td valign="top">
Un elemento con un contenido único. ContentView tiene muy poco uso de su propio. Su objetivo es servir como clase base para las vistas compuestas definido por el usuario.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentViewDemoPage.cs"><img src="layouts-images/ContentView.png" title="Ejemplo de ContentView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/">Marco</a>
    </td>
    <td valign="top">
Un elemento que contiene a un único elemento secundario, con algunas opciones de tramas. Marco tienen un valor predeterminado <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/">Xamarin.Forms.Layout.Padding</a> de 20.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/FrameDemoPage.cs"><img src="layouts-images/Frame.png" title="Ejemplo de marco" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>
    </td>
    <td valign="top">
Se requiere un elemento capaz de desplazamiento si es contenido.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ScrollViewDemoPage.cs"><img src="layouts-images/ScrollView.png" title="En el ejemplo se ScrollView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/">TemplatedView</a>
    </td>
    <td valign="top">
Un elemento que muestra el contenido con una plantilla de control y la clase base para <a href=""/api/type/Xamarin.Forms.ContentView/">ContentView</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="layouts-images/TemplatedView.png" title="Ejemplo de TemplatedView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/">AbsoluteLayout</a>
    </td>
    <td valign="top">
Coloca los elementos secundarios en posiciones absolutas de solicitado. Usuario asignado delimitadores y los límites define la posición y el tamaño del control.
    </td>
    <td valign="top">
      <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/AbsoluteLayoutDemoPage.cs"><img src="layouts-images/AbsoluteLayout.png" title="Ejemplo de AbsoluteLayout" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/">Grid</a>
    </td>
    <td valign="top">
Un diseño que contiene vistas organizadas en filas y columnas.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/GridDemoPage.cs"><img src="layouts-images/Grid.png" title="Ejemplo de cuadrícula" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/">RelativeLayout</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/%601">diseño</a> que utiliza <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/">restricción</a>s al diseño de sus elementos secundarios.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/RelativeLayoutDemoPage.cs"><img src="layouts-images/RelativeLayout.png" title="Ejemplo de RelativeLayout" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/">diseño</a> que coloca los elementos secundarios en una única línea que puede orientarse horizontal o verticalmente. Este diseño establecerá al elemento secundario límites automáticamente durante un ciclo de diseño. Usuario asignado límites se sobrescribirá y, por tanto, no se debe establecer en un elemento secundario por el usuario.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/StackLayoutDemoPage.cs"><img src="layouts-images/StackLayout.png" title="Ejemplo de StackLayout" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Galería de Xamarin.Forms (ejemplo)](https://developer.xamarin.com/samples/FormsGallery/)
- [Ejemplos de Xamarin.Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentación de API de Xamarin.Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms)
