---
title: ScrollView toques contenido en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir iOS específicos de la plataforma que controla si un ScrollView controla un movimiento táctil o pasa a su contenido.
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 7f6ec13b1b21a1526bb53f260c4d80e881e7feba
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952239"
---
# <a name="scrollview-content-touches-on-ios"></a>ScrollView toques contenido en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Se desencadena un temporizador implícito cuando se inicia un movimiento táctil en un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) en iOS y el `ScrollView` decide, en función de la acción del usuario dentro del intervalo del temporizador, independientemente de que se debe controlar el gesto o pasarlo a su contenido. De forma predeterminada, el archivo iOS `ScrollView` toques contenido retrasos, pero esto puede causar problemas en algunas circunstancias con el `ScrollView` contenido no ganando el gesto cuando debería. Por lo tanto, este controles específicos de la plataforma si un `ScrollView` controla un movimiento táctil o pasa a su contenido. Se consume en XAML estableciendo el `ScrollView.ShouldDelayContentTouches` propiedad adjunta un `boolean` valor:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

El `ScrollView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `ScrollView.SetShouldDelayContentTouches` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) controla un movimiento táctil o pasa a su contenido. Además, el `SetShouldDelayContentTouches` método puede utilizarse para activar o desactivar retrasar toques contenidos mediante una llamada a la `ShouldDelayContentTouches` método para devolver si se retrasan toques de contenido:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

El resultado es que un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) puede deshabilitar retrasar recibir contenidos toques, así que en este escenario el [ `Slider` ](xref:Xamarin.Forms.Slider) recibe el gesto en lugar de [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) página de la [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](scrollview-content-touches-images/scrollview-delay-content-touches.png "Contenido de retraso de ScrollView toca específicos de la plataforma")](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "toca el retraso ScrollView contenido específico de la plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
