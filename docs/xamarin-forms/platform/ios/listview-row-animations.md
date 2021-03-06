---
title: Animaciones de la fila de ListView en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. Este artículo explica cómo usar iOS específicos de la plataforma que controla si las animaciones de fila están deshabilitadas cuando se actualiza la colección de elementos ListView.
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: ac71be25492866b1cf2b12d3343c2f4095fc738d
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925788"
---
# <a name="listview-row-animations-on-ios"></a>Animaciones de la fila de ListView en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este controles específicos de la plataforma de iOS si las animaciones de fila están deshabilitados cuando la [ `ListView` ](xref:Xamarin.Forms.ListView) se está actualizando la colección de elementos. Se consume en XAML estableciendo el `ListView.RowAnimationsEnabled` propiedad enlazable a `false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

El `ListView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `ListView.SetRowAnimationsEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si las animaciones de fila son deshabilitada cuando el [ `ListView` ](xref:Xamarin.Forms.ListView) se está actualizando la colección de elementos. Además, el `ListView.GetRowAnimationsEnabled` método puede utilizarse para devolver si se deshabilitan las animaciones de la fila en la `ListView`.

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) las animaciones de la fila se habilitan de forma predeterminada. Por lo tanto, se produce una animación cuando se inserta una nueva fila en un `ListView`.

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
