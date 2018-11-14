---
title: Localización de derecha a izquierda
description: Localización de derecha a izquierda, agrega compatibilidad para la dirección del flujo de derecha a izquierda para las aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 67b0d90290b18c7a5b55c5e3496b54970a8cfc38
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617610"
---
# <a name="right-to-left-localization"></a>Localización de derecha a izquierda

_Localización de derecha a izquierda, agrega compatibilidad para la dirección del flujo de derecha a izquierda para las aplicaciones de Xamarin.Forms._

> [!NOTE]
> Localización de derecha a izquierda, requiere el uso de iOS 9 o versiones posteriores y la API 17 o posterior en Android.

Dirección del flujo es la dirección en la que se analizan los elementos de interfaz de usuario en la página por el ojo. Algunos lenguajes, como el árabe y hebreo, requieren que se colocan los elementos de interfaz de usuario en una dirección de flujo de derecha a izquierda. Esto puede lograrse estableciendo el [ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad. Esta propiedad obtiene o establece la dirección en que fluyen de elementos de interfaz de usuario dentro de cualquier elemento primario que controle su diseño y debe establecerse en uno de los [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valores de enumeración:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Establecer el [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft) en un elemento generalmente establece la alineación a la derecha, el orden de lectura de derecha a izquierda y el diseño del control fluya desde de derecha a izquierda:

[![TodoItemPage en árabe, con una dirección de flujo de derecha a izquierda](rtl-images/TodoItemPage-Arabic.png "TodoItemPage en árabe, con una dirección de flujo de derecha a izquierda")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage en árabe, con una dirección de flujo de derecha a izquierda")

> [!TIP]
> Solo se debe establecer el [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad en el diseño inicial. Si cambia este valor en tiempo de ejecución hace que un proceso de diseño costosos que afectará al rendimiento.

El valor predeterminado [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) es el valor de propiedad para un elemento sin un elemento primario [ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight), mientras que el valor predeterminado `FlowDirection` para un elemento con un elemento primario es [ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Por lo tanto, un elemento hereda la `FlowDirection` valor de propiedad de su elemento primario en el árbol visual y cualquier elemento puede invalidar el valor obtiene de su elemento primario.

> [!TIP]
> Al localizar una aplicación para idiomas de derecha a izquierda, establezca el [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad en un diseño de página o raíz. Esto hace que todos los elementos contenidos dentro de la página o el diseño de la raíz, responde adecuadamente a la dirección del flujo.

## <a name="respecting-device-flow-direction"></a>Respetar la dirección del flujo de dispositivo

Respetar la dirección del flujo del dispositivo según el idioma seleccionado y la región es una opción explícita de desarrollador y no se realiza automáticamente. Se consigue estableciendo la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad en una página o el diseño de raíz, a la `static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valor:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Todos los elementos secundarios de la página o el diseño de la raíz, de forma predeterminada, a continuación, heredará la [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valor.

## <a name="platform-setup"></a>Configuración de la plataforma

Configuración de la plataforma específica es necesaria para habilitar las configuraciones regionales de derecha a izquierda.

### <a name="ios"></a>iOS

La configuración regional de derecha a izquierda necesaria debe agregarse como un lenguaje compatible para los elementos de matriz para el `CFBundleLocalizations` clave **Info.plist**. En el ejemplo siguiente se muestra que se han agregado a la matriz para el árabe el `CFBundleLocalizations` clave:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Idiomas admitidos de Info.plist](rtl-images/ios-locales.png "Info.plist idiomas admitidos")

Para obtener más información, consulte [aspectos básicos de localización en iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

A continuación, se puede probar la localización de derecha a izquierda cambiando el idioma y región en el simulador de dispositivos a una configuración regional de derecha a izquierda que se especificó en **Info.plist**.

> [!WARNING]
> Tenga en cuenta que al cambiar el idioma y región para una configuración regional de derecha a izquierda en iOS, cualquier [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) vistas iniciará una excepción si no se incluyen los recursos necesarios para la configuración regional. Por ejemplo, al probar una aplicación en árabe, que tiene un `DatePicker`, asegúrese de que **Oriente** está seleccionado en el **internacionalización** sección de la **compilación de iOS** panel.

### <a name="android"></a>Android

La aplicación **AndroidManifest.xml** archivo debe actualizarse para que el `<uses-sdk>` conjuntos de nodos el `android:minSdkVersion` atribuir a 17 y el `<application>` conjuntos de nodos el `android:supportsRtl` atribuir a `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

A continuación, se puede probar la localización de derecha a izquierda cambiando el dispositivo/emulador para usar el idioma de derecha a izquierda o habilitando **dirección del diseño de derecha a izquierda Force** en **configuración > Opciones del desarrollador**.

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

Se deben especificar los recursos de idioma necesarios en el `<Resources>` nodo de la **Package.appxmanifest** archivo. El ejemplo siguiente muestra el árabe que se han agregado a la `<Resources>` nodo:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Además, UWP requiere que la referencia cultural predeterminada de la aplicación se define explícitamente en la biblioteca .NET Standard. Esto puede realizarse estableciendo el `NeutralResourcesLanguage` atributo `AssemblyInfo.cs`, o en otra clase, para la referencia cultural predeterminada:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

A continuación, se puede probar la localización de derecha a izquierda cambiando el idioma y región en el dispositivo a la configuración regional de derecha a izquierda adecuada.

## <a name="limitations"></a>Limitaciones

Localización de derecha a izquierda Xamarin.Forms tiene actualmente una serie de limitaciones:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) elemento de barra de herramientas de ubicación del botón, ubicación y animación de transición se controla mediante la configuración regional del dispositivo, en lugar de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) dirección de deslizar rápidamente no voltea.
- [`Image`](xref:Xamarin.Forms.Image) no se voltea el contenido visual.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) y [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) orientación se controla mediante la configuración regional del dispositivo, en lugar de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`WebView`](xref:Xamarin.Forms.WebView) contenido no respeta el [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- Un `TextDirection` propiedad debe agregarse, para controlar la alineación del texto.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) orientación se controla mediante la configuración regional del dispositivo, en lugar de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) alineación del texto se controla mediante la configuración regional del dispositivo, en lugar de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) no se revierten los gestos y alineación.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) orientación se controla mediante la configuración regional del dispositivo, en lugar de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) selección de ubicación se controla mediante la configuración regional del dispositivo, en lugar de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) alineación del texto se controla mediante la configuración regional del dispositivo, en lugar de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad no es heredada por [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) elementos secundarios.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) alineación del texto se controla mediante la configuración regional del dispositivo, en lugar de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>De derecha a izquierda lenguaje admiten con Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Admitir Xamarin.Forms 3.0 de derecha a izquierda, por [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo TodoLocalizedRTL](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
