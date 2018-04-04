---
title: Compresión de diseño
description: Compresión de diseño quita diseños especificados del árbol visual en un intento de mejorar el rendimiento de la presentación de página. En este artículo se explica cómo habilitar la compresión de diseño y los beneficios que puede ofrecer.
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 9c698d539ab671ee2a033ae5943a46e0cc870f76
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2018
---
# <a name="layout-compression"></a>Compresión de diseño

_Compresión de diseño quita diseños especificados del árbol visual en un intento de mejorar el rendimiento de la presentación de página. En este artículo se explica cómo habilitar la compresión de diseño y los beneficios que puede ofrecer._

## <a name="overview"></a>Información general

Xamarin.Forms realiza diseño usando dos series de llamadas al método recursiva:

- Diseño comienza en la parte superior del árbol visual con una página y pasa a través de todas las ramas del árbol visual para abarcar todos los elementos visuales en una página. Elementos que son nodos principales de otros elementos están responsables de ajustar el tamaño y la posición de sus elementos secundarios con respecto a ellos mismos.
- Invalidación es el proceso por el cual un cambio en un elemento de una página desencadena un nuevo ciclo de diseño. Los elementos se consideran no válidos cuando ya no tienen el tamaño correcto o la posición. Cada elemento en el árbol visual que tiene elementos secundarios es una alerta cada vez que cambia una de sus elementos secundarios tamaños. Por lo tanto, un cambio en el tamaño de un elemento en el árbol visual puede provocar cambios que ripple el árbol.

Para obtener más información acerca de cómo Xamarin.Forms realiza el diseño, vea [crear un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

El resultado del proceso de diseño es una jerarquía de controles nativos. Sin embargo, esta jerarquía incluye los representadores de contenedor adicionales y contenedores de los representadores de plataforma, lo que infla aún más la jerarquía de vista de anidamiento. La profundidad del nivel de anidamiento, mayor es la cantidad de trabajo que Xamarin.Forms tiene que realizar para mostrar una página. Para diseños complejos, la jerarquía de la vista puede ser amplia y dilatada, con varios niveles de anidamiento.

Por ejemplo, considere el siguiente botón de la aplicación de ejemplo para iniciar sesión en Facebook:

![](layout-compression-images/facebook-button.png "Botón de Facebook")

Este botón se especifica como un control personalizado con la siguiente jerarquía de vista XAML:

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

Se puede examinar la jerarquía resultante de la vista anidada con [Xamarin Inspector](~/tools/inspector/index.md). En Android, la jerarquía de la vista anidada contiene 17 vistas:

![](layout-compression-images/no-compression.png "Jerarquía de vista para el botón de Facebook")

Compresión de diseño, que está disponible para aplicaciones de Xamarin.Forms en las plataformas iOS y Android, pretende simplificar la vista anidar quitando diseños especificados desde el árbol visual, lo que puede mejorar el rendimiento de la presentación de página. La ventaja de rendimiento que se entrega varía según la complejidad de una página, la versión del sistema operativo que se va a usar y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos.

> [!NOTE]
> Aunque en este artículo se centra en los resultados de aplicar la compresión de diseño en Android, es igualmente aplicable a iOS.

## <a name="layout-compression"></a>Compresión de diseño

En XAML, compresión de diseño se puede habilitar estableciendo la `CompressedLayout.IsHeadless` propiedad adjunta `true` en una clase de diseño:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

Como alternativa, puede habilitarse en C# mediante la especificación de la instancia de diseño como el primer argumento para el `CompressedLayout.SetIsHeadless` método:

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Puesto que la compresión de diseño, quita un diseño del árbol visual, no es adecuado para diseños que tienen un aspecto visual, o para que obtener la entrada táctil. Por lo tanto, los diseños que establezca [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) propiedades (como [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/), [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/), [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/), [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/), [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) y [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)) o que aceptan movimientos, no son aptos para el diseño compresión. Sin embargo, al habilitar la compresión de diseño en un diseño que establece las propiedades de apariencia visual o que acepta gestos, no se producirá un error de compilación o en tiempo de ejecución. En su lugar, se aplicará la compresión de diseño y las propiedades de apariencia visual y el reconocimiento de gestos en modo silencioso producirá un error.

Para el botón de Facebook, compresión de diseño puede habilitarse en las clases de tres diseño:

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

En Android, el resultado de una jerarquía de vista anidada de 14 vistas:

![](layout-compression-images/layout-compression.png "Ver jerarquía de botón de Facebook con compresión de diseño")

En comparación con la jerarquía de vista anidada original de 17 vistas, esto representa una reducción en el número de vistas de un 17%. Aunque esta reducción puede parecer insignificante, la reducción de vista a través de una página completa puede ser más importante.

### <a name="fast-renderers"></a>Representadores rápidos

Representadores rápidos reducen la inflación y los costos de representación de controles de Xamarin.Forms en Android mediante la reducción de la jerarquía resultante de la vista nativa. Más Esto mejora el rendimiento mediante la creación de menos objetos, lo que a su vez resulta en un árbol visual menos complejo y menos uso de memoria. Para obtener más información acerca de los representadores rápidos, consulte [representadores rápido](~/xamarin-forms/internals/fast-renderers.md).

Para el botón de Facebook en la aplicación de ejemplo, combinar representadores rápidos y compresión de diseño, produce una jerarquía anidada de la vista de 8 vistas:

![](layout-compression-images/layout-compression-with-fast-renderers.png "Ver jerarquía de botón de Facebook con compresión de diseño y representadores rápidos")

En comparación con la jerarquía de vista anidada original de 17 vistas, esto representa una reducción de 52%.

La aplicación de ejemplo contiene una página que se extraen de una aplicación real. Sin compresión de diseño y la rápida representadores, la página genera una jerarquía anidada de la vista de 130 vistas en Android. Al habilitar representadores rápidos y compresión de diseño en las clases de diseño apropiado, reduce la jerarquía de la vista anidada a 70 vistas, una reducción del 46%.

## <a name="summary"></a>Resumen

Compresión de diseño quita diseños especificados del árbol visual en un intento de mejorar el rendimiento de la presentación de página. La ventaja de rendimiento que esto ofrece varía según la complejidad de una página, la versión del sistema operativo que se va a usar y el dispositivo en el que se ejecuta la aplicación. Sin embargo, las mejoras de rendimiento más importantes se apreciarán en los dispositivos más antiguos.


## <a name="related-links"></a>Vínculos relacionados

- [Creación de un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Representadores rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutcompression/)
