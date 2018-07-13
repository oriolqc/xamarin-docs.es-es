---
title: Características de la plataforma de Xamarin.Forms
description: Esta guía explica cómo aprovechar las ventajas de las características específicas de plataforma en las aplicaciones de Xamarin.Forms mediante una variedad de técnicas.
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2018
ms.openlocfilehash: 9bac53f71178ac321dea162d346295556a8f7adb
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998760"
---
# <a name="xamarinforms-platform-features"></a>Características de la plataforma de Xamarin.Forms

Xamarin.Forms es extensible y le permite incorporar características específicas de plataforma mediante [efectos](~/xamarin-forms/app-fundamentals/effects/index.md), [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md), el [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)y mucho más.

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

Esta guía describe cómo implementar Material Design mediante la actualización de aplicaciones de Xamarin.Forms Android existentes.

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[Indexación de la aplicación y vinculación en profundidad](deep-linking.md)

Indexación de la aplicación permite a las aplicaciones que de lo contrario serían olvidadas después de unos pocos se usa para mantener su relevancia por aparezcan en los resultados de búsqueda. Vinculación en profundidad permite a las aplicaciones responder a un resultado de búsqueda que contiene los datos de la aplicación, normalmente, vaya a una página que se hace referencia desde un vínculo profundo.

## <a name="device-classdevicemd"></a>[Clase de dispositivo](device.md)

Cómo usar el `Device` clase para crear un comportamiento específico de la plataforma en código compartido y la interfaz de usuario (incluido el uso de XAML). También cubre `BeginInvokeOnMainThread` que es esencial al modificar los controles de interfaz de usuario de subprocesos en segundo plano.

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

Algunos estilos de iOS se pueden realizar a través de **Info.plist** y `UIAppearance` API. Esta guía incluye ejemplos de cómo incluir características de iOS 9 en la aplicación de iOS de una solución Xamarin.Forms, incluidos la búsqueda de Spotlight Core.

## <a name="gtkgtkmd"></a>[GTK](gtk.md)

Xamarin.Forms tiene ahora soporte técnico de vista previa para aplicaciones de GTK #.

## <a name="macmacmd"></a>[Mac](mac.md)

Xamarin.Forms tiene ahora la compatibilidad de versión preliminar para las aplicaciones de Mac OS.

## <a name="native-formsnative-formsmd"></a>[Formularios nativos](native-forms.md)

Formularios nativos permiten Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivados de las páginas que será consumido por los proyectos nativos de Xamarin.iOS, Xamarin.Android y plataforma Universal de Windows (UWP).

## <a name="native-viewsnative-viewsindexmd"></a>[Vistas nativas](native-views/index.md)

Pueden hacer referencia directamente a vistas nativas de iOS, Android y la plataforma Universal de Windows de Xamarin.Forms. Se pueden establecer las propiedades y los controladores de eventos en vistas nativas, y pueden interactuar con las vistas de Xamarin.Forms.

## <a name="platform-specificsplatform-specificsindexmd"></a>[Funcionalidades específicas de plataforma](platform-specifics/index.md)

Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de representadores personalizados o los efectos.

## <a name="pluginspluginsmd"></a>[Complementos](plugins.md)

Hay una amplia variedad de complementos de código abierto en Github, Nuget y el Store del componente Xamarin para ayudar a ampliar las aplicaciones de Xamarin.Forms.

## <a name="tizentizenmd"></a>[Tizen](tizen.md)

Tizen .NET le permite crear aplicaciones .NET con Xamarin.Forms y Tizen .NET framework.

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

Xamarin.Forms tiene compatibilidad para la plataforma Universal de Windows (UWP) en Windows 10. En este artículo se describe cómo agregar un un proyecto UWP a una solución existente de Xamarin.Forms.

## <a name="wpfwpfmd"></a>[WPF](wpf.md)

Xamarin.Forms tiene ahora soporte técnico de vista previa para aplicaciones de Windows Presentation Foundation (WPF).
