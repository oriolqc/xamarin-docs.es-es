---
title: Clase de dispositivo de Xamarin.Forms
description: En este artículo se explica cómo usar la clase de dispositivo de Xamarin.Forms para un mayor control sobre la funcionalidad y los diseños de por la plataforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: ff707cdf73665ae07881d2d17ec837a4cfacaca0
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935376"
---
# <a name="xamarinforms-device-class"></a>Clase de dispositivo de Xamarin.Forms

El [ `Device` ](xref:Xamarin.Forms.Device) clase contiene un número de propiedades y métodos para ayudar a los desarrolladores personalizar diseño y la funcionalidad de acuerdo con la plataforma.

Además de métodos y propiedades para el código de destino en los tipos de hardware específico y tamaños, el `Device` clase incluye el [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) método que se debe usar al interactuar con la interfaz de usuario de controles subprocesos en segundo plano.

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>Proporcionar valores específicos de la plataforma

Antes de Xamarin.Forms 2.3.4, se pudo obtener la plataforma de la aplicación se ejecuta en examinando el [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) propiedad y compararla con la [ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS), [ `TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android), [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone), y [ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows) valores de enumeración. De forma similar, uno de los [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) sobrecargas podrían usarse para proporcionar los valores específicos de la plataforma a un control.

Sin embargo, desde Xamarin.Forms 2.3.4 estas API se ha desusado y reemplazado. El [ `Device` ](xref:Xamarin.Forms.Device) clase ahora contiene las constantes de cadena pública que identifican las plataformas: [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS), [ `Device.Android` ](xref:Xamarin.Forms.Device.Android), `Device.WinPhone`() en desuso), `Device.WinRT` (en desuso) [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP), y [ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS). De forma similar, el [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) sobrecargas se han reemplazado con el [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) y [ `On` ](xref:Xamarin.Forms.On) API.

En C#, se pueden proporcionar valores específicos de la plataforma mediante la creación de un `switch` instrucción en el [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) propiedad y, a continuación, proporcionar `case` instrucciones para las plataformas necesarias:

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

El [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) y [ `On` ](xref:Xamarin.Forms.On) clases proporcionan la misma funcionalidad en XAML:

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

El [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) clase es una clase genérica y por lo que debe crearse con un `x:TypeArguments` atributo que coincida con el tipo de destino. En el [ `On` ](xref:Xamarin.Forms.On) (clase), el [ `Platform` ](xref:Xamarin.Forms.On.Platform) atributo puede aceptar un único `string` valor o varios delimitada por comas `string` valores.

> [!IMPORTANT]
> Proporcionar una incorrecta `Platform` atributo valor en el `On` clase no se producirá un error. En su lugar, el código se ejecutará sin el valor específico de la plataforma que se va a aplicar.

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

El `Device.Idiom` puede usarse para modificar los diseños o funcionalidad según el dispositivo se está ejecutando la aplicación en. El [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom) enumeración contiene los siguientes valores:

-  **Teléfono** – iPhone, iPod touch y los dispositivos Android más estrechas que 600 DIP ^
-  **Tablet** : iPad, los dispositivos de Windows y dispositivos Android más amplio que 600 DIP ^
-  **Escritorio** : solo se devuelven en [aplicaciones para UWP](~/xamarin-forms/platform/windows/installation/index.md) en equipos de escritorio de Windows 10 (devuelve `Phone` en dispositivos móviles de Windows, como en escenarios de Continuum)
-  **TV** – dispositivos Tizen TV
-  **No compatible** : no se utiliza

*^ DIP no es necesariamente el número de píxeles físicos*

`Idiom` es especialmente útil para la creación de diseños que se benefician de las pantallas más grandes, similar al siguiente:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

## <a name="deviceflowdirection"></a>Device.FlowDirection

El [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valor recupera un [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valor de enumeración que representa la dirección del flujo actual que usa el dispositivo. Dirección del flujo es la dirección en la que se analizan los elementos de interfaz de usuario en la página por el ojo. Los valores de la enumeración son:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToRight`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

En XAML, el [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valor se puede recuperar mediante el `x:Static` extensión de marcado:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

El código equivalente en C# es:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Para obtener más información acerca de la dirección del flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

El [ `Styles` propiedad](~/xamarin-forms/user-interface/styles/index.md) contiene las definiciones de estilo integradas que pueden aplicarse a algunos de los controles (como `Label`) `Style` propiedad. Los estilos disponibles son:

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

<a name="Device_GetNamedSize" />

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` puede utilizarse al establecer [ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md) en código C#:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

<a name="Device_OpenUri" />

## <a name="deviceopenuri"></a>Device.OpenUri

El `OpenUri` método puede usarse para desencadenar las operaciones en la plataforma subyacente, como abrir una dirección URL en el explorador web nativos (**Safari** en iOS o **Internet** en Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

El [ejemplo WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) incluye un ejemplo que usa `OpenUri` para abrir las direcciones URL y desencadenar también llamadas telefónicas.

El [ejemplo mapas](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) también usa `Device.OpenUri` para mostrar mapas y las direcciones mediante nativo **asigna** aplicaciones en iOS y Android.

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

El `Device` clase también tiene un `StartTimer` método que proporciona una manera sencilla de desencadenar tareas dependientes del tiempo que funciona en el código común de Xamarin.Forms, incluidas una biblioteca .NET Standard. Pasar un `TimeSpan` para establecer el intervalo y devolver `true` para mantener el temporizador de ejecución o `false` para detenerlo después de la invocación actual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si el código del temporizador interactúa con la interfaz de usuario (como establecer el texto de un `Label` o mostrar una alerta) debe realizarse dentro de un `BeginInvokeOnMainThread` expresión (ver abajo).

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

Nunca se deben tener acceso a los elementos de la interfaz de usuario por los subprocesos en segundo plano, por ejemplo, el código que se ejecuta en un temporizador o un controlador de finalización para las operaciones asincrónicas, como las solicitudes web. Cualquier código en segundo plano que se deba actualizar la interfaz de usuario se debe incluir en [ `BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/). Este es el equivalente de `InvokeOnMainThread` en iOS, `RunOnUiThread` en Android, y `Dispatcher.RunAsync` en la plataforma Universal de Windows.

El código de Xamarin.Forms es:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Tenga en cuenta que los métodos mediante `async/await` no es necesario usar `BeginInvokeOnMainThread` si se están ejecutando en el subproceso de interfaz de usuario principal.

## <a name="summary"></a>Resumen

Xamarin.Forms `Device` clase permite un mayor control sobre la funcionalidad y los diseños de por la plataforma: aunque el código (proyectos de biblioteca de .NET Standard o proyectos compartidos) en común.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de dispositivo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Ejemplo de estilos](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dispositivo](xref:Xamarin.Forms.Device)
