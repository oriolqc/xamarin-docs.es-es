---
title: Ajuste de escala de accesibilidad para los tamaños de fuente denominado en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el iOS específicos de la plataforma que deshabilita el ajuste de escala para los tamaños de fuente con nombre de accesibilidad.
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 1fc6fefe1f9fe48fe2abb367b376a5a6f3484462
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517954"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Ajuste de escala de accesibilidad para los tamaños de fuente denominado en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este específicos de plataforma de iOS deshabilita accesibilidad escalado para tamaños de fuente con nombre. Se consume en XAML estableciendo el `Application.EnableAccessibilityScalingForNamedFontSizes` propiedad enlazable a `false`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

El `Application.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Application.SetEnableAccessibilityScalingForNamedFontSizes` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para deshabilitar los tamaños de fuente con nombre que se va a escalar mediante la configuración de accesibilidad de iOS. Además, el `Application.GetEnableAccessibilityScalingForNamedFontSizes` método puede utilizarse para devolver si se escalan los tamaños de fuente con nombre mediante la configuración de accesibilidad de iOS.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
