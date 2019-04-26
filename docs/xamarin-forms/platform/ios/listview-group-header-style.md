---
title: Estilo de encabezado de grupo de ListView en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir iOS específicos de la plataforma que controla si las celdas de encabezado ListView float durante el desplazamiento.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 58b3787b71cff9b78f1c6b577be6c320367f1cee
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952157"
---
# <a name="listview-group-header-style-on-ios"></a>Estilo de encabezado de grupo de ListView en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este controles específicos de la plataforma de iOS si [ `ListView` ](xref:Xamarin.Forms.ListView) las celdas de encabezado float durante el desplazamiento. Se consume en XAML estableciendo el `ListView.GroupHeaderStyle` propiedad enlazable en un valor de la `GroupHeaderStyle` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.GroupHeaderStyle="Grouped">
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

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

El `ListView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `ListView.SetGroupHeaderStyle` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si [ `ListView` ](xref:Xamarin.Forms.ListView) las celdas de encabezado float durante el desplazamiento. El `GroupHeaderStyle` enumeración proporciona dos valores posibles:

- `Plain` : indica que las celdas de encabezado float cuando la [ `ListView` ](xref:Xamarin.Forms.ListView) es desplazado (valor predeterminado).
- `Grouped` : indica que las celdas de encabezado no flotantes cuando el [ `ListView` ](xref:Xamarin.Forms.ListView) se desplaza.

Además, el `ListView.GetGroupHeaderStyle` método puede utilizarse para devolver el `GroupHeaderStyle` que se aplica a la [ `ListView` ](xref:Xamarin.Forms.ListView).

El resultado es que un determinado `GroupHeaderStyle` valor se aplica a la [ `ListView` ](xref:Xamarin.Forms.ListView), que controla si las celdas de encabezado float durante el desplazamiento:

[![Captura de pantalla de punto flotantes y no flotante ListView las celdas de encabezado, en iOS](listview-group-header-style-images/group-header-styles.png "ListView con celdas de encabezado de punto flotante y no flotante")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView con celdas de encabezado de punto flotante y no flotante")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
