---
title: "obtener información específica de la plataforma de iOS"
description: "Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo consumir el iOS plataforma específicos que están integrados en Xamarin.Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/16/2017
ms.openlocfilehash: 798bb2b15534a620acbe76080e171af1a548ac25
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="ios-platform-specifics"></a>obtener información específica de la plataforma de iOS

_Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo consumir el iOS plataforma específicos que están integrados en Xamarin.Forms._

En iOS, Xamarin.Forms contiene los detalles siguientes de la plataforma:

- Desenfoque compatibilidad con [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/). Para obtener más información, consulte [Aplicar desenfoque](#blur).
- Controlar si el título de página se muestra como un título en la barra de navegación de página grande. Para obtener más información, consulte [mostrar títulos grandes](#large_title).
- Asegurarse de esa página de contenido se coloca en un área de la pantalla que es seguro para todos los dispositivos iOS. Para obtener más información, consulte [habilitación de la Guía de diseño del área segura](#safe_area_layout).
- Una barra de navegación translúcido. Para obtener más información, consulte [realizar la translúcido de barra de navegación](#translucent_navigation_bar).
- Controlar si el texto de la barra de estado de color en un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) se ajusta para que coincida con la luminosidad de la barra de navegación. Para obtener más información, consulte [ajustar el modo de Color del texto de barra de estado](#status_bar_color_mode).
- Asegurarse de que especifica el texto se ajusta en un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) ajustando el tamaño de fuente. Para obtener más información, consulte [ajustar el tamaño de fuente de una entrada](#adjust_font_size).
- Controlar cuándo se produce la selección de elementos en una [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Para obtener más información, consulte [controlar la selección de elementos de selector](#picker_update_mode).
- Establecer la visibilidad de la barra de estado en un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Para obtener más información, consulte [establecer la visibilidad de la barra de estado en una página](#set_status_bar_visibility).
- Controlar si una [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) controla un gesto táctil o pasa a su contenido. Para obtener más información, consulte [retrasar toques contenido en un ScrollView](#delay_content_touches).

<a name="blur" />

## <a name="applying-blur"></a>Aplicar desenfoque

Este específica de la plataforma se usa para difuminar el contenido superpuesto situado debajo de él y se consume en XAML estableciendo el [ `VisualElement.BlurEffect` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty/) propiedad adjunta a un valor de la [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
    <Image Source="monkeyface.png" />
    <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

El `BoxView.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El [ `VisualElement.UseBlurEffect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres, se usa para aplicar el efecto de desenfoque, con el [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) enumeración proporciona cuatro valores: [ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None/), [ `ExtraLight` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight/), [ `Light` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light/), y [ `Dark` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark/).

El resultado es que un determinado [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) se aplica a la [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) instancia qué desenfoque el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) niveles por debajo de ella:

![](ios-images/blur-effect.png "Desenfoque efecto específico de la plataforma")

<a name="large_title" />

## <a name="displaying-large-titles"></a>Mostrar títulos grandes

Este específica de la plataforma se usa para mostrar el título de página como un título de gran tamaño en la barra de navegación, para los dispositivos que usan iOS 11 o superior. Un título de gran tamaño se alinea a la izquierda y usa una fuente mayor y pasa a un título estándar cuando el usuario comienza a desplazamiento de contenido, por lo que se usen de forma eficaz el espacio real en pantalla. Sin embargo, en orientación horizontal, el título se devolverá al centro de la barra de navegación para optimizar el diseño del contenido. Se consume en XAML estableciendo el `NavigationPage.PrefersLargeTitles` propiedad adjunta un `boolean` valor:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

También puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

El `NavigationPage.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El `NavigationPage.SetPrefersLargeTitle` método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) controla el espacio de nombres, si se habilitan los títulos grandes.

Siempre que los títulos grandes están habilitados en el [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), todas las páginas en la pila de navegación muestra títulos grandes. Este comportamiento puede invalidarse en páginas estableciendo el `Page.LargeTitleDisplay` propiedad adjunta a un valor de la `LargeTitleDisplayMode` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, puede reemplazarse el comportamiento de la página de C# utilizando la API fluida de:

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

El `Page.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El `Page.SetLargeTitleDisplay` método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres, controla el comportamiento de título grande en la [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), con el `LargeTitleDisplayMode` enumeración proporciona tres posibles valores:

- `Always` : forzar la barra de navegación y la fuente tamaño que se utilizará el formato de gran tamaño.
- `Automatic` : use el mismo estilo (grande o pequeño) que el elemento anterior en la pila de navegación.
- `Never` – forzar el uso de la barra de navegación de formato normal, pequeña.

Además, el `SetLargeTitleDisplay` método se puede utilizar para activar o desactivar los valores de enumeración mediante una llamada a la `LargeTitleDisplay` método, que devuelve el valor actual `LargeTitleDisplayMode`:

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

El resultado es que un determinado `LargeTitleDisplayMode` se aplica a la [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), que controla el comportamiento de título grande:

![](ios-images/large-title.png "Desenfoque efecto específico de la plataforma")

<a name="safe_area_layout" />

## <a name="enabling-the-safe-area-layout-guide"></a>Habilitación de la Guía de diseño de la zona segura

Se utiliza este específica de la plataforma para asegurarse de que el contenido de la página está situado en un área de la pantalla que es seguro para todos los dispositivos que usan iOS 11 y versiones posteriores. En concreto, le servirá de ayuda para asegurarse de que el contenido no recortado por esquinas redondeadas dispositivo, el indicador de inicio o el alojamiento del sensor en un iPhone X. Se consume en XAML estableciendo el `Page.UseSafeArea` propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

El `Page.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El `Page.SetUseSafeArea` método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres, controla si la Guía de diseño de la zona segura está habilitada.

El resultado es que el contenido de la página se puede colocar en un área de la pantalla que es seguro para todos los iPhone:

[![](ios-images/safe-area-layout.png "Guía de diseño de la zona segura")](ios-images/safe-area-layout-large.png#lightbox "Guía de diseño de la zona segura")

> [!NOTE]
> Se usa la zona segura de definidos por Apple de Xamarin.Forms para establecer el [ `Page.Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) propiedad y reemplazan los valores anteriores de esta propiedad que se han establecido.

La zona segura puede personalizarse mediante la recuperación de su [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) valor con el `Page.SafeAreaInsets` método desde el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres. A continuación, se puede modificar como necesaria y volver a asignar a la `Padding` propiedad en el constructor de página o [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) invalidar:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="translucent_navigation_bar" />

## <a name="making-the-navigation-bar-translucent"></a>Hacer que la barra de navegación translúcido

Este específica de la plataforma se utiliza para cambiar la transparencia de la barra de navegación y se consume en XAML estableciendo el [ `NavigationPage.IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty/) propiedad adjunta un `boolean` valor:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

El `NavigationPage.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El [ `NavigationPage.EnableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres, se utiliza para hacer que la barra de navegación translúcido. Además, el [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage/) clase en el `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espacio de nombres también tiene un [ `DisableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método que restaura la barra de navegación a su estado predeterminado, y un [ `SetIsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/System.Boolean/) método que se puede usar para activar o desactivar la transparencia de la barra de navegación mediante una llamada a la [ `IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

El resultado es que se puede cambiar la transparencia de la barra de navegación:

![](ios-images/translucent-navigation-bar.png "Barra de navegación translúcido específica de la plataforma")

<a name="status_bar_color_mode" />

## <a name="adjusting-the-status-bar-text-color-mode"></a>Ajustar el modo de Color de texto de la barra de estado

Esta específica de la plataforma controla si el texto de la barra de estado de color en un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) se ajusta para que coincida con la luminosidad de la barra de navegación. Se consume en XAML estableciendo el [ `NavigationPage.StatusBarTextColorMode` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty/) propiedad adjunta a un valor de la [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumeración:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

El `NavigationPage.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El [ `NavigationPage.SetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres, controles si el texto de la barra de estado de color en la [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) se ajusta para que coincida con el luminosidad de la barra de navegación, con el [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumeración proporciona dos valores posibles:

- [`DoNotAdjust`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust/) : indica que no se debe ajustar el color del texto de la barra de estado.
- [`MatchNavigationBarTextLuminosity`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity/) : indica que el color del texto de la barra de estado debe coincidir con la luminosidad de la barra de navegación.

Además, el [ `GetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/) método se puede utilizar para recuperar el valor actual de la [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumeración que se aplica a la [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/).

El resultado es que el estado de la barra de color del texto en un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) se puede ajustar para que coincida con la luminosidad de la barra de navegación. En este ejemplo, lo cambios de color de texto de la barra de estado como el usuario cambia entre el [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) y [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) páginas de un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

![](ios-images/status-bar-text-color-mode.png "Barra de estado texto Color modo específico de la plataforma")

<a name="adjust_font_size" />

## <a name="adjusting-the-font-size-of-an-entry"></a>Ajustar el tamaño de fuente de una entrada

Este específica de la plataforma se usa para escalar el tamaño de fuente de un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) para asegurarse de que el texto de entrada se ajuste en el control. Se consume en XAML estableciendo el [ `Entry.AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty/) propiedad adjunta un `boolean` valor:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

El `Entry.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El [ `Entry.EnableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres, se usa para escalar el tamaño de fuente del texto de entrada para asegurarse de que se ajuste a el [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). Además, el [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry/) clase en el `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espacio de nombres también tiene un [ `DisableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método que deshabilita este específica de la plataforma, y un [ `SetAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/System.Boolean/) método que se puede usar para cambiar el tamaño de fuente escalar mediante una llamada a la [ `AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

El resultado es que el tamaño de fuente de la [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) se escala para asegurarse de que el texto de entrada se ajuste en el control:

![](ios-images/entry-font-size.png "Ajustar el tamaño de fuente de entrada plataforma-específicos")

<a name="picker_update_mode" />

## <a name="controlling-picker-item-selection"></a>Controlar la selección de elementos de selector

Este específica de la plataforma controla cuando se produce la selección de elementos en una [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), que permite al usuario especificar que la selección de elementos se produce al examinar los elementos del control, o solo una vez que la **realiza** se presionó el botón. Se consume en XAML estableciendo el `Picker.UpdateMode` propiedad adjunta a un valor de la `UpdateMode` enumeración:

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

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

El `Picker.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El `Picker.SetUpdateMode` método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres, se utiliza para controlar cuándo se produce la selección de elementos, con el `UpdateMode` enumeración proporciona dos valores posibles:

- `Immediately` : selección de elementos se produce cuando el usuario examina los elementos de la [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Este es el comportamiento predeterminado de Xamarin.Forms.
- `WhenFinished` : selección de elementos solo se produce una vez que el usuario ha presionado el **realiza** botón en el [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/).

Además, el `SetUpdateMode` método se puede utilizar para activar o desactivar los valores de enumeración mediante una llamada a la `UpdateMode` método, que devuelve el valor actual `UpdateMode`:

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

El resultado es que un determinado `UpdateMode` se aplica a la [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), que controla cuándo se produce la selección de elementos:

[![](ios-images/picker-updatemode.png "Selector de UpdateMode específico de plataforma")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="set_status_bar_visibility" />

## <a name="setting-the-status-bar-visibility-on-a-page"></a>Configuración de la barra de estado de visibilidad en una página

Este específica de la plataforma se usa para establecer la visibilidad de la barra de estado en un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), e incluye la capacidad para controlar cómo la barra de estado entra o sale de la `Page`. Se consume en XAML estableciendo el `Page.PrefersStatusBarHidden` propiedad adjunta a un valor de la `StatusBarHiddenMode` enumeración y, opcionalmente, el `Page.PreferredStatusBarUpdateAnimation` propiedad adjunta a un valor de la `UIStatusBarAnimation` enumeración:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

El `Page.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El `Page.SetPrefersStatusBarHidden` método, en la `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espacio de nombres, se usa para establecer la visibilidad de la barra de estado en un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) especificando uno de los `StatusBarHiddenMode` valores de enumeración: `Default`, `True` , o `False`. El `StatusBarHiddenMode.True` y `StatusBarHiddenMode.False` valores establecen la visibilidad de la barra de estado, independientemente de la orientación del dispositivo y la `StatusBarHiddenMode.Default` valor oculta la barra de estado en un entorno de compact verticalmente.

El resultado es que la visibilidad de la barra de estado en un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) se pueden establecer:

![](ios-images/hide-status-bar.png "Barra de estado de visibilidad plataforma específica")

> [!NOTE]
> En un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), especificado `StatusBarHiddenMode` valor de enumeración también actualizará la barra de estado en todas las páginas secundarias. En todos los demás [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-tipos derivados, especificados `StatusBarHiddenMode` valor de enumeración solo actualizará la barra de estado de la página actual.

El `Page.SetPreferredStatusBarUpdateAnimation` método se usa para establecer cómo la barra de estado entra o sale de la [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) especificando uno de los `UIStatusBarAnimation` valores de enumeración: `None`, `Fade`, o `Slide`. Si el `Fade` o `Slide` se especifica el valor de enumeración, un segundo 0,25 animación se ejecuta como la barra de estado entra o sale de la `Page`.

<a name="delay_content_touches" />

## <a name="delaying-content-touches-in-a-scrollview"></a>Retrasar los últimos retoques contenidos en un ScrollView

Un temporizador implícita se desencadena cuando comienzan una gestos táctiles en un [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) en iOS y `ScrollView` decide, en función de la acción del usuario en el intervalo del temporizador, si debe controlar el gesto o pasar a su contenido. De forma predeterminada, el archivo iOS `ScrollView` los últimos retoques contenido retrasos, pero esto puede causar problemas en algunas circunstancias con la `ScrollView` contenido no ganando el gesto cuando debería. Por lo tanto, este controles específicos de la plataforma si un `ScrollView` controla un gesto táctil o pasa a su contenido. Se consume en XAML estableciendo el `ScrollView.ShouldDelayContentTouches` propiedad adjunta un `boolean` valor:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

El `ScrollView.On<iOS>` método especifica que sólo se ejecutará este específica de la plataforma de iOS. El `ScrollView.SetShouldDelayContentTouches` método, en la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) espacio de nombres, se utiliza para controlar si una [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) controla un gesto táctil o pasa a su contenido. Además, el `SetShouldDelayContentTouches` método se puede utilizar para activar o desactivar retrasar los últimos retoques contenidos mediante una llamada a la `ShouldDelayContentTouches` método devuelva un valor si se retrasan los últimos retoques contenidos:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

El resultado es que un [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) puede deshabilitar retrasar recibir contenido los últimos retoques, así que en este escenario el [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) recibe el movimiento en lugar de la [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) página de la [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

[![](ios-images/scrollview-delay-content-touches.png "Contenido de retraso ScrollView toca específica de la plataforma")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

## <a name="summary"></a>Resumen

Este artículo muestra cómo consumir el iOS plataforma específicos que están integrados en Xamarin.Forms. Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos.


## <a name="related-links"></a>Vínculos relacionados

- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)
