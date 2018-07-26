---
title: Controles deslizantes, conmutadores y controles segmentados en Xamarin.iOS
description: Este documento describen las diapositivas, conmutadores y controles segmentados en Xamarin.iOS, que describen cómo trabajar con ellos mediante programación y en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 7df79cb6f225326dda6656fa9dfe9534e35f2457
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241996"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Controles deslizantes, conmutadores y controles segmentados en Xamarin.iOS

<a name="Sliders" />

## <a name="sliders"></a>Controles deslizantes

El control deslizante permite la selección simple de un valor dentro de un intervalo numérico. El valor predeterminado es el control en un valor entre 0 y 1, pero se pueden personalizar estos límites.

 [![](slider-switch-segmented-controls-images/image25a.png "Control deslizante")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Captura de pantalla siguiente muestra las propiedades que son editables en el diseñador:

 [![](slider-switch-segmented-controls-images/image26a.png "Propiedades del control deslizante")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Puede establecer estos valores en el código tal como se muestra a continuación, incluida la inserción de un controlador para mostrar el valor seleccionado actualmente en un `UILabel` control:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

También puede personalizar la apariencia visual del control deslizante mediante configuración

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

El control deslizante personalizado tiene este aspecto:

 [![](slider-switch-segmented-controls-images/image27a.png "Control deslizante personalizado")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Actualmente no hay un [error](http://stackoverflow.com/a/19496179) causando la `ThumbTint` no se represente en tiempo de ejecución según lo previsto. Puede agregar la siguiente línea de código **antes** el código anterior para solucionar este problema. [[Origen](http://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Puede usar cualquier imagen, tal y como se va a reemplazar, pero asegúrese de que se coloca _en_ el directorio de recursos y se llama en el código.

<a name="Switch" />

## <a name="switch"></a>Modificador

iOS usa el `UISwitch` como un valor booleano de entrada que puede representarse mediante un botón de radio en otras plataformas. El usuario puede manipular el control moviendo el *thumb* entre el **activado/desactivado** posiciones.

 [![](slider-switch-segmented-controls-images/image28a.png "Conmutador")](slider-switch-segmented-controls-images/image28a.png#lightbox)

Se puede personalizar la apariencia del conmutador en el **panel de propiedades** del diseñador, que le permitirá controlar el estado de forma predeterminada, **o desactivar el tinte** colores y un **on/off imagen**. Esto se ilustra en la imagen siguiente:

 [![](slider-switch-segmented-controls-images/image29a.png "Propiedades del conmutador")](slider-switch-segmented-controls-images/image29a.png#lightbox)

También se pueden establecer las propiedades del conmutador en el código, por ejemplo, el código siguiente muestra un conmutador con el valor predeterminado de `On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>Controles segmentados

Un Control segmentado es una forma organizada para permitir a los usuarios interactuar con un número pequeño de opciones. Se colocan horizontalmente y cada segmento funciona como un botón independiente. Cuando se usa el diseñador, se puede encontrar el Control segmentado en **cuadro de herramientas > controles**y debe ser similar a la siguiente imagen:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Control segmentado")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Una característica exclusiva del diseñador permite para que cada segmento seleccionarse individualmente en la superficie de diseño, como se muestra a continuación:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Control segmentado")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

Esto permite que el panel de propiedades que se usará para controlar con mayor precisión de las propiedades de cada segmento. Puede ver las propiedades editables en la captura de pantalla siguiente:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Control segmentado")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Debe tenerse en cuenta que el estilo del Control segmentado ha quedado obsoleto en iOS7 y, por lo tanto, ajuste las opciones para esto en una aplicación en iOS7 no tendrán ningún efecto.

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
- [Controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
