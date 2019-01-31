---
title: Características de la plataforma Windows
description: En este artículo se explica la compatibilidad de plataforma de Windows que está disponible en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: f14ea52e419ae5d639319f49ae4779de6a2eed31
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292186"
---
# <a name="windows-platform-features"></a>Características de la plataforma Windows

Desarrollar aplicaciones de Xamarin.Forms para plataformas de Windows requiere Visual Studio. El [página requisitos](~/get-started/installation.md) contiene más información sobre los requisitos previos.

![](images/allhanselman.png "Aplicaciones de Xamarin.Forms que se ejecutan en Windows")

## <a name="platform-specifics"></a>Funcionalidades específicas de plataforma

Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

La siguiente funcionalidad específica de la plataforma se proporciona para las vistas de Xamarin.Forms, páginas y diseños en la plataforma Universal de Windows (UWP):

- Establecer una tecla de acceso para un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement claves de acceso en Windows](#visualelement-accesskeys).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [VisualElement modo de Color heredado en Windows](#legacy-color-mode).

Se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin.Forms en UWP:

- Detectar el orden de lectura del texto contenido en [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), y [ `Label` ](xref:Xamarin.Forms.Label) instancias. Para obtener más información, consulte [InputView el orden de lectura en Windows](#inputview-readingorder).
- Habilitar la compatibilidad de derivación de gestos en un [ `ListView` ](xref:Xamarin.Forms.ListView). Para obtener más información, consulte [ListView SelectionMode en Windows](#listview-selectionmode).
- Habilitar un [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interactuar con el motor de corrección ortográfica. Para obtener más información, consulte [SearchBar Spell Check en Windows](#searchbar-spellcheck).
- Habilitar un [ `WebView` ](xref:Xamarin.Forms.WebView) para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje UWP. Para obtener más información, consulte [alertas de JavaScript de WebView en Windows](#webview-javascript-alert).

Se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin.Forms para UWP:

- Contraer el [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) barra de navegación. Para obtener más información, consulte [MasterDetailPage barra de navegación en Windows](#collapsable_navigation_bar).
- Opciones de ubicación de la barra de herramientas de configuración. Para obtener más información, consulte [posición de la barra de herramientas de página en Windows](#toolbar_placement).
- Habilitación de los iconos de página que se mostrará en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de herramientas. Para obtener más información, consulte [TabbedPage iconos en Windows](#tabbedpage-icons).

## <a name="platform-support"></a>Compatibilidad con la plataforma

Las plantillas de Xamarin.Forms disponibles en Visual Studio contienen un proyecto de plataforma Universal de Windows (UWP).

> [!NOTE]
> Compatibilidad de Xamarin.Forms 1.x y 2.x _Windows Phone 8 Silverlight_, _Windows Phone 8.1_, y _Windows 8.1_ desarrollo de aplicaciones. Sin embargo, estos tipos de proyecto han quedado desusados.

## <a name="getting-started"></a>Introducción

Vaya a **archivo > Nuevo > proyecto** en Visual Studio y elija uno de los **multiplataforma > aplicación vacía (Xamarin.Forms)** plantillas para comenzar.

Las soluciones de Xamarin.Forms anteriores o los creados en macOS, no tendrá todos los proyectos de Windows enumerados anteriormente (pero deben agregarse manualmente). Si desea tener como destino la plataforma de Windows no está en la solución, viste el [instrucciones de instalación](installation/index.md) para agregar el deseado de Windows/s de tipo de proyecto.

## <a name="samples"></a>Muestras

[Todos los ejemplos](https://github.com/xamarin/xamarin-forms-book-preview-2) de libro de Charles [ *Creating Mobile Apps with Xamarin.Forms* ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) se incluyen los proyectos de plataforma Universal de Windows (para Windows 10).

El ["Scott Hanselman" aplicación de demostración](https://github.com/jamesmontemagno/Hanselman.Forms) está disponible por separado y también incluye proyectos de Apple Watch y Android Wear (con Xamarin.iOS y Xamarin.Android, respectivamente, Xamarin.Forms no se ejecuta en estas plataformas).

## <a name="related-links"></a>Vínculos relacionados

- [Proyectos de instalación de Windows](~/xamarin-forms/platform/windows/installation/index.md)
