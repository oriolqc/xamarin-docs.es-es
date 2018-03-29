---
title: Pasar argumentos en XAML
description: Este artículo muestra cómo utilizar los atributos XAML que pueden utilizarse para pasar argumentos a los constructores no predeterminados para llamar a métodos de generador y para especificar el tipo de argumento genérico.
ms.topic: article
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2016
ms.openlocfilehash: 232f60bb7afca7acf73e63bd7e11e1b6ec47fbd2
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="passing-arguments-in-xaml"></a>Pasar argumentos en XAML

_Este artículo muestra cómo utilizar los atributos XAML que pueden utilizarse para pasar argumentos a los constructores no predeterminados para llamar a métodos de generador y para especificar el tipo de argumento genérico._

## <a name="overview"></a>Información general

A menudo es necesario crear instancias de objetos con constructores que requieren argumentos, o mediante una llamada a un método de creación estático. Esto puede lograrse en XAML mediante el uso de la `x:Arguments` y `x:FactoryMethod` atributos:

- El `x:Arguments` atributo se utiliza para especificar los argumentos de constructor de un constructor no predeterminado o de una declaración de objeto del método de fábrica. Para obtener más información, consulte [pasar argumentos de Constructor](#constructor_arguments).
- El `x:FactoryMethod` atributo se utiliza para especificar un método de generador que puede usarse para inicializar un objeto. Para obtener más información, consulte [al llamar a métodos de generador](#factory_methods).

Además, la `x:TypeArguments` atributo se puede usar para especificar los argumentos de tipo genérico para el constructor de un tipo genérico. Para obtener más información, consulte [especificar un argumento de tipo genérico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Pasar argumentos de Constructor

Pueden pasar argumentos a un constructor no predeterminado utilizando el `x:Arguments` atributo. Cada argumento de constructor debe estar delimitado dentro de un elemento XML que representa el tipo del argumento. Xamarin.Forms es compatible con los siguientes elementos para los tipos básicos:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

En el ejemplo de código siguiente se muestra cómo utilizar el `x:Arguments` atributo con tres [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) constructores:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

El número de elementos de la `x:Arguments` etiqueta y los tipos de estos elementos, que deben coincidir con uno de los [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) constructores. El `Color` [constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/) con un solo parámetro requiere un valor de escala de grises de 0 (negro) y 1 (blanco). El `Color` [constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/) con tres parámetros requiere un valor de rojo, verde y azul comprendido entre 0 y 1. El `Color` [constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/) con cuatro parámetros agrega un canal alfa como cuarto parámetro.

Las capturas de pantalla siguientes muestran el resultado de llamar a cada uno de ellos [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) constructor con los valores de argumento especificado:

![](passing-arguments-images/passing-arguments.png "BoxView.Color especificado con x: Arguments")

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Llamar a métodos de fábrica

Se puede llamar métodos de fábrica en XAML mediante la especificación del método nombre con el `x:FactoryMethod` atributo y sus argumentos mediante el `x:Arguments` atributo. Un método de fábrica es un `public static` método que devuelve objetos o valores del mismo tipo que la clase o estructura que define los métodos.

El [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) estructura define una serie de métodos de generador y, en el ejemplo de código siguiente se muestra que realiza la llamada tres de ellas:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                      
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

El número de elementos de la `x:Arguments` etiqueta y los tipos de estos elementos, que deben coincidir con los argumentos del método de generador que se va a invocar. El [ `FromRgba` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) método generador requiere cuatro [ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32) parámetros, que representan los valores de color rojo, verde, azul y alfabéticos, comprendido entre 0 y 255 respectivamente. El [ `FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) método generador requiere cuatro [ `Double` ](https://docs.microsoft.com/dotnet/api/system.double) parámetros, que representan el matiz, saturación, luminosidad y los valores alfa, comprendido entre 0 y 1 respectivamente. El [ `FromHex` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) método generador requiere un [ `String` ](https://docs.microsoft.com/dotnet/api/system.string) que representa el valor hexadecimal (A) color RGB.

Las capturas de pantalla siguientes muestran el resultado de llamar a cada uno de ellos [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) método de fábrica con los valores de argumento especificado:

![](passing-arguments-images/factory-methods.png "BoxView.Color especificado con x: FactoryMethod () y x: Arguments")

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Si se especifica un argumento de tipo genérico

Argumentos de tipo genérico para el constructor de un tipo genérico pueden especificarse mediante la `x:TypeArguments` de atributo, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="WinPhone, Windows" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

El [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) clase es una clase genérica y se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino. En el [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) (clase), el [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) atributo puede aceptar un único `string` múltiples delimitada por comas o valor `string` valores. En este ejemplo, el [ `StackLayout.Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propiedad está establecida en una específica de la plataforma [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/).

## <a name="summary"></a>Resumen

Este artículo se muestra utilizando los atributos XAML que pueden utilizarse para pasar argumentos a los constructores no predeterminados para llamar a métodos de generador y para especificar el tipo de argumento genérico.


## <a name="related-links"></a>Vínculos relacionados

- [Espacios de nombres XAML](~/xamarin-forms/xaml/namespaces.md)
- [Pasar argumentos de Constructor (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/passingconstructorarguments/)
- [Llamar a métodos de fábrica (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/callingfactorymethods/)
