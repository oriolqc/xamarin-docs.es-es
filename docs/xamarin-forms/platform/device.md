---
title: Clase Device de Xamarin.Forms
description: En este artículo se explica cómo usar la clase Device de Xamarin.Forms para un mayor control sobre la funcionalidad y los diseños por plataforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 671abb0f61a5582a99165aa16c6b99db2ee8b1ee
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512878"
---
# <a name="xamarinforms-device-class"></a>Clase Device de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)

La clase [ `Device` ](xref:Xamarin.Forms.Device) contiene un número de propiedades y métodos para ayudar a los desarrolladores personalizar diseño y funcionalidad de acuerdo con cada plataforma.

Además de métodos y propiedades para el código de destino en los tipos de hardware específico y tamaños, el `Device` clase incluye métodos que se pueden usar para interactuar con los controles de interfaz de usuario de subprocesos en segundo plano. Para obtener más información, consulte [interactúe con la interfaz de usuario de subprocesos en segundo plano](#interact-with-the-ui-from-background-threads).

## <a name="providing-platform-specific-values"></a>Proporcionar valores específicos de la plataforma

Antes de Xamarin.Forms 2.3.4, se pudo obtener la plataforma de la aplicación se ejecuta en examinando el [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) propiedad y compararla con la [ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS), [ `TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android), [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone), y [ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows) valores de enumeración. De forma similar, uno de los [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) sobrecargas podrían usarse para proporcionar los valores específicos de la plataforma a un control.

Sin embargo, desde Xamarin.Forms 2.3.4 estas API se ha desusado y reemplazado. El [ `Device` ](xref:Xamarin.Forms.Device) clase ahora contiene las constantes de cadena pública que identifican las plataformas: [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS), [ `Device.Android` ](xref:Xamarin.Forms.Device.Android), `Device.WinPhone`() en desuso), `Device.WinRT` (en desuso) [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP), y [ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS). De forma similar, el [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) sobrecargas se han reemplazado con el [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) y [ `On` ](xref:Xamarin.Forms.On) API.

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

El [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) clase es una clase genérica que debe crearse con un `x:TypeArguments` atributo que coincida con el tipo de destino. En la clase [ `On` ](xref:Xamarin.Forms.On), el atributo [ `Platform` ](xref:Xamarin.Forms.On.Platform) puede aceptar un único valor `string` o varios valores `string` delimitados por comas.

> [!IMPORTANT]
> Proporcionar un valor incorrecto en el atributo `Platform` de la clase `On` no producirá un error. En su lugar, el código se ejecutará sin el valor específico de la plataforma que se va a aplicar.

Como alternativa, el `OnPlatform` se puede usar extensión de marcado en XAML para personalizar la apariencia de la interfaz de usuario en forma de acuerdo con la plataforma. Para obtener más información, consulte [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Device.Idiom

El `Device.Idiom` propiedad puede usarse para modificar los diseños o funcionalidad en función del dispositivo de la aplicación se ejecuta en. El enumerador [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom) contiene los siguientes valores:

-  **Teléfono** – iPhone, iPod touch y los dispositivos Android más estrechas que 600 DIP ^
-  **Tablet** : iPad, los dispositivos de Windows y dispositivos Android más amplio que 600 DIP ^
-  **Escritorio** : solo se devuelven en [aplicaciones para UWP](~/xamarin-forms/platform/windows/installation/index.md) en equipos de escritorio de Windows 10 (devuelve `Phone` en dispositivos móviles de Windows, como en escenarios de Continuum)
-  **TV**: dispositivos de TV con Tizen
-  **Watch** – relojes Tizen
-  **Unsupported** : no se utiliza

*^ DIP no es necesariamente el número de píxeles físicos*

El `Idiom` propiedad resulta especialmente útil para la creación de diseños que se benefician de las pantallas más grandes, similar al siguiente:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

El [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1) clase proporciona la misma funcionalidad en XAML:

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

El [ `OnIdiom` ](xref:Xamarin.Forms.OnPlatform`1) clase es una clase genérica que debe crearse con un `x:TypeArguments` atributo que coincida con el tipo de destino.

Como alternativa, el `OnIdiom` se puede usar extensión de marcado en XAML para personalizar la apariencia de la interfaz de usuario en función de la expresión del dispositivo se está ejecutando la aplicación en. Para obtener más información, consulte [OnIdiom Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device.FlowDirection

El [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valor recupera un [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valor de enumeración que representa la dirección del flujo actual que usa el dispositivo. La dirección de flujo es la dirección en la que el ojo humano lee los elementos de la interfaz de usuario en la página. Los valores de la enumeración son:

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

## <a name="devicestyles"></a>Device.Styles

La [propiedad `Styles`](~/xamarin-forms/user-interface/styles/index.md) contiene las definiciones de estilo integradas que pueden aplicarse a algunos de los controles (como `Label`) la propiedad `Style`. Los estilos disponibles son:

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

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

## <a name="deviceopenuri"></a>Device.OpenUri

El metodo `OpenUri` puede usarse para desencadenar operaciones en la plataforma nativa, como abrir una dirección URL en el explorador web nativo (**Safari** en iOS o **Internet** en Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

El [ejemplo WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) incluye un ejemplo que usa `OpenUri` para abrir las direcciones URL y desencadenar también llamadas telefónicas.

El [ejemplo mapas](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) también usa `Device.OpenUri` para mostrar mapas y las direcciones mediante la aplicacion de **Mapas** nativa en iOS y Android.

## <a name="devicestarttimer"></a>Device.StartTimer

La clase `Device` también tiene un metodo `StartTimer` que proporciona una manera sencilla de desencadenar tareas dependientes del tiempo que funcionan en el código común de Xamarin.Forms, incluidas una biblioteca .NET Standard. Pasa un valor `TimeSpan` para establecer el intervalo y devuelve `true` para mantener el temporizador en ejecución o `false` para detenerlo después de la invocación actual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si el código del temporizador interactúa con la interfaz de usuario (como establecer el texto de un `Label` o mostrar una alerta) debe realizarse dentro de una expresión `BeginInvokeOnMainThread` (ver abajo).

## <a name="interact-with-the-ui-from-background-threads"></a>Interactuar con la interfaz de usuario de subprocesos en segundo plano

Mayoría de los sistemas operativos, incluidos iOS, Android y la plataforma Universal de Windows, utilice un modelo de subprocesamiento único para el código que implican la interfaz de usuario. Este subproceso se suele denominar el *subproceso principal* o *el subproceso de interfaz de usuario*. Una consecuencia de este modelo es que todo el código que tiene acceso a elementos de la interfaz de usuario debe ejecutar en el subproceso principal de la aplicación.

A veces, las aplicaciones usar subprocesos en segundo plano para realizar operaciones potencialmente larga, como la recuperación de datos de un servicio web. Si necesita código que se ejecuta en un subproceso en segundo plano tener acceso a elementos de la interfaz de usuario, se debe ejecutar ese código en el subproceso principal.

El `Device` clase incluye lo siguiente `static` elementos desde subprocesos de fondos de la interfaz de métodos que se pueden usar para interactuar con el usuario:

| Método | Argumentos | Valores devueltos | Finalidad |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Invoca un `Action` en el subproceso principal y no espera a que finalice. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Invoca un `Func<T>` en el subproceso principal y espera a que finalice. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Invoca un `Action` en el subproceso principal y espera a que finalice. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Invoca un `Func<Task<T>>` en el subproceso principal y espera a que finalice. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Invoca un `Func<Task>` en el subproceso principal y espera a que finalice. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Devuelve el `SynchronizationContext` del subproceso principal. |

El código siguiente muestra un ejemplo del uso de la `BeginInvokeOnMainThread` método:

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo Device](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Ejemplo Styles](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device](xref:Xamarin.Forms.Device)
