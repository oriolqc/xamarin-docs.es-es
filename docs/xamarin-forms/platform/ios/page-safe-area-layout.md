---
title: Guía de diseño de área segura en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir iOS específicos de la plataforma que garantiza que el contenido de la página se coloca en un área de la pantalla que es seguro para todos los dispositivos que usan iOS 11 y versiones posteriores.
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: bc18f1e1f051e18a970464b134733f2af39681ae
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209130"
---
# <a name="safe-area-layout-guide-on-ios"></a>Guía de diseño de área segura en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Se usan este específicos de plataforma de iOS para asegurarse de que el contenido de la página se coloca en un área de la pantalla que es seguro para todos los dispositivos que usan iOS 11 y versiones posteriores. En concreto, le ayudará a asegurarse de que el contenido no recortado por dispositivo esquinas redondeadas, el indicador principal o el alojamiento del sensor en un iPhone X. Se consume en XAML estableciendo el `Page.UseSafeArea` propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetUseSafeArea` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) controla el espacio de nombres, si está habilitada la Guía de diseño de área segura.

El resultado es que el contenido de la página puede colocarse en un área de la pantalla que es seguro para todos los iPhone:

[![](page-safe-area-images/safe-area-layout.png "Guía de diseño de área segura")](page-safe-area-images/safe-area-layout-large.png#lightbox "Guía de diseño de área segura")

> [!NOTE]
> El área segura definido por Apple que se usa en Xamarin.Forms para establecer el [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding) propiedad y reemplazará los valores anteriores de esta propiedad que se han establecido.

El área segura se puede personalizar mediante la recuperación de su [ `Thickness` ](xref:Xamarin.Forms.Thickness) valor con el `Page.SafeAreaInsets` método desde el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres. A continuación, se puede modificar como necesarios y volver a asignar a la `Padding` propiedad en el constructor de la página o [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) invalidar:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
