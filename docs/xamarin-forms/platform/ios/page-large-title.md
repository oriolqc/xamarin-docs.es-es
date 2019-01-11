---
title: Títulos de página grande en iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el iOS específicos de la plataforma que se muestra el título de página como un título de gran tamaño en la barra de navegación de una NavigationPage.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ad1c7f94467c6f32b9108ab7f6abe85d31679d3a
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208990"
---
# <a name="large-page-titles-on-ios"></a>Títulos de página grande en iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos de plataforma de iOS se usan para mostrar el título de página como un título de gran tamaño en la barra de navegación de un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), para los dispositivos que usan iOS 11 o superior. Un gran título está alineado a la izquierda y usa una fuente mayor y realiza la transición a un título estándar como el usuario comienza a desplazarse por el contenido, por lo que se usan de forma eficaz el espacio en pantalla. Sin embargo, en orientación horizontal, el título devolverá al centro de la barra de navegación para optimizar el diseño del contenido. Se consume en XAML estableciendo el `NavigationPage.PrefersLargeTitles` propiedad adjunta un `boolean` valor:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Como alternativa puede usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

El `NavigationPage.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `NavigationPage.SetPrefersLargeTitle` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) controla el espacio de nombres, si están habilitados los títulos de gran tamaño.

Siempre que los títulos de gran tamaño están habilitados en el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), todas las páginas de la pila de navegación mostrará los títulos de gran tamaño. Este comportamiento puede invalidarse en páginas estableciendo el `Page.LargeTitleDisplay` propiedad adjunta a un valor de la `LargeTitleDisplayMode` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, se puede invalidar el comportamiento de la página de C# con la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetLargeTitleDisplay` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, controla el comportamiento de título grande en la [ `Page` ](xref:Xamarin.Forms.Page), con el `LargeTitleDisplayMode` enumeración que proporciona tres posibles valores:

- `Always` : fuerza la barra de navegación y la fuente tamaño usando el formato grande.
- `Automatic` : use el mismo estilo (grande o pequeño) que el elemento anterior en la pila de navegación.
- `Never` – forzar el uso de la barra de navegación de formato estándar, pequeñas.

Además, el `SetLargeTitleDisplay` método puede utilizarse para activar o desactivar los valores de enumeración mediante una llamada a la `LargeTitleDisplay` método, que devuelve el valor actual `LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

El resultado es que un determinado `LargeTitleDisplayMode` se aplica a la [ `Page` ](xref:Xamarin.Forms.Page), que controla el comportamiento de título grande:

![](page-large-title-images/large-title.png "Específico de la plataforma efecto de desenfoque")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
