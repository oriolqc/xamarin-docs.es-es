---
title: "Diseños de Xamarin.Forms"
description: "Diseños de Xamarin.Forms se utilizan para crear controles de interfaz de usuario en estructuras visuales."
ms.topic: article
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: aa16c5aa0304b012aa6197e192ef073b472817cc
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="xamarinforms-layouts"></a>Diseños de Xamarin.Forms

_Diseños de Xamarin.Forms se utilizan para crear controles de interfaz de usuario en estructuras visuales._

El [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout) y [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) las clases de Xamarin.Forms son subtipos especializadas de vistas que actúan como contenedores para otros diseños y vistas. El `Layout` propia clase se deriva de [ `View` ](views.md). Un `Layout` derivado suele contener lógica para establecer la posición y el tamaño de los elementos secundarios en las aplicaciones de Xamarin.Forms.

 [ ![](layouts-images/layouts-sml.png "Tipos de diseño de Xamarin.Forms")](layouts-images/layouts.png#lightbox "tipos de diseño de Xamarin.Forms")

Las clases que derivan de `Layout` puede dividirse en dos categorías:

## <a name="layouts-with-single-content"></a>Diseños con contenido único

Estas clases derivan de [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/), que define [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) y [ `IsClippedToBounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.IsClippedToBounds/) propiedades.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) contiene un único elemento secundario que se establece con el [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propiedad. El `Content` propiedad puede establecerse en cualquier `View` derivado, incluidos otros `Layout` derivados. `ContentView` se usa principalmente como un elemento estructural y actúa como una clase base para [ `Frame` ](#frame).<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) | [![Ejemplo de ContentView](layouts-images/ContentView.png "ContentView ejemplo")](layouts-images/ContentView-Large.png#lightbox "ContentView ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Fotograma

|     |     |
| --- | --- |
| El [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) clase se deriva de [ `ContentView` ](#contentView) y muestra un marco rectangular alrededor de su elemento secundario. `Frame` tiene una restricción default [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) valor de 20 y también define [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/), [ `CornerRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.CornerRadius/), y [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/)propiedades.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) | [![Ejemplo de marco](layouts-images/Frame.png "marco ejemplo")](layouts-images/Frame-Large.png#lightbox "ejemplo de marco")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) es capaz de desplazar su contenido. Establecer el [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/) propiedad a una vista o diseño demasiado grande para caber en la pantalla. (El contenido de un `ScrollView` es muy a menudo un [ `StackLayout` ](#stackLayout).) Establecer el [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/) propiedad para indicar si el desplazamiento debe ser vertical, horizontal o ambos.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) / [guía](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![En el ejemplo se ScrollView](layouts-images/ScrollView.png "ejemplo ScrollView")](layouts-images/ScrollView-Large.png#lightbox "ejemplo ScrollView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) Muestra el contenido con una plantilla de control, y es la clase base para [ `ContentView` ](#contentView).<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) / [guía](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Ejemplo de TemplatedView](layouts-images/TemplatedView.png "TemplatedView ejemplo")](layouts-images/TemplatedView.png#lightbox "TemplatedView ejemplo") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) es un administrador de diseño de vistas con plantilla, utilizadas dentro de un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) para marcar dónde aparece el contenido que se van a presentar.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) / [guía](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Ejemplo de ContentPresenter](layouts-images/ContentPresenter.png "ejemplo ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "ejemplo ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Diseños con varios elementos secundarios

Estas clases derivan de [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) coloca los elementos secundarios en una pila horizontal o verticalmente se basa en el [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/) propiedad. El [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/) propiedad controla el espaciado entre los elementos secundarios y tiene un valor predeterminado de 6.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) / [guía](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![Ejemplo de StackLayout](layouts-images/StackLayout.png "StackLayout ejemplo")](layouts-images/StackLayout-Large.png#lightbox "StackLayout ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [página XAML]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml)) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Cuadrícula

|     |     |
| --- | --- |
| [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) sus elementos secundarios se coloca en una cuadrícula de filas y columnas. Posición del elemento secundario se indica mediante el [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/), [ `Column` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/), [ `RowSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/), y [ `ColumnSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/).<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) / [guía](~/xamarin-forms/user-interface/layouts/grid.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Ejemplo de cuadrícula](layouts-images/Grid.png "ejemplo cuadrícula")](layouts-images/Grid-Large.png#lightbox "ejemplo de cuadrícula")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [página XAML]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml)) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) coloca los elementos secundarios en ubicaciones específicas en relación con su elemento primario. Posición del elemento secundario se indica mediante el [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/) y [ `LayoutFlags` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/). Un `AbsoluteLayout` es útil para animar las posiciones de vistas.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) / [guía](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Ejemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "AbsoluteLayout ejemplo")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayout.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayout.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) coloca los elementos secundarios relativo a la `RelativeLayout` sí misma o a sus elementos del mismo nivel. Posición del elemento secundario se indica mediante el [propiedades adjuntas](~/xamarin-forms/xaml/attached-properties.md) que se establecen en objetos de tipo [ `Constraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/) y [ `BoundsConstraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/).<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)/ [guía](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Ejemplo de RelativeLayout](layouts-images/RelativeLayout.png "RelativeLayout ejemplo")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [página XAML]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml)) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Ejemplo de Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Documentación de API de Xamarin.Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
