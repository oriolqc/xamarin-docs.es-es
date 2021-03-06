---
title: Estilo de presentación de la página modal de iPad
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el estilo de presentación de una página modal en un iPad de los conjuntos específicos de la plataforma de iOS.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: fa851ddc753d1fb9cb39f4c08dcfde518a123b62
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926739"
---
# <a name="ipad-modal-page-presentation-style"></a>Estilo de presentación de la página modal de iPad

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este específicos de plataforma de iOS se usan para establecer el estilo de presentación de una página modal en un iPad. Se consume en XAML estableciendo el `Page.ModalPresentationStyle` propiedad enlazable para un `UIModalPresentationStyle` valor de enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetModalPresentationStyle` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para establecer el estilo de presentación modal en un [ `Page` ](xref:Xamarin.Forms.Page) especificando uno de los siguientes `UIModalPresentationStyle` (enumeración) valores:

- `FullScreen`, que establece el estilo de presentación modal para abarcar toda la pantalla. De forma predeterminada, las páginas modales se muestran con este estilo de presentación.
- `FormSheet`, que establece el estilo de presentación modal sea menor que la pantalla y centrado en.

Además, el `GetModalPresentationStyle` método puede utilizarse para recuperar el valor actual de la `UIModalPresentationStyle` enumeración que se aplica a la [ `Page` ](xref:Xamarin.Forms.Page).

El resultado es que el estilo de presentación modal en un [ `Page` ](xref:Xamarin.Forms.Page) se pueden establecer:

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal estilos de presentación en un iPad")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal estilos de presentación en un iPad")

> [!NOTE]
> Las páginas que usan este específicos de la plataforma para establecer el estilo de presentación modal deben usar la navegación modal. Para obtener más información, consulte [páginas modales de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
