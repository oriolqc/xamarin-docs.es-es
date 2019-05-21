---
title: Eventos de ciclo de vida de página en Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. Este artículo explica cómo consumir el Android específicos de la plataforma deshabilita el Disappearing y eventos de la página de aparición en la aplicación, pausar y reanudación, respectivamente.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 295b060ee795244184b3ea88b101c720436d1fe5
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926528"
---
# <a name="page-lifecycle-events-on-android"></a>Eventos de ciclo de vida de página en Android

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este Android específicos de plataforma se usan para deshabilitar la [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) y [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) eventos de página en la aplicación pausar y reanudar, respectivamente, para las aplicaciones que usan AppCompat. Además, incluye la capacidad para controlar si se muestra el teclado en pantalla al reanudar si se mostrara en pausa, siempre que el modo de funcionamiento del teclado en pantalla se establece en [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

> [!NOTE]
> Tenga en cuenta que estos eventos están habilitados de forma predeterminada para conservar el comportamiento existente para las aplicaciones que dependen de los eventos. Deshabilitar estos eventos hace que el ciclo de eventos AppCompat coincide con el ciclo de eventos anteriores a AppCompat.

Esta plataforma específicas que pueden utilizarse en XAML estableciendo el [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty), [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty), y [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) propiedades adjuntas a `boolean` valores:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

El `Application.Current.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espacio de nombres, se usa para habilitar o deshabilitar la activación de la [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) eventos de página, cuando la aplicación entra en segundo plano. El [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) método se utiliza para habilitar o deshabilitar la activación de la [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) eventos de página, cuando se reanuda la aplicación en segundo plano. El [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) se usa el método de control si el teclado en pantalla se muestra en la reanudación, si se mostró en pausa, proporcionado que se establece el modo de funcionamiento del teclado en pantalla en [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

El resultado es que el [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) y [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) eventos de página no se desencadena en pausa de la aplicación y reanudación, respectivamente, y fue el teclado en pantalla de ese if que se muestra cuando la aplicación estaba en pausa, también se mostrará cuando se reanuda la aplicación:

[![](page-lifecycle-events-images/keyboard-on-resume.png "Específico de plataforma de los eventos del ciclo de vida")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "específicos de plataforma de los eventos del ciclo de vida")

## <a name="related-links"></a>Vínculos relacionados

- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
