---
title: Páginas de Xamarin.Forms
description: Xamarin.Forms páginas representan las pantallas de aplicaciones móviles multiplataforma. Este artículo enumeran las páginas que se incluyen en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: f4b8d895c6c9c7f47b8af8e56ad60a5a453e2d56
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243163"
---
# <a name="xamarinforms-pages"></a>Páginas de Xamarin.Forms

_Xamarin.Forms páginas representan las pantallas de aplicaciones móviles multiplataforma._

Todos los tipos de página que se describen a continuación se derivan de la Xamarin.Forms [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) clase. Estos elementos visuales ocupan todos o la mayoría de la pantalla. A `Page` objeto representa un `ViewController` en iOS y un `Page` en la plataforma Universal de Windows. En Android, cada página ocupa la pantalla como una `Activity`, pero son de páginas de Xamarin.Forms *no* `Activity` objetos.

[ ![](pages-images/pages-sml.png "Tipos de páginas de Xamarin.Forms")](pages-images/pages.png#lightbox "tipos de página de Xamarin.Forms")

## <a name="pages"></a>Páginas

Xamarin.Forms es compatible con los siguientes tipos de página:

<a name="contentPage" />

### <a name="contentpage"></a>ContenidoPage

|     |     |
| --- | --- |
| [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) es el tipo más sencillo y más común de página. Establecer el [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentPage.Content/) propiedad a una sola [ `View` ](views.md) objeto, que suele ser un [ `Layout` ](layouts.md) como [ `StackLayout` ](layouts.md#stackLayout), [ `Grid` ](layouts.md#grid), o [ `ScrollView` ](layouts.md#scrollView).<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) | [![Ejemplo de contenidoPage](pages-images/ContentPage.png "contenidoPage ejemplo")](pages-images/ContentPage-Large.png#lightbox "contenidoPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| A [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) administra dos paneles de información. Establecer el [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) propiedad a una página por lo general que muestra una lista o un menú. Establecer el [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) propiedad a una página que muestra un elemento seleccionado de la página maestra. El [ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) propiedad controla si está visible la página maestra o de detalle.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) / [guía](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![Ejemplo de MasterDetailPage](pages-images/MasterDetailPage.png "MasterDetailPage ejemplo")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| El [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) administra la navegación entre otras páginas con una arquitectura basada en pilas. Cuando se utiliza la navegación de una página en la aplicación, una instancia de la página principal debe pasarse al constructor de un `NavigationPage` objeto.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) / [guía](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [ejemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/), [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/), y [3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![Ejemplo de NavigationPage](pages-images/NavigationPage.png "NavigationPage ejemplo")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) con [código = detrás](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) se deriva de la clase abstracta [ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/) clase y permite la navegación entre secundarios páginas con pestañas. Establecer el [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) propiedad a una colección de páginas, o un conjunto el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) propiedad a una colección de objetos de datos y la [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) propiedad para un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que describe la forma de cada objeto para representar visualmente.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) / [guía](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [ejemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/) y [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![Ejemplo de TabbedPage](pages-images/TabbedPage.png "TabbedPage ejemplo")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) se deriva de la clase abstracta [ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/) clase y permite la navegación entre secundarios páginas a través de un dedo Deslizar rápidamente. Establecer el [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) propiedad a una colección de [ `ContentPage` ](#contentPage) objetos, o un conjunto el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) propiedad a una colección de objetos de datos y el [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) propiedad a una [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que describe la forma de cada objeto para representar visualmente.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) / [guía](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [ejemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/) y [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![Ejemplo de CarouselPage](pages-images/CarouselPage.png "CarouselPage ejemplo")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) Muestra el contenido de pantalla completa con una plantilla de control, y es la clase base para [ `ContentPage` ](#contentPage).<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) / [guía](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Ejemplo de TemplatedPage](pages-images/TemplatedPage.png "TemplatedPage ejemplo")](pages-images/TemplatedPage.png "TemplatedPage ejemplo") |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Ejemplo de Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Documentación de API de Xamarin.Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
