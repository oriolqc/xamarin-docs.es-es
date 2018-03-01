---
title: Representador de clases Base y los controles nativos
description: "Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Este artículo enumeran las clases de control nativo que implementan cada página Xamarin.Forms, diseño, vista y celda y el representador."
ms.topic: article
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2016
ms.openlocfilehash: 95518d9b23db68cc972549c730deeea968512444
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="renderer-base-classes-and-native-controls"></a>Representador de clases Base y los controles nativos

_Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo. Este artículo enumeran las clases de control nativo que implementan cada página Xamarin.Forms, diseño, vista y celda y el representador._

Con la excepción de la `MapRenderer` (clase), el representador específico de la plataforma no se encuentra en los espacios de nombres siguientes:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** : Xamarin.Forms.Platform.Android.AppCompat
- **Windows Phone 8** – Xamarin.Forms.Platform.WinPhone
- **WinRT** – Xamarin.Forms.Platform.WinRT
- **Plataforma universal de Windows (UWP)** : Xamarin.Forms.Platform.UWP

La `MapRenderer` clase puede encontrarse en los espacios de nombres siguientes:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **Windows Phone 8** – Xamarin.Forms.Maps.WP8
- **WinRT** – Xamarin.Forms.Maps.WinRT
- **Plataforma universal de Windows (UWP)** : Xamarin.Forms.Maps.UWP

## <a name="pages"></a>Páginas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [página](~/xamarin-forms/user-interface/controls/pages.md) tipo:

<table>
 <thead>
   <tr>
     <td><strong>página</strong></td>
     <td><strong>Representador de</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Android (AppCompat)</strong></td>
     <td><strong>Windows Phone 8 < /strong</td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md">PageRenderer</a></td>
     <td>UIViewController</td>
     <td>Vista ViewGroup</td>
     <td></td>
     <td>Panel</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/">MasterDetailPage</a></td>
     <td><p>PhoneMasterDetailRenderer (iOS: teléfono)</p><p>TabletMasterDetailPageRenderer (iOS: Tablet PC)</p><p>MasterDetailRenderer (Android)</p><p>MasterDetailPageRenderer (AppCompat Android)</p><p>MasterDetailRenderer (Windows Phone)</p><p>MasterDetailPageRenderer (WinRT)</p></td>
     <td><p>UIViewController (Phone)</p><p>UISplitViewController (Tablet PC)</p></td>
     <td>DrawerLayout (v4)</td>
     <td>DrawerLayout (v4)</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement (Control personalizado)</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/">NavigationPage</a></td>
     <td><p>NavigationRenderer (iOS y Android)</p><p>NavigationPageRenderer (AppCompat Android)</p><p>NavigationPageRenderer (Windows Phone 8 y WinRT)</p></td>
     <td>UIToolbar</td>
     <td>Vista ViewGroup</td>
     <td>Vista ViewGroup</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement (Control personalizado)</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/">TabbedPage</a></td>
     <td><p>TabbedRenderer (iOS y Android)</p><p>TabbedPageRenderer (AppCompat Android)</p><p>TabbedPageRenderer (Windows Phone 8 y WinRT)</p></td>
     <td>UIView</td>
     <td>ViewPager</td>
     <td>ViewPager</td>
     <td>UIElement (dinámica)</td>
     <td>FrameworkElement (dinámica)</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/">TemplatedPage</a></td>
     <td>PageRenderer</td>
     <td>UIViewController</td>
     <td>Vista ViewGroup</td>
     <td></td>
     <td>Panel</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/">CarouselPage<a/></td>
     <td>CarouselPageRenderer</td>
     <td>UIScrollView</td>
     <td>ViewPager</td>
     <td>ViewPager</td>
     <td>UIElement (panorámica)</td>
     <td>FrameworkElement (FlipView)</td>
   </tr>
 </tbody>
</table>

## <a name="layouts"></a>Diseños de

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [diseño](~/xamarin-forms/user-interface/controls/layouts.md) tipo:

