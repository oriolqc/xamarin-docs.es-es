---
title: Controles deslizantes, los conmutadores y los controles segmentados en Xamarin.iOS
description: Este documento describen diapositivas, conmutadores y controles segmentados en Xamarin.iOS, que describen cómo trabajar con ellos mediante programación o en el Diseñador de iOS.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 09a5d9e76c41eba4e16cab041daa67d3a5d8a584
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790034"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Controles deslizantes, los conmutadores y los controles segmentados en Xamarin.iOS

<a name="Sliders" />

## <a name="sliders"></a>Controles deslizantes

El control deslizante permite simple selección de un valor numérico dentro de un intervalo. El control tiene como valor predeterminado un valor entre 0 y 1, pero se pueden personalizar estos límites.

 [![](slider-switch-segmented-controls-images/image25a.png "Control deslizante")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Captura de pantalla siguiente muestra las propiedades que se pueden editables en el diseñador:

 [![](slider-switch-segmented-controls-images/image26a.png "Propiedades de control deslizante")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Puede establecer estos valores en el código tal como se muestra a continuación, incluida la conexión de un controlador para mostrar el valor seleccionado actualmente en un `UILabel` control:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

También puede personalizar la apariencia visual del control deslizante por valor

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

El control deslizante personalizado tiene el siguiente aspecto:

 [![](slider-switch-segmented-controls-images/image27a.png "Control deslizante personalizado")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Actualmente no hay un [error](http://stackoverflow.com/a/19496179) provocando el `ThumbTint` no se represente en tiempo de ejecución según lo previsto. Puede agregar la siguiente línea de código **antes de** el código anterior para solucionar este problema. [[Origen](http://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Puede utilizar cualquier imagen, se invalidará; sin embargo, asegúrese de que éste se sitúa _en_ el directorio de recursos y se llama en el código.

<a name="Switch" />

## <a name="switch"></a>Modificador

iOS usa el `UISwitch` como un valor booleano de entrada que se puede representar con un botón de opción en otras plataformas. El usuario puede manipular el control moviendo el *thumb* entre el **activado/desactivado** posiciones.

 [![](slider-switch-segmented-controls-images/image28a.png "Conmutador")](slider-switch-segmented-controls-images/image28a.png#lightbox)

Se puede personalizar la apariencia del conmutador de la **panel de propiedades** del diseñador, que le permitirá controlar el estado de forma predeterminada, **activado/desactivado tono** colores y una **on/off de imagen**. Esto se muestra en la imagen siguiente:

 [![](slider-switch-segmented-controls-images/image29a.png "Propiedades del conmutador")](slider-switch-segmented-controls-images/image29a.png#lightbox)

También se pueden establecer las propiedades del conmutador en el código, por ejemplo, el código siguiente mostrará un conmutador con el valor predeterminado de `On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>Controles segmentados

Un Control segmentados es una manera organizada para permitir a los usuarios interactuar con un número pequeño de opciones. Que se muestre horizontalmente y cada segmento funciona como un botón independiente. Al usar el diseñador, el Control segmentados pueden encontrarse en **cuadro de herramientas > controles**y debería ser similar a la siguiente imagen:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Control segmentado")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Una característica exclusiva del diseñador permite para que cada segmento de seleccionarse individualmente en la superficie de diseño, como se muestra a continuación:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Control segmentado")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

Esto permite que el panel de propiedades que se usará para controlar con mayor precisión de las propiedades de cada segmento. Puede ver las propiedades editables de la captura de pantalla siguiente:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Control segmentado")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Debe tenerse en cuenta que el estilo del Control segmentados está en desuso en iOS7 y, por lo tanto, ajustar las opciones para esto en una aplicación de iOS7 tendrá ningún efecto.

## <a name="related-links"></a>Vínculos relacionados

- [Controles (ejemplo)](https://developer.xamarin.com/samples/Controls/)
- [Controlador de alerta](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
