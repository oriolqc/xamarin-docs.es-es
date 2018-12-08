---
title: funcionalidades específicas de plataforma de iOS
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se muestra cómo consumir el iOS funcionalidades específicas de plataforma que se integran en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 749ed2876881f1c2005226e17a84bcb3a44425f8
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056751"
---
# <a name="ios-platform-specifics"></a>funcionalidades específicas de plataforma de iOS

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

_Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se muestra cómo consumir el iOS funcionalidades específicas de plataforma que se integran en Xamarin.Forms._

## <a name="visualelements"></a>VisualElements

En iOS, se proporciona la siguiente funcionalidad específica de la plataforma para las páginas, vistas y diseños de Xamarin.Forms:

- Desenfoque ninguna compatibilidad con [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [Aplicar desenfoque](#blur).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [deshabilitar el modo heredado Color](#legacy-color-mode).
- Habilitación de una sombra paralela en un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [habilitar una sombra paralela](#drop-shadow).

<a name="blur" />

### <a name="applying-blur"></a>Aplicación de desenfoque

Este específicos de la plataforma se usa para difuminar el contenido en capas por debajo de ella y se consume en XAML mediante el establecimiento del [ `VisualElement.BlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) propiedad adjunta a un valor de la [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeración:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

El `BoxView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `VisualElement.UseBlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para aplicar el efecto de desenfoque, con el [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeración que proporciona cuatro valores: [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None), [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight), [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light), y [ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark).

El resultado es que un determinado [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) se aplica a la [ `BoxView` ](xref:Xamarin.Forms.BoxView) instancia que desenfoca la [ `Image` ](xref:Xamarin.Forms.Image) por niveles por debajo de ella:

![](ios-images/blur-effect.png "Específico de la plataforma efecto de desenfoque")

> [!NOTE]
> Al agregar un efecto de desenfoque a una [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), eventos de toque, todavía recibirán el `VisualElement`.

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>Deshabilitar el modo de Color heredado

Algunas de las vistas de Xamarin.Forms cuentan con un modo de color heredado. En este modo, cuando el [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) se establece la propiedad de la vista en `false`, la vista invalida los colores establecidos por el usuario con los colores nativo predeterminado para el estado deshabilitado. Para hacia atrás compatibilidad, este modo heredado de color permanece el comportamiento predeterminado para las vistas admitidas.

Este específicos de la plataforma deshabilita este modo heredado de color, para que los colores establecidos en una vista por el usuario permanezcan incluso cuando la vista está deshabilitada. Se consume en XAML estableciendo el [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta `false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si se deshabilita el modo de color heredado. Además, el [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) método puede utilizarse para devolver si está deshabilitado el modo de color heredado.

El resultado es que se puede deshabilitar el modo de color heredados, para que los colores establecidos en una vista por el usuario permanezcan incluso cuando se deshabilita la vista:

![](ios-images/legacy-color-mode-disabled.png "Modo heredado color deshabilitado")

> [!NOTE]
> Al establecer un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) en una vista, se omite completamente el modo de color heredado. Para obtener más información acerca de los estados visuales, vea [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="drop-shadow" />

### <a name="enabling-a-drop-shadow"></a>Habilitación de una sombra paralela

Este específicos de la plataforma se utilizan para habilitar una sombra paralela en un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Se consume en XAML estableciendo el [ `VisualElement.IsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) propiedad adjunta `true`, junto con un número de adicional opcional adjunta propiedades que controlan el efecto de sombra:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

El `VisualElement.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `VisualElement.SetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si una sombra paralela está habilitada en el `VisualElement`. Además, se pueden invocar los métodos siguientes para controlar el efecto de sombra:

- [`SetShadowColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Color)) : establece el color de la sombra paralela. El color predeterminado es [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- [`SetShadowOffset`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Size)) : establece el desplazamiento de la sombra paralela. El desplazamiento cambia la dirección de la sombra se convierte y se especifica como un [ `Size` ](xref:Xamarin.Forms.Size) valor. El `Size` los valores de la estructura se expresan en unidades independientes del dispositivo, con el primer valor que se va a la distancia a la izquierda (valor negativo) o la derecha (valor positivo) y el segundo valor que se va a la distancia anterior (negativo) o por debajo (valor positivo) . El valor predeterminado de esta propiedad es (0.0, 0.0), que da como resultado la sombra que se va a convertir en torno a cada lado de la `VisualElement`.
- [`SetShadowOpacity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) : establece la opacidad de la sombra paralela, con el valor está en el intervalo 0.0 (transparente) a 1.0 (opaco). El valor de opacidad predeterminado es 0,5.
- [`SetShadowRadius`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) : establece el radio de desenfoque utilizado para representar la sombra paralela. El valor de radio predeterminado es 10,0.

> [!NOTE]
> Se puede consultar el estado de una sombra paralela mediante una llamada a la [ `GetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowOffset` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowOpacity` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), y [ `GetShadowRadius` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) métodos.

El resultado es que se puede habilitar una sombra paralela en un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement):

![](ios-images/drop-shadow.png "Efecto de sombra habilitado")

## <a name="views"></a>Vistas

En iOS, se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin.Forms:

- Asegurarse de que especifica el texto se ajusta a un [ `Entry` ](xref:Xamarin.Forms.Entry) ajustando el tamaño de fuente. Para obtener más información, consulte [ajustar el tamaño de fuente de una entrada](#adjust_font_size).
- Establecer el color de cursor de un [ `Entry` ](xref:Xamarin.Forms.Entry). Para obtener más información, consulte [al establecer el Color de Cursor de movimiento](#entry-cursorcolor).
- Establecer el estilo del separador en un [ `ListView` ](xref:Xamarin.Forms.ListView). Para obtener más información, consulte [establecer el estilo del separador en un ListView](#listview-separatorstyle).
- Controlar cuándo se produce la selección de elementos en un [ `Picker` ](xref:Xamarin.Forms.Picker). Para obtener más información, consulte [selección de elementos de control de selector de](#picker_update_mode).
- Habilitar la [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad debe establecerse si pulsa en una posición en la [ `Slider` ](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar la `Slider` thumb. Para obtener más información, consulte [habilitación de un control deslizante para mover al pulsar](#slider-updateontap).

<a name="adjust_font_size" />

### <a name="adjusting-the-font-size-of-an-entry"></a>Ajustar el tamaño de fuente de una entrada

Se usan este específicos de la plataforma para escalar el tamaño de fuente de un [ `Entry` ](xref:Xamarin.Forms.Entry) para asegurarse de que se ajuste el texto de entrada en el control. Se consume en XAML estableciendo el [ `Entry.AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) propiedad adjunta un `boolean` valor:

```xaml
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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

El `Entry.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `Entry.EnableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para escalar el tamaño de fuente del texto de entrada para asegurarse de que se ajuste el [ `Entry` ](xref:Xamarin.Forms.Entry). Además, el [ `Entry` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) clase en el `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espacio de nombres también tiene un [ `DisableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método que se deshabilita este específicos de la plataforma, y un [ `SetAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},System.Boolean)) método que se puede usar para cambiar el tamaño de fuente escalado mediante una llamada a la [ `AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

El resultado es que el tamaño de fuente de la [ `Entry` ](xref:Xamarin.Forms.Entry) se escala para asegurarse de que se ajuste el texto de entrada en el control:

![](ios-images/entry-font-size.png "Ajustar la entrada fuente tamaño específico de la plataforma")

<a name="entry-cursorcolor" />

### <a name="setting-the-entry-cursor-color"></a>Establecer el Color de Cursor de movimiento

Este específicos de la plataforma establece el color de cursor en un [ `Entry` ](xref:Xamarin.Forms.Entry) en un color especificado. Se consume en XAML estableciendo el [ `Entry.CursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) propiedad enlazable para un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

El `Entry.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `Entry.SetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},Xamarin.Forms.Color)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, Establece el color de cursor en un determinado [ `Color` ](xref:Xamarin.Forms.Color). Además, el [ `Entry.GetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.GetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método puede utilizarse para recuperar el color actual del cursor.

El resultado es que el color del cursor en un [ `Entry` ](xref:Xamarin.Forms.Entry) se puede establecer en un determinado [ `Color` ](xref:Xamarin.Forms.Color):

![](ios-images/entry-cursorcolor.png "Color de Cursor de movimiento")

<a name="listview-separatorstyle" />

### <a name="setting-the-separator-style-on-a-listview"></a>Establecer el estilo del separador en un ListView

Este específicos de la plataforma controla si el separador entre las celdas de un [ `ListView` ](xref:Xamarin.Forms.ListView) usa todo el ancho de la `ListView`. Se consume en XAML estableciendo el [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) propiedad adjunta a un valor de la [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

El `ListView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si el separador entre las celdas de la [ `ListView` ](xref:Xamarin.Forms.ListView) usa completo ancho de la `ListView`, con el [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeración que proporciona dos valores posibles:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) : indica el comportamiento de separador de iOS de forma predeterminada. Este es el comportamiento predeterminado en Xamarin.Forms.
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) : indica que se dibujarán los separadores de uno de los bordes de la `ListView` a otro.

El resultado es que un determinado [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valor se aplica a la [ `ListView` ](xref:Xamarin.Forms.ListView), que controla el ancho del separador entre las celdas:

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle específico de plataforma")

> [!NOTE]
> Una vez que se ha establecido el estilo del separador en `FullWidth`, no se puede cambiar a `Default` en tiempo de ejecución.

<a name="picker_update_mode" />

### <a name="controlling-picker-item-selection"></a>Controlar la selección del elemento selector

Este específicos de la plataforma controla cuándo se produce la selección de elementos en un [ `Picker` ](xref:Xamarin.Forms.Picker), lo que permite al usuario especificar que la selección de elementos se produce al examinar los elementos del control, o solo una vez que la **realiza** botón está presionado. Se consume en XAML estableciendo el `Picker.UpdateMode` propiedad adjunta a un valor de la `UpdateMode` enumeración:

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

[![](ios-images/picker-updatemode.png "Específico de plataforma selector UpdateMode")](ios-images/picker-updatemode-large.png#lightbox "selector UpdateMode específico de plataforma")

<a name="slider-updateontap" />

### <a name="enabling-a-slider-thumb-to-move-on-tap"></a>Habilitación de un control deslizante para mover al pulsar el

Este específicos de la plataforma permite la [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad debe establecerse si pulsa en una posición en la [ `Slider` ](xref:Xamarin.Forms.Slider) barra, en lugar de tener que arrastrar la `Slider` thumb. Se consume en XAML estableciendo el [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) propiedad enlazable a `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

El `Slider.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si una derivación de la `Slider` barra establecerá el [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad. Además, el [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider})) método puede utilizarse para devolver si una derivación de en el `Slider` barra establecerá el `Slider.Value` propiedad.

El resultado es que una derivación de en el [ `Slider` ](xref:Xamarin.Forms.Slider) puede mover la barra de la `Slider` thumb y establezca el [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propiedad:

![](ios-images/slider-updateontap.png "Actualización del control deslizante al pulsar habilitado")

## <a name="pages"></a>Páginas

En iOS, se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin.Forms:

- Ocultar el separador de barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obtener más información, consulte [ocultar el separador de barra de navegación en una NavigationPage](#navigationpage-hideseparatorbar).
- Controlar si la barra de navegación es translúcida. Para obtener más información, consulte [que hace el translúcido de barra de navegación](#translucent_navigation_bar).
- Controlar si el texto de la barra de estado de color en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se ajusta para que coincida con la luminosidad de la barra de navegación. Para obtener más información, consulte [ajustar el modo de Color del texto de barra de estado](#status_bar_color_mode).
- Controlar si el título de página se muestra como un título en la barra de navegación de página grande. Para obtener más información, consulte [mostrar títulos grandes](#large_title).
- Establecer la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page). Para obtener más información, consulte [establecer la visibilidad de la barra de estado en una página](#set_status_bar_visibility).
- Garantizar ese contenido de la página se coloca en un área de la pantalla que sea seguro para todos los dispositivos iOS. Para obtener más información, consulte [habilitación de la Guía de diseño de área segura](#safe_area_layout).
- Establecer el estilo de presentación de páginas modales en un iPad. Para obtener más información, consulte [establecer el estilo de presentación de página Modal en un iPad](#modal-page-presentation-style).

<a name="navigationpage-hideseparatorbar" />

### <a name="hiding-the-navigation-bar-separator-on-a-navigationpage"></a>Ocultar la barra de navegación separador en una NavigationPage

Este específicos de la plataforma ocultan la línea de separación y la sombra que se encuentra en la parte inferior de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Se consume en XAML estableciendo el [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) propiedad enlazable a `false`:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

El `NavigationPage.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `NavigationPage.SetHideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetHideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si se oculta el separador de barra de navegación. Además, el [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método puede utilizarse para devolver si se oculta el separador de barra de navegación.

El resultado es que el separador de barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) puede estar oculto:

![](ios-images/navigationpage-hideseparatorbar.png "Barra de navegación NavigationPage ocultos")

<a name="translucent_navigation_bar" />

### <a name="making-the-navigation-bar-translucent"></a>Hacer que la barra de navegación translúcido

Este específicos de la plataforma se usa para cambiar la transparencia de la barra de navegación y se consume en XAML estableciendo el [ `NavigationPage.IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) propiedad adjunta un `boolean` valor:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

El `NavigationPage.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `NavigationPage.EnableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para hacer que la barra de navegación translúcido. Además, el [ `NavigationPage` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) clase en el `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espacio de nombres también tiene un [ `DisableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método que restaura la barra de navegación a su estado predeterminado, y un [ `SetIsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) método que se puede usar para activar o desactivar la transparencia de la barra de navegación mediante una llamada a la [ `IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

El resultado es que se puede cambiar la transparencia de la barra de navegación:

![](ios-images/translucent-navigation-bar.png "Barra de navegación translúcido específicos de la plataforma")

<a name="status_bar_color_mode" />

### <a name="adjusting-the-status-bar-text-color-mode"></a>Ajustar el modo de Color del texto de la barra de estado

Este controles específicos de la plataforma si el texto de la barra de estado de color en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se ajusta para que coincida con la luminosidad de la barra de navegación. Se consume en XAML estableciendo el [ `NavigationPage.StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) propiedad adjunta a un valor de la [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeración:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

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

El `NavigationPage.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `NavigationPage.SetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, los controles si el texto de la barra de estado de color en el [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se ajusta para que coincida con el luminosidad de la barra de navegación con el [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeración que proporciona dos valores posibles:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) : indica que no se debe ajustar el color del texto de la barra de estado.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) : indica que el color del texto de la barra de estado debe coincidir con la luminosidad de la barra de navegación.

Además, el [ `GetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método puede utilizarse para recuperar el valor actual de la [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeración que se aplica a la [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

El resultado es que el estado de la barra de color del texto en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se puede ajustar para que coincida con la luminosidad de la barra de navegación. En este ejemplo, lo cambios de color del texto de la barra de estado como el usuario alterna entre el [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) y [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) páginas de un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

![](ios-images/status-bar-text-color-mode.png "Barra de estado texto Color modo específico de la plataforma")

<a name="large_title" />

### <a name="displaying-large-titles"></a>Mostrar títulos grandes

Este específicos de la plataforma se usan para mostrar el título de página como un título de gran tamaño en la barra de navegación para los dispositivos que usan iOS 11 o superior. Un gran título está alineado a la izquierda y usa una fuente mayor y realiza la transición a un título estándar como el usuario comienza a desplazarse por el contenido, por lo que se usan de forma eficaz el espacio en pantalla. Sin embargo, en orientación horizontal, el título devolverá al centro de la barra de navegación para optimizar el diseño del contenido. Se consume en XAML estableciendo el `NavigationPage.PrefersLargeTitles` propiedad adjunta un `boolean` valor:

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

![](ios-images/large-title.png "Específico de la plataforma efecto de desenfoque")

<a name="set_status_bar_visibility" />

### <a name="setting-the-status-bar-visibility-on-a-page"></a>Configuración de la barra de estado de visibilidad de una página

Este específicos de la plataforma se usan para establecer la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page), e incluye la capacidad para controlar cómo la barra de estado entra o sale de la `Page`. Se consume en XAML estableciendo el `Page.PrefersStatusBarHidden` propiedad adjunta a un valor de la `StatusBarHiddenMode` enumeración y, opcionalmente, el `Page.PreferredStatusBarUpdateAnimation` propiedad adjunta a un valor de la `UIStatusBarAnimation` enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetPrefersStatusBarHidden` método, en el `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espacio de nombres, se usa para establecer la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page) especificando uno de los `StatusBarHiddenMode` valores de enumeración: `Default`, `True` , o `False`. El `StatusBarHiddenMode.True` y `StatusBarHiddenMode.False` valores establecen la visibilidad de la barra de estado, independientemente de la orientación del dispositivo y el `StatusBarHiddenMode.Default` valor oculta la barra de estado en un entorno de compact verticalmente.

El resultado es que la visibilidad de la barra de estado en un [ `Page` ](xref:Xamarin.Forms.Page) se pueden establecer:

![](ios-images/hide-status-bar.png "Barra de estado de visibilidad plataforma específica")

> [!NOTE]
> En un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), especificado `StatusBarHiddenMode` valor de enumeración también actualizará la barra de estado en todas las páginas secundarias. En todos los demás [ `Page` ](xref:Xamarin.Forms.Page)-tipos derivados, especificados `StatusBarHiddenMode` valor de enumeración solo actualizará la barra de estado en la página actual.

El `Page.SetPreferredStatusBarUpdateAnimation` método se usa para establecer cómo la barra de estado entra o sale de la [ `Page` ](xref:Xamarin.Forms.Page) especificando uno de los `UIStatusBarAnimation` valores de enumeración: `None`, `Fade`, o `Slide`. Si el `Fade` o `Slide` se especifica el valor de enumeración, 0.25 segunda animación se ejecuta como la barra de estado entra o sale de la `Page`.

<a name="safe_area_layout" />

### <a name="enabling-the-safe-area-layout-guide"></a>Habilitación de la Guía de diseño de área segura

Este específicos de la plataforma se usan para garantizar que el contenido de la página se coloca en un área de la pantalla que es seguro para todos los dispositivos que usan iOS 11 y versiones posteriores. En concreto, le ayudará a asegurarse de que el contenido no recortado por dispositivo esquinas redondeadas, el indicador principal o el alojamiento del sensor en un iPhone X. Se consume en XAML estableciendo el `Page.UseSafeArea` propiedad adjunta un `boolean` valor:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetUseSafeArea` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) controla el espacio de nombres, si está habilitada la Guía de diseño de área segura.

El resultado es que el contenido de la página puede colocarse en un área de la pantalla que es seguro para todos los iPhone:

[![](ios-images/safe-area-layout.png "Guía de diseño de área segura")](ios-images/safe-area-layout-large.png#lightbox "Guía de diseño de área segura")

> [!NOTE]
> El área segura definido por Apple que se usa en Xamarin.Forms para establecer el [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding) propiedad y reemplazará los valores anteriores de esta propiedad que se han establecido.

El área segura se puede personalizar mediante la recuperación de su [ `Thickness` ](xref:Xamarin.Forms.Thickness) valor con el `Page.SafeAreaInsets` método desde el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres. A continuación, se puede modificar como necesarios y volver a asignar a la `Padding` propiedad en el constructor de la página o [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) invalidar:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="modal-page-presentation-style" />

### <a name="setting-the-modal-page-presentation-style-on-an-ipad"></a>Establecer el estilo de presentación de página Modal en un iPad

Este específicos de la plataforma se usan para establecer el estilo de presentación de una página modal en un iPad. Se consume en XAML estableciendo el `Page.ModalPresentationStyle` propiedad enlazable para un `UIModalPresentationStyle` valor de enumeración:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

El `Page.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Page.SetModalPresentationStyle` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para establecer el estilo de presentación modal en un [ `Page` ](xref:Xamarin.Forms.Page) especificando uno de los siguientes `UIModalPresentationStyle` (enumeración) valores:

- `FullScreen`, que establece el estilo de presentación modal para abarcar toda la pantalla. De forma predeterminada, las páginas modales se muestran con este estilo de presentación.
- `FormSheet`, que establece el estilo de presentación modal sea menor que la pantalla y centrado en.

Además, el `GetModalPresentationStyle` método puede utilizarse para recuperar el valor actual de la `UIModalPresentationStyle` enumeración que se aplica a la [ `Page` ](xref:Xamarin.Forms.Page).

El resultado es que el estilo de presentación modal en un [ `Page` ](xref:Xamarin.Forms.Page) se pueden establecer:

[![](ios-images/modal-presentation-style-small.png "Modal estilos de presentación en un iPad")](ios-images/modal-presentation-style-large.png#lightbox "Modal estilos de presentación en un iPad")

> [!NOTE]
> Las páginas que usan este específicos de la plataforma para establecer el estilo de presentación modal deben usar la navegación modal. Para obtener más información, consulte [páginas modales de Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="layouts"></a>Diseños

En iOS, se proporciona la siguiente funcionalidad específica de la plataforma para los diseños de Xamarin.Forms:

- Controlar si un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) controla un movimiento táctil o pasa a su contenido. Para obtener más información, consulte [retrasar toques contenido en un ScrollView](#delay_content_touches).

<a name="delay_content_touches" />

### <a name="delaying-content-touches-in-a-scrollview"></a>Retrasar toques contenidos en un ScrollView

Se desencadena un temporizador implícito cuando se inicia un movimiento táctil en un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) en iOS y el `ScrollView` decide, en función de la acción del usuario dentro del intervalo del temporizador, independientemente de que se debe controlar el gesto o pasarlo a su contenido. De forma predeterminada, el archivo iOS `ScrollView` toques contenido retrasos, pero esto puede causar problemas en algunas circunstancias con el `ScrollView` contenido no ganando el gesto cuando debería. Por lo tanto, este controles específicos de la plataforma si un `ScrollView` controla un movimiento táctil o pasa a su contenido. Se consume en XAML estableciendo el `ScrollView.ShouldDelayContentTouches` propiedad adjunta un `boolean` valor:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

El `ScrollView.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `ScrollView.SetShouldDelayContentTouches` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se utiliza para controlar si un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) controla un movimiento táctil o pasa a su contenido. Además, el `SetShouldDelayContentTouches` método puede utilizarse para activar o desactivar retrasar toques contenidos mediante una llamada a la `ShouldDelayContentTouches` método para devolver si se retrasan toques de contenido:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

El resultado es que un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) puede deshabilitar retrasar recibir contenidos toques, así que en este escenario el [ `Slider` ](xref:Xamarin.Forms.Slider) recibe el gesto en lugar de [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) página de la [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](ios-images/scrollview-delay-content-touches.png "Contenido de retraso de ScrollView toca específicos de la plataforma")](ios-images/scrollview-delay-content-touches-large.png#lightbox "toca el retraso ScrollView contenido específico de la plataforma")

## <a name="application"></a>Application

En iOS, se proporciona la siguiente funcionalidad específica de la plataforma para Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application) clase:

- Habilitación de diseño del control y la representación de las actualizaciones que se realizará en el subproceso principal. Para obtener más información, consulte [controlar las actualizaciones de Control en el subproceso principal](#update-on-main-thread).
- Habilitar un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) en una vista desplazable para capturar y compartir el movimiento panorámico con la vista de desplazamiento. Para obtener más información, consulte [habilitar el reconocimiento de gestos de movimiento panorámico simultáneas](#simultaneous-pan-gesture).

<a name="update-on-main-thread" />

### <a name="handling-control-updates-on-the-main-thread"></a>Control de controlar las actualizaciones en el subproceso principal

Este específicos de la plataforma permite controlar el diseño y representación de las actualizaciones que se realizará en el subproceso principal, en lugar de que se va a realizar en un subproceso en segundo plano. Debería ser necesario con poca frecuencia, pero en algunos casos puede evitar que se bloquee. Su XAML consumido en estableciendo el `Application.HandleControlUpdatesOnMainThread` propiedad enlazable a `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

El `Application.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El `Application.SetHandleControlUpdatesOnMainThread` método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si el diseño del control y la representación de las actualizaciones se realizan en el subproceso principal, en lugar de que se va a realizar en un subproceso en segundo plano. Además, el `Application.GetHandleControlUpdatesOnMainThread` método puede utilizarse para devolver si el diseño de controles y la representación de las actualizaciones se realizan en el subproceso principal.

<a name="simultaneous-pan-gesture" />

### <a name="enabling-simultaneous-pan-gesture-recognition"></a>Habilitar el reconocimiento de gestos de Pan simultáneas

Cuando un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) se adjunta a una vista dentro de una vista desplazable, todo el pan gestos capturados por el `PanGestureRecognizer` y no se pasan a la vista de desplazamiento. Por lo tanto, ya no se desplazará la vista de desplazamiento.

Este específicos de la plataforma permite una `PanGestureRecognizer` en una vista desplazable para capturar y compartir el movimiento panorámico con la vista de desplazamiento. Se consume en XAML estableciendo el [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) propiedad adjunta `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

El `Application.On<iOS>` método especifica que solo se ejecutarán este específicos de la plataforma de iOS. El [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espacio de nombres, se usa para controlar si un reconocedor de movimiento panorámico en una vista desplazable capturará el movimiento panorámico, o capturar y compartir el desplazamiento panorámico con la vista desplazable de gestos. Además, el [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) método puede utilizarse para devolver si se comparte el movimiento panorámico con la vista de desplazamiento que contiene el [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer).

Por lo tanto, con este específicos de la plataforma habilitada, cuando un [ `ListView` ](xref:Xamarin.Forms.ListView) contiene un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer), tanto el `ListView` y `PanGestureRecognizer` recibirán el movimiento panorámico y procesarlo. Sin embargo, con este específicos de la plataforma deshabilitada, cuando un `ListView` contiene un `PanGestureRecognizer`, el `PanGestureRecognizer` capturará el movimiento panorámico y procesarlos y el `ListView` no recibirá el movimiento panorámico.

## <a name="summary"></a>Resumen

En este artículo se muestra cómo consumir el iOS funcionalidades específicas de plataforma que se integran en Xamarin.Forms. Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

## <a name="related-links"></a>Vínculos relacionados

- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
