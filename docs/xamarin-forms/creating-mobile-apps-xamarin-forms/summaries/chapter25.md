---
title: Resumen de capítulo 25. Variedades de página
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 951ae41763d8338d5adf73fb46ebc6defa64f8f8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-25-page-varieties"></a>Resumen de capítulo 25. Variedades de página

Hasta ahora ha visto dos clases que derivan de `Page`: `ContentPage` y `NavigationPage`. Este capítulo presentan los otros dos:

- [`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) administra dos páginas, una maestra y un detalle
- [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) administra varias páginas secundarias tiene accesibles a través de las fichas

Estos tipos de página proporcionan más sofisticadas opciones de navegación a la `NavagationPage` se describe en [capítulo 24. Página de navegación](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Maestro y detalles

El [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) define dos propiedades de tipo `Page`: [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) y [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/). Normalmente, se establece cada una de estas propiedades en un `ContentPage`. La `MasterDetailPage` muestra y cambia entre estas dos páginas.

Hay dos aspectos fundamentales para cambiar entre estas dos páginas:

- *dividir* donde estén instalados en paralelo el maestro y detalles
- *popover* en la página de detalles se trata o cubre parcialmente el maestro de página

Hay diversas variaciones de la *popover* enfoque (*diapositiva*, *se superponen*, y *intercambio*), pero generalmente son plataforma dependientes. Puede establecer la [ `MasterDetailBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) propiedad de `MasterDetailPage` a un miembro de la [ `MasterBehavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterBehavior/) enumeración:

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Default/)
- [`Split`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Split/)
- [`SplitOnLandscape`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnLandscape/)
- [`SplitOnPortrait`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnPortrait/)
- [`Popover`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Popover/)

Sin embargo, esta propiedad tiene ningún efecto en los teléfonos. Teléfonos siempre tienen un comportamiento de popover. Solo tabletas y escritorio windows pueden tener un comportamiento de división.

### <a name="exploring-the-behaviors"></a>Explorar los comportamientos

El [ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) ejemplo le permite experimentar con el comportamiento predeterminado de los diversos dispositivos. El programa contiene dos `ContentPage` derivados de la maestra y de detalles (con un `Title` propiedad establecida en ambos) y otra clase que deriva de `MasterDetailPage` que combina. La página de detalles se incluye en un `NavigationPage` porque el programa UWP no iba a funcionar sin él.

Las plataformas de Windows 8.1 y Windows Phone 8.1 requieren el establecimiento de un mapa de bits el `Icon` propiedad de la página maestra.

### <a name="back-to-school"></a>Vuelta al cole

El [ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) ejemplo toma un enfoque ligeramente diferente para construir el programa para mostrar a los alumnos desde el [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca.

El `Master` y `Detail` propiedades se definen con árboles visuales en el [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) archivo, que se deriva de `MasterDetailPage`. Esta disposición permite enlaces de datos a establecerse entre las páginas maestra y de detalles.

Que el archivo XAML también establece la [ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) propiedad de `MasterDetailPage` a `True`. Esto hace que la página maestra que se mostrará en el inicio; de forma predeterminada se muestra la página de detalles. El [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) archivo establece `IsPresented` a `false` cuando se selecciona un elemento de la `ListView` en la página maestra. A continuación, se muestra la página de detalles:

[![Captura de pantalla triple del centro educativo y detalle](images/ch25fg09-small.png "página de detalles de un MasterDetailPage")](images/ch25fg09-large.png#lightbox "página de detalles de un MasterDetailPage")

### <a name="your-own-user-interface"></a>Su propia interfaz de usuario

Aunque Xamarin.Forms proporciona una interfaz de usuario para cambiar entre las vistas de maestro y detalles, puede proporcionar sus propios. Para ello:

- Establecer el [ `IsGestureEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsGestureEnabled/) propiedad `false` para deshabilitar Deslizar rápidamente
- Invalidar el [ `ShouldShowToolbarButton` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton()/) método y devuelven `false` para ocultar los botones de barra de herramientas de Windows 8.1 y Windows Phone 8.1.

A continuación, debe proporcionar un medio para cambiar entre las páginas maestra y detalle, tal como se muestra en el [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) ejemplo.

El [ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) ejemplo muestra el uso de otro enfoque un `TapGestureRecognizer` en las páginas maestra y de detalles.

## <a name="tabbedpage"></a>TabbedPage

El [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) es una colección de páginas que puede cambiar entre pestañas. Deriva de `MultiPage<Page>` y no define propiedades o métodos públicos de su propio. [`MultiPage<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/), sin embargo, definir una propiedad:

- [`Children`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) propiedad de tipo `IList<T>`

Cumplimente este `Children` colección de objetos de la página.

Otro enfoque le permite definir la `TabbedPage` elementos secundarios, como mucho una `ListView` utilizando estas dos propiedades que generan las páginas con pestañas automáticamente:

- [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) de tipo `IEnumerable`
- [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) de tipo `DataTemplate`

Sin embargo, este enfoque no funciona bien en iOS, cuando la colección contiene más de unos pocos elementos.

`MultiPage<T>` define dos propiedades más que le permiten realizar un seguimiento de la página que está visible actualmente:

- [`CurrentPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.CurrentPage/) de tipo `T`, que hace referencia a la página
- [`SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.SelectedItem/) de tipo `Object`, que hace referencia al objeto en el `ItemsSource` colección

`MultiPage<T>` También define dos eventos:

- [`PagesChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.PagesChanged/) Cuando el `ItemsSource` cambios en la colección
- [`CurrentPageChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.CurrentPageChanged/) Cuando se cambia la página vista

### <a name="discrete-tab-pages"></a>Páginas de fichas discretos

El [ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) ejemplo consta de tres páginas con pestañas que mostrar colores de tres maneras diferentes. Cada pestaña es un `ContentPage` derivado y, a continuación, el `TabbedPage` derivado [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina las tres páginas.

Para cada página que aparece en un `TabbedPage`, `Title` propiedad es necesaria para especificar el texto en la ficha y la tienda de Apple requiere que se utilizará un icono, por lo que el `Icon` propiedad está establecida para iOS:

[![Captura de pantalla triple de colores diferenciados por pestañas](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

El [ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) ejemplo tiene una página de inicio que enumera todos los alumnos. Cuando un alumno que se derivan, esto se desplaza a un `TabbedPage` derivado, [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), que incorpora tres `ContentPage` objetos de su árbol visual, uno de los cuales permite introducir algunas notas para ese alumno.

### <a name="using-an-itemtemplate"></a>Mediante un ItemTemplate

El [ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) ejemplo usa el [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. El [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) archivo establece la `DataTemplate` propiedad de `TabbedPage` a partir de un árbol visual `ContentPage` que contiene enlaces a las propiedades de `NamedColor` (incluido un enlace a la `Title` propiedad).

Sin embargo, esto es problemático en iOS. Solo algunos de los elementos se pueden mostrar, y no hay ninguna manera buena para darles iconos.



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 25 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Ejemplos de capítulo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Página de detalles principales](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Página con pestañas](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
