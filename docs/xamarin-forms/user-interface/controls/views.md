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
ms.openlocfilehash: c5bafe12c2cf8c5f8d75757b22223c708ae248dc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="xamarinforms-views"></a>Vistas de Xamarin.Forms

_Xamarin.Forms vistas son los bloques de creación de interfaces de usuario móviles entre plataformas._

Las vistas son objetos de interfaz de usuario, como etiquetas y botones, controles deslizantes que se conocen normalmente como *controles* o *widgets* en otros entornos de programación de gráficos. Las vistas admitidas por Xamarin.Forms todos se derivan los [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) clase. Pueden dividirse en varias categorías:

## <a name="views-for-presentation"></a>Vistas de presentación

### <a name="label"></a>Etiqueta

|     |     |
| --- | --- |
| [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) Muestra las cadenas de texto de línea o bloques de varias líneas de texto, ya sea con formato constante o una variable. Establecer el [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propiedad en una cadena de constante de formato, o un conjunto el [ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/) propiedad a una [ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/) objeto de variable el formato.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) / [guía](~/xamarin-forms/user-interface/text/label.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/) | [![Etiquetar ejemplo](views-images/Label.png "etiquetar ejemplo")](views-images/Label-Large.png#lightbox "etiquetar ejemplo")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/LabelDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/LabelDemoPage.xaml) |
|     |     |

### <a name="image"></a>Imagen

|     |     |
| --- | --- |
| [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) muestra un mapa de bits. Mapas de bits se pueden descargar a través de Internet, incrustan como recursos en el proyecto común o proyectos de plataforma o creados mediante .NET `Stream` objeto.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) / [guía](~/xamarin-forms/user-interface/images.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/) | [![Ejemplo de la imagen](views-images/Image.png "la imagen de ejemplo")](views-images/Image-Large.png#lightbox "la imagen de ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageDemoPage.xaml) |
|     |     |

### <a name="boxview"></a>BoxView

|     |    |
| --- | ---|
| [`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) muestra un rectángulo relleno con color de la [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) propiedad. `BoxView` tiene una solicitud de tamaño predeterminado de 40 x 40. Para otros tamaños, asignar la [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) y [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) propiedades.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) / [guía](~/xamarin-forms/user-interface/boxview.md) / [ejemplo 1](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView), [2](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration), [3](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox), [4 ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife), [5](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock), y [6](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock) | [![Ejemplo de BoxView](views-images/BoxView.png "BoxView ejemplo")](views-images/BoxView-Large.png#lightbox "BoxView ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/BoxViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/BoxViewDemoPage.xaml) |
|     |     |

### <a name="webview"></a>WebView

|     |     |
| --- | --- |
| [`WebView`](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) Muestra las páginas Web o contenido HTML en función de si la [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) propiedad está establecida en un [ `UriWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UrlWebViewSource/) o un [ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/) objeto.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) / [guía](~/xamarin-forms/user-interface/webview.md) / [ejemplo 1](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/) y [2](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) | [![Ejemplo de WebView](views-images/WebView.png "ejemplo WebView")](views-images/WebView-Large.png#lightbox "ejemplo WebView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/WebViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/WebViewDemoPage.xaml) |
|     |     |

### <a name="openglview"></a>OpenGLView

|     |     |
| --- | --- |
| [`OpenGLView`](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/) Muestra gráficos OpenGL en iOS y Android proyectos. No hay ninguna compatibilidad para la plataforma Universal de Windows. IOS y Android proyectos requieren una referencia a la **OpenTK 1.0** ensamblado o el **OpenTK** ensamblado de la versión 1.0.0.0. `OpenGLView` es más fácil de usar en un proyecto compartido; Si se utilizan en una biblioteca PCL o .NET estándar, un servicio de dependencia también será necesario (como se muestra en el código de ejemplo).<br /><br />Se trata de la instalación de solo gráficos que está integrada en Xamarin.Forms, pero también puede representar una aplicación de Xamarin.Forms gráfico mediante [ `CocosSharp` ](~/xamarin-forms/user-interface/graphics/cocossharp.md), [ `SkiaSharp` ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md), o [ `UrhoSharp` ](~/xamarin-forms/user-interface/graphics/urhosharp.md).<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/)<br /><br /> | [![Ejemplo de OpenGLView](views-images/OpenGLView.png "OpenGLView ejemplo")](views-images/OpenGLView-Large.png#lightbox "OpenGLView ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/OpenGLViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/OpenGLViewDemoPage.xaml.cs) |
|     |     |

### <a name="map"></a>Asignación

|     |     |
| --- | --- |
| [`Map`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) muestra una asignación. El **Xamarin.Forms.Maps** debe estar instalado el paquete de Nuget. Android y plataforma Universal de Windows requieren una clave de autorización del mapa.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) / [guía](~/xamarin-forms/user-interface/map.md) / [ejemplo](https://developer.xamarin.com/samples/WorkingWithMaps/) | [![Asignar ejemplo](views-images/Map.png "asignar ejemplo")](views-images/Map-Large.png#lightbox "asignar ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MapDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MapDemoPage.xaml) |
|     |     |

## <a name="views-that-initiate-commands"></a>Vistas que inician comandos

### <a name="button"></a>Botón

|     |     |
| --- | --- |
| [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) es un objeto rectangular que muestra el texto, y que se activa un [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) evento cuando se ha presionado.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) | [![Ejemplo de botón](views-images/Button.png "botón ejemplo")](views-images/Button-Large.png#lightbox "ejemplo de botón")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ButtonDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ButtonDemoPage.xaml.cs) |
|     |     |

### <a name="searchbar"></a>SearchBar

|     |     |
| --- | --- |
| [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) muestra un área para el usuario escriba una cadena de texto y un botón (o una tecla del teclado) que indica a la aplicación para realizar una búsqueda. El [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.Text/) propiedad proporciona acceso al texto y el [ `SearchButtonPressed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/) evento indica que se ha presionado el botón.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) | [![Ejemplo de SearchBar](views-images/SearchBar.png "SearchBar ejemplo")](views-images/SearchBar-Large.png#lightbox "SearchBar ejemplo")<br /> [Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SearchBarDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SearchBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-for-setting-values"></a>Vistas para establecer valores 

### <a name="slider"></a>Slider

|     |     |
| --- | --- |
| [`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) permite al usuario seleccionar un `double` valor de un intervalo continuo especificado con el [ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) y [ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) propiedades.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) | [![Ejemplo de control deslizante](views-images/Slider.png "ejemplo de control deslizante")](views-images/Slider-Large.png#lightbox "ejemplo de control deslizante")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SliderDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SliderDemoPage.xaml) |
|     |     |

### <a name="stepper"></a>Paso a paso desencadene

|     |     |
| --- | --- |
| [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) permite al usuario seleccionar un `double` valor de un intervalo de valores incrementales especificados con la [ `Minimum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Minimum/), [ `Maximum` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Maximum/), y [ `Increment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) propiedades.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) | [![Ejemplo paso a paso desencadene](views-images/Stepper.png "ejemplo paso a paso desencadene")](views-images/Stepper-Large.png#lightbox "ejemplo paso a paso desencadene")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StepperDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StepperDemoPage.xaml) |
|     |     |

### <a name="switch"></a>Modificador 

|     |     |
| --- | --- |
| [`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) toma la forma de un conmutador de encendido/apagado para permitir al usuario seleccionar un valor booleano. El [ `IsToggled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) propiedad es el estado del conmutador y la [ `Toggled` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) evento se desencadena cuando cambia el estado.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) | [![Cambiar en el ejemplo se](views-images/Switch.png "cambiar ejemplo")](views-images/Stepper-Large.png#lightbox "cambiar ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchDemoPage.xaml) |
|     |     |

### <a name="datepicker"></a>DatePicker

|     |     |
| --- | --- |
| [`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) permite al usuario seleccionar una fecha con el selector de fecha de la plataforma. Establecer un intervalo de fechas permitidos con el [ `MinimumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) y [ `MaximumDate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) propiedades. El [ `Date` ](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) propiedad es la fecha seleccionada y el [ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/) evento se desencadena cuando se cambia dicha propiedad.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) | [![Ejemplo de DatePicker](views-images/DatePicker.png "ejemplo DatePicker")](views-images/DatePicker-Large.png#lightbox "ejemplo DatePicker")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/DatePickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/DatePickerDemoPage.xaml) |
|     |     |

### <a name="timepicker"></a>TimePicker

|     |     |
| --- | --- |
| [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) permite al usuario seleccionar una hora con el selector de tiempo de la plataforma. El [ `Time` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) propiedad es el tiempo seleccionado. Una aplicación puede supervisar los cambios en el `Time` propiedad mediante la instalación de un controlador para el [ `PropertyChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.PropertyChanged/) eventos.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) | [![En el ejemplo se TimePicker](views-images/TimePicker.png "ejemplo TimePicker")](views-images/TimePicker-Large.png#lightbox "ejemplo TimePicker")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TimePickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TimePickerDemoPage.xaml) |
|     |     |

## <a name="views-for-editing-text"></a>Vistas para editar texto

Estas dos clases se derivan de la [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/) (clase), que define la [ `Keyboard` ](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) propiedad.

<a name="entry" />

### <a name="entry"></a>Entrada

|     |     |
| --- | --- |
| [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) permite al usuario escribir y editar una sola línea de texto. El texto está disponible como el [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) propiedad y el [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) y [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/) los eventos se activan cuando los cambios de texto o el usuario señales de finalización al pulsar la tecla ENTRAR.<br /><br />Use un [ `Editor` ](#editor) para escribir y editar varias líneas de texto.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) / [guía](~/xamarin-forms/user-interface/text/entry.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Ejemplo de entrada de](views-images/Entry.png "ejemplo de entrada de")](views-images/Entry-Large.png#lightbox "ejemplo de entrada")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryDemoPage.xaml) |
|     |     |

<a name="editor" />

### <a name="editor"></a>Editor

|     |     |
| --- | --- |
| [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) permite al usuario escribir y editar varias líneas de texto. El texto está disponible como el [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Editor.Text/) propiedad y el [ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/) y [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/) los eventos se activan cuando los cambios de texto o el usuario envía una señal de finalización.<br /><br />Use un [ `Entry` ](#entry) vista para escribir y editar una sola línea de texto.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) / [guía](~/xamarin-forms/user-interface/text/editor.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text) | [![Ejemplo de entrada de](views-images/Editor.png "ejemplo Editor")](views-images/Editor-Large.png#lightbox "Editor de ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EditorDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EditorDemoPage.xaml) |
|     |     |

## <a name="views-to-indicate-activity"></a>Vistas para indicar la actividad

<a name="activityindicator" />

### <a name="activityindicator"></a>ActivityIndicator

|     |     |
| --- | --- |
| [`ActivityIndicator`](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) usa una animación para mostrar que la aplicación esté implicada en una actividad larga sin dar ninguna indicación de progreso. El [ `IsRunning` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ActivityIndicator.IsRunning/) propiedad controla la animación.<br /><br />Si se conoce el progreso de la actividad, use un [ `ProgressBar` ](#progressbar) en su lugar.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/) | [![Ejemplo de ActivityIndicator](views-images/ActivityIndicator.png "ActivityIndicator ejemplo")](views-images/ActivityIndicator-Large.png#lightbox "ActivityIndicator ejemplo")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ActivityIndicatorDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ActivityIndicatorDemoPage.xaml) |
|     |     |

<a name="progressbar" />

### <a name="progressbar"></a>ProgressBar

|     |     |
| --- | --- |
| [`ProgressBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) usa una animación para mostrar que la aplicación está progresando a través de una actividad larga. Establecer el [ `Progress` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ProgressBar.Progress/) propiedad con valores entre 0 y 1 para indicar el progreso.<br /><br />Si se desconoce el progreso de la actividad, use un [ `ActivityIndicator` ](#activityindicator) en su lugar.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/) | [![Ejemplo de barra de progreso](views-images/ProgressBar.png "ejemplo ProgressBar")](views-images/ProgressBar-Large.png#lightbox "ejemplo de barra de progreso")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ProgressBarDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ProgressBarDemoPage.xaml.cs) |
|     |     |

## <a name="views-that-display-collections"></a>Vistas que muestran colecciones

### <a name="picker"></a>Selector de

|     |     |
| --- | --- |
| [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) muestra un elemento seleccionado de una lista de cadenas de texto y permite al seleccionar este elemento al que se derivan la vista. Establecer el [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) propiedad a una lista de cadenas, o la [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) propiedad a una colección de objetos. El [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) evento se desencadena cuando se selecciona un elemento.<br /><br />La `Picker` muestra la lista de elementos solo cuando se selecciona. Use un [ `ListView` ](#listView) o [ `TableView` ](#tableView) para obtener una lista desplazable que permanece en la página.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) / [guía](~/xamarin-forms/user-interface/picker/index.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/) | [![Ejemplo de selector](views-images/Picker.png "ejemplo selector")](views-images/Picker-Large.png#lightbox "ejemplo selector")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/PickerDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml) con [código subyacente](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/PickerDemoPage.xaml.cs) |
|     |     |

<a name="listView" />

### <a name="listview"></a>ListView

|     |     |
| --- | --- |
| [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) se deriva de [ `ItemsView[Cell]` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/) y muestra una lista desplazable de elementos de datos seleccionable. Establecer el [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) propiedad a una colección de objetos y establezca el [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) propiedad a una [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) objeto que describe cómo los elementos son para tener el formato. El [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) evento indica que se ha realizado una selección, que está disponible como el [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) propiedad.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) / [guía](~/xamarin-forms/user-interface/listview/index.md) / [ejemplo](https://developer.xamarin.com/samples/WorkingWithListview) | [![Ejemplo de ListView](views-images/ListView.png "ejemplo de ListView")](views-images/ListView-Large.png#lightbox "ejemplo de ListView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ListViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ListViewDemoPage.xaml) |
|     |     |

<a name="tableView" />

### <a name="tableview"></a>TableView

|     |     |
| --- | --- |
| [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) muestra una lista de filas de tipo [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) con encabezados opcionales y subcabeceras. Establecer el [ `Root` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/) propiedad a un objeto de tipo [ `TableRoot` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/)y agregue [ `TableSection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/) objetos a los que `TableRoot`. Cada `TableSection` es una colección de `Cell` objetos.<br /><br />[Documentación de la API](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) / [guía](~/xamarin-forms/user-interface/tableview.md) / [ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView) | [![En el ejemplo se TableView](views-images/TableView.png "ejemplo TableView")](views-images/TableView-Large.png#lightbox "ejemplo TableView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TableViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TableViewDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Ejemplo de Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Documentación de API de Xamarin.Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
