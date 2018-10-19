---
title: Funcionalidades específicas de plataforma de Windows
description: Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se muestra cómo consumir el Windows funcionalidades específicas de plataforma que se integran en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 2130bbbedbab66fac9427947ca42f21c346360ce
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2018
ms.locfileid: "38998421"
---
# <a name="windows-platform-specifics"></a>Funcionalidades específicas de plataforma de Windows

_Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos. En este artículo se muestra cómo consumir el Windows funcionalidades específicas de plataforma que se integran en Xamarin.Forms._

## <a name="visualelements"></a>VisualElements

En la plataforma Universal de Windows, se proporciona la siguiente funcionalidad específica de la plataforma para las páginas, vistas y diseños de Xamarin.Forms:

- Establecer una tecla de acceso para un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [claves de acceso de configuración VisualElement](#visualelement-accesskeys).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [deshabilitar el modo heredado Color](#legacy-color-mode).

<a name="visualelement-accesskeys" />

### <a name="setting-visualelement-access-keys"></a>Establecimiento de las claves de acceso VisualElement

Teclas de acceso son métodos abreviados de teclado que mejoran la facilidad de uso y accesibilidad de las aplicaciones en la plataforma Universal de Windows proporcionando una manera intuitiva para los usuarios a navegar rápidamente e interactuar con de interfaz de usuario visible la aplicación a través de un teclado en lugar de a través de toque o un botón del mouse. Son combinaciones de la tecla Alt y uno o más teclas alfanuméricas, normalmente presionadas de manera secuencial. Métodos abreviados de teclado automáticamente son compatibles con las claves de acceso que utilizan un carácter alfanumérico individual.

Sugerencias de teclas de acceso son flotantes distintivos que se muestra al lado de los controles que incluyen las claves de acceso. Cada sugerencia de clave de acceso contiene las teclas alfanuméricas que activación el control asociado. Cuando un usuario presiona la tecla Alt, se muestran las sugerencias de teclas de acceso.

Este específicos de la plataforma se usan para especificar una clave de acceso para un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Se consume en XAML estableciendo el [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) propiedad adjunta a un valor alfanumérico y, opcionalmente, establezca el [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) propiedad adjunta a un valor de la [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumeración, el [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) propiedad adjunta un `double`y el [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) propiedad adjunta a un `double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

El `VisualElement.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para establecer el valor de clave de acceso para el `VisualElement`. El [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement)) método, opcionalmente, especifica la posición que se usará para mostrar la sugerencia de clave de acceso, con el [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumeración que proporciona los siguientes valores posibles:

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) : indica que la selección de ubicación de acceso keytips vendrá determinado por el sistema operativo.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) : indica que la sugerencia de clave de acceso aparecerá por encima del borde superior de la `VisualElement`.
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) : indica que la sugerencia de clave de acceso aparecerá debajo de la parte inferior de la `VisualElement`.
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) : indica que aparecerá la sugerencia de clave de acceso a la derecha del borde derecho de la `VisualElement`.
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) : indica que aparecerá la sugerencia de clave de acceso a la izquierda del borde izquierdo de la `VisualElement`.
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) : indica que la sugerencia de clave de acceso aparecen superpuesta en el centro de la `VisualElement`.

> [!NOTE]
> Normalmente, el [ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto) colocación keytips es suficiente, lo que incluye compatibilidad con interfaces de usuario adaptable.

El [ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) y [ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) pueden usarse métodos de control más detallado de la ubicación de acceso keytips. El argumento para el `SetAccessKeyHorizontalOffset` método indica el momento para mover la punta de clave de acceso hacia la izquierda o derecha y el argumento para el `SetAccessKeyVerticalOffset` método indica la distancia que hay que mover la sugerencia de clave de acceso hacia arriba o hacia abajo.

>[!NOTE]
> Desplazamientos de sugerencia de clave de acceso no pueden establecerse cuando se establece la posición de clave de acceso `Auto`.

Además, el [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), y [ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) pueden usarse métodos para recuperar el acceso de un valor y su ubicación de la clave.

