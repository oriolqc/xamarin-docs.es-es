---
title: Localización de derecha a izquierda
description: Localización de derecha a izquierda agrega compatibilidad para la dirección del flujo de derecha a izquierda para aplicaciones de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 90E0CB16-C42A-4CC8-A70E-0C2CFB64A429
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 92ca58374292c829df4098274abae870b355a7b0
ms.sourcegitcommit: 9f8e7393019791bbd6af4fefaa24a1602adabb4e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2018
---
# <a name="right-to-left-localization"></a>Localización de derecha a izquierda

_Localización de derecha a izquierda agrega compatibilidad para la dirección del flujo de derecha a izquierda para aplicaciones de Xamarin.Forms._

> [!NOTE]
> Localización de derecha a izquierda requiere el uso de iOS 9 o versiones posteriores así como API 17 o superior en Android.

Dirección de flujo es la dirección en la que el ojo examina los elementos de interfaz de usuario en la página. Algunos lenguajes, como el árabe y hebreo, requieren que los elementos de interfaz de usuario están dispuestos en una dirección de flujo de derecha a izquierda. Esto puede lograrse estableciendo la [ `VisualElement.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad. Esta propiedad obtiene o establece la dirección en que fluyen de elementos de interfaz de usuario dentro de cualquier elemento primario que controle su diseño y debe establecerse en uno de los [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valores de enumeración:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Establecer el [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft) en un elemento por lo general establece la alineación a la derecha, el orden de lectura de derecha a izquierda y el diseño del control para que fluya desde escritura de derecha a izquierda:

[![TodoItemPage en árabe con una dirección de flujo de derecha a izquierda](rtl-images/TodoItemPage-Arabic.png "TodoItemPage en árabe con una dirección de flujo de derecha a izquierda")](rtl-images/TodoItemPage-Arabic-Large.png#lightbox "TodoItemPage en árabe con una dirección de flujo de derecha a izquierda")

> [!TIP]
> Solo se debe establecer el [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad en el diseño inicial. Si cambia este valor en tiempo de ejecución hace que un proceso de diseño costoso que afectará al rendimiento.

El valor predeterminado [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valor de propiedad para un elemento sin un elemento primario es [ `LeftToRight` ](xref:Xamarin.Forms.FlowDirection.LeftToRight), mientras que el valor predeterminado `FlowDirection` para un elemento con un elemento primario es [ `MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Por lo tanto, un elemento hereda la `FlowDirection` valor de la propiedad de su elemento primario en el árbol visual y cualquier elemento puede invalidar el valor obtiene de su elemento primario.

> [!TIP]
> Al localizar una aplicación para idiomas de derecha a izquierda, establecer el [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad en un diseño de página o raíz. Esto hace que todos los elementos incluidos en la página, o un diseño de raíz, responder apropiadamente a la dirección del flujo.

## <a name="respecting-device-flow-direction"></a>Respeta la dirección del flujo de dispositivo

Respeta la dirección del flujo del dispositivo según el idioma seleccionado y de región es una opción explícita para desarrolladores y no se realiza automáticamente. Se consigue estableciendo la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad en una página o el diseño de raíz para la `static` [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valor:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

```csharp
this.FlowDirection = Device.FlowDirection;
```

Todos los elementos secundarios de la página o el diseño de raíz, de forma predeterminada, a continuación, heredará la [ `Device.FlowDirection` ](xref:Xamarin.Forms.Device.FlowDirection) valor.

## <a name="platform-setup"></a>Programa de instalación de la plataforma

El programa de instalación de la plataforma específica es necesario para habilitar configuraciones regionales de derecha a izquierda.

### <a name="ios"></a>iOS

La configuración regional de derecha a izquierda necesaria debe agregarse como un lenguaje compatible a los elementos de matriz para el `CFBundleLocalizations` clave en **Info.plist**. En el ejemplo siguiente se muestra el árabe que ha agregado a la matriz para el `CFBundleLocalizations` clave:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>ar</string>
</array>
```

![Info.plist admitidas idiomas](rtl-images/ios-locales.png "Info.plist los idiomas compatibles")

Para obtener más información, consulte [conceptos básicos de localización en iOS](https://docs.microsoft.com/en-gb/xamarin/ios/app-fundamentals/localization/#localization-basics-in-ios).

Localización de derecha a izquierda, a continuación, se puede probar cambiando el idioma y región en el simulador de dispositivos para una configuración regional de derecha a izquierda que se especificó en **Info.plist**.

> [!WARNING]
> Tenga en cuenta que al cambiar el idioma y región para una configuración regional de derecha a izquierda en iOS, cualquier [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) vistas iniciará una excepción si no se incluyen los recursos necesarios para la configuración regional. Por ejemplo, al probar una aplicación en árabe que tenga un `DatePicker`, asegúrese de que **Oriente** está seleccionado en el **internacionalización** sección de la **iOS compilación** panel.

### <a name="android"></a>Android

La aplicación **AndroidManifest.xml** archivo debe actualizarse para que la `<uses-sdk>` los conjuntos de nodos el `android:minSdkVersion` atribuir a 17 y el `<application>` los conjuntos de nodos el `android:supportsRtl` atribuir a `true`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <uses-sdk android:minSdkVersion="17" ... />
    <application ... android:supportsRtl="true">
    </application>
</manifest>
```

A continuación, se puede probar la localización de derecha a izquierda cambiando el emulador de dispositivos o para usar el idioma de derecha a izquierda o habilitando **dirección de diseño de derecha a izquierda por fuerza** en **configuración > Opciones del desarrollador**.

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

Los recursos de idioma necesarios deben especificarse en el `<Resources>` nodo de la **Package.appxmanifest** archivo. En el ejemplo siguiente se muestra el árabe porque se haya agregado a la `<Resources>` nodo:

```xml
<Resources>
    <Resource Language="x-generate"/>
    <Resource Language="en" />
    <Resource Language="ar" />
</Resources>
```

Además, UWP requiere que la referencia cultural predeterminada de la aplicación se define explícitamente en la biblioteca estándar de .NET. Esto puede realizarse estableciendo la `NeutralResourcesLanguage` atributo `AssemblyInfo.cs`, o en otra clase, para la referencia cultural predeterminada:

```csharp
using System.Resources;

[assembly: NeutralResourcesLanguage("en")]
```

Localización de derecha a izquierda se puede probar cambiando el idioma y región en el dispositivo para el idioma apropiado de derecha a izquierda.

## <a name="limitations"></a>Limitaciones

Localización de derecha a izquierda Xamarin.Forms tiene actualmente una serie de limitaciones:

- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ubicación del botón, elemento de barra de herramientas ubicación y después, animación de transición se controla mediante la configuración regional del dispositivo, en lugar de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) no se voltea dirección deslice el dedo.
- [`Image`](xref:Xamarin.Forms.Image) no se voltea contenido visual.
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) y [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/) orientación se controla mediante la configuración regional del dispositivo, en lugar de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`WebView`](xref:Xamarin.Forms.WebView) contenido no respeta la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- Un `TextDirection` propiedad debe agregarse, para controlar la alineación del texto.

