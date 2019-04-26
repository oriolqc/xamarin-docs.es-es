---
title: Control deslizante Thumb pulsar en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. Este artículo explica cómo usar iOS específicos de la plataforma que permite la propiedad Slider.Value debe establecerse si pulsa en el control deslizante.
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 43cc87f9d319295ce65d55488e1be032ae00a697
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082202"
---
# <a name="slider-thumb-tap-on-ios"></a>Control deslizante Thumb pulsar en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos de plataforma de iOS permite la [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad debe establecerse si pulsa en una posición en la [ `Slider` ](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar la `Slider` thumb. Se consume en XAML estableciendo el [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) propiedad enlazable a `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

El `Slider.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si una derivación de la `Slider` barra establecerá el [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad. Además, el [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider})) método puede utilizarse para devolver si una derivación de en el `Slider` barra establecerá el `Slider.Value` propiedad.

El resultado es que una derivación de en el [ `Slider` ](xref:Xamarin.Forms.Slider) puede mover la barra de la `Slider` thumb y establezca el [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad:

![](slider-thumb-images/slider-updateontap.png "Actualización del control deslizante al pulsar habilitado")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
