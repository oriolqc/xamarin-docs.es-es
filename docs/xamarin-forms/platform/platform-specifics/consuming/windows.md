---
title: Detalles de plataforma de Windows
description: Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo utilizar la plataforma-detalles de Windows que están integrados en Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 7299de658a3491928e9bbeaa4dd192a8e95c435e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732806"
---
# <a name="windows-platform-specifics"></a>Detalles de plataforma de Windows

_Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos. Este artículo demuestra cómo utilizar la plataforma-detalles de Windows que están integrados en Xamarin.Forms._

En la plataforma Universal de Windows (UWP), Xamarin.Forms contiene los detalles siguientes de la plataforma:

- Opciones de colocación de la barra de herramientas de configuración. Para obtener más información, consulte [cambiar la posición de la barra de herramientas](#toolbar_placement).
- Contraer el [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) barra de navegación. Para obtener más información, consulte [contraer una barra de navegación de MasterDetailPage](#collapsable_navigation_bar).
- Habilitar un [ `WebView` ](xref:Xamarin.Forms.WebView) para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje UWP. Para obtener más información, consulte [mostrar alertas de JavaScript](#webview-javascript-alert).
- Habilitar un [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interactuar con el motor de corrección ortográfica. Para obtener más información, consulte [Habilitar corrector ortográfico a SearchBar](#searchbar-spellcheck).
- Detectar el orden de lectura del texto contenido en [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), y [ `Label` ](xref:Xamarin.Forms.Label) instancias. Para obtener más información, consulte [detectar el orden de lectura de contenido](#inputview-readingorder).
- Deshabilitar el modo de color heredado en compatible [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obtener más información, consulte [deshabilitar el modo de Color heredado](#legacy-color-mode).
- Habilitar la compatibilidad de gestos de derivación en una [ `ListView` ](xref:Xamarin.Forms.ListView). Para obtener más información, consulte [Habilitar compatibilidad de gestos puntee en un control ListView](#listview-selectionmode).

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

<a name="webview-javascript-alert" />

## <a name="displaying-javascript-alerts"></a>Mostrar alertas de JavaScript

Este específica de la plataforma permite una [ `WebView` ](xref:Xamarin.Forms.WebView) para mostrar las alertas de JavaScript en un cuadro de diálogo de mensaje UWP. Se consume en XAML estableciendo el [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

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

El `WebView.On<Windows>` método especifica que sólo se ejecutará este específica de la plataforma en la plataforma Universal de Windows. El [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) método, en la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si se han habilitado las alertas de JavaScript. Además, el `WebView.SetIsJavaScriptAlertEnabled` método se puede utilizar para activar o desactivar las alertas de JavaScript mediante una llamada a la [ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) método devuelva un valor si están habilitadas:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

El resultado es que las alertas de JavaScript pueden mostrarse en un cuadro de diálogo de mensaje UWP:

![Alerta de WebView JavaScript específico de la plataforma](windows-images/webview-javascript-alert.png "WebView JavaScript alerta específica de la plataforma")

<a name="searchbar-spellcheck" />

## <a name="enabling-searchbar-spell-check"></a>Habilitar SearchBar corrector ortográfico

Este específica de la plataforma permite una [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interactuar con el motor de corrección ortográfica. Se consume en XAML estableciendo el [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

El `SearchBar.On<Windows>` método especifica que sólo se ejecutará este específica de la plataforma en la plataforma Universal de Windows. El [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) método, en la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, el corrector ortográfico en activa y desactiva. Además, el `SearchBar.SetIsSpellCheckEnabled` método se puede utilizar para activar o desactivar el corrector ortográfico mediante una llamada a la [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) método devuelva un valor si el corrector ortográfico está habilitado:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

El resultado es que el texto escrito en el [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) puede corrector comprobarse, con la ortografía incorrecta que se indica al usuario:

![SearchBar corrector verificación específico de la plataforma](windows-images/searchbar-spellcheck.png "SearchBar corrector verificación específico de la plataforma")

> [!NOTE]
> El `SearchBar` clase en el [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) también dispone de espacio de nombres [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) y [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) métodos que pueden utilizarse para habilitar y deshabilitar el corrector ortográfico en la `SearchBar`, respectivamente.

<a name="inputview-readingorder" />

## <a name="detecting-reading-order-from-content"></a>Detectar el orden de lectura de contenido

Este específica de la plataforma permite que el orden de lectura (de izquierda a derecha o de derecha a izquierda) de texto bidireccional en [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), y [ `Label` ](xref:Xamarin.Forms.Label) instancias que se ha detectado dinámicamente. Se consume en XAML estableciendo el [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (para `Entry` y `Editor` instancias) o [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) propiedad adjunta un `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

El `Editor.On<Windows>` método especifica que sólo se ejecutará este específica de la plataforma en la plataforma Universal de Windows. El [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) método, en la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si se detecta el orden de lectura del contenido en el [ `InputView` ](xref:Xamarin.Forms.InputView). Además, el `InputView.SetDetectReadingOrderFromContent` método se puede utilizar para alternar si se detecta el orden de lectura del contenido mediante una llamada a la [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) método para devolver el valor actual:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

El resultado es que [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), y [ `Label` ](xref:Xamarin.Forms.Label) instancias pueden tener el orden de lectura de su contenido dinámicamente detectado:

[![InputView detectar el orden de lectura de contenido específico de la plataforma](windows-images/editor-readingorder.png "InputView detectar el orden de lectura de contenido específico de la plataforma")](windows-images/editor-readingorder-large.png#lightbox "InputView detectar el orden de lectura de contenido específico de la plataforma")

> [!NOTE]
> A diferencia de la configuración de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad, la lógica para las vistas que detectar el orden de lectura de su contenido de texto no afectará a la alineación del texto dentro de la vista. En su lugar, ajusta el orden en que están dispuestos de bloques de texto bidireccional.

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>Deshabilitar el modo de Color heredado

Algunas de las vistas de Xamarin.Forms las características de un modo de color heredado. En este modo, cuando la [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propiedad de la vista se establece en `false`, la vista invalidará los colores establecidos por el usuario con los colores nativo de forma predeterminada para el estado deshabilitado. Para hacia atrás compatibilidad, este modo de color heredado sigue siendo el comportamiento predeterminado para vistas admitidas.

Este específica de la plataforma deshabilita este modo heredado de color, para que sigan estando colores establecido en una vista por el usuario incluso cuando se deshabilita la vista. Se consume en XAML estableciendo el [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propiedad adjunta `false`:

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

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

El `VisualElement.On<Windows>` método especifica que sólo se ejecutará este específica de la plataforma en Windows. El [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) método, en la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si se deshabilita el modo de color heredado. Además, el [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) método se puede utilizar para devolver si está deshabilitado el modo de color heredado.

El resultado es que se puede deshabilitar el modo de color heredados, para que los colores establecidos en una vista por el usuario permanecen incluso cuando se deshabilita la vista:

![](windows-images/legacy-color-mode-disabled.png "Modo de color heredado deshabilitado")

> [!NOTE]
> Al establecer un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) en una vista, se omite por completo el modo de color heredado. Para obtener más información sobre los estados visuales, consulte [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="listview-selectionmode" />

## <a name="enabling-tap-gesture-support-in-a-listview"></a>Habilitar la compatibilidad de derivación de gestos en un control ListView

En la plataforma Universal de Windows, de forma predeterminada el Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) usa nativo `ItemClick` evento para responder a interacción, en lugar de nativo `Tapped` eventos. Esto proporciona la funcionalidad de accesibilidad para que el Narrador de Windows y el teclado pueden interactuar con el `ListView`. Sin embargo, sino que también presenta los movimientos de tap dentro de la `ListView` no funciona.

Este controles específicos de la plataforma si los elementos de un [ `ListView` ](xref:Xamarin.Forms.ListView) puede responder pulse gestos y por lo tanto, si nativo `ListView` se activa el `ItemClick` o `Tapped` eventos. Se consume en XAML estableciendo el [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) propiedad adjunta a un valor de la [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeración:

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

Como alternativa, puede utilizarse desde C# utilizando la API fluida de:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

El `ListView.On<Windows>` método especifica que sólo se ejecutará este específica de la plataforma en la plataforma Universal de Windows. El [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) método, en la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espacio de nombres, se utiliza para controlar si los elementos de un [ `ListView` ](xref:Xamarin.Forms.ListView) puede responder pulse gestos, con la [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeración proporciona dos valores posibles:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) : indica que la `ListView` se activará nativo `ItemClick` eventos para controlar la interacción y, por lo tanto, proporcionar la funcionalidad de accesibilidad. Por lo tanto, el Narrador de Windows y el teclado pueden interactuar con el `ListView`. Sin embargo, los elementos de la `ListView` no puede responder para pulsar movimientos. Éste es el comportamiento predeterminado para `ListView` instancias en la plataforma Universal de Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) : indica que la `ListView` se activará nativo `Tapped` eventos para controlar la interacción. Por lo tanto, los elementos de la `ListView` puede responder pulse movimientos. Sin embargo, no hay ninguna funcionalidad de accesibilidad y, por lo que el Narrador de Windows y el teclado no pueden interactuar con el `ListView`.

> [!NOTE]
> El `Accessible` y `Inaccessible` modos de selección son mutuamente excluyentes, y debe elegir entre una accesible [ `ListView` ](xref:Xamarin.Forms.ListView) o un `ListView` que puede responder a pulse movimientos.

Además, el [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) método se puede utilizar para devolver la actual [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

El resultado es que un determinado [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) se aplica a la [ `ListView` ](xref:Xamarin.Forms.ListView), que controla si los elementos de la `ListView` puede responder pulse gestos y por lo tanto, si nativo `ListView` se activa el `ItemClick` o `Tapped` eventos.

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar la plataforma-detalles de Windows que están integrados en Xamarin.Forms. Obtener información específica de la plataforma le permite utilizar la funcionalidad que solo está disponible en una plataforma concreta, sin necesidad de implementar representadores personalizados o los efectos.

## <a name="related-links"></a>Vínculos relacionados

- [Creación funcionalidades específicas de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
