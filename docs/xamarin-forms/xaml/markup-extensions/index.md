---
title: Extensiones de marcado XAML
description: "Ampliar la variedad de orígenes de qué XAML se establecen atributos"
ms.topic: article
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 11889115b65608c750690c33052a9c86f7081e25
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-markup-extensions"></a>Extensiones de marcado XAML

Las extensiones de marcado XAML ayuda a ampliar la eficacia y flexibilidad de XAML al permitir que los atributos del elemento que se pueden establecer desde orígenes que no son cadenas de texto literal.

Por ejemplo, normalmente, debe establecer el `Color` propiedad de `BoxView` similar a la siguiente:

```xaml
<BoxView Color="Blue" />
```

O bien, puede establecerlo en un valor hexadecimal del color RGB:

```xaml
<BoxView Color="#FF0080" />
```

En cualquier caso, la cadena de texto se establece en el `Color` atributo se convierte en una `Color` valor por el [ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/) clase.

Es preferible en su lugar establecer el `Color` atributo desde un valor almacenado en un diccionario de recursos, o el valor de una propiedad estática de una clase que ha creado o una propiedad de tipo `Color` de otro elemento en la página, o construido desde Separe los valores de matiz, saturación y luminosidad.

Todas estas opciones son posibles mediante extensiones de marcado XAML. Pero no permita que la frase "las extensiones de marcado" intimidar: extensiones de marcado XAML son *no* extensiones a XML. Incluso con las extensiones de marcado XAML, XAML siempre es XML válido. 

Una extensión de marcado es simplemente una manera diferente para expresar un atributo de un elemento. Las extensiones de marcado XAML son suelen identificarse por un valor de atributo que se encierra entre llaves:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Cualquier valor de atributo entre llaves es *siempre* una extensión de marcado XAML. Sin embargo, como verá, las extensiones de marcado XAML también pueden hacer referencia sin el uso de llaves.

En este artículo se divide en dos partes:

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Consumir las extensiones de marcado XAML](consuming.md)  

Usar las extensiones de marcado XAML definidas en Xamarin.Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Crear extensiones de marcado XAML](creating.md) 

Escribir sus propias extensiones de marcado XAML personalizados.



## <a name="related-links"></a>Vínculos relacionados

- [Extensiones de marcado (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capítulo de extensiones de marcado XAML de libreta de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Diccionarios de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinámicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Enlace de datos](~/xamarin-forms/app-fundamentals/data-binding/index.md)
