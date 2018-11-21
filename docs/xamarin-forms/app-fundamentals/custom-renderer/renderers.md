---
title: Representador de clases Base y los controles nativos
description: Todos los controles Xamarin.Forms tienen un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. En este artículo se enumera las clases de control nativo que implementan cada página de Xamarin.Forms, diseño, vista y celda y el representador.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 56df2f7e6b83ddd4a5780506471cbd32a3aced40
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171955"
---
# <a name="renderer-base-classes-and-native-controls"></a>Representador de clases Base y los controles nativos

_Todos los controles Xamarin.Forms tienen un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo. En este artículo se enumera las clases de control nativo que implementan cada página de Xamarin.Forms, diseño, vista y celda y el representador._

Con la excepción de la `MapRenderer` (clase), los representadores específicos de la plataforma pueden encontrarse en los espacios de nombres siguientes:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **Plataforma universal de Windows (UWP)** – Xamarin.Forms.Platform.UWP

La `MapRenderer` clase puede encontrarse en los espacios de nombres siguientes:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **Plataforma universal de Windows (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>Páginas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [página](~/xamarin-forms/user-interface/controls/pages.md) tipo:

|Página|Representador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|Grupo de vista||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS: teléfono), TabletMasterDetailPageRenderer (iOS – Tablet PC), MasterDetailRenderer (Android), MasterDetailPageRenderer (AppCompat Android), MasterDetailPageRenderer (UWP)|UIViewController (teléfono), UISplitViewController (Tablet PC)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (Control personalizado)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS y Android), NavigationPageRenderer (AppCompat Android), NavigationPageRenderer (UWP)|UIToolbar|Grupo de vista|Grupo de vista|FrameworkElement (Control personalizado)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS y Android), TabbedPageRenderer (AppCompat Android), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (dinámica)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|Grupo de vista||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Diseños

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [diseño](~/xamarin-forms/user-interface/controls/layouts.md) tipo:

|Diseño|Representador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|Grupo de vista|Borde|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|Ver|FrameworkElement|

## <a name="views"></a>Vistas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [vista](~/xamarin-forms/user-interface/controls/views.md) tipo:

|Vistas|Representador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS y Android), BoxViewRenderer (UWP)|UIView|Grupo de vista||Rectángulo|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Botón|AppCompatButton|Botón|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|Interfaz de usuario|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|Interfaz de usuario|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Imagen|
|`ImageButton`|ImageButtonRenderer|UIButton||AppCompatImageButton|Botón|
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|Mkmapview corriente|MapView||MapControl|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|Interfaz de usuario|EditText|EditText|ComboBox|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|Vista búsqueda||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Slider|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Control|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Modificador|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|Interfaz de usuario|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WebViewRenderer|UIWebView|WebView||WebView|

## <a name="cells"></a>Celdas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [celda](~/xamarin-forms/user-interface/controls/cells.md) tipo:

|Celdas|Representador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell con una interfaz de usuario|LinearLayout con una vista de texto y EditText|Plantilla de datos con un control TextBox|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell con un UISwitch|Modificador|Plantilla de datos con una cuadrícula que contiene un TextBlock y ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout con dos objetos de TextView|Plantilla de datos con un elemento StackPanel que contiene dos bloques de texto|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell con un UIImage|LinearLayout con objetos dos TextView y un ImageView|Plantilla de datos con una cuadrícula que contiene una imagen y dos bloques de texto|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Ver|Plantilla de datos con un ContentPresenter|

## <a name="summary"></a>Resumen

En este artículo se muestra las clases de control nativo que implementan cada página de Xamarin.Forms, diseño, vista y celda y el representador. Todos los controles Xamarin.Forms tienen un representador que lo acompaña para cada plataforma que crea una instancia de un control nativo.

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados (vídeo de Xamarin University)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
