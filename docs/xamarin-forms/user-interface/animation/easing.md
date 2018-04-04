---
title: Funciones de aceleración
description: Xamarin.Forms incluye una clase de entrada y salida lenta que le permite especificar una función de transferencia que controla cómo las animaciones aceleran o ralenticen tal y como se está ejecutando. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: e9171b885bdf5958b6969719301a1d7dad51d95b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="easing-functions"></a>Funciones de aceleración

_Xamarin.Forms incluye una clase de entrada y salida lenta que le permite especificar una función de transferencia que controla cómo las animaciones aceleran o ralenticen tal y como se está ejecutando. En este artículo se muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas._


El [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) clase define una serie de funciones de aceleración que puede ser utilizado por las animaciones:

- El [ `BounceIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) función de aceleración la animación al principio de devoluciones de mensajes.
- El [ `BounceOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/) función de aceleración rebota la animación al final.
- El [ `CubicIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/) lenta de la función de aceleración acelera la animación.
- El [ `CubicInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/) función de aceleración acelera la animación al principio y se ralentiza la animación al final.
- El [ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/) rápidamente en función de aceleración reduce la velocidad de la animación.
- El [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) función de aceleración usa una velocidad constante y se función de aceleración de manera predeterminada.
- El [ `SinIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/) sin problemas en función de aceleración acelera la animación.
- El [ `SinInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/) sin problemas en función de aceleración acelera la animación al principio y reduce la velocidad sin problemas la animación al final.
- El [ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/) sin problemas en función de aceleración reduce la velocidad de la animación.
- El [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) función de aceleración hace que la animación acelerar rápidamente hacia el final.
- El [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) función de aceleración hace que la animación que se ralentice rápidamente hacia el final.

El `In` y `Out` sufijos indican si el efecto proporcionado por la función de aceleración es apreciable al principio de la animación al final, o ambos.

Además, pueden crearse las funciones de aceleración personalizadas. Para obtener más información, consulte [funciones de aceleración personalizada](#customeasing).

## <a name="consuming-an-easing-function"></a>Consumir una función de aceleración

Los métodos de extensión de la animación en la [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase permite que una función de aceleración que se especifique como parámetro del método final, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Al especificar una función de aceleración para una animación, la velocidad de animación se convierte en no lineal y genera el efecto proporcionado por la función de aceleración. Si se omite una función de aceleración al crear una animación hace que la animación usar el valor predeterminado [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) cómo facilitar la función, lo que produce una velocidad lineal.

Para obtener más información acerca del uso de los métodos de extensión de la animación en la [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) de clases, consulte [animaciones Simple](~/xamarin-forms/user-interface/animation/simple.md). Funciones de aceleración también pueden ser utilizada por el [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) clase. Para obtener más información, consulte [personalizado animaciones](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Funciones de aceleración de personalizado

Existen tres métodos principales para crear una función de aceleración personalizada:

1. Crear un método que toma un `double` argumento y devuelve un `double` resultado.
1. Creará un control `Func<double, double>`.
1. Especifique la función de aceleración como argumento para la [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) constructor.

En los tres casos, la función de aceleración personalizada debería devolver 0 para un argumento de 0 y 1 para un argumento de 1. Sin embargo, se puede devolver cualquier valor entre los valores de argumento de 0 y 1. A su vez ahora se explicará cada enfoque.

### <a name="custom-easing-method"></a>Custom aceleración (método)

Una función de aceleración personalizada se puede definir como un método que toma un `double` argumento y devuelve un `double` como resultado, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

El `CustomEase` método trunca el valor de entrada a los valores 0, 0,2, 0,4, 0,6, 0,8 y 1. Por lo tanto, la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia se traduce en saltos discretos, en lugar de manera fluida.

### <a name="custom-easing-func"></a>Custom aceleración Func

También se puede definir una función de aceleración personalizada como un `Func<double, double>`, como se muestra en el ejemplo de código siguiente:

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

El `CustomEase` `Func` representa una función de aceleración que comienza rápido, se ralentiza e invierte curso y, a continuación, se invierte nuevo curso para acelerar rápidamente hacia el final. Por lo tanto, mientras que el movimiento general de la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia es hacia abajo, invierte temporalmente curso a mitad de camino a través de la animación.

### <a name="custom-easing-constructor"></a>Custom aceleración Constructor

También se puede definir una función de aceleración personalizada como el argumento de la [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) constructor, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

La función de aceleración personalizada se especifica como un argumento de función lambda para la [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) constructor y se utiliza el `Math.Cos` método para crear un efecto de colocación lento que terminaron por la `Math.Exp` método. Por lo tanto, la [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) se traduce la instancia para que parezca llegue a su lugar descansen final.

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar las funciones de aceleración predefinidas y cómo crear funciones de aceleración personalizadas. Xamarin.Forms incluye una [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) clase que permite especificar una función de transferencia que controla cómo aceleran las animaciones o ralentizar tal y como se está ejecutando.



## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [Funciones de aceleración (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)
- [Easing](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