<table>
 <thead>
   <tr>
     <td><strong>Diseño</strong></td>
     <td><strong>Representador de</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/">ContentPresenter</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Ver</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/">ContentView</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Ver</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/">Frame</a></td>
     <td>FrameRenderer</td>
     <td>UIView</td>
     <td>Vista ViewGroup</td>
     <td>Borde</td>
     <td>Borde</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a></td>
     <td>ScrollViewRenderer</td>
     <td>UIScrollView</td>
     <td>ScrollView</td>
     <td>ScrollViewer</td>
     <td>ScrollViewer</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/">TemplatedView</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Ver</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/">AbsoluteLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Ver</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/">Grid</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Ver</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/">RelativeLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Ver</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a></td>
     <td>ViewRenderer</td>
     <td>UIView</td>
     <td>Ver</td>
     <td>FrameworkElement</td>
     <td>FrameworkElement</td>
   </tr>
 </tbody>
</table>

## <a name="views"></a>Vistas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [vista](~/xamarin-forms/user-interface/controls/views.md) tipo:

<table>
 <thead>
   <tr>
     <td><strong>Vistas</strong></td>
     <td><strong>Representador de</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Android (AppCompat)</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/">ActivityIndicator</a></td>
     <td>ActivityIndicatorRenderer</td>
     <td>UIActivityIndicator</td>
     <td>ProgressBar</td>
     <td></td>
     <td>ProgressBar</td>
     <td>ProgressBar</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/">BoxView</a></td>
     <td><p>BoxRenderer (iOS y Android)</p><p>BoxViewRenderer (Windows Phone y WinRT)</p></td>
     <td>UIView</td>
     <td>Vista ViewGroup</td>
     <td></td>
     <td>Rectángulo</td>
     <td>Rectángulo</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Button/">Button</a></td>
     <td>ButtonRenderer</td>
     <td>UIButton</td>
     <td>Botón</td>
     <td>AppCompatButton</td>
     <td>Botón</td>
     <td>Botón</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/">CarouselView</a></td>
     <td>CarouselViewRenderer</td>
     <td>UIScrollView</td>
     <td>RecyclerView</td>
     <td></td>
     <td>FlipView</td>
     <td>FlipView</td>
   </tr>   
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/">DatePicker</a></td>
     <td>DatePickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>DatePicker</td>
     <td>DatePicker</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/">Editor</a></td>
     <td>EditorRenderer</td>
     <td>UITextView</td>
     <td>EditText</td>
     <td></td>
     <td>TextBox</td>
     <td>TextBox</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">Entrada</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/entry.md">EntryRenderer</a></td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>PhoneTextBox/PasswordBox</td>
     <td>TextBox</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Image</a></td>
     <td>ImageRenderer</td>
     <td>UIImageView</td>
     <td>ImageView</td>
     <td></td>
     <td>Imagen</td>
     <td>Imagen</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Label/">Label</a></td>
     <td>LabelRenderer</td>
     <td>UILabel</td>
     <td>TextView</td>
     <td></td>
     <td>TextBlock</td>
     <td>TextBlock</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">ListView</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/listview.md">ListViewRenderer</a></td>
     <td>UITableView</td>
     <td>ListView</td>
     <td></td>
     <td>LongListSelector</td>
     <td>ListView</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/">Map</a></td>
     <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md">MapRenderer</a></td>
     <td>MKMapView</td>
     <td>MapView</td>
     <td></td>
     <td>Asignación</td>
     <td>MapControl</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/">Picker</a></td>
     <td>PickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td>EditText</td>
     <td>FrameworkElement</td>
     <td>ComboBox</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/">Barra de progreso</a></td>
     <td>ProgressBarRenderer</td>
     <td>UIProgressView</td>
     <td>ProgressBar</td>
     <td></td>
     <td>ProgressBar</td>
     <td>ProgressBar</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/">SearchBar</a></td>
     <td>SearchBarRenderer</td>
     <td>UISearchBar</td>
     <td>Vista búsqueda</td>
     <td></td>
     <td>PhoneTextBox</td>
     <td><p>Searchbox. (WinRT)</p><p>AutoSuggestBox (UWP)</p></td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/">Slider</a></td>
     <td>SliderRenderer</td>
     <td>UISlider</td>
     <td>SeekBar</td>
     <td></td>
     <td>Slider</td>
     <td>Slider</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/">Stepper</a></td>
     <td>StepperRenderer</td>
     <td>UIStepper</td>
     <td>LinearLayout</td>
     <td></td>
     <td>Borde con un StackPanel y dos botones</td>
     <td><p>UserControl (WinRT)</p><p>Control (UWP)</p></td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/">Switch</a></td>
     <td>SwitchRenderer</td>
     <td>UISwitch</td>
     <td>Modificador</td>
     <td>SwitchCompat</td>
     <td>Borde con un ToggleSwitchButton</td>
     <td>ToggleSwitch</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView</a></td>
     <td>TableViewRenderer</td>
     <td>UITableView</td>
     <td>ListView</td>
     <td></td>
     <td>Cuadrícula con un bloque de texto y cuadro de lista</td>
     <td>ListView</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/">TimePicker</a></td>
     <td>TimePickerRenderer</td>
     <td>UITextField</td>
     <td>EditText</td>
     <td></td>
     <td>TimePicker</td>
     <td>TimePicker</td>
   </tr>
   <tr>
     <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">WebView</a></td>
     <td>WebViewRenderer</td>
     <td>UIWebView</td>
     <td>WebView</td>
     <td></td>
     <td>ExploradorWeb</td>
     <td>WebView</td>
   </tr>
 </tbody>
