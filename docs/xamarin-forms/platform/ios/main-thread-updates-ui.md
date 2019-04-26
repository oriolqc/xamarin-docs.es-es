---
title: Actualizaciones de Control de subproceso principal en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. Este artículo explica cómo consumir iOS específicos de la plataforma que permite controlar el diseño y representación de las actualizaciones que se realizará en el subproceso principal.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 56109cc9064de4b995e75ceb967abe4995504660
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953401"
---
# <a name="main-thread-control-updates-on-ios"></a>Actualizaciones de Control de subproceso principal en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos de plataforma de iOS permite controlar el diseño y representación de las actualizaciones que se realizará en el subproceso principal, en lugar de que se va a realizar en un subproceso en segundo plano. Debería ser necesario con poca frecuencia, pero en algunos casos puede evitar que se bloquee. Su XAML consumido en estableciendo el `Application.HandleControlUpdatesOnMainThread` propiedad enlazable a `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

El `Application.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Application.SetHandleControlUpdatesOnMainThread` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si el diseño del control y la representación de las actualizaciones se realizan en el subproceso principal, en lugar de que se va a realizar en un subproceso en segundo plano. Además, el `Application.GetHandleControlUpdatesOnMainThread` método puede utilizarse para devolver si el diseño de controles y la representación de las actualizaciones se realizan en el subproceso principal.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
