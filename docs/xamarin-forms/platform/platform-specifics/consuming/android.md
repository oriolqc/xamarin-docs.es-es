---
title: Funcionalidades específicas de plataforma Android
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se muestra cómo consumir el Android funcionalidades específicas de plataforma que se integran en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: c505b12a745017664e56e8c84d0cf847a262208d
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2018
ms.locfileid: "53060218"
---
# <a name="android-platform-specifics"></a>Funcionalidades específicas de plataforma Android

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

_Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se muestra cómo consumir el Android funcionalidades específicas de plataforma que se integran en Xamarin.Forms._

## <a name="visualelements"></a>VisualElements

En Android, se proporciona la siguiente funcionalidad específica de la plataforma para las páginas, vistas y diseños de Xamarin.Forms:

- Controlar el orden Z de los elementos visuales para determinar el orden de dibujo. Para obtener más información, consulte [controlar la elevación de elementos visuales](#elevation).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [deshabilitar el modo heredado Color](#legacy-color-mode).

<a name="elevation" />

### <a name="controlling-the-elevation-of-visual-elements"></a>Controlar la elevación de los elementos visuales

Este específicos de la plataforma se usa para controlar la elevación o el orden Z de los elementos visuales en aplicaciones que tienen como destino API 21 o mayor. La elevación de un elemento visual determina su orden de dibujo, con elementos visuales con los valores más altos de Z occluding elementos visuales con los valores más bajos de Z. Se consume en XAML estableciendo el `VisualElement.Elevation` propiedad adjunta un `boolean` valor:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

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

El `Button.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `VisualElement.SetElevation` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la elevación del elemento visual que acepta valores NULL `float`. Además, el `VisualElement.GetElevation` método puede utilizarse para recuperar el valor de elevación de un elemento visual.

El resultado es que se puede controlar la elevación de los elementos visuales para que los elementos visuales con los valores más altos de Z occlude elementos visuales con los valores más bajos de Z. Por lo tanto, en este ejemplo el segundo [ `Button` ](xref:Xamarin.Forms.Button) se representa por encima del [ `BoxView` ](xref:Xamarin.Forms.BoxView) porque tiene un valor mayor de elevación:

![](android-images/elevation.png)

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>Deshabilitar el modo de Color heredado

Algunas de las vistas de Xamarin.Forms cuentan con un modo de color heredado. En este modo, cuando el [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) se establece la propiedad de la vista en `false`, la vista invalida los colores establecidos por el usuario con los colores nativo predeterminado para el estado deshabilitado. Para hacia atrás compatibilidad, este modo heredado de color permanece el comportamiento predeterminado para las vistas admitidas.

Este específicos de la plataforma deshabilita este modo heredado de color, para que los colores establecidos en una vista por el usuario permanezcan incluso cuando la vista está deshabilitada. Se consume en XAML estableciendo el [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta `false`:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si se deshabilita el modo de color heredado. Además, el [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement})) método puede utilizarse para devolver si está deshabilitado el modo de color heredado.

El resultado es que se puede deshabilitar el modo de color heredados, para que los colores establecidos en una vista por el usuario permanezcan incluso cuando se deshabilita la vista:

![](android-images/legacy-color-mode-disabled.png "Modo heredado color deshabilitado")

> [!NOTE]
> Al establecer un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) en una vista, se omite completamente el modo de color heredado. Para obtener más información acerca de los estados visuales, vea [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="views"></a>Vistas

En Android, se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin.Forms:

- Utilizando los valores de la sombra de los botones de Android y el relleno predeterminado. Para obtener más información, consulte [utilizando botones de Android](#button-padding-shadow).
- Establecer el método de entrada de opciones del editor para el teclado en pantalla para una [ `Entry` ](xref:Xamarin.Forms.Entry). Para obtener más información, consulte [opciones de configuración de entrada de Input Method Editor](#entry-imeoptions).
- Habilitación de una sombra paralela en un `ImageButton`. Para obtener más información, consulte [habilitar una sombra paralela en un ImageButton](#imagebutton-drop-shadow).
- Habilitar el desplazamiento rápido en un [ `ListView` ](xref:Xamarin.Forms.ListView) para obtener más información, consulte [Habilitar desplazamiento rápido en una ListView](#fastscroll).
- Controlar si un [ `WebView` ](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto. Para obtener más información, consulte [Habilitar contenido mixto en un objeto WebView](#webview-mixed-content).

<a name="button-padding-shadow" />

### <a name="using-android-buttons"></a>Mediante botones de Android

Este específicos de la plataforma controla si los botones de Xamarin.Forms usan los valores de sombra de botones de Android y el relleno predeterminado. Se consume en XAML estableciendo el [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) y [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) adjunta propiedades a `boolean` valores:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

El `Button.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) y[ `Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) métodos, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utilizan para controlar si los botones de Xamarin.Forms usan el valor predeterminado relleno y los valores de la sombra de botones de Android. Además, el [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) y [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) métodos pueden usarse para devolver si un botón usa el valor predeterminado de relleno valor y el valor de la sombra de forma predeterminada, respectivamente.

El resultado es que los botones de Xamarin.Forms pueden usar el relleno predeterminado y los valores de sombra de los botones de Android:

![](android-images/button-padding-and-shadow.png "Modo heredado color deshabilitado")

Tenga en cuenta que en la captura de pantalla encima de cada [ `Button` ](xref:Xamarin.Forms.Button) tiene definiciones idénticas, salvo que el derecho `Button` utiliza el relleno predeterminado y los valores de sombra de los botones de Android.

<a name="entry-imeoptions" />

### <a name="setting-entry-input-method-editor-options"></a>Opciones del Editor de métodos de entrada de configuración de entrada

Este específicos de la plataforma establece el método de entrada de opciones del editor (IME) para el teclado en pantalla para una [ `Entry` ](xref:Xamarin.Forms.Entry). Esto incluye la configuración del botón de acción del usuario en la esquina inferior del teclado en pantalla y las interacciones con el `Entry`. Se consume en XAML estableciendo el [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) propiedad adjunta a un valor de la [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

El `Entry.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la opción de acción del método de entrada para el teclado en pantalla para el [ `Entry` ](xref:Xamarin.Forms.Entry), con el [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeración que proporciona los siguientes valores:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) : indica que se requiere ninguna acción específica de clave y que el control subyacente producirá sí solo si puede. Esto será `Next` o `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) : indica que ninguna clave de la acción estará disponible.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) : indica que la clave de acción llevará a cabo una operación de "Ir", al usuario en el destino del texto escrito.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) : indica que la clave de acción realiza una operación de "búsqueda", al usuario a los resultados de buscar el texto han escrito.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) : indica que la clave de acción llevará a cabo una operación de "Enviar", que se entrega el texto a su destino.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) : indica que la clave de acción llevará a cabo una operación de "siguiente", tomar el usuario al siguiente campo que aceptará el texto.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) : indica que la clave de acción llevará a cabo una operación de "done", el teclado en pantalla de cierre.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) : indica que la clave de acción llevará a cabo una operación de "anterior", tomar el usuario al campo anterior que aceptará el texto.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – la máscara para seleccionar las opciones de acción.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) : indica que el corrector ortográfico se obtenga la información del usuario, ni sugerir correcciones según lo que el usuario ha escrito anteriormente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) : indica que la interfaz de usuario no debería pasar a pantalla completa.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) : indica que no se mostrará ninguna interfaz de usuario para el texto extraído.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) : indica que no se mostrará ninguna interfaz de usuario para las acciones personalizadas.

El resultado es que un determinado [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) se aplica el valor para el teclado en pantalla para el [ `Entry` ](xref:Xamarin.Forms.Entry), que establece el método de entrada en las opciones del editor:

[![Entrada específica plataforma editor del método de entrada](android-images/entry-imeoptions.png "entrada específica plataforma editor del método de entrada")](android-images/entry-imeoptions-large.png#lightbox "entrada específica plataforma editor del método de entrada")

<a name="imagebutton-drop-shadow" />

### <a name="enabling-a-drop-shadow-on-a-imagebutton"></a>Habilitación de una sombra paralela en un ImageButton

Este específicos de la plataforma se utilizan para habilitar una sombra paralela en un `ImageButton`. Se consume en XAML estableciendo el `ImageButton.IsShadowEnabled` propiedad enlazable a `true`, junto con un número de propiedades enlazables opcionales adicionales que controlan el efecto de sombra:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> Se dibuja una sombra paralela como parte de la `ImageButton` en segundo plano y el fondo se dibuja solo si el `BackgroundColor` se establece la propiedad. Por lo tanto, una sombra paralela no se dibujarán si el `ImageButton.BackgroundColor` no se establece la propiedad.

El `ImageButton.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `ImageButton.SetIsShadowEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si una sombra paralela está habilitada en el `ImageButton`. Además, se pueden invocar los métodos siguientes para controlar el efecto de sombra:

- `SetShadowColor` : establece el color de la sombra paralela. El color predeterminado es [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- `SetShadowOffset` : establece el desplazamiento de la sombra paralela. El desplazamiento cambia la dirección de la sombra se convierte y se especifica como un [ `Size` ](xref:Xamarin.Forms.Size) valor. El `Size` los valores de la estructura se expresan en unidades independientes del dispositivo, con el primer valor que se va a la distancia a la izquierda (valor negativo) o la derecha (valor positivo) y el segundo valor que se va a la distancia anterior (negativo) o por debajo (valor positivo) . El valor predeterminado de esta propiedad es (0.0, 0.0), que da como resultado la sombra que se va a convertir en torno a cada lado de la `ImageButton`.
- `SetShadowRadius`: establece el radio de desenfoque utilizado para representar la sombra paralela. El valor de radio predeterminado es 10,0.

> [!NOTE]
> Se puede consultar el estado de una sombra paralela mediante una llamada a la `GetIsShadowEnabled`, `GetShadowColor`, `GetShadowOffset`, y `GetShadowRadius` métodos.

El resultado es que se puede habilitar una sombra paralela en un `ImageButton`:

![](android-images/imagebutton-drop-shadow.png "ImageButton con sombra paralela")

<a name="fastscroll" />

### <a name="enabling-fast-scrolling-in-a-listview"></a>Habilitar el desplazamiento rápido en una ListView

Este específicos de la plataforma se usan para habilitar el desplazamiento rápido a través de los datos en un [ `ListView` ](xref:Xamarin.Forms.ListView). Se consume en XAML estableciendo el `ListView.IsFastScrollEnabled` propiedad adjunta un `boolean` valor:

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

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

El `ListView.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `ListView.SetIsFastScrollEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para habilitar el desplazamiento rápido a través de los datos en un [ `ListView` ](xref:Xamarin.Forms.ListView). Además, el `SetIsFastScrollEnabled` método puede utilizarse para activar o desactivar el desplazamiento rápido mediante una llamada a la `IsFastScrollEnabled` método para devolver si se habilita el desplazamiento rápido:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

El resultado es ese desplazamiento rápido a través de los datos en un [ `ListView` ](xref:Xamarin.Forms.ListView) puede habilitarse, que cambia el tamaño de la miniatura de desplazamiento:

[![](android-images/fastscroll.png "Específico de plataforma ListView FastScroll")](android-images/fastscroll-large.png#lightbox "ListView FastScroll específico de plataforma")

<a name="webview-mixed-content" />

### <a name="enabling-mixed-content-in-a-webview"></a>Lo que permite contenido mixto en una vista Web

Este controles específicos de la plataforma si un [ `WebView` ](xref:Xamarin.Forms.WebView) puede mostrar contenido mixto en aplicaciones que tienen como destino API 21 o posterior. El contenido mixto es el contenido que se cargó inicialmente a través de una conexión HTTPS, pero que cargan recursos (por ejemplo, imágenes, audio, vídeo, hojas de estilo, scripts) en una conexión HTTP. Se consume en XAML estableciendo el [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propiedad adjunta a un valor de la [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeración:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

El `WebView.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si se puede mostrar contenido mixto, con el [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeración que proporciona tres valores posibles:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indica que el [ `WebView` ](xref:Xamarin.Forms.WebView) permitirá a un origen HTTPS cargar el contenido desde un origen HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indica que el [ `WebView` ](xref:Xamarin.Forms.WebView) no permitirá que un origen HTTPS cargar el contenido desde un origen HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indica que el [ `WebView` ](xref:Xamarin.Forms.WebView) intentará sea compatible con el enfoque del explorador de web del dispositivo más reciente. Puede tener algún contenido HTTP se puede cargar un origen de HTTPS y se bloquearán otros tipos de contenido. Los tipos de contenido que se bloquean o permiten pueden cambiar con cada versión de sistema operativo.

El resultado es que un determinado [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valor se aplica a la [ `WebView` ](xref:Xamarin.Forms.WebView), que controla si se puede mostrar contenido mixto:

[![WebView mixto de control de contenido específico de la plataforma](android-images/webview-mixedcontent.png "WebView mixto de control de contenido específico de la plataforma")](android-images/webview-mixedcontent-large.png#lightbox "WebView mixto de control de contenido específico de la plataforma")

## <a name="pages"></a>Páginas

En Android, se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin.Forms:

- Establecer el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obtener más información, consulte [establecer el alto de la barra de navegación en una NavigationPage](#navigationpage-barheight).
- Deshabilitar las animaciones de transición al navegar a través de páginas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, consulte [deshabilitar las animaciones de transición de página en una TabbedPage](#tabbedpage-transition-animations).
- Habilitar el gesto de deslizar rápidamente entre las páginas de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, consulte [habilitar gesto de deslizar rápidamente entre páginas en una TabbedPage](#enable_swipe_paging).
- Establecer la posición de la barra de herramientas y el color en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obtener más información, consulte [configuración TabbedPage barra de herramientas de selección de ubicación y Color](#tabbedpage-toolbar).

<a name="navigationpage-barheight" />

### <a name="setting-the-navigation-bar-height-on-a-navigationpage"></a>Configuración de la barra de navegación alto en una NavigationPage

Este específicos de la plataforma establece el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Se consume en XAML estableciendo el [ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) propiedad enlazable en un valor entero:

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

El `NavigationPage.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en la compatibilidad de aplicaciones Android. El [ `NavigationPage.SetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.SetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage},System.Int32)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espacio de nombres, se usa para establecer el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Además, el [ `NavigationPage.GetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.GetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage})) método puede utilizarse para devolver el alto de la barra de navegación en el `NavigationPage`.

El resultado es que el alto de la barra de navegación en un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) se pueden establecer:

![](android-images/navigationpage-barheight.png "NavigationPage alto de la barra de navegación")

<a name="tabbedpage-transition-animations" />

### <a name="disabling-page-transition-animations-in-a-tabbedpage"></a>Deshabilitar las animaciones de transición de página en una TabbedPage

Este específicos de la plataforma se usan para deshabilitar las animaciones de transición al navegar a través de páginas, ya sea mediante programación o cuando se usa la barra de pestañas, en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Se consume en XAML estableciendo el `TabbedPage.IsSmoothScrollEnabled` propiedad enlazable a `false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

El `TabbedPage.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El `TabbedPage.SetIsSmoothScrollEnabled` método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para controlar si las animaciones de transición se mostrará al navegar entre páginas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espacio de nombres también tiene los métodos siguientes:

- `IsSmoothScrollEnabled`, que se usa para recuperar datos si las animaciones de transición se mostrará al navegar entre páginas en un `TabbedPage`.
- `EnableSmoothScroll`, que se usa para habilitar animaciones de transición al navegar entre páginas en un `TabbedPage`.
- `DisableSmoothScroll`, que se usa para deshabilitar las animaciones de transición al navegar entre páginas en un `TabbedPage`.

<a name="enable_swipe_paging" />

### <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>Habilitar el gesto de deslizar rápidamente entre páginas en una TabbedPage

Este específicos de la plataforma se utilizan para habilitar el gesto de deslizar rápidamente con un gesto del dedo horizontal entre las páginas de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Se consume en XAML estableciendo el [ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) propiedad adjunta un `boolean` valor:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

El `TabbedPage.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `TabbedPage.SetIsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled(Xamarin.Forms.BindableObject,System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se utiliza para habilitar el gesto de deslizar rápidamente entre las páginas de un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espacio de nombres también tiene un [ `EnableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) método que permite este específicos de la plataforma, y un [ `DisableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) método que deshabilita Esta plataforma específica. El [ `TabbedPage.OffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) propiedad adjunta, y [ `SetOffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit(Xamarin.Forms.BindableObject,System.Int32)) método, se utilizan para establecer el número de páginas que se deben conservar en un estado de inactividad a cada lado de la página actual.

El resultado es que la paginación a través de las páginas se muestran por Deslizar rápidamente un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) está habilitado:

![](android-images/tabbedpage-swipe.png)

<a name="tabbedpage-toolbar" />

### <a name="setting-tabbedpage-toolbar-placement-and-color"></a>Establecer ubicación de la barra de herramientas TabbedPage y Color

Estas funcionalidades específicas de plataforma se utilizan para establecer la selección de ubicación y color de la barra de herramientas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Que se consumen en XAML estableciendo el [ `TabbedPage.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) propiedad adjunta a un valor de la [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeración y el [ `TabbedPage.BarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) y [ `TabbedPage.BarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) adjunta propiedades a un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

El `TabbedPage.On<Android>` método especifica que solo se ejecutarán estas funcionalidades específicas de plataforma en Android. El [ `TabbedPage.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer la posición de la barra de herramientas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), con el [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeración que proporciona los siguientes valores:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) : indica que la barra de herramientas se coloca en la ubicación predeterminada en la página. Se trata de la parte superior de la página en teléfonos y la parte inferior de la página en otras expresiones de dispositivo.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) : indica que la barra de herramientas se coloca en la parte superior de la página.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) : indica que la barra de herramientas se coloca en la parte inferior de la página.

Además, el [ `TabbedPage.SetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) y [ `TabbedPage.SetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) métodos se usan para establecer el color de elementos de barra de herramientas y elementos de barra de herramientas seleccionado, respectivamente.

> [!NOTE]
> El [ `GetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), [ `GetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), y [ `GetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) métodos pueden usarse para recuperar la selección de ubicación y color de la [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de herramientas.

El resultado es que se pueden establecer la posición de la barra de herramientas, el color de los elementos de la barra de herramientas y el color del elemento seleccionado de la barra de herramientas en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

![](android-images/tabbedpage-toolbar-placement.png)

## <a name="application"></a>Application

En Android, se proporciona la siguiente funcionalidad específica de la plataforma para Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application) clase:

- Establecer el modo de funcionamiento de un teclado en pantalla. Para obtener más información, consulte [establecer el modo de entrada de teclado temporal](#soft_input_mode).
- Deshabilitar la [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) y [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) página eventos de ciclo de vida en pausa y reanudar, respectivamente, para las aplicaciones que usan AppCompat. Para obtener más información, consulte [deshabilitando el Disappearing y eventos de ciclo de vida de la página que aparece](#disable_lifecycle_events).

<a name="soft_input_mode" />

### <a name="setting-the-soft-keyboard-input-mode"></a>Establecer el modo de entrada de teclado en pantalla

Este específicos de la plataforma se usa para establecer el modo de funcionamiento para un área de entrada de teclado en pantalla y se consume en XAML estableciendo el [ `Application.WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) propiedad adjunta a un valor de la [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumeración:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

El `Application.On<Android>` método especifica que solo se ejecutarán este específicos de la plataforma en Android. El [ `Application.UseWindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)) método, en el [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espacio de nombres, se usa para establecer el modo de funcionamiento del área de entrada de teclado en pantalla, con el [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumeración que proporciona dos valores: [ `Pan` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) y [ `Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize). El `Pan` valor usa la [ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/) la opción de ajuste, que no cambiar el tamaño de la ventana cuando un control de entrada tiene el foco. En su lugar, el contenido de la ventana distribuido para que el foco actual no está oculto por el teclado en pantalla. El `Resize` valor usa la [ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/) la opción de ajuste, que cambia el tamaño de la ventana cuando un control de entrada tiene el foco, para dejar espacio para el teclado en pantalla.

El resultado es que el teclado en pantalla se puede establecer el modo de funcionamiento cuando un control de entrada tiene el foco del área de entrada:

[![](android-images/pan-resize.png "Funcionamiento modo específico de la plataforma de teclado temporal")](android-images/pan-resize-large.png#lightbox "teclado en pantalla funciona en modo específico de la plataforma")

<a name="disable_lifecycle_events" />

### <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>Deshabilitar los eventos de ciclo de vida de página que aparece y desaparece

Este específicos de la plataforma se usan para deshabilitar la [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) y [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) eventos de página en la aplicación pausar y reanudar, respectivamente, para las aplicaciones que usan AppCompat. Además, incluye la capacidad para controlar si se muestra el teclado en pantalla al reanudar si se mostrara en pausa, siempre que el modo de funcionamiento del teclado en pantalla se establece en [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

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

[![](android-images/keyboard-on-resume.png "Específico de plataforma de los eventos del ciclo de vida")](android-images/keyboard-on-resume-large.png#lightbox "específicos de plataforma de los eventos del ciclo de vida")

## <a name="summary"></a>Resumen

En este artículo se muestra cómo consumir el Android funcionalidades específicas de plataforma que se integran en Xamarin.Forms. Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

## <a name="related-links"></a>Vínculos relacionados

- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
