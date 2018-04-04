---
title: Colores
description: Xamarin.Forms ofrece una clase de Color flexible y multiplataforma.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: 8d6276a96d591363c88be4ff7edd8a9d039fb29d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="colors"></a>Colores

_Xamarin.Forms ofrece una clase de Color flexible y multiplataforma._

Este artículo detallan las distintas formas en el `Color` clase puede usarse en Xamarin.Forms.

La `Color` clase proporciona una serie de métodos para crear una instancia de color

-  **Colores con nombre** -una colección de denominado-los colores comunes, incluidos los `Red`, `Green`, y `Blue`.
-  **FromHex** -valor similar a la sintaxis utilizada en HTML, por ejemplo, "00FF00" de la cadena. Alfa es opcionalmente se puede especificar como el primer par de caracteres ("CC00FF00").
-  **FromHsla** -matiz, saturación y luminosidad `double` valores, cuyo valor alfa opcional (0.0 y 1.0).
-  **FromRgb** -rojo, verde y azul `int` valores (0-255).
-  **FromRgba** -rojo, verde, azul y alfa `int` valores (0-255).
-  **FromUint** -establecer un único `double` que representa el valor **argb**.

Presentamos algunos colores de ejemplo, asignado a la `BackgroundColor` de algunas etiquetas utilizando diferentes variaciones de la sintaxis permitida:

```csharp
var red    = new Label { Text = "Red",   BackgroundColor = Color.Red };
var orange = new Label { Text = "Orange",BackgroundColor = Color.FromHex("FF6A00") };
var yellow = new Label { Text = "Yellow",BackgroundColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
var green  = new Label { Text = "Green", BackgroundColor = Color.FromRgb (38, 127, 0) };
var blue   = new Label { Text = "Blue",  BackgroundColor = Color.FromRgba(0, 38, 255, 255) };
var indigo = new Label { Text = "Indigo",BackgroundColor = Color.FromRgb (0, 72, 255) };
var violet = new Label { Text = "Violet",BackgroundColor = Color.FromHsla(0.82, 1, 0.25, 1) };

var transparent = new Label { Text = "Transparent",BackgroundColor = Color.Transparent };
var @default = new Label    { Text = "Default",    BackgroundColor = Color.Default };
var accent = new Label      { Text = "Accent",     BackgroundColor = Color.Accent };
```

Estos colores se muestran en cada plataforma siguiente. Tenga en cuenta el color final - `Accent` -es un color blue-ish para iOS y Android; este valor se define mediante Xamarin.Forms. En Windows Phone la `Accent` se muestra en rojo *ya que es el color de énfasis seleccionados por el usuario para dicho dispositivo*; este valor cambia según las preferencias del usuario.

 [![Demostración de color](colors-images/colors-sml.png "Color demostración")](colors-images/colors.png#lightbox "demostración de Color")

## <a name="colordefault"></a>Color.Default

Use el `Default` para establecer un valor de color a la predeterminada de la plataforma (descripción que representa un color subyacente diferente en cada plataforma para cada propiedad) (o volver a establecer).

Los desarrolladores pueden usar este valor para establecer un `Color` propiedad pero no debería **no** consultar esta instancia para su componente valores RGB (que tiene todo listo en -1).

## <a name="colortransparent"></a>Color.Transparent

Establecer el color que desea borrar.

## <a name="coloraccent"></a>Color.Accent

En Windows Phone, éste es el color complementario elegido por el usuario. Buenas aplicaciones de Windows Phone utilizan como parte de su aplicación de estilos para proporcionar una apariencia nativa.

En iOS y Android esta instancia se establece en un color de contraste que está visible en el fondo predeterminado, pero no es el mismo que el color del texto de forma predeterminada.

## <a name="additional-methods"></a>Métodos adicionales

`Color` instancias incluyen métodos adicionales que pueden usarse para crear nuevos colores:

-  **AddLuminosity** -devuelve un nuevo color modificando la luminosidad el delta proporcionado.
-  **WithHue** -devuelve un nuevo color, reemplazando el matiz con el valor proporcionado.
-  **WithLuminosity** -devuelve un nuevo color, reemplazando la luminosidad con el valor proporcionado.
-  **WithSaturation** -devuelve un nuevo color, reemplazando la saturación con el valor proporcionado.
-  **MultiplyAlpha** -devuelve un nuevo color modificando el canal alfa, multiplicar por el valor alfa proporcionado.

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

Este fragmento de código utiliza el `Device.RuntimePlatform` propiedad para establecer el color de forma selectiva un `ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>Uso de XAML

Colores también pueden hacer referencia fácilmente en XAML mediante los nombres de colores definidos o las representaciones hexadecimales que se muestra a continuación:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

## <a name="summary"></a>Resumen

El Xamarin.Forms `Color` clase se utiliza para crear referencias de color basadas en la plataforma. Se puede utilizar en XAML y código compartido.


## <a name="related-links"></a>Vínculos relacionados

- [ColorsSample](https://developer.xamarin.com/samples/WorkingWithColors)
- [Selector de enlazable (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
