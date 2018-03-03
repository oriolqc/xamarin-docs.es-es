---
title: Vistas de Xamarin.Forms
description: "Xamarin.Forms vistas son los bloques de creación de interfaces de usuario móviles entre plataformas."
ms.topic: article
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: df8c8463b2556035c5369c70cb10dbc3dc6b6743
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-views"></a>Vistas de Xamarin.Forms

_Xamarin.Forms vistas son los bloques de creación de interfaces de usuario móviles entre plataformas._

<style>.tableimg {ancho máximo: ninguno! importante;}</style>

## <a name="views"></a>Vistas

Xamarin.Forms utiliza la palabra *vista* para hacer referencia a objetos visuales, como botones, etiquetas o cuadros de entrada de texto - que pueden ser más conocidos como controles de widgets.

Estos elementos de interfaz de usuario son normalmente son subclases de [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/).

<br clear="right" />

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
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/">ActivityIndicator</a>
    </td>
    <td valign="top">
Un control visual que se utiliza para indicar que algo es continua. Este control proporciona una indicación visual al usuario que está ocurriendo algo, sin información sobre su progreso.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ActivityIndicatorDemoPage.cs"><img src="views-images/ActivityIndicator.png" title="Ejemplo de ActivityIndicator" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/">BoxView</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> utilizado para dibujar un rectángulo de color sólido. BoxView es un suplente útil para las imágenes o los elementos personalizados al realizar la creación de prototipos inicial. BoxView tiene una solicitud de tamaño predeterminado de 40 x 40. Si tiene un tamaño diferente, asigne el <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/">VisualElement.WidthRequest</a> y <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/">VisualElement.HeightRequest</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/BoxViewDemoPage.cs"><img src="views-images/BoxView.png" title="Ejemplo de BoxView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Button/">Button</a>
    </td>
    <td align="center" valign="top">
Un botón <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> que responde a los eventos de toque.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ButtonDemoPage.cs"><img src="views-images/Button.png" title="Ejemplo de botón" class="tableimg">
    </a></td>
  </tr>
  <tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/">DatePicker</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> que permite la selección de fecha. Es muy similar de la representación visual de un DatePicker <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">entrada</a>, excepto en que aparece un control especial para seleccionar una fecha en lugar de un teclado </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/DatePickerDemoPage.cs"><img src="views-images/DatePicker.png" title="Ejemplo de DatePicker" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/">Editor</a>
    </td>
    <td valign="top">
Un control que puede editar varias líneas de texto. Para las entradas de línea única, consulte <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">entrada</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EditorDemoPage.cs"><img src="views-images/Editor.png" title="Ejemplo de editor" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">Entrada</a>
    </td>
    <td valign="top">
Un control que puede editar una sola línea de texto. Entrada es una entrada de texto de línea única. Mejor sirve para recopilar pequeños elementos discretos de información, como nombres de usuario y contraseñas.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="views-images/Entry.png" title="Ejemplo de entrada" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Imagen</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> que contiene una imagen.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Imagen de API</a>
    <br />
    <a href="~/xamarin-forms/user-interface/images.md">Trabajar con imágenes</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageDemoPage.cs">Origen de demostración</a>
    </td>
    <td>
    <img src="views-images/Image.png" title="Ejemplo de imagen" class="tableimg">
    </td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Label/">Etiqueta</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> que muestra texto en un formato solo lectura. Una etiqueta se utiliza para mostrar elementos de texto de línea, así como bloques de varias líneas de texto.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/LabelDemoPage.cs"><img src="views-images/Label.png" title="Ejemplo de etiqueta" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">ListView</a>
    </td>
    <td valign="top">
Un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/">artículoVer</a> que muestra una colección de datos como una lista vertical.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">API de ListView</a>
    <br />
    <a href="~/xamarin-forms/user-interface/listview/index.md">Documentación de ListView</a>
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ListViewDemoPage.cs"><img src="views-images/ListView.png" title="Ejemplo de ListView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/">OpenGLView</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> que muestra el contenido de OpenGL.
    <ul>
      <li>Solo funciona para iOS y Android proyectos (no hay compatibilidad de Windows Phone).
      <li>Requiere una referencia a la <b>OpenTK 1.0</b> ensamblado en iOS y Android proyectos.</li>
      <li>Es idónea si necesita para usar en los proyectos compartidos; Si se utiliza en una PCL un DependencyService también será necesario.</li>
    </ul>
    </td>
    <td>
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/"><img src="views-images/OpenGL.png" title="Ejemplo de OpenGlView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/">Selector de</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> control para seleccionar un elemento en una lista. Es similar a la representación visual de un selector de un <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">entrada</a>, pero aparece un control de selector en lugar de un teclado.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/PickerDemoPage.cs"><img src="views-images/Picker.png" title="Ejemplo de selector" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/">ProgressBar</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> control que indica un progreso.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ProgressBarDemoPage.cs"><img src="views-images/ProgressBar.png" title="Clase de ejemplo de barra de progreso ="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/">SearchBar</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> control que proporciona un cuadro de búsqueda.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SearchBarDemoPage.cs"><img src="views-images/SearchBar.png" title="Ejemplo de SearchBar" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/">Control deslizante</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> control que introduce un valor lineal.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SliderDemoPage.cs"><img src="views-images/Slider.png" title="Ejemplo de control deslizante" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/">Stepper</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> control que introduce un valor discreto, restringido a un intervalo.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/StepperDemoPage.cs"><img src="views-images/Stepper.png" title="Ejemplo paso a paso desencadene" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/">Conmutador</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> control que proporciona un valor de estado de alternancia.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchDemoPage.cs"><img src="views-images/Switch.png" title="Ejemplo de conmutador" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> que contiene filas de <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">celda</a>s.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView API</a>
    <br />
    <a href="~/xamarin-forms/user-interface/tableview.md">Documentación de TableView</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TableViewFormDemoPage.cs">Origen de demostración</a>
    </td>
    <td>
    <img src="views-images/TableViewNewest.png" title="En el ejemplo se TableView" class="tableimg">
    </td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/">TimePicker</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> control que proporciona la selección de tiempo. Es muy similar de la representación visual de un TimePicker <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">entrada</a>, excepto en que un control especial para seleccionar uno aparece en lugar de un teclado.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TimePickerDemoPage.cs"><img src="views-images/TimePicker.png" title="En el ejemplo se TimePicker" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">WebView</a>
    </td>
    <td valign="top">
A <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">vista</a> que presenta el contenido HTML.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">Vista Web API</a>
    <br />
    <a href="~/xamarin-forms/user-interface/webview.md">Documentación de WebView</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/WebViewDemoPage.cs">Origen de demostración</a>
    </td>
    <td>
    <img src="views-images/WebView.png" title="Ejemplo de vista Web" class="tableimg">
    </td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Galería de Xamarin.Forms (ejemplo)](https://developer.xamarin.com/samples/FormsGallery/)
- [Ejemplos de Xamarin.Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentación de API de Xamarin.Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