</table>

## <a name="cells"></a>Celdas

En la tabla siguiente se enumera las clases de control nativo que implementan cada Xamarin.Forms y representador [celda](~/xamarin-forms/user-interface/controls/cells.md) tipo:

<table>
 <thead>
   <tr>
     <td><strong>Cells</strong></td>
     <td><strong>Representador de</strong></td>
     <td><strong>iOS</strong></td>
     <td><strong>Android</strong></td>
     <td><strong>Windows Phone 8</strong></td>
     <td><strong>WinRT / UWP</strong></td>
   </tr>
 </thead>
 <tbody>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/">EntryCell</a></td>
   <td>EntryCellRenderer</td>
   <td>UITableViewCell con un UITextField</td>
   <td>LinearLayout con un TextView y EditText</td>
   <td>Plantilla de datos con una cuadrícula que contiene un bloque de texto y un PhoneTextBox</td>
   <td>Plantilla de datos con un cuadro de texto</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/">SwitchCell</a></td>
   <td>SwitchCellRenderer</td>
   <td>UITableViewCell con un UISwitch</td>
   <td>Modificador</td>
   <td>Plantilla de datos con un ToggleSwitch</td>
   <td>Plantilla de datos con una cuadrícula que contiene un TextBlock y ToggleSwitch</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">TextCell</a></td>
   <td>TextCellRenderer</td>
   <td>UITableViewCell</td>
   <td>LinearLayout con dos TextViews</td>
   <td>Plantilla de datos con un botón que contiene un elemento StackPanel con dos TextBlocks</td>
   <td>Plantilla de datos con un StackPanel que contiene dos TextBlocks</td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/">ImageCell</a></td>
   <td>ImageCellRenderer</td>
   <td>UITableViewCell con un UIImage</td>
   <td>LinearLayout con dos TextViews y un ImageView</td>
   <td>Plantilla de datos con un botón que contiene una cuadrícula con una imagen y un StackPanel que contiene dos TextBlocks</td>
   <td>Plantilla de datos con una cuadrícula que contiene una imagen y dos TextBlocks</td>  
   </td>
 </tr>
 <tr>
   <td><a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/">ViewCell</a></td>
   <td><a href="~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md">ViewCellRenderer</a></td>
   <td>UITableViewCell</td>
   <td>Ver</td>
   <td>Plantilla de datos con un ContentPresenter</td>
   <td>Plantilla de datos con un ContentPresenter</td>  
   </td>
 </tr>
 </tbody>
</table>

## <a name="summary"></a>Resumen

En este artículo se enumeró el representador y las clases de control nativo que implementan cada página Xamarin.Forms, diseño, vista y celda. Cada control Xamarin.Forms tiene un representador que lo acompañan para cada plataforma que crea una instancia de un control nativo.



## <a name="related-links"></a>Vínculos relacionados

- [Representadores personalizados (vídeo de la Universidad de Xamarin)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
