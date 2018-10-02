---
title: Clase Device de Xamarin.Forms
description: En este artículo se explica cómo usar la clase Device de Xamarin.Forms para un mayor control sobre la funcionalidad y los diseños por plataforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c706d50962fb707208203a97374d4ae26f141ebf
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998272"
---
# <a name="xamarinforms-device-class"></a>Clase Device de Xamarin.Forms

La clase [ `Device` ](xref:Xamarin.Forms.Device) contiene un número de propiedades y métodos para ayudar a los desarrolladores personalizar diseño y funcionalidad de acuerdo con cada plataforma.

Además de métodos y propiedades correr código en los diferentes tipos de hardware y tamaños, la clase `Device` incluye [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) método que se debe usar al interactuar con controles de la interfaz de usuario desde subprocesos en segundo plano.

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>Proporcionando valores específicos de plataforma

Antes de la version 2.3.4 de Xamarin.Forms, se podia obtener la plataforma que ejecuta la aplicación examinando la propiedad [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) y comparandola con los valores de enumeracion [ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS), [ `TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android), [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone), y [ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows). De forma similar, [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) podia usarse para proporcionar valores específicos de cada plataforma a un control.

Sin embargo, desde Xamarin.Forms 2.3.4 estas APIs se han vuelto obsoletas y remplazadas. La clase [ `Device` ](xref:Xamarin.Forms.Device) ahora contiene las constantes de texto públicas que identifican las plataformas: [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS), [ `Device.Android` ](xref:Xamarin.Forms.Device.Android), `Device.WinPhone`() Obsoleta), `Device.WinRT` Obsoleta [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP), y [ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS). De forma similar, el metodo [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) se ha reemplazado con los metodos [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) y [ `On` ](xref:Xamarin.Forms.On).

En C#, se pueden proporcionar valores específicos por plataforma mediante la creación de una instruccion `switch` con la propiedad [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) y, a continuación, proporcionar las instrucciones `case` para las plataformas necesarias:

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

Las clases [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) y [ `On` ](xref:Xamarin.Forms.On) proporcionan la misma funcionalidad en XAML:

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

La clase [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) es una clase genérica y por lo que debe crearse con un atributo `x:TypeArguments` que coincida con el tipo de destino. En la clase [ `On` ](xref:Xamarin.Forms.On), el atributo [ `Platform` ](xref:Xamarin.Forms.On.Platform) puede aceptar un único valor `string` o varios valores `string` delimitados por comas.

> [!IMPORTANT]
> Proporcionar un valor incorrecto en el atributo `Platform` de la clase `On` no producirá un error. En su lugar, el código se ejecutará sin el valor específico de la plataforma que se va a aplicar.

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

La propiedad `Device.Idiom` puede usarse para modificar los diseños o funcionalidad según el dispositivo que en el que se este ejecutando la aplicación. El enumerador [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom) contiene los siguientes valores:

-  **Phone** – iPhone, iPod touch y los dispositivos Android más estrechos que 600 dips^
-  **Tablet** : iPad, los dispositivos de Windows y dispositivos Android más amplios que 600 dips^
-  **Desktop** : solo se devuelve en [aplicaciones para UWP](~/xamarin-forms/platform/windows/installation/index.md) en equipos de escritorio de Windows 10 (devuelve `Phone` en dispositivos móviles de Windows, Incluyendo escenarios continuos)
-  **TV** – dispositivos TV Tizen
-  **Watch** – relojes Tizen
-  **Unsupported** : no se utiliza

*^ dips no es necesariamente el número de píxeles físicos*

`Idiom` es especialmente útil para la creación de diseños en dispositivos con pantallas más grandes, como el siguiente:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

## <a name="deviceflowdirection"></a>Device.FlowDirection

La propiedad [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) regresa un valor de enumeracion [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) que representa la dirección del flujo actual que usa el dispositivo. Dirección del flujo es la dirección en la que se analizan los elementos de interfaz de usuario en la página por el ojo. Los valores de la enumeración son:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToRight`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

En XAML, el valor [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) se puede recuperar mediante la extensión de marcado `x:Static`:

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

La [propiedad `Styles`](~/xamarin-forms/user-interface/styles/index.md) contiene las definiciones de estilo integradas que pueden aplicarse a algunos de los controles (como `Label`) la propiedad `Style`. Los estilos disponibles son:

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

El metodo `OpenUri` puede usarse para desencadenar operaciones en la plataforma nativa, como abrir una dirección URL en el explorador web nativo (**Safari** en iOS o **Internet** en Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

El [ejemplo WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) incluye un ejemplo que usa `OpenUri` para abrir las direcciones URL y realizar también llamadas telefónicas.

El [ejemplo mapas](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) también usa `Device.OpenUri` para mostrar mapas y las direcciones mediante la aplicacion de **Mapas** nativa en iOS y Android.

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

La clase `Device` también tiene un metodo `StartTimer` que proporciona una manera sencilla de desencadenar tareas dependientes del tiempo que funcionan en el código común de Xamarin.Forms, incluidas una biblioteca .NET Standard. Pasa un valor `TimeSpan` para establecer el intervalo y devuelve `true` para mantener el temporizador en ejecución o `false` para detenerlo después de la invocación actual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si el código del temporizador interactúa con la interfaz de usuario (como establecer el texto de un `Label` o mostrar una alerta) debe realizarse dentro de una expresión `BeginInvokeOnMainThread` (ver abajo).

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

Los elementos de la interfaz de usuario nunca deben de ser accesados por los subprocesos en segundo plano, por ejemplo, el código que se ejecuta en un temporizador o un controlador de finalización para operaciones asincrónicas, como las solicitudes web. Cualquier código en segundo plano que se deba actualizar la interfaz de usuario se debe incluir en [ `BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)). Este es el equivalente de `InvokeOnMainThread` en iOS, `RunOnUiThread` en Android, y `Dispatcher.RunAsync` en Universal Windows Platform.

El código Xamarin.Forms es:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Tenga en cuenta que los métodos mediante `async/await` no es necesario usar `BeginInvokeOnMainThread` si se están ejecutando en el subproceso de interfaz de usuario principal.

## <a name="summary"></a>Resumen

La clase `Device` de Xamarin.Forms permite un mayor control sobre la funcionalidad y los diseños por plataforma: incluso en código comun (proyectos de biblioteca de .NET Standard o proyectos compartidos).


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo Device](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Ejemplo Styles](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device](xref:Xamarin.Forms.Device)
