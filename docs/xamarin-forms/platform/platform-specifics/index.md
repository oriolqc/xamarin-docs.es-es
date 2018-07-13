---
title: Funcionalidades específicas de plataforma
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 10adb46493a1cdbb6bc6a2fd67b5191633d7eeeb
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997304"
---
# <a name="platform-specifics"></a>Funcionalidades específicas de plataforma

_Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos._

La siguiente funcionalidad específica de la plataforma está integrada en Xamarin.Forms:

|iOS|Android|Windows|
|--- |--- |--- |
|[VisualElement.BlurEffect](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#blur)|[Application.WindowSoftInputModeAdjust](~/xamarin-forms/platform/platform-specifics/consuming/android.md#soft_input_mode)|[Page.ToolbarPlacement](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#toolbar_placement)|
|[NavigationPage.PrefersLargeTitles](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title)|[ListView.IsFastScrollEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#fastscroll)|[MasterDetailPage.CollapsedPaneWidth y MasterDetailPage.CollapseStyle](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#collapsable_navigation_bar)|
|[Page.UseSafeArea](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#safe_area_layout)|[TabbedPage.IsSwipePagingEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#enable_swipe_paging)|[WebView.IsJavaScriptAlertEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#webview-javascript-alert)
|[NavigationPage.IsNavigationBarTranslucent](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#translucent_navigation_bar)|[VisualElement.Elevation](~/xamarin-forms/platform/platform-specifics/consuming/android.md#elevation)|[SearchBar.IsSpellCheckEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#searchbar-spellcheck)
|[NavigationPage.StatusBarTextColorMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#status_bar_color_mode)|[Application.SendDisappearingEventOnPause Application.SendAppearingEventOnResume y Application.ShouldPreserveKeyboardOnResume](~/xamarin-forms/platform/platform-specifics/consuming/android.md#disable_lifecycle_events)|[InputView.DetectReadingOrderFromContent, Label.DetectReadingOrderFromContent](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#inputview-readingorder)
|[Entry.AdjustsFontSizeToFitWidth](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#adjust_font_size)|[WebView.MixedContentMode](~/xamarin-forms/platform/platform-specifics/consuming/android.md#webview-mixed-content)|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#legacy-color-mode)|
|[Picker.UpdateMode](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)|[Entry.ImeOptions](~/xamarin-forms/platform/platform-specifics/consuming/android.md#entry-imeoptions)|[ListView.SelectionMode](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#listview-selectionmode)|
|[Page.PrefersStatusBarHidden y Page.PreferredStatusBarUpdateAnimation](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#set_status_bar_visibility)|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/android.md#legacy-color-mode)|[TabbedPage.HeaderIconsEnabled y TabbedPage.HeaderIconsSize](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#tabbedpage-icons)|
|[ScrollView.ShouldDelayContentTouches](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#delay_content_touches)|[Button.UseDefaultPadding y Button.UseDefaultShadow](~/xamarin-forms/platform/platform-specifics/consuming/android.md#button-padding-shadow)|[VisualElement.AccessKey, VisualElement.AccessKeyPlacement, VisualElement.AccessKeyHorizontalOffset y VisualElement.AccessKeyVerticalOffset](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys)|
|[ListView.SeparatorStyle](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#listview-separatorstyle)|[TabbedPage.ToolbarPlacement TabbedPage.BarItemColor y TabbedPage.BarSelectedItemColor](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar)|
|[VisualElement.IsLegacyColorModeEnabled](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#legacy-color-mode)|
|[VisualElement.IsShadowEnabled](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#drop-shadow)|
|[Application.PanGestureRecognizerShouldRecognizeSimultaneously](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#simultaneous-pan-gesture)|

El proceso para consumir una plataforma específica a través de XAML, o a través de la API fluida de código es como sigue:

1. Agregar un `xmlns` declaración o `using` la directiva para el [ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration) espacio de nombres.
1. Agregar un `xmlns` declaración o `using` la directiva para el espacio de nombres que contiene la funcionalidad específica de la plataforma:
    1. En iOS, se trata la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres.
    1. En Android, se trata la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres. Para Android AppCompat, este es el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espacio de nombres.
    1. En la plataforma Universal de Windows, se trata la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres.
1. Aplicar el específico de la plataforma de XAML, o de código con el `On<T>` API fluida. El valor de `T` puede ser el [ `iOS` ](xref:Xamarin.Forms.PlatformConfiguration.iOS), [ `Android` ](xref:Xamarin.Forms.PlatformConfiguration.Android), o [ `Windows` ](xref:Xamarin.Forms.PlatformConfiguration.Windows) tipos a partir de la [ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration) espacio de nombres.

> [!NOTE]
> Tenga en cuenta que intenta consumir un específico de la plataforma en una plataforma que no está disponible no dará como resultado un error. En su lugar, el código se ejecutará sin el específico de la plataforma que se va a aplicar.

Funcionalidades específicas de plataforma consumen a través de la `On<T>` devueltos de la API de código fluent [ `IPlatformElementConfiguration` ](xref:Xamarin.Forms.IPlatformElementConfiguration`2) objetos. Esto permite que varias funcionalidades específicas de plataforma que se invocará en el mismo objeto con el método en cascada.

Para obtener más información sobre las funcionalidades específicas de plataforma, consulte [consumiendo funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/consuming/index.md) y [creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md).


## <a name="related-links"></a>Vínculos relacionados

- [Consumo de funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/consuming/index.md)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
