---
title: Clase de dispositivo
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 7ba3808e7b8d948d502be3f80b8830e1aaf3b52f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="device-class"></a>Clase de dispositivo

El [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) clase contiene una serie de propiedades y métodos para ayudar a los desarrolladores personalizar el diseño y la funcionalidad de forma según la plataforma.

Además de métodos y propiedades para el código de destino en tipos de hardware específicos y tamaños, el `Device` clase incluye el [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) método que se debe usar al interactuar con la interfaz de usuario a controles de subprocesos en segundo plano.

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>Proporcionar valores específicos de la plataforma

Antes de Xamarin.Forms 2.3.4, la plataforma de la aplicación se estaba ejecutando en puede obtenerse mediante el examen de la [ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) propiedad y lo compara a la [ `TargetPlatform.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/), [ `TargetPlatform.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/), [ `TargetPlatform.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/), y [ `TargetPlatform.Windows` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) valores de enumeración. De forma similar, uno de los [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) sobrecargas pudieron usarse para proporcionar los valores específicos de la plataforma a un control.

Sin embargo, desde Xamarin.Forms 2.3.4 estas API se ha desusado y reemplazado. El [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) clase ahora contiene las constantes de cadena pública que identifican las plataformas – [ `Device.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), [ `Device.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), [ `Device.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/), [ `Device.WinRT` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinRT/), [ `Device.UWP` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), y [ `Device.macOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.macOS/). De forma similar, el [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) sobrecargas se han reemplazado por la [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) y [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) API.

En C#, se pueden proporcionar valores específicos de la plataforma mediante la creación de un `switch` instrucción en el [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) propiedad y, a continuación, proporcionar `case` instrucciones para las plataformas necesarias:

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.WinPhone:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

El [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) y [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) clases proporcionan la misma funcionalidad en XAML:

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, WinPhone, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

El [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) clase es una clase genérica y por lo que se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino. En el [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) (clase), el [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) atributo puede aceptar un único `string` múltiples delimitada por comas o valor `string` valores.

> [!IMPORTANT]
> Proporcionar incorrecta `Platform` atributo valor en el `On` clase no se producirá un error. En su lugar, el código se ejecutará sin el valor específico de la plataforma que se va a aplicar.

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

La `Device.Idiom` puede usarse para modificar diseños o funcionalidad en función del dispositivo que se ejecuta la aplicación. El [ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/) enumeración contiene los siguientes valores:

-  **Teléfono** – iPhone, iPod touch, Windows Phone, dispositivos Android más estrechas que 600 DIP ^
-  **Tablet** : iPad, equipos de Windows 8.1, dispositivos Android ocupa más de 600 DIP ^
-  **Escritorio** : solo se devuelven en [aplicaciones UWP](~/xamarin-forms/platform/windows/installation/universal.md) en equipos de escritorio de Windows 10 (devuelve `Phone` en dispositivos móviles de Windows, como en escenarios de Continuum)
-  **TV** – dispositivos Tizen TV
-  **No compatible** : no se utiliza

*^ DIP no es necesariamente el número de píxeles físicos*

`Idiom` es especialmente útil para la creación de diseños que se benefician de las pantallas más grandes, como el siguiente:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

El [ `Styles` propiedad](~/xamarin-forms/user-interface/styles/index.md) contiene las definiciones de estilo integradas que pueden aplicarse a algunos controles (como `Label`) `Style` propiedad. Los estilos disponibles son:

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

El `OpenUri` método se puede utilizar para desencadenar las operaciones en la plataforma subyacente, como abrir una dirección URL en el explorador web nativo (**Safari** en iOS o **Internet** en Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

El [ejemplo WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) incluye un ejemplo del uso `OpenUri` para abrir direcciones URL y desencadenar también llamadas telefónicas.

El [ejemplo mapas](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) también utiliza `Device.OpenUri` para mostrar mapas y direcciones mediante nativo **asigna** aplicaciones en iOS y Android.

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

El `Device` clase también tiene un `StartTimer` método que proporciona una manera sencilla de desencadenar las tareas dependientes del tiempo que funciona en el código común de Xamarin.Forms (incluidos PCLs). Pasar un `TimeSpan` para establecer el intervalo y devolver `true` para mantener el temporizador de ejecución o `false` detenerla después de la llamada actual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si el código en el temporizador interactúa con la interfaz de usuario (por ejemplo, configurar el texto de un `Label` o mostrar una alerta) debe realizarse dentro de un `BeginInvokeOnMainThread` expresión (ver abajo).

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

Nunca subprocesos en segundo plano, como el código que se está ejecutando en un temporizador o un controlador de finalización de operaciones asincrónicas como las solicitudes web deben tener acceso a elementos de la interfaz de usuario. Cualquier código en segundo plano que necesita para actualizar la interfaz de usuario debe incluirse dentro de [ `BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/). Éste es el equivalente de `InvokeOnMainThread` en iOS, `RunOnUiThread` en Android, y `Dispatcher.BeginInvoke` en Windows Phone.

El código de Xamarin.Forms es:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Tenga en cuenta que métodos mediante `async/await` no es necesario usar `BeginInvokeOnMainThread` si se están ejecutando en el subproceso de interfaz de usuario principal.

## <a name="summary"></a>Resumen

El Xamarin.Forms `Device` clase permite a un control específico sobre la funcionalidad y diseños por la plataforma: incluido el código (PCL o proyectos compartidos) en común.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de dispositivo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Ejemplo de estilos](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dispositivo](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)
