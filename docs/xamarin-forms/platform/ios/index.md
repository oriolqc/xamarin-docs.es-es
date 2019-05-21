---
title: características de la plataforma de iOS de Xamarin.Forms
description: Agregar funcionalidad específica de iOS a las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/22/2019
ms.openlocfilehash: f77b2346808f78e182edc59f9ea92d0a8a99344f
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971017"
---
# <a name="ios-platform-features-in-xamarinforms"></a>características de la plataforma de iOS de Xamarin.Forms

Desarrollo de aplicaciones de Xamarin.Forms para iOS requiere Visual Studio. El [página requisitos](~/get-started/requirements.md) contiene más información sobre los requisitos previos.

## <a name="platform-specifics"></a>Funcionalidades específicas de plataforma

Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

La siguiente funcionalidad específica de la plataforma se proporciona para las vistas de Xamarin.Forms, páginas y diseños en iOS:

- Desenfoque ninguna compatibilidad con [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement desenfoque en iOS](visualelement-blur.md).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement modo de Color heredado en iOS](legacy-color-mode.md).
- Habilitación de una sombra paralela en un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement Drop sombras en iOS](visualelement-drop-shadow.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin.Forms en iOS:

- Establecer el [ `Cell` ](xref:Xamarin.Forms.Cell) color de fondo. Para obtener más información, consulte [Color de fondo de celda en iOS](cell-background-color.md).
- Asegurarse de que especifica el texto se ajusta a un [ `Entry` ](xref:Xamarin.Forms.Entry) ajustando el tamaño de fuente. Para obtener más información, consulte [tamaño de fuente de entrada en iOS](entry-font-size.md).
- Establecer el color de cursor de un [ `Entry` ](xref:Xamarin.Forms.Entry). Para obtener más información, consulte [Color de Cursor de movimiento en iOS](entry-cursor-color.md).
- Controlar si [ `ListView` ](xref:Xamarin.Forms.ListView) las celdas de encabezado float durante el desplazamiento. Para obtener más información, consulte [ListView el estilo del encabezado de grupo en iOS](listview-group-header-style.md).
- Controlar si las animaciones de fila están deshabilitadas cuando la [ `ListView` ](xref:Xamarin.Forms.ListView) se está actualizando la colección de elementos. Para obtener más información, consulte [ListView fila animaciones en iOS](listview-row-animations.md).
- Establecer el estilo del separador en un [ `ListView` ](xref:Xamarin.Forms.ListView). Para obtener más información, consulte [estilo del separador de ListView en iOS](listview-separator-style.md).
- Controlar cuándo se produce la selección de elementos en un [ `Picker` ](xref:Xamarin.Forms.Picker). Para obtener más información, consulte [selector de selección de elementos en iOS](picker-selection.md).
- Habilitar la [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad debe establecerse si pulsa en una posición en la [ `Slider` ](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar la `Slider` thumb. Para obtener más información, consulte [pulse de Thumb deslizante en iOS](slider-thumb.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin.Forms en iOS:

- Ocultar el separador de barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obtener más información, consulte [separador de barra NavigationPage en iOS](navigation-bar-separator.md).
- Controlar si la barra de navegación es translúcida. Para obtener más información, consulte [translucidez de barra de navegación en iOS](navigation-bar-translucent.md).
- Controlar si el texto de la barra de estado de color en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se ajusta para que coincida con la luminosidad de la barra de navegación. Para obtener más información, consulte [NavigationPage barra el modo de Color de texto en iOS](status-bar-text-color.md).
- Controlar si el título de página se muestra como un título en la barra de navegación de página grande. Para obtener más información, consulte [grandes títulos de página en iOS](page-large-title.md).
- Establecer la visibilidad del indicador principal en un [ `Page` ](xref:Xamarin.Forms.Page). Para obtener más información, consulte [inicio indicador visibilidad en iOS](page-home-indicator.md).
- Establecer la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page). Para obtener más información, consulte [visibilidad de barra de estado de página en iOS](page-status-bar-visibility.md).
- Garantizar ese contenido de la página se coloca en un área de la pantalla que sea seguro para todos los dispositivos iOS. Para obtener más información, consulte [Guía de diseño de área segura en iOS](page-safe-area-layout.md).
- Establecer el estilo de presentación de páginas modales en un iPad. Para obtener más información, consulte [iPad estilo de presentación de página Modal](ipad-page-presentation-style.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para los diseños de Xamarin.Forms en iOS:

- Controlar si un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) controla un movimiento táctil o pasa a su contenido. Para obtener más información, consulte [ScrollView toques contenido en iOS](scrollview-content-touches.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application) clase en iOS:

- Habilitación de diseño del control y la representación de las actualizaciones que se realizará en el subproceso principal. Para obtener más información, consulte [Main subprocesos controlar las actualizaciones en iOS](main-thread-updates-ui.md).
- Habilitar un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) en una vista desplazable para capturar y compartir el movimiento panorámico con la vista de desplazamiento. Para obtener más información, consulte [simultáneas reconocimiento de gestos de movimiento panorámico en iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>de formato específica de iOS

Xamarin.Forms permite estilos de la interfaz de usuario entre plataformas y los colores para establecerse - pero hay otras opciones para establecer el tema de su iOS mediante las API de plataforma en el proyecto de iOS.

[Obtenga más](formatting.md) acerca del formato de la interfaz de usuario mediante las API de iOS específicas, como **Info.plist** configuración y el `UIAppearance` API.

![](images/status-white-sml.png "Temas de iOS")

## <a name="other-ios-features"></a>Otras características de iOS

Mediante [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)y el [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), es posible incorporar una gran variedad de funciones nativas en Aplicaciones de Xamarin.Forms para iOS.
