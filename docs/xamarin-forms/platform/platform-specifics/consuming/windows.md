---
title: Detalles de plataforma de Windows
description: Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo utilizar la plataforma-detalles de Windows que están integrados en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: d1610e4c9e6a8799362ff955061953962dd755ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="windows-platform-specifics"></a>Detalles de plataforma de Windows

_Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo utilizar la plataforma-detalles de Windows que están integrados en Xamarin.Forms._

En la plataforma Universal de Windows (UWP), Xamarin.Forms contiene los detalles siguientes de la plataforma:

- Opciones de colocación de la barra de herramientas. Para obtener más información, consulte [cambiar la posición de la barra de herramientas](#toolbar_placement).
- Un puede contraerse parcialmente [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) barra de navegación. Para obtener más información, consulte [contraer una barra de navegación de MasterDetailPage](#collapsable_navigation_bar).

<a name="toolbar_placement" />

## <a name="changing-the-toolbar-placement"></a>Cambiar la posición de la barra de herramientas

Este específica de la plataforma se utiliza para cambiar la posición de una barra de herramientas en un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)y se consume en XAML estableciendo el [ `Page.ToolbarPlacement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty/) propiedad adjunta a un valor de la [ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) enumeración:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>

```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

El `Page.On<Windows>` método especifica que sólo se ejecutará este específica de la plataforma en Windows. El [ `Page.SetToolbarPlacement` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) (método), en la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) espacio de nombres, se usa para establecer la posición de la barra de herramientas, con el [ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) proporcionar (enumeración) estos tres valores: [ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default/), [ `Top` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top/), y [ `Bottom` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom/).

El resultado es que la selección de ubicación de la barra de herramientas especificada se aplica a la [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) instancia:

[![](windows-images/toolbar-placement.png "Barra de herramientas de selección de ubicación plataforma específica")](windows-images/toolbar-placement-large.png#lightbox "barra de herramientas de selección de ubicación plataforma específica")

<a name="collapsable_navigation_bar" />

## <a name="collapsing-a-masterdetailpage-navigation-bar"></a>Contraer una barra de navegación de MasterDetailPage

Se utiliza este específica de la plataforma para contraer la barra de navegación en un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)y se consume en XAML estableciendo el [ `MasterDetailPage.CollapseStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty/) y [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty/)propiedades adjuntas:

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

El `MasterDetailPage.On<Windows>` método especifica que sólo se ejecutará este específica de la plataforma en Windows. El [ `Page.SetCollapseStyle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) método, en la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) espacio de nombres, se utiliza para especificar el estilo de contraer, con el [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) enumeración proporciona dos valores: [ `Full` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full/) y [ `Partial` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial/). El [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/System.Double/) método se usa para especificar el ancho de una barra de navegación parcialmente contraído.

El resultado es que un determinado [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) se aplica a la [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) instancia, con el ancho también se especifican:

[![](windows-images/collapsed-navigation-bar.png "Contraer la barra de navegación específica de la plataforma")](windows-images/collapsed-navigation-bar-large.png#lightbox "contraído barra de navegación específica de la plataforma")

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar la plataforma-detalles de Windows que están integrados en Xamarin.Forms. Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos.


## <a name="related-links"></a>Vínculos relacionados

- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
