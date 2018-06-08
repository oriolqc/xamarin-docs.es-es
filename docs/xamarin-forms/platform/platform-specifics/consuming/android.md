---
title: Detalles de plataforma Android
description: Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo utilizar las características de plataforma Android que se integran en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 705c3037505b61bfb364772187e0ce8ead14b27f
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847776"
---
# <a name="android-platform-specifics"></a>Detalles de plataforma Android

_Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo utilizar las características de plataforma Android que se integran en Xamarin.Forms._

En Android, Xamarin.Forms contiene los detalles siguientes de la plataforma:

- Establecer el modo de funcionamiento de un teclado programable. Para obtener más información, consulte [establecer el modo de entrada de teclado suave](#soft_input_mode).
- Habilitar desplazamiento rápido en una [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) para obtener más información, consulte [Habilitar desplazamiento rápido en un control ListView](#fastscroll).
- Habilitación de deslizar rápidamente entre las páginas de un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Para obtener más información, consulte [habilitar Deslizar rápidamente entre páginas en un TabbedPage](#enable_swipe_paging).
- Controlar el orden Z de los elementos visuales para determinar el orden de dibujo. Para obtener más información, consulte [controlar la elevación de elementos visuales](#elevation).
- Deshabilitar la [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) y [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) página eventos de ciclo de vida en pausa y reanudar respectivamente, para las aplicaciones que utilizan AppCompat. Para obtener más información, consulte [deshabilitar la Disappearing y eventos de ciclo de vida de la página que aparecen](#disable_lifecycle_events).
- Controlar si una [ `WebView` ](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto. Para obtener más información, consulte [Habilitar contenido mixto en un WebView](#webview-mixed-content).
- Establecer el método de entrada de opciones del editor para el teclado de pantalla para una [ `Entry` ](xref:Xamarin.Forms.Entry). Para obtener más información, consulte [opciones de configuración de entrada Input Method Editor](#entry-imeoptions).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [deshabilitar el modo de Color heredado](#legacy-color-mode).
- Usando los valores de instantáneas de los botones de Android y relleno predeterminado. Para obtener más información, consulte [utilizando botones de Android](#button-padding-shadow).

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

<a name="webview-mixed-content" />

## <a name="enabling-mixed-content-in-a-webview"></a>Habilitar contenido mixto en una vista Web

Esta específica de la plataforma controla si un [ `WebView` ](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto en aplicaciones que tienen como destino API 21 o mayor. Contenido mixto es el contenido que se carga por primera vez a través de una conexión HTTPS, pero que cargan recursos (como imágenes, audio, vídeo, hojas de estilos, las secuencias de comandos) en una conexión HTTP. Se consume en XAML estableciendo el [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propiedad adjunta a un valor de la [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

El `WebView.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) método, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para controlar si se puede mostrar contenido mixto, con el [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeración proporciona tres valores posibles:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indica que la [ `WebView` ](xref:Xamarin.Forms.WebView) permitirá a un origen HTTPS cargar el contenido desde un origen HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indica que la [ `WebView` ](xref:Xamarin.Forms.WebView) no permitirá que un origen HTTPS cargar el contenido desde un origen HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indica que la [ `WebView` ](xref:Xamarin.Forms.WebView) tratará de ser compatible con el enfoque del explorador de web del dispositivo más reciente. Algún contenido HTTP puede permitirse sean cargados por un origen HTTPS y otros tipos de contenido se bloqueará. Los tipos de contenido que se bloquean o permiten pueden cambiar con cada versión de sistema operativo.

El resultado es que un determinado [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valor se aplica a la [ `WebView` ](xref:Xamarin.Forms.WebView), que controla si se puede mostrar contenido mixto:

[![WebView mixto control de contenido específico de la plataforma](android-images/webview-mixedcontent.png "WebView mixto control de contenido específico de la plataforma")](android-images/webview-mixedcontent-large.png#lightbox "WebView mixto control de contenido específico de la plataforma")

<a name="entry-imeoptions" />

## <a name="setting-entry-input-method-editor-options"></a>Opciones del Editor de métodos de entrada de configuración de entrada

Este específica de la plataforma establece el método de entrada opciones del editor (IME) para el teclado de pantalla para una [ `Entry` ](xref:Xamarin.Forms.Entry). Esto incluye la configuración del botón de acción de usuario en la esquina inferior del teclado automático y las interacciones con el `Entry`. Se consume en XAML estableciendo el [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) propiedad adjunta a un valor de la [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

El `Entry.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) método, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la opción de acción del método de entrada para el teclado programable para la [ `Entry` ](xref:Xamarin.Forms.Entry), con el [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeración proporciona los valores siguientes:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) : indica que no se requiere ninguna clave de acción específica y que el control subyacente generará sí solo si es posible. Esto será `Next` o `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) : indica que ninguna clave de acción estará disponible.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) : indica que la clave de acción llevará a cabo una operación de "Ir", que tarda el usuario en el destino del texto escrito.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) : indica que la clave de acción realiza una operación de "búsqueda", que tarda el usuario a los resultados de búsqueda para el texto han escrito.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) : indica que la clave de acción llevará a cabo una operación de "Enviar", que se entrega el texto a su destino.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) : indica que la clave de acción llevará a cabo una operación "siguiente", que tarda el usuario en el siguiente campo que acepte texto.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) : indica que la clave de acción llevará a cabo una operación de "listo", cierre del teclado de pantalla.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) : indica que la clave de acción llevará a cabo una operación "anterior", realizar el usuario al campo anterior que acepte texto.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) : la máscara para seleccionar opciones de acción.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) : indica que el corrector ortográfico se obtenga información acerca del usuario, ni sugerir correcciones basadas en lo que el usuario ha escrito anteriormente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) : indica que la interfaz de usuario no debe pasar a pantalla completa.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) : indica que no se debe mostrar ninguna interfaz de usuario para el texto extraído.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) : indica que no se mostrará ninguna interfaz de usuario para las acciones personalizadas.

El resultado es que un determinado [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valor se aplica a del teclado de pantalla para el [ `Entry` ](xref:Xamarin.Forms.Entry), que establece el método de entrada de opciones del editor:

[![Método editor-específica de la plataforma de entrada de entrada](android-images/entry-imeoptions.png "método editor-específica de la plataforma de entrada de entrada")](android-images/entry-imeoptions-large.png#lightbox "método editor-específica de la plataforma de entrada de entrada")

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>Deshabilitar el modo de Color heredado

Algunas de las vistas de Xamarin.Forms las características de un modo de color heredado. En este modo, cuando la [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad de la vista se establece en `false`, la vista invalidará los colores establecidos por el usuario con los colores nativo de forma predeterminada para el estado deshabilitado. Para hacia atrás compatibilidad, este modo de color heredado sigue siendo el comportamiento predeterminado para vistas admitidas.

Este específica de la plataforma deshabilita este modo heredado de color, para que sigan estando colores establecido en una vista por el usuario incluso cuando se deshabilita la vista. Se consume en XAML estableciendo el [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta `false`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean)) método, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para controlar si se deshabilita el modo de color heredado. Además, el [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement})) método se puede utilizar para devolver si está deshabilitado el modo de color heredado.

El resultado es que se puede deshabilitar el modo de color heredados, para que los colores establecidos en una vista por el usuario permanecen incluso cuando se deshabilita la vista:

![](android-images/legacy-color-mode-disabled.png "Modo de color heredado deshabilitado")

> [!NOTE]
> Al establecer un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) en una vista, se omite por completo el modo de color heredado. Para obtener más información sobre los estados visuales, consulte [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="button-padding-shadow" />

## <a name="using-android-buttons"></a>Con los botones de Android

Este específica de la plataforma controla si los botones de Xamarin.Forms usar el relleno predeterminado y los valores de sombra de botones Android. Se consume en XAML estableciendo el [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) y [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) adjunta propiedades a `boolean` valores:

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

El `Button.On<Android>` método especifica que sólo se ejecutará este específica de la plataforma en Android. El [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) y[ `Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) métodos, en la [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utilizan para controlar si los botones de Xamarin.Forms utilizan el valor predeterminado relleno y los valores de sombra de botones Android. Además, el [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) y [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) métodos pueden usarse para devolver si un botón usa el valor predeterminado de relleno valor y el valor de sombra de forma predeterminada, respectivamente.

El resultado es que pueden usar los botones de Xamarin.Forms el relleno predeterminado y los valores de sombra de botones Android:

![](android-images/button-padding-and-shadow.png "Modo de color heredado deshabilitado")

Tenga en cuenta que en la captura de pantalla por encima de cada uno de ellos [ `Button` ](xref:Xamarin.Forms.Button) tiene definiciones idénticas, salvo que el derecho `Button` utiliza el valor de relleno predeterminado y los valores de instantáneas de los botones de Android.

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar las características de plataforma Android que se integran en Xamarin.Forms. Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos.

## <a name="related-links"></a>Vínculos relacionados

- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
