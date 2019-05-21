---
title: Zoom de WebView en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el Android específicos de la plataforma que permite el nivel de zoom en una vista Web.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 0e180ca5019bd4e5d1351cfb2f7a8c28ade2afe2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971647"
---
# <a name="webview-zoom-on-android"></a>Zoom de WebView en Android

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este Android específicos de plataforma permite acercar para alejar y un control de zoom en un [ `WebView` ](xref:Xamarin.Forms.WebView). Se consume en XAML estableciendo el `WebView.EnableZoomControls` y `WebView.DisplayZoomControls` propiedades enlazables a `boolean` valores:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

El `WebView.EnableZoomControls` propiedad enlazable controla si pinch a zoom está habilitado en el [ `WebView` ](xref:Xamarin.Forms.WebView)y el `WebView.DisplayZoomControls` propiedad enlazable controla si los controles de zoom se superponen en el `WebView`.

Como alternativa, se puede consumir el específico de la plataforma desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

El `WebView.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `WebView.EnableZoomControls` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si pinch a zoom está habilitado en el [ `WebView` ](xref:Xamarin.Forms.WebView). El `WebView.DisplayZoomControls` método, en el mismo espacio de nombres, se utiliza para controlar si los controles de zoom se superponen en el `WebView`. Además, el `WebView.ZoomControlsEnabled` y `WebView.ZoomControlsDisplayed` métodos pueden usarse para devolver si se habilitan los controles de zoom y acercar para alejar, respectivamente.

El resultado es que acercar para alejar puede habilitarse en un [ `WebView` ](xref:Xamarin.Forms.WebView), y se pueden superponer los controles de zoom en el `WebView`:

[![Captura de pantalla de ampliada WebView en Android](webview-zoom-controls-images/webview-zoom.png "ampliada WebView")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "WebView ampliada")

> [!IMPORTANT]
> Controles de zoom deben ser habilitados y muestra, a través de las respectivas propiedades enlazables o métodos, que se superpone en un [ `WebView` ](xref:Xamarin.Forms.WebView).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
