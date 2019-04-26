---
title: Selector de selección de elementos en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. Este artículo explica cómo consumir iOS específicos de la plataforma que controla cuándo se produce la selección de elementos en un selector.
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 21c4c289a3fd30db890be6811875412ce4913cf5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250638"
---
# <a name="picker-item-selection-on-ios"></a>Selector de selección de elementos en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este iOS específicos de la plataforma controla cuándo se produce la selección de elementos en un [ `Picker` ](xref:Xamarin.Forms.Picker), lo que permite al usuario especificar que la selección de elementos se produce al examinar los elementos del control, o solo una vez que la **realiza** botón está presionado. Se consume en XAML estableciendo el `Picker.UpdateMode` propiedad adjunta a un valor de la `UpdateMode` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

El `Picker.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Picker.SetUpdateMode` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar cuándo se produce la selección de elementos, con el `UpdateMode` enumeración que proporciona dos valores posibles:

- `Immediately` : selección de elementos se produce cuando el usuario explora los elementos de la [ `Picker` ](xref:Xamarin.Forms.Picker). Este es el comportamiento predeterminado en Xamarin.Forms.
- `WhenFinished` : selección de elementos solo se produce una vez que el usuario ha presionado el **realiza** situado en la [ `Picker` ](xref:Xamarin.Forms.Picker).

Además, el `SetUpdateMode` método puede utilizarse para activar o desactivar los valores de enumeración mediante una llamada a la `UpdateMode` método, que devuelve el valor actual `UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

El resultado es que un determinado `UpdateMode` se aplica a la [ `Picker` ](xref:Xamarin.Forms.Picker), que controla cuándo se produce la selección de elementos:

[![](picker-selection-images/picker-updatemode.png "Específico de plataforma selector UpdateMode")](picker-selection-images/picker-updatemode-large.png#lightbox "selector UpdateMode específico de plataforma")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
