---
title: Reconocimiento de gestos de movimiento panorámico simultáneas en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir iOS específicos de la plataforma que permite el reconocimiento de gestos de pan simultáneas que se usará en una aplicación.
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 4587bb89ddfe43873e666a07514075f1a952e985
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926784"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Reconocimiento de gestos de movimiento panorámico simultáneas en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Cuando un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) se adjunta a una vista dentro de una vista desplazable, todo el pan gestos capturados por el `PanGestureRecognizer` y no se pasan a la vista de desplazamiento. Por lo tanto, ya no se desplazará la vista de desplazamiento.

Este específicos de plataforma de iOS permite un `PanGestureRecognizer` en una vista desplazable para capturar y compartir el movimiento panorámico con la vista de desplazamiento. Se consume en XAML estableciendo el [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) propiedad adjunta `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

El `Application.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si un reconocedor de movimiento panorámico en una vista desplazable capturará el movimiento panorámico, o capturar y compartir el desplazamiento panorámico con la vista desplazable de gestos. Además, el [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) método puede utilizarse para devolver si se comparte el movimiento panorámico con la vista de desplazamiento que contiene el [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer).

Por lo tanto, con este específicos de la plataforma habilitada, cuando un [ `ListView` ](xref:Xamarin.Forms.ListView) contiene un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer), tanto el `ListView` y `PanGestureRecognizer` recibirán el movimiento panorámico y procesarlo. Sin embargo, con este específicos de la plataforma deshabilitada, cuando un `ListView` contiene un `PanGestureRecognizer`, el `PanGestureRecognizer` capturará el movimiento panorámico y procesarlos y el `ListView` no recibirá el movimiento panorámico.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
