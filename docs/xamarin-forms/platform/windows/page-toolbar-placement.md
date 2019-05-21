---
title: Colocación de la barra de herramientas de página en Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se explica cómo consumir el Windows específicos de la plataforma que cambia la posición de una barra de herramientas en una página.
ms.prod: xamarin
ms.assetid: 99F29E95-0C36-4A3B-BDE8-7E9F119E844E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 6b314a7864ba4c99b980f4b536a0c2103389b666
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924709"
---
# <a name="page-toolbar-placement-on-windows"></a>Colocación de la barra de herramientas de página en Windows

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Esta plataforma Universal de Windows específicos de la plataforma se usan para cambiar la ubicación de una barra de herramientas en un [ `Page` ](xref:Xamarin.Forms.Page)y se consume en XAML estableciendo el [ `Page.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) propiedad adjunta un valor de la [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumeración:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

El `Page.On<Windows>` método especifica que solo se ejecutarán este específicos de la plataforma en Windows. El [ `Page.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para establecer la posición de la barra de herramientas, con el [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) que proporciona la enumeración estos tres valores: [ `Default` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default), [ `Top` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top), y [ `Bottom` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom).

El resultado es que la selección de ubicación de la barra de herramientas especificada se aplica a la [ `Page` ](xref:Xamarin.Forms.Page) instancia:

[![](page-toolbar-placement-images/toolbar-placement.png "Barra de herramientas ubicación específicos de plataforma")](page-toolbar-placement-images/toolbar-placement-large.png#lightbox "específicos de plataforma de barra de herramientas Selección de ubicación")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
