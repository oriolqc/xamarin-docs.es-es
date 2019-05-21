---
title: Visibilidad de barra de estado de página en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el iOS específicos de la plataforma que establece la visibilidad de la barra de estado de una página.
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 66bd6c1ca2ccc3e5335a790950cbf1a792a7df82
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925398"
---
# <a name="page-status-bar-visibility-on-ios"></a>Visibilidad de barra de estado de página en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este específicos de plataforma de iOS se usan para establecer la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page), e incluye la capacidad para controlar cómo la barra de estado entra o sale de la `Page`. Se consume en XAML estableciendo el `Page.PrefersStatusBarHidden` propiedad adjunta a un valor de la `StatusBarHiddenMode` enumeración y, opcionalmente, el `Page.PreferredStatusBarUpdateAnimation` propiedad adjunta a un valor de la `UIStatusBarAnimation` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetPrefersStatusBarHidden` método, en el `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espacio de nombres, se usa para establecer la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page) especificando uno de los `StatusBarHiddenMode` valores de enumeración: `Default`, `True` , o `False`. El `StatusBarHiddenMode.True` y `StatusBarHiddenMode.False` valores establecen la visibilidad de la barra de estado, independientemente de la orientación del dispositivo y el `StatusBarHiddenMode.Default` valor oculta la barra de estado en un entorno de compact verticalmente.

El resultado es que la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page) se pueden establecer:

![](page-status-bar-visibility-images/hide-status-bar.png "Barra de estado de visibilidad plataforma específica")

> [!NOTE]
> En un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), especificado `StatusBarHiddenMode` valor de enumeración también actualizará la barra de estado en todas las páginas secundarias. En todos los demás [ `Page` ](xref:Xamarin.Forms.Page)-tipos derivados, especificados `StatusBarHiddenMode` valor de enumeración solo actualizará la barra de estado en la página actual.

El `Page.SetPreferredStatusBarUpdateAnimation` método se usa para establecer cómo la barra de estado entra o sale de la [ `Page` ](xref:Xamarin.Forms.Page) especificando uno de los `UIStatusBarAnimation` valores de enumeración: `None`, `Fade`, o `Slide`. Si el `Fade` o `Slide` se especifica el valor de enumeración, 0.25 segunda animación se ejecuta como la barra de estado entra o sale de la `Page`.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
