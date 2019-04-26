---
title: Fast ListView desplazamiento en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el Android específicos de la plataforma que permite desplazarse rápidamente a través de datos en un ListView.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: d4b4a72d2bfe77c433c17fa9f427a95121855e01
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216441"
---
# <a name="listview-fast-scrolling-on-android"></a>Fast ListView desplazamiento en Android

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este Android específicos de plataforma se usan para habilitar el desplazamiento rápido a través de los datos en un [ `ListView` ](xref:Xamarin.Forms.ListView). Se consume en XAML estableciendo el `ListView.IsFastScrollEnabled` propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

El `ListView.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `ListView.SetIsFastScrollEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para habilitar el desplazamiento rápido a través de los datos en un [ `ListView` ](xref:Xamarin.Forms.ListView). Además, el `SetIsFastScrollEnabled` método puede utilizarse para activar o desactivar el desplazamiento rápido mediante una llamada a la `IsFastScrollEnabled` método para devolver si se habilita el desplazamiento rápido:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

El resultado es ese desplazamiento rápido a través de los datos en un [ `ListView` ](xref:Xamarin.Forms.ListView) puede habilitarse, que cambia el tamaño de la miniatura de desplazamiento:

[![](listview-fast-scrolling-images/fastscroll.png "Específico de plataforma ListView FastScroll")](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll específico de plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
