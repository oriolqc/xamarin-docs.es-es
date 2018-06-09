---
title: Características de la plataforma de Xamarin.Forms
description: Esta guía explica cómo aprovechar características específicas de la plataforma de Xamarin.Forms aplicaciones mediante una variedad de técnicas.
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2018
ms.openlocfilehash: 2e8eb19411799e7723be338e9e3f6df35058eb8c
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242805"
---
# <a name="xamarinforms-platform-features"></a>Características de la plataforma de Xamarin.Forms

Xamarin.Forms es extensible y le permite incorporar características específicas de la plataforma mediante [efectos](~/xamarin-forms/app-fundamentals/effects/index.md), [representadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md), el [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)y mucho más.

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

Esta guía describe cómo implementar el Material de diseño mediante la actualización de las aplicaciones existentes de Xamarin.Forms Android.

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[Indexación de la aplicación y vinculación en profundidad](deep-linking.md)

Indización de aplicación permite a las aplicaciones que de lo contrario serían olvidadas después de que algunos se usa para seguir siendo relevante que aparecen en los resultados de la búsqueda. Vinculación en profundidad permite a las aplicaciones responder a un resultado de búsqueda que contiene datos de aplicación, por lo general, vaya a una página que se hace referencia desde un vínculo profundo.

## <a name="device-classdevicemd"></a>[Clase de dispositivo](device.md)

Cómo usar la `Device` clase para crear un comportamiento específico de la plataforma en el código compartido y la interfaz de usuario (incluido el uso de XAML). También cubre la `BeginInvokeOnMainThread` que es esencial para modificar controles de interfaz de usuario desde los subprocesos en segundo plano.

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

Algunos estilos iOS pueden realizarse a través de **Info.plist** y `UIAppearance` API. Esta guía incluye ejemplos de cómo incluir características de iOS 9 en la aplicación de iOS de una solución Xamarin.Forms, incluidos la búsqueda de Spotlight Core.

## <a name="gtkgtkmd"></a>[GTK](gtk.md)

Xamarin.Forms ahora es compatible con vista previa aplicaciones GTK #.

## <a name="macmacmd"></a>[Mac](mac.md)

Xamarin.Forms tiene ahora compatibilidad con vista previa para las aplicaciones de Mac OS.

## <a name="native-formsnative-formsmd"></a>[Formularios nativos](native-forms.md)

Los formularios nativo permiten Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado páginas a ser consumidos por los proyectos nativos de Xamarin.iOS y Xamarin.Android, plataforma Universal de Windows (UWP).

## <a name="native-viewsnative-viewsindexmd"></a>[Vistas nativas](native-views/index.md)

Pueden hacer referencia directamente a las vistas nativo de la plataforma Universal de Windows, iOS y Android de Xamarin.Forms. Propiedades y los controladores de eventos se pueden establecer en vistas nativo y pueden interactuar con vistas de Xamarin.Forms.

## <a name="platform-specificsplatform-specificsindexmd"></a>[Obtener información específica de la plataforma](platform-specifics/index.md)

Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de representadores personalizados o los efectos.

## <a name="pluginspluginsmd"></a>[Complementos](plugins.md)

Hay una amplia variedad de complementos de código abierto disponible en Github, Nuget y el almacén de componentes de Xamarin que ayuda a ampliar aplicaciones de Xamarin.Forms.

## <a name="tizentizenmd"></a>[tizen](tizen.md)

Tizen .NET le permite crear aplicaciones de .NET con Xamarin.Forms y Tizen .NET framework.

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

Xamarin.Forms dispone de compatibilidad para la plataforma Universal de Windows (UWP) en Windows 10. Este artículo describe cómo agregar un un proyecto de UWP para una solución de Xamarin.Forms existente.

## <a name="wpfwpfmd"></a>[WPF](wpf.md)

Xamarin.Forms tiene ahora compatibilidad con vista previa para las aplicaciones de Windows Presentation Foundation (WPF).
