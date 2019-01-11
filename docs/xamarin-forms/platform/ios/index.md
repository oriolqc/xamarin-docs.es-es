---
title: Características de la plataforma iOS
description: Agregar funcionalidad específica de iOS a las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
ms.openlocfilehash: e5ec152032a068c0c1e83c8df5e6f128bfa30c83
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207770"
---
# <a name="ios-platform-features"></a>Características de la plataforma iOS

Desarrollo de aplicaciones de Xamarin.Forms para iOS requiere Visual Studio. El [página requisitos](~/xamarin-forms/get-started/installation.md) contiene más información sobre los requisitos previos.

## <a name="platform-specifics"></a>Funcionalidades específicas de plataforma

Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

La siguiente funcionalidad específica de la plataforma se proporciona para las vistas de Xamarin.Forms, páginas y diseños en iOS:

- Desenfoque ninguna compatibilidad con [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement desenfoque en iOS](visualelement-blur.md).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement modo de Color heredado en iOS](legacy-color-mode.md).
- Habilitación de una sombra paralela en un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement Drop sombras en iOS](visualelement-drop-shadow.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin.Forms en iOS:

- Asegurarse de que especifica el texto se ajusta a un [ `Entry` ](xref:Xamarin.Forms.Entry) ajustando el tamaño de fuente. Para obtener más información, consulte [tamaño de fuente de entrada en iOS](entry-font-size.md).
- Establecer el color de cursor de un [ `Entry` ](xref:Xamarin.Forms.Entry). Para obtener más información, consulte [Color de Cursor de movimiento en iOS](entry-cursor-color.md).
- Establecer el estilo del separador en un [ `ListView` ](xref:Xamarin.Forms.ListView). Para obtener más información, consulte [estilo del separador de ListView en iOS](listview-separator-style.md).
- Controlar cuándo se produce la selección de elementos en un [ `Picker` ](xref:Xamarin.Forms.Picker). Para obtener más información, consulte [selector de selección de elementos en iOS](picker-selection.md).
- Habilitar la [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad debe establecerse si pulsa en una posición en la [ `Slider` ](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar la `Slider` thumb. Para obtener más información, consulte [pulse de Thumb deslizante en iOS](slider-thumb.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin.Forms en iOS:

- Ocultar el separador de barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obtener más información, consulte [separador de barra NavigationPage en iOS](navigation-bar-separator.md).
- Controlar si la barra de navegación es translúcida. Para obtener más información, consulte [translucidez de barra de navegación en iOS](navigation-bar-translucent.md).
- Controlar si el texto de la barra de estado de color en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se ajusta para que coincida con la luminosidad de la barra de navegación. Para obtener más información, consulte [NavigationPage barra el modo de Color de texto en iOS](status-bar-text-color.md).
- Controlar si el título de página se muestra como un título en la barra de navegación de página grande. Para obtener más información, consulte [grandes títulos de página en iOS](page-large-title.md).
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
