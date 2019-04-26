---
title: Página TabbedPage desplazándose en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el Android específicos de la plataforma que permite el gesto de deslizar rápidamente con un gesto del dedo horizontal entre las páginas en una TabbedPage.
ms.prod: xamarin
ms.assetid: D1C09CCB-7246-41A4-8BD2-FA6FABCF1C72
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 7de2c903da40c186560085d61b94fd38ffe9d219
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61361197"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Página TabbedPage desplazándose en Android

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este Android específicos de plataforma se utilizan para habilitar el gesto de deslizar rápidamente con un gesto del dedo horizontal entre las páginas de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Se consume en XAML estableciendo el [ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) propiedad adjunta un `boolean` valor:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

El `TabbedPage.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `TabbedPage.SetIsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled(Xamarin.Forms.BindableObject,System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para habilitar el gesto de deslizar rápidamente entre las páginas de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espacio de nombres también tiene un [ `EnableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) método que permite este específicos de la plataforma, y un [ `DisableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) método que deshabilita Esta plataforma específica. El [ `TabbedPage.OffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) propiedad adjunta, y [ `SetOffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit(Xamarin.Forms.BindableObject,System.Int32)) método, se utilizan para establecer el número de páginas que se deben conservar en un estado de inactividad a cada lado de la página actual.

El resultado es que la paginación a través de las páginas se muestran por Deslizar rápidamente un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) está habilitado:

![](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
