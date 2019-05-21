---
title: WebView mezclado en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el Android específicos de la plataforma que muestran contenido mixto en un objeto WebView en aplicaciones de dicha API 21 o superior de destino.
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 67ab68ceda69f9000bb160d1e443fd82ad7b61b3
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924567"
---
# <a name="webview-mixed-content-on-android"></a>WebView mezclado en Android

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este Android específicos de plataforma controla si un [ `WebView` ](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto en aplicaciones que tienen como destino API 21 o posterior. El contenido mixto es el contenido que se cargó inicialmente a través de una conexión HTTPS, pero que cargan recursos (por ejemplo, imágenes, audio, vídeo, hojas de estilo, scripts) en una conexión HTTP. Se consume en XAML estableciendo el [ `WebView.MixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propiedad adjunta a un valor de la [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

El `WebView.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si se puede mostrar contenido mixto, con el [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeración que proporciona tres valores posibles:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indica que el [ `WebView` ](xref:Xamarin.Forms.WebView) permitirá a un origen HTTPS cargar el contenido desde un origen HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indica que el [ `WebView` ](xref:Xamarin.Forms.WebView) no permitirá que un origen HTTPS cargar el contenido desde un origen HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indica que el [ `WebView` ](xref:Xamarin.Forms.WebView) intentará sea compatible con el enfoque del explorador de web del dispositivo más reciente. Puede tener algún contenido HTTP se puede cargar un origen de HTTPS y se bloquearán otros tipos de contenido. Los tipos de contenido que se bloquean o permiten pueden cambiar con cada versión de sistema operativo.

El resultado es que un determinado [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valor se aplica a la [ `WebView` ](xref:Xamarin.Forms.WebView), que controla si se puede mostrar contenido mixto:

[![WebView mixto de control de contenido específico de la plataforma](webview-mixed-content-images/webview-mixedcontent.png "WebView mixto de control de contenido específico de la plataforma")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView mixto de control de contenido específico de la plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
