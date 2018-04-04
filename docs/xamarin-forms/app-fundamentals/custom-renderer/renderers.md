---
title: Representador de clases Base y los controles nativos
description: Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Este artículo enumeran las clases de control nativo que implementan cada página Xamarin.Forms, diseño, vista y celda y el representador.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2016
ms.openlocfilehash: 9402bd53ab3bfb0b11182eb700aa560e8f962de3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="renderer-base-classes-and-native-controls"></a>Representador de clases Base y los controles nativos

_Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Este artículo enumeran las clases de control nativo que implementan cada página Xamarin.Forms, diseño, vista y celda y el representador._

Con la excepción de la `MapRenderer` (clase), el representador específico de la plataforma no se encuentra en los espacios de nombres siguientes:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** : Xamarin.Forms.Platform.Android.AppCompat
- **Plataforma universal de Windows (UWP)** : Xamarin.Forms.Platform.UWP

La `MapRenderer` clase puede encontrarse en los espacios de nombres siguientes:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **Plataforma universal de Windows (UWP)** : Xamarin.Forms.Maps.UWP

## <a name="pages"></a>Páginas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [página](~/xamarin-forms/user-interface/controls/pages.md) tipo:

|Página|Representador de|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|Vista ViewGroup||FrameworkElement|
|[`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)|PhoneMasterDetailRenderer (iOS: teléfono), TabletMasterDetailPageRenderer (iOS: Tablet PC), MasterDetailRenderer (Android), MasterDetailPageRenderer (AppCompat Android), MasterDetailPageRenderer (UWP)|UIViewController (teléfono), UISplitViewController (Tablet PC)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (Control personalizado)|
|[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)|NavigationRenderer (iOS y Android), NavigationPageRenderer (AppCompat Android), NavigationPageRenderer (UWP)|UIToolbar|Vista ViewGroup|Vista ViewGroup|FrameworkElement (Control personalizado)|
|[`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)|TabbedRenderer (iOS y Android), TabbedPageRenderer (AppCompat Android), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (dinámica)|
|[`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/)|PageRenderer|UIViewController|Vista ViewGroup||FrameworkElement|
|[`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Diseños de

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [diseño](~/xamarin-forms/user-interface/controls/layouts.md) tipo:

|Diseño|Representador de|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`Frame`](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)|FrameRenderer|UIView|Vista ViewGroup|Borde|
|[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)|ViewRenderer|UIView|Ver|FrameworkElement|
|[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)|ViewRenderer|UIView|Ver|FrameworkElement|

## <a name="views"></a>Vistas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [vista](~/xamarin-forms/user-interface/controls/views.md) tipo:

|Vistas|Representador de|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)|BoxRenderer (iOS y Android), BoxViewRenderer (Windows Phone y WinRT)|UIView|Vista ViewGroup||Rectángulo|
|[`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)|ButtonRenderer|UIButton|Botón|AppCompatButton|Botón|
|[`CarouselView`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/)|CarouselViewRenderer|UIScrollView|RecyclerView||FlipView|
|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)|ImageRenderer|UIImageView|ImageView||Imagen|
|[`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|SearchBarRenderer|UISearchBar|Vista búsqueda||AutoSuggestBox|
|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)|SliderRenderer|UISlider|SeekBar||Slider|
|[`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|StepperRenderer|UIStepper|LinearLayout||Control|
|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|SwitchRenderer|UISwitch|Modificador|SwitchCompat|ToggleSwitch|
|[`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)|WebViewRenderer|UIWebView|WebView||WebView|

## <a name="cells"></a>Celdas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [celda](~/xamarin-forms/user-interface/controls/cells.md) tipo:

|Celdas|Representador de|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/)|EntryCellRenderer|UITableViewCell con un UITextField|LinearLayout con un TextView y EditText|Plantilla de datos con un cuadro de texto|
|[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/)|SwitchCellRenderer|UITableViewCell con un UISwitch|Modificador|Plantilla de datos con una cuadrícula que contiene un TextBlock y ToggleSwitch|
|[`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/)|TextCellRenderer|UITableViewCell|LinearLayout con dos TextViews|Plantilla de datos con un StackPanel que contiene dos TextBlocks|
|[`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/)|ImageCellRenderer|UITableViewCell con un UIImage|LinearLayout con dos TextViews y un ImageView|Plantilla de datos con una cuadrícula que contiene una imagen y dos TextBlocks|
|[`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Ver|Plantilla de datos con un ContentPresenter|

## <a name="summary"></a>Resumen

En este artículo se enumeró el representador y las clases de control nativo que implementan cada página Xamarin.Forms, diseño, vista y celda. Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo.

## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados (vídeo de la Universidad de Xamarin)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
