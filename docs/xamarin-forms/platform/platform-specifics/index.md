---
title: Obtener información específica de la plataforma
description: Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/17/2017
ms.openlocfilehash: eb0933aeda7438e0d1f81a9b9d0ef35ceb543a1b
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2018
---
# <a name="platform-specifics"></a>Obtener información específica de la plataforma

_Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos._

La siguiente funcionalidad específica de la plataforma se compila a Xamarin.Forms:

|iOS|Android|Windows|
|--- |--- |--- |
|[VisualElement.BlurEffect](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#blur)|[Application.WindowSoftInputModeAdjust](~/xamarin-forms/platform/platform-specifics/consuming/android.md#soft_input_mode)|[Page.ToolbarPlacement](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#toolbar_placement)|
|[NavigationPage.PrefersLargeTitles](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title)|[ListView.IsFastScrollEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#fastscroll)|[MasterDetailPage.CollapsedPaneWidth y MasterDetailPage.CollapseStyle](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#collapsable_navigation_bar)|
|[Page.UseSafeArea](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#safe_area_layout)|[TabbedPage.IsSwipePagingEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#enable_swipe_paging)|
|[NavigationPage.IsNavigationBarTranslucent](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#translucent_navigation_bar)|[VisualElement.Elevation](~/xamarin-forms/platform/platform-specifics/consuming/android.md#elevation)|
|[NavigationPage.StatusBarTextColorMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#status_bar_color_mode)|[Application.SendDisappearingEventOnPause, Application.SendAppearingEventOnResume y Application.ShouldPreserveKeyboardOnResume](~/xamarin-forms/platform/platform-specifics/consuming/android.md#disable_lifecycle_events)|
|[Entry.AdjustsFontSizeToFitWidth](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#adjust_font_size)|
|[Picker.UpdateMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)|
|[Page.PrefersStatusBarHidden y Page.PreferredStatusBarUpdateAnimation](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#set_status_bar_visibility)|
|[ScrollView.ShouldDelayContentTouches](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#delay_content_touches)|

El proceso para consumir un específico de la plataforma a través de XAML, o a través de una API fluida de código es como sigue:

1. Agregar un `xmlns` declaración o `using` la directiva para la [ `Xamarin.Forms.PlatformConfiguration` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/) espacio de nombres.
1. Agregar un `xmlns` declaración o `using` la directiva para el espacio de nombres que contiene la funcionalidad específica de la plataforma:
    1. En iOS, se trata la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres.
    1. En Android, se trata la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) espacio de nombres. Para Android AppCompat, este es el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/) espacio de nombres.
    1. En la plataforma Universal de Windows, se trata la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) espacio de nombres.
1. Aplicar la específica de la plataforma de XAML, o de código con el `On<T>` API fluida. El valor de `T` puede ser el [ `iOS` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOS/), [ `Android` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.Android/), o [ `Windows` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.Windows/) tipos desde el [ `Xamarin.Forms.PlatformConfiguration` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/) espacio de nombres.

> [!NOTE]
> Tenga en cuenta que intenta consumir una plataforma específica en una plataforma que no está disponible no darán como resultado un error. En su lugar, el código se ejecutará sin el específico de la plataforma que se va a aplicar.

Obtener información específica de la plataforma de consume a través de la `On<T>` código fluida devueltos de la API [ `IPlatformElementConfiguration` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IPlatformElementConfiguration%3CTPlatform,TElement%3E/) objetos. Esto permite que varios específicos de plataforma que se invocará en el mismo objeto con el método en cascada.

Para obtener más información acerca de cómo obtener información específica de la plataforma, consulte [específicos de plataforma consumiendo](~/xamarin-forms/platform/platform-specifics/consuming/index.md) y [específicos de plataforma crear](~/xamarin-forms/platform/platform-specifics/creating.md).


## <a name="related-links"></a>Vínculos relacionados

- [Consumo de funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/consuming/index.md)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [PlatformConfiguration](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/)
