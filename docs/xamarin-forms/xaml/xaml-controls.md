---
title: Controles de XAML
description: Todas las vistas que se definen en Xamarin.Forms pueden hacer referencia a partir de los archivos XAML.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/03/2019
ms.openlocfilehash: a8a61ac505eab8c458c49bde9184d6e96583d37f
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "59020267"
---
# <a name="xaml-controls"></a>Controles de XAML

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/FormsGallery/)

Las vistas son objetos de interfaz de usuario, como etiquetas, botones y los controles deslizantes que se conocen normalmente como *controles* o *widgets* en otros entornos de programación de gráficos. Las vistas compatibles con Xamarin.Forms todos se derivan los [ `View` ](xref:Xamarin.Forms.View) clase.

Todas las vistas que se definen en Xamarin.Forms pueden hacer referencia a partir de los archivos XAML.

## <a name="views-for-presentation"></a>Vistas de presentación

|     |     |
| --- | --- |
| <h3>BoxView</h3>Muestra un rectángulo de un color determinado.<p align="center">![Captura de pantalla de un BoxView](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView) / [guía](https://developer.xamarin.com/guides/xamarin-forms/user-interface/boxview/) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>Imagen</h3>muestra un mapa de bits.<p align="center">![Captura de pantalla de una imagen](xaml-controls-images/Image.png "imagen")</p>[API](xref:Xamarin.Forms.Image) / [guía](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Etiqueta</h3>Muestra una o varias líneas de texto.<p align="center">![Captura de pantalla de una etiqueta](xaml-controls-images/Label.png "etiqueta")</p>[API](xref:Xamarin.Forms.Label) / [guía](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>Asignación</h3>muestra un mapa.<p align="center">![Captura de pantalla de un mapa](xaml-controls-images/Map.png "mapa")</p>[API](xref:Xamarin.Forms.Maps.Map) / [guía](~/xamarin-forms/user-interface/map.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>Muestra las páginas Web o contenido HTML.<p align="center">![Captura de pantalla de un objeto WebView](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView) / [guía](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>Vistas que inician comandos

|     |     |
| --- | --- |
| <h3>Botón</h3>Muestra el texto en un objeto rectangular.<p align="center">![Captura de pantalla de un botón](xaml-controls-images/Button.png "botón")</p>[API](xref:Xamarin.Forms.Button) / [guía](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>Muestra una imagen de un objeto rectangular.<p align="center">![Captura de pantalla de un control ImageButton](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton) / [guía](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>Barra de búsqueda</h3>Muestra una barra de búsqueda, para realizar una búsqueda.<p align="center">![Captura de pantalla de un SearchBar](xaml-controls-images/SearchBar.png "SearchBar")</p>[API](xref:Xamarin.Forms.SearchBar) | <p valign="center"><pre>&lt;SearchBar Placeholder="Xamarin.Forms Property"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>Para establecer valores de las vistas

|     |     |
| --- | --- |
| <h3>Slider</h3>Permite la selección de un `double` valor desde un intervalo continuo.<p align="center">![Captura de pantalla de un control deslizante](xaml-controls-images/Slider.png "control deslizante")</p>[API](xref:Xamarin.Forms.Slider) / [guía](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Control de incremento</h3>Permite la selección de un `double` valor a partir de un intervalo incremental.<p align="center">![Captura de pantalla de un motor paso a paso](xaml-controls-images/Stepper.png "motor paso a paso")</p>[API](xref:Xamarin.Forms.Stepper) / [guía](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Modificador</h3>Permite la selección de un `boolean` valor.<p align="center">![Captura de pantalla de un conmutador](xaml-controls-images/Switch.png "conmutador")</p>[API](xref:Xamarin.Forms.Switch) | <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>Permite la selección de una fecha.<p align="center">![Captura de pantalla de un DatePicker](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker) / [guía](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>Permite la selección de una hora.<p align="center">![Captura de pantalla de un objeto TimePicker](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker) / [guía](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>Vistas para editar texto

|     |     |
| --- | --- |
| <h3>Entrada</h3>Permite que una sola línea de texto que se especifique y se puede editar.<p align="center">![Captura de pantalla de una entrada](xaml-controls-images/Entry.png "entrada")</p>[API](xref:Xamarin.Forms.Entry) / [guía](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Editor</h3>Permite varias líneas de texto que se especifique y se puede editar.<p align="center">![Captura de pantalla de un Editor](xaml-controls-images/Editor.png "etiqueta")</p>[API](xref:Xamarin.Forms.Editor) / [guía](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>Vistas para indicar actividad

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>Muestra una animación para mostrar que la aplicación esté implicada en una actividad larga, sin dar ninguna indicación del progreso.<p align="center">![Captura de pantalla de un ActivityIndicator](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>Muestra una animación para mostrar que la aplicación está progresando a través de una actividad larga.<p align="center">![Captura de pantalla de ProgressBar](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>Vistas que muestran colecciones

|     |     |
| --- | --- |
| <h3>CollectionView</h3>muestra una lista desplazable de elementos de datos seleccionable, utilizando las especificaciones de diseño diferente.<p align="center">![Captura de pantalla de una colección mediante CollectionView](xaml-controls-images/CollectionView.png "CollectionView")</p>[Guía](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>ListView</h3>Muestra una lista desplazable de elementos de datos seleccionable.<p align="center">![Captura de pantalla de un ListView](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView) / [guía](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>Selector</h3>Muestra un elemento select de una lista de cadenas de texto.<p align="center">![Captura de pantalla de un selector de](xaml-controls-images/Picker.png "selector")</p>[API](xref:Xamarin.Forms.Picker) / [guía](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>Muestra una lista de filas interactivas.<p align="center">![Captura de pantalla de un objeto TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView) / [guía](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de Xamarin.Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms Samples](https://developer.xamarin.com/samples/xamarin-forms/all/) (Ejemplos de Xamarin.Forms)
- [Documentación de la API de Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
