---
title: Animaciones de transición de página TabbedPage en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el Android específicos de la plataforma que deshabilita las animaciones de transición al navegar a través de páginas en una TabbedPage.
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 1e9c46fe9535c313581d6d0053559a28aa327887
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61359900"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Animaciones de transición de página TabbedPage en Android

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este Android específicos de plataforma se usan para deshabilitar las animaciones de transición al navegar a través de páginas, ya sea mediante programación o cuando se usa la barra de pestañas, en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Se consume en XAML estableciendo el `TabbedPage.IsSmoothScrollEnabled` propiedad enlazable a `false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

El `TabbedPage.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `TabbedPage.SetIsSmoothScrollEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si las animaciones de transición se mostrará al navegar entre páginas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espacio de nombres también tiene los métodos siguientes:

- `IsSmoothScrollEnabled`, que se usa para recuperar datos si las animaciones de transición se mostrará al navegar entre páginas en un `TabbedPage`.
- `EnableSmoothScroll`, que se usa para habilitar animaciones de transición al navegar entre páginas en un `TabbedPage`.
- `DisableSmoothScroll`, que se usa para deshabilitar las animaciones de transición al navegar entre páginas en un `TabbedPage`.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