### <a name="ios"></a>iOS

- [`Stepper`](xref:Xamarin.Forms.Stepper) orientación se controla mediante la configuración regional del dispositivo, en lugar de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) alineación del texto se controla mediante la configuración regional del dispositivo, en lugar de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) no se revierten los movimientos y la alineación.

### <a name="android"></a>Android

- [`SearchBar`](xref:Xamarin.Forms.SearchBar) orientación se controla mediante la configuración regional del dispositivo, en lugar de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) selección de ubicación se controla mediante la configuración regional del dispositivo, en lugar de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.

### <a name="uwp"></a>UWP

- [`Editor`](xref:Xamarin.Forms.Editor) alineación del texto se controla mediante la configuración regional del dispositivo, en lugar de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.
- [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad no es heredada por [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) elementos secundarios.
- [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) alineación del texto se controla mediante la configuración regional del dispositivo, en lugar de la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad.

## <a name="right-to-left-language-support-with-xamarinuniversity"></a>De derecha a izquierda lenguaje admite con Xamarin.University

> [!VIDEO https://youtube.com/embed/f2lQ5yw3iiU]

**Admitir Xamarin.Forms 3.0 de derecha a izquierda, por [Universidad de Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Vínculos relacionados

- [Aplicación de ejemplo de TodoLocalizedRTL](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalizedRTL/)