El resultado es que se pueden mostrar sugerencias de teclas de acceso junto a cualquiera [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instancias que definen el acceso de las claves, presionando la tecla Alt:

![Específico de la plataforma de las claves de acceso de VisualElement](windows-images/visualelement-accesskeys.png "VisualElement acceso claves específicas de la plataforma")

Cuando un usuario activa una tecla de acceso presionando la tecla Alt, seguida por el acceso de clave, la acción predeterminada para el `VisualElement` se ejecutará. Por ejemplo, cuando un usuario activa la clave de acceso en un [ `Switch` ](xref:Xamarin.Forms.Switch), el `Switch` se alterna. Cuando un usuario activa la clave de acceso en un [ `Entry` ](xref:Xamarin.Forms.Entry), el `Entry` recibe el foco. Cuando un usuario activa la clave de acceso en un [ `Button` ](xref:Xamarin.Forms.Button), el controlador de eventos para el [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) se ejecuta el evento.

Para obtener más información acerca de las claves de acceso, consulte [claves de acceso](/windows/uwp/design/input/access-keys#key-tip-positioning).

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>Deshabilitar el modo de Color heredado

Algunas de las vistas de Xamarin.Forms cuentan con un modo de color heredado. En este modo, cuando el [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) se establece la propiedad de la vista en `false`, la vista invalida los colores establecidos por el usuario con los colores nativo predeterminado para el estado deshabilitado. Para hacia atrás compatibilidad, este modo heredado de color permanece el comportamiento predeterminado para las vistas admitidas.

Este específicos de la plataforma deshabilita este modo heredado de color, para que los colores establecidos en una vista por el usuario permanezcan incluso cuando la vista está deshabilitada. Se consume en XAML estableciendo el [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta `false`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<Windows>` método especifica que solo se ejecutarán este específicos de la plataforma en Windows. El [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para controlar si se deshabilita el modo de color heredado. Además, el [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) método puede utilizarse para devolver si está deshabilitado el modo de color heredado.

El resultado es que se puede deshabilitar el modo de color heredados, para que los colores establecidos en una vista por el usuario permanezcan incluso cuando se deshabilita la vista:

![](windows-images/legacy-color-mode-disabled.png "Modo heredado color deshabilitado")

> [!NOTE]
> Al establecer un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) en una vista, se omite completamente el modo de color heredado. Para obtener más información acerca de los estados visuales, vea [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="views"></a>Vistas

En la plataforma Universal de Windows (UWP), se proporciona la siguiente funcionalidad específica de la plataforma para las vistas de Xamarin.Forms:

- Detectar el orden de lectura del texto contenido en [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), y [ `Label` ](xref:Xamarin.Forms.Label) instancias. Para obtener más información, consulte [detectar el orden de lectura de contenido](#inputview-readingorder).
- Habilitar la compatibilidad de derivación de gestos en un [ `ListView` ](xref:Xamarin.Forms.ListView). Para obtener más información, consulte [Habilitar compatibilidad de gestos puntee en un ListView](#listview-selectionmode).
- Habilitar un [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interactuar con el motor de corrección ortográfica. Para obtener más información, consulte [Habilitar corrector ortográfico a SearchBar](#searchbar-spellcheck).
- Habilitar un [ `WebView` ](xref:Xamarin.Forms.WebView) para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje UWP. Para obtener más información, consulte [mostrar alertas de JavaScript](#webview-javascript-alert).

<a name="inputview-readingorder" />

### <a name="detecting-reading-order-from-content"></a>Detectar el orden de lectura de contenido

Este específicos de la plataforma permite que el orden de lectura (de izquierda a derecha o de derecha a izquierda) de texto bidireccional en [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), y [ `Label` ](xref:Xamarin.Forms.Label) instancias que se ha detectado dinámicamente. Se consume en XAML estableciendo el [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (para `Entry` y `Editor` instancias) o [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

El `Editor.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para controlar si se detecta el orden de lectura del contenido en el [ `InputView` ](xref:Xamarin.Forms.InputView). Además, el `InputView.SetDetectReadingOrderFromContent` método puede utilizarse para alternar si se detecta el orden de lectura del contenido mediante una llamada a la [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) método para devolver el valor actual:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

El resultado es que [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), y [ `Label` ](xref:Xamarin.Forms.Label) instancias pueden tener el orden de lectura de su contenido dinámicamente detectado:

[![Detectar el orden de lectura de contenido específico de la plataforma de InputView](windows-images/editor-readingorder.png "InputView detectar el orden de lectura de contenido específico de la plataforma")](windows-images/editor-readingorder-large.png#lightbox "InputView detectar el orden de lectura de contenido específico de la plataforma")

> [!NOTE]
> A diferencia de la configuración de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad, la lógica para las vistas que detectar el orden de lectura de su contenido de texto no afectará a la alineación del texto dentro de la vista. En su lugar, ajusta el orden en el que se colocan los bloques de texto bidireccional.

<a name="listview-selectionmode" />

### <a name="enabling-tap-gesture-support-in-a-listview"></a>Habilitar la compatibilidad de derivación de gestos en un ListView

En la plataforma Universal de Windows, de forma predeterminada el Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) usa nativo `ItemClick` eventos para responder a interacción, en lugar de nativo `Tapped` eventos. Esto proporciona la funcionalidad de accesibilidad para que el Narrador de Windows y el teclado pueden interactuar con el `ListView`. Sin embargo, también presenta los gestos de tap dentro de la `ListView` no funciona.

Este controles específicos de la plataforma si los elementos de un [ `ListView` ](xref:Xamarin.Forms.ListView) pulse gestos, puede responder y, por tanto, si nativo `ListView` se activa el `ItemClick` o `Tapped` eventos. Se consume en XAML estableciendo el [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) propiedad adjunta a un valor de la [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeración:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

El `ListView.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si los elementos de un [ `ListView` ](xref:Xamarin.Forms.ListView) puede responder pulse gestos, con el [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeración que proporciona dos valores posibles:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) : indica que el `ListView` desencadenará nativo `ItemClick` eventos para controlar la interacción y, por lo tanto, proporcionar una funcionalidad de accesibilidad. Por lo tanto, el Narrador de Windows y el teclado pueden interactuar con el `ListView`. Sin embargo, los elementos de la `ListView` no puede responder para puntear gestos. Este es el comportamiento predeterminado para `ListView` instancias en la plataforma Universal de Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) : indica que el `ListView` desencadenará nativo `Tapped` eventos para controlar la interacción. Por lo tanto, los elementos de la `ListView` pulse gestos puede responder. Sin embargo, no hay ninguna funcionalidad de accesibilidad y, por tanto, el Narrador de Windows y el teclado no pueden interactuar con el `ListView`.

> [!NOTE]
> El `Accessible` y `Inaccessible` modos de selección son mutuamente excluyentes y tendrá que elegir entre una accesible [ `ListView` ](xref:Xamarin.Forms.ListView) o un `ListView` que puede responder a los gestos de pulse.

Además, el [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) método puede utilizarse para devolver actual [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

El resultado es que un determinado [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) se aplica a la [ `ListView` ](xref:Xamarin.Forms.ListView), que controla si los elementos de la `ListView` pulse gestos, puede responder y, por tanto, si nativo `ListView` se activa el `ItemClick` o `Tapped` eventos.

<a name="searchbar-spellcheck" />

### <a name="enabling-searchbar-spell-check"></a>Habilitar SearchBar corrector ortográfico

Este específicos de la plataforma permite una [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interactuar con el motor de corrección ortográfica. Se consume en XAML estableciendo el [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

El `SearchBar.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se activa el corrector ortográfico y desactiva. Además, el `SearchBar.SetIsSpellCheckEnabled` método puede utilizarse para activar o desactivar el corrector ortográfico mediante una llamada a la [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) método devuelva si el corrector ortográfico está habilitado:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

El resultado es que el texto escrito en el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) puede spell comprobarse, con ortografía incorrecta que se indica al usuario:

![SearchBar spell check específicas de la plataforma](windows-images/searchbar-spellcheck.png "SearchBar spell check específicas de la plataforma")

> [!NOTE]
> El `SearchBar` clase en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) también dispone de espacio de nombres [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) y [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) métodos que pueden usarse para habilitar y deshabilitar el corrector ortográfico en el `SearchBar`, respectivamente.

<a name="webview-javascript-alert" />

### <a name="displaying-javascript-alerts"></a>Mostrar alertas de JavaScript

Este específicos de la plataforma permite una [ `WebView` ](xref:Xamarin.Forms.WebView) para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje UWP. Se consume en XAML estableciendo el [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

El `WebView.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para controlar si las alertas de JavaScript están habilitadas. Además, el `WebView.SetIsJavaScriptAlertEnabled` método puede utilizarse para activar o desactivar las alertas de JavaScript mediante una llamada a la [ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) método para devolver si están habilitadas:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

El resultado es que se pueden mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje UWP:

![Alerta de JavaScript en WebView específicos de la plataforma](windows-images/webview-javascript-alert.png "alerta específica de la plataforma de JavaScript en WebView")

## <a name="pages"></a>Páginas

En la plataforma Universal de Windows, se proporciona la siguiente funcionalidad específica de la plataforma para las páginas de Xamarin.Forms:

- Contraer el [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) barra de navegación. Para obtener más información, consulte [contraer una barra de navegación MasterDetailPage](#collapsable_navigation_bar).
- Opciones de ubicación de la barra de herramientas de configuración. Para obtener más información, consulte [cambiar la posición de la barra de herramientas de página](#toolbar_placement).
- Habilitación de los iconos de página que se mostrará en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de herramientas. Para obtener más información, consulte [Habilitar iconos en una TabbedPage](#tabbedpage-icons).

<a name="collapsable_navigation_bar" />

### <a name="collapsing-a-masterdetailpage-navigation-bar"></a>Contraer una barra de navegación MasterDetailPage

Este específicos de la plataforma se usan para contraer la barra de navegación en un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)y se consume en XAML estableciendo el [ `MasterDetailPage.CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty) y [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty)propiedades adjuntas:

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

El `MasterDetailPage.On<Windows>` método especifica que solo se ejecutarán este específicos de la plataforma en Windows. El [ `Page.SetCollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para especificar el estilo de contraer, con el [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) enumeración que proporciona dos valores: [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) y [ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial). El [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},System.Double)) método se utiliza para especificar el ancho de una barra de navegación parcialmente contraído.

El resultado es que un determinado [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) se aplica a la [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) instancia, con el ancho también se especifica:

[![](windows-images/collapsed-navigation-bar.png "Contraer barra de navegación específicos de la plataforma")](windows-images/collapsed-navigation-bar-large.png#lightbox "Contraer barra de navegación específicos de la plataforma")

<a name="toolbar_placement" />

### <a name="changing-the-page-toolbar-placement"></a>Cambiar la posición de la barra de herramientas de página

Este específicos de la plataforma se usan para cambiar la ubicación de una barra de herramientas en un [ `Page` ](xref:Xamarin.Forms.Page)y se consume en XAML estableciendo el [ `Page.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) propiedad adjunta a un valor de la [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumeración:

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

[![](windows-images/toolbar-placement.png "Barra de herramientas ubicación específicos de plataforma")](windows-images/toolbar-placement-large.png#lightbox "específicos de plataforma de barra de herramientas Selección de ubicación")

<a name="tabbedpage-icons" />

### <a name="enabling-icons-on-a-tabbedpage"></a>Habilitación de los iconos en una TabbedPage

Este específicos de la plataforma permite que los iconos de página que se mostrará en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de herramientas y proporciona la capacidad de especificar opcionalmente el tamaño del icono. Se consume en XAML estableciendo el [ `TabbedPage.HeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) propiedad adjunta `true`y, opcionalmente, estableciendo el [ `TabbedPage.HeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) propiedad adjunta un [ `Size` ](xref:Xamarin.Forms.Size) valor:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" Icon="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" Icon="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" Icon="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

Como alternativa, pueden usarse desde C# mediante la API fluida:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
    {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", Icon = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", Icon = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", Icon = "contacts.png" });
  }
}
```

El `TabbedPage.On<Windows>` método especifica que solo se ejecutan este específicos de la plataforma en la plataforma Universal de Windows. El [ `TabbedPage.SetHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},System.Boolean)) método, en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se usa para activar o desactivar los iconos de encabezado. El [ `TabbedPage.SetHeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsSize(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},Xamarin.Forms.Size)) método, opcionalmente, especifica el tamaño del icono de encabezado con un [ `Size` ](xref:Xamarin.Forms.Size) valor.

Además, el `TabbedPage` clase en el `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` espacio de nombres también tiene un [ `EnableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) método que permite a los iconos de encabezado, un [ `DisableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) método que deshabilita los iconos de encabezado, y un [ `IsHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) método que devuelve un `boolean` valor que indica si se habilitan los iconos de encabezado.

El resultado es esa página se pueden mostrar los iconos en un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de herramientas, con el tamaño de icono que se va a establecer opcionalmente al tamaño que desee:

![Específico de plataforma iconos habilitados TabbedPage](windows-images/tabbedpage-icons.png "TabbedPage iconos habilitados específico de plataforma")

## <a name="summary"></a>Resumen

En este artículo se muestra cómo consumir el Windows funcionalidades específicas de plataforma que se integran en Xamarin.Forms. Funcionalidades específicas de plataforma permiten utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar los representadores personalizados o los efectos.

## <a name="related-links"></a>Vínculos relacionados

- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
