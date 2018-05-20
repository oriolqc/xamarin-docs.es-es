---
title: Detalles de plataforma Android
description: Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo utilizar las características de plataforma Android que se integran en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/17/2017
ms.openlocfilehash: 8aa17c868ce1d0343eab6758c03aaf042c27130e
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2018
---
# <a name="android-platform-specifics"></a>Detalles de plataforma Android

_Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo utilizar las características de plataforma Android que se integran en Xamarin.Forms._

En Android, Xamarin.Forms contiene los detalles siguientes de la plataforma:

- Establecer el modo de funcionamiento de un teclado programable. Para obtener más información, consulte [establecer el modo de entrada de teclado suave](#soft_input_mode).
- Habilitar desplazamiento rápido en una [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) para obtener más información, consulte [Habilitar desplazamiento rápido en un control ListView](#fastscroll).
- Habilitación de deslizar rápidamente entre las páginas de un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Para obtener más información, consulte [habilitar Deslizar rápidamente entre páginas en un TabbedPage](#enable_swipe_paging).
- Controlar el orden Z de los elementos visuales para determinar el orden de dibujo. Para obtener más información, consulte [controlar la elevación de elementos visuales](#elevation).
- Deshabilitar la [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) y [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) página eventos de ciclo de vida en pausa y reanudar respectivamente, para las aplicaciones que utilizan AppCompat. Para obtener más información, consulte [deshabilitar la Disappearing y eventos de ciclo de vida de la página que aparecen](#disable_lifecycle_events).

<a name="soft_input_mode" />

## <a name="setting-the-soft-keyboard-input-mode"></a>Establecer el modo de entrada de teclado programable

Este específica de la plataforma se usa para establecer el modo de funcionamiento para un área de entrada de teclado de pantalla y se consume en XAML estableciendo el [ `Application.WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty/) propiedad adjunta a un valor de la [ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) enumeración:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

El `Application.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El [ `Application.UseWindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) método, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) espacio de nombres, se usa para establecer el modo de funcionamiento del área de entrada de teclado de pantalla, con el [ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) proporciona dos valores de enumeración: [ `Pan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan/) y [ `Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/). El `Pan` valor usa la [ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/) opción de ajuste, que no cambiar el tamaño de la ventana cuando un control de entrada tiene el foco. En su lugar, el contenido de la ventana se cambia a panorámica para que no oculte el foco actual del teclado de pantalla. El `Resize` valor usa la [ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/) opción de ajuste, que cambia el tamaño de la ventana cuando un control de entrada tiene el foco, para dejar espacio para el teclado programable.

El resultado es que el teclado de pantalla área se puede establecer el modo de funcionamiento cuando un control de entrada tiene el foco de entrada:

[![](android-images/pan-resize.png "Teclado programable operativo modo específico de la plataforma")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll" />

## <a name="enabling-fast-scrolling-in-a-listview"></a>Habilitar desplazamiento rápido en un control ListView

Este específica de la plataforma se utiliza para habilitar el desplazamiento rápido a través de los datos en un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Se consume en XAML estableciendo el `ListView.IsFastScrollEnabled` propiedad adjunta un `boolean` valor:

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

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

El `ListView.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El `ListView.SetIsFastScrollEnabled` método, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) espacio de nombres, se utiliza para habilitar el desplazamiento rápido a través de los datos en un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Además, el `SetIsFastScrollEnabled` método se puede utilizar para activar o desactivar desplazamiento rápido mediante una llamada a la `IsFastScrollEnabled` método devuelva un valor si está habilitado el desplazamiento rápido:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

El resultado es ese desplazamiento rápido a través de los datos en un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) pueden habilitarse, que cambia el tamaño de la posición de desplazamiento:

[![](android-images/fastscroll.png "ListView FastScroll específico de plataforma")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging" />

## <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>Habilitación de deslizar rápidamente entre las páginas de un TabbedPage

Se utiliza este específica de la plataforma para habilitar Deslizar rápidamente con un gesto del dedo horizontal entre las páginas de un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Se consume en XAML estableciendo el [ `TabbedPage.IsSwipePagingEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty/) propiedad adjunta un `boolean` valor:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

El `TabbedPage.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El [ `TabbedPage.SetIsSwipePagingEnabled` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled/p/Xamarin.Forms.BindableObject/System.Boolean/) método, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) espacio de nombres, se utiliza para habilitar Deslizar rápidamente entre las páginas de un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espacio de nombres también tiene un [ `EnableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/) método que permite este específica de la plataforma, y un [ `DisableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/) método que deshabilita Este específica de la plataforma. El [ `TabbedPage.OffscreenPageLimit` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty/) propiedad adjunta, y [ `SetOffscreenPageLimit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit/p/Xamarin.Forms.BindableObject/System.Int32/) método, se utilizan para establecer el número de páginas que se deben conservar en un estado de inactividad a cada lado de la página actual.

El resultado es que la paginación deslice el dedo a través de las páginas se muestran por un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) está habilitada:

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>Controlar la elevación de los elementos visuales

Este específica de la plataforma se usa para controlar la elevación o el orden Z de los elementos visuales en las aplicaciones que tienen como destino API 21 o mayor. La elevación de un elemento visual determina su orden de dibujo, con elementos visuales con valores más altos de Z occluding elementos visuales con valores más bajos de Z. Se consume en XAML estableciendo el `VisualElement.Elevation` propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

El `Button.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El `VisualElement.SetElevation` método, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) espacio de nombres, se usa para establecer la elevación de los elementos visuales a una que aceptan valores NULL `float`. Además, la `VisualElement.GetElevation` método se puede utilizar para recuperar el valor de elevación de un elemento visual.

El resultado es que se puede controlar la elevación de los elementos visuales para que los elementos visuales con los valores más altos de Z occlude elementos visuales con valores más bajos de Z. Por lo tanto, en este ejemplo el segundo [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) se representa por encima del [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) porque tiene un valor más alto de elevación:

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>Deshabilitar los eventos de ciclo de vida de página que aparece y desaparece

Este específica de la plataforma se utiliza para deshabilitar la [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) y [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) eventos de página en la aplicación pausar y reanudar respectivamente, para las aplicaciones que utilizan AppCompat. Además, incluye la capacidad para controlar si el teclado de pantalla se muestra al reanudar si se mostró en pausa, siempre que se establece el modo de funcionamiento del teclado suave en [ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

> [!NOTE]
> Tenga en cuenta que estos eventos están habilitados de forma predeterminada para conservar el comportamiento existente para las aplicaciones que dependen de los eventos. Deshabilitar estos eventos hace que el ciclo de eventos AppCompat coincide con el ciclo de eventos anterior AppCompat.

Este específica de la plataforma puede utilizarse en XAML estableciendo el [ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty/), [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty/), y [ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty/) propiedades adjuntas a `boolean` valores:

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

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

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

El `Application.Current.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El [ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) método, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/) espacio de nombres, se utiliza para habilitar o deshabilitar la activación de la [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) eventos de página, cuando la aplicación entra en segundo plano. El [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) método se utiliza para habilitar o deshabilitar la activación de la [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) eventos de página, cuando la aplicación se reanuda desde el fondo. El [ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) se utiliza el método de control si el teclado de pantalla se muestra al reanudar si se mostró en pausa, proporcionado que se establece el modo de funcionamiento del teclado suave en [ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

El resultado es que la [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) y [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) eventos de página no se desencadena en pausa de la aplicación y reanudar respectivamente y era de ese if del teclado de pantalla que se muestran cuando la aplicación estaba en pausa, también se mostrará cuando se reanude la aplicación:

[![](android-images/keyboard-on-resume.png "Específico de plataforma de eventos del ciclo de vida")](android-images/keyboard-on-resume-large.png#lightbox "específico de plataforma de eventos del ciclo de vida")

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar las características de plataforma Android que se integran en Xamarin.Forms. Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos.


## <a name="related-links"></a>Vínculos relacionados

- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
