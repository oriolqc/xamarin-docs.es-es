---
title: Características de la plataforma Android
description: En este artículo se explica cómo agregar la funcionalidad específica de Android para las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
ms.openlocfilehash: dc02fdc8754db4ae97c29ba2a496804b2263abdc
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970728"
---
# <a name="android-platform-features"></a>Características de la plataforma Android

Desarrollo de aplicaciones de Xamarin.Forms para Android requiere Visual Studio. El [página requisitos](~/get-started/requirements.md) contiene más información sobre los requisitos previos.

## <a name="platform-specifics"></a>Funcionalidades específicas de plataforma

Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

La siguiente funcionalidad específica de la plataforma se proporciona para los diseños en Android, páginas y las vistas de Xamarin.Forms:

- Controlar el orden Z de los elementos visuales para determinar el orden de dibujo. Para obtener más información, consulte [VisualElement elevación en Android](visualelement-elevation.md).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement modo de Color heredado en Android](legacy-color-mode.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin.Forms en Android:

- Utilizando los valores de la sombra de los botones de Android y el relleno predeterminado. Para obtener más información, consulte [relleno de botón y las sombras en Android](button-padding-shadow.md).
- Establecer el método de entrada de opciones del editor para el teclado en pantalla para una [ `Entry` ](xref:Xamarin.Forms.Entry). Para obtener más información, consulte [opciones del Editor de métodos de entrada de entrada en Android](entry-ime-options.md).
- Habilitación de una sombra paralela en un `ImageButton`. Para obtener más información, consulte [ImageButton Drop sombras en Android](imagebutton-drop-shadow.md).
- Habilitar el desplazamiento rápido en un [ `ListView` ](xref:Xamarin.Forms.ListView) para obtener más información, consulte [desplazamiento rápido ListView en Android](listview-fast-scrolling.md).
- Controlar si un [ `WebView` ](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto. Para obtener más información, consulte [WebView al contenido mixto en Android](webview-mixed-content.md).
- Habilitar zoom en un [ `WebView` ](xref:Xamarin.Forms.WebView). Para obtener más información, consulte [WebView Zoom en Android](webview-zoom-controls.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin.Forms en Android:

- Establecer el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obtener más información, consulte [alto de la barra de NavigationPage en Android](navigationpage-bar-height.md).
- Deshabilitar las animaciones de transición al navegar a través de páginas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, consulte [animaciones de transición de página TabbedPage en Android](tabbedpage-transition-animations.md).
- Habilitar el gesto de deslizar rápidamente entre las páginas de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, consulte [TabbedPage página desplazándose en Android](tabbedpage-page-swiping.md).
- Establecer la posición de la barra de herramientas y el color en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, consulte [TabbedPage barra de herramientas ubicación y Color en Android](tabbedpage-toolbar-placement-color.md).

Se proporciona la siguiente funcionalidad específica de la plataforma para Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application) clase en Android:

- Establecer el modo de funcionamiento de un teclado en pantalla. Para obtener más información, consulte [el modo de entrada de teclado temporal en Android](soft-keyboard-input-mode.md).
- Deshabilitar la [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) y [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) página eventos de ciclo de vida en pausa y reanudar, respectivamente, para las aplicaciones que usan AppCompat. Para obtener más información, consulte [eventos de ciclo de vida de página en Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Compatibilidad con la plataforma

Originalmente, el valor predeterminado de proyecto de Xamarin.Forms para Android usa un estilo anterior de la representación del control que era común anteriores a Android 5.0. Las aplicaciones compiladas con la plantilla tienen `FormsApplicationActivity` como clase base de su actividad principal.

## <a name="material-design-via-appcompat"></a>Diseño material a través de AppCompat

Ahora, use los proyectos de Xamarin.Forms Android `FormsAppCompatActivity` como clase base de su actividad principal. Esta clase usa **AppCompat** características proporcionadas por Android para implementar los temas de diseño de materiales.

Para agregar temas de diseño de Material a su proyecto de Xamarin.Forms Android, siga el [admiten instrucciones de instalación de AppCompat](appcompat-material-design.md)

Este es el **Todo** ejemplo con el valor predeterminado `FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "Aplicación de ejemplo \"todo\" sin AppCompat")](images/before-appcompat.png#lightbox "aplicación de ejemplo \"todo\" sin AppCompat")

Y este es el mismo código después de actualizar el proyecto para usar `FormsAppCompatActivity` (y agregar la información del tema adicional):

[![](images/post-appcompat-sml.png "Aplicación de ejemplo \"todo\" con AppCompat y temas")](images/post-appcompat.png#lightbox "aplicación de ejemplo \"todo\" con AppCompat y temas")

> [!NOTE]
> Cuando se usa `FormsAppCompatActivity`, [clases base para algunos representadores personalizados Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) será diferente.

## <a name="related-links"></a>Vínculos relacionados

- [Agregar compatibilidad con Material Design](appcompat-material-design.md)
