---
title: Conceptos básicos de XAML de Xamarin.Forms
description: Introducción a marcado de multiplataforma para dispositivos móviles
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 991d928c2c58f05098a41c84aba295a31636ab96
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-xaml-basics"></a>Conceptos básicos de XAML de Xamarin.Forms

XAML (lenguaje de marcado de aplicaciones extensible) permite a los desarrolladores definir interfaces de usuario en Xamarin.Forms utilizando el marcado en lugar de código. XAML nunca es necesario en un programa de Xamarin.Forms, pero suele ser más concisas y visualmente más coherente que el código equivalente y potencialmente herramientas. XAML es especialmente adecuado para su uso con la arquitectura de aplicaciones popular de MVVM (Model-View-ViewModel): XAML define la vista que está vinculada al código de modelo de vista a través de enlaces de datos basadas en XAML.

## <a name="xaml-basics-contents"></a>Contenido de conceptos básicos XAML

* [Información general](#Overview)
* [Parte 1. Introducción a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [Parte 2. Sintaxis XAML esencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [Parte 3. Extensiones de marcado XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [Parte 4. Conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [Parte 5. Desde el enlace de datos a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

Además de estos artículos Conceptos básicos de XAML, puede descargar capítulos del libro [crear aplicaciones móviles con Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "Cubierta de libro")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

Temas XAML se tratan con más detalle en muchos capítulos del libro, incluidos:

<table style="border:0px; box-shadow:0 0px 0px" cellpadding="0" cellspacing="2" border="0" width="85%">
<tr style="background:#ecf0f1">
  <td style="border:0px;">
    <h4>Capítulo 7. Frente a XAML. Código</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf">Descarga de PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md">Resumen</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 8. Código y XAML en armonía</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf">Descarga de PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md">Resumen</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 10. Extensiones de marcado XAML</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf">Descarga de PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md">Resumen</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capítulo 18. MVVM</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf">Descarga de PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md">Resumen</a></td></tr>
</table>

Pueden ser estos capítulos [descargar gratuitamente](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

<a name="Overview" />

## <a name="overview"></a>Información general

XAML es un lenguaje basado en XML creado por Microsoft como alternativa al código de programación para crear instancias e inicializar objetos y organizar los objetos de las jerarquías de elementos primarios y secundarios. XAML se haya adaptado para varias tecnologías en .NET framework, pero encontró su mayor utilidad para definir el diseño de interfaces de usuario de Windows Presentation Foundation (WPF), Silverlight, el tiempo de ejecución de Windows y Windows Universal Plataforma (UWP).

XAML también forma parte de Xamarin.Forms, la multiplataforma basada en forma nativa interfaz de programación para UWP, iOS y Android dispositivos móviles. En el archivo XAML, el desarrollador de Xamarin.Forms puede definir utilizando todos los Xamarin.Forms vistas, diseños y páginas, como las clases personalizadas así como las interfaces de usuario. El archivo XAML puede compilar o incrustar en el archivo ejecutable. En cualquier caso, se analiza la información de XAML en tiempo de compilación para buscar objetos con nombre y de nuevo en tiempo de ejecución para crear una instancia e inicializar objetos y para establecer vínculos entre estos objetos y el código de programación.

XAML tiene varias ventajas sobre el código equivalente:

-  XAML suele ser más concisas y legibles que el código equivalente.
-  La jerarquía de elementos primarios y secundarios inherente en XML permite XAML imitar con mayor claridad visual de la jerarquía de elementos primarios y secundarios de objetos de interfaz de usuario.
-  XAML puede ser fácilmente escrita a mano por los programadores, pero también se presta para ser herramientas y generados por herramientas de diseño visual.

Por supuesto, también hay inconvenientes, están relacionadas principalmente con las limitaciones que son intrínsecas de lenguajes de marcado:

-  XAML no puede contener código. Todos los controladores de eventos deben definirse en un archivo de código.
-  XAML no puede contener bucles para su procesamiento repetitivo. (Sin embargo, varios objetos visuales de Xamarin.Forms, sobre todo [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) , puede generar varios elementos secundarios en función de los objetos en su `ItemsSource` colección.)
-  XAML no puede contener procesamiento condicional (sin embargo, un enlace de datos puede hacer referencia a un convertidor de enlace basado en código que permite eficazmente un procesamiento condicional.)
-  Por lo general XAML no puede crear instancias de clases que no se definen un constructor sin parámetros. (Sin embargo, hay algunas veces un mecanismo para evitar esta restricción).
-  Por lo general, XAML no puede llamar a métodos. (De nuevo, esta restricción a veces se puede superar.)

Aún no tiene un diseñador visual para generar XAML en aplicaciones de Xamarin.Forms. Todo el código XAML debe ser escrita a mano, pero no hay un [controlador de vista previa de XAML](~/xamarin-forms/xaml/xaml-previewer.md). Los programadores nuevos en XAML conviene con frecuencia, compilar y ejecutar sus aplicaciones, especialmente después de cualquier cosa que no sean obviamente correctos. Incluso los programadores con mucha experiencia en XAML saben que se ha satisfecho experimentación.

XAML es, básicamente, XML, pero XAML tiene algunas características de sintaxis única. Las más importantes son:

- Elementos de propiedad
- Propiedades adjuntas
- Extensiones de marcado

Estas características son *no* extensiones XML. XAML es XML completamente válido. Pero estas características de sintaxis XAML utilizan XML de manera exclusiva. Se describen en detalle en los artículos siguientes, que concluir con una introducción al uso de XAML para implementar MVVM.

## <a name="requirements"></a>Requisitos

En este artículo se da por supuesto una familiaridad con Xamarin.Forms. Leer [An Introduction to Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md) es muy recomendable.

En este artículo también se da por supuesto un poco familiarizado con XML, incluidas la comprensión de la utilización de las declaraciones de espacio de nombres XML y los términos *elemento*, *etiqueta*, y *atributo*.

Cuando esté familiarizado con Xamarin.Forms y XML, empezar a leer [parte 1. Introducción a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).



## <a name="related-links"></a>Vínculos relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Creación de aplicaciones móviles libro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
