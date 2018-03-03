---
title: Animaciones personalizadas
description: "La clase de animación es el bloque de creación de todas las animaciones de Xamarin.Forms, con los métodos de extensión en la clase ViewExtensions crear uno o varios objetos de animación. En este artículo se muestra cómo utilizar la clase de animación para crear y cancelar las animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan propiedades que no están animados por los métodos existentes de la animación."
ms.topic: article
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 42ef3e6c82763831b5114f3de7603bba8f59eac6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="custom-animations"></a>Animaciones personalizadas

_La clase de animación es el bloque de creación de todas las animaciones de Xamarin.Forms, con los métodos de extensión en la clase ViewExtensions crear uno o varios objetos de animación. En este artículo se muestra cómo utilizar la clase de animación para crear y cancelar las animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan propiedades que no están animados por los métodos existentes de la animación._


Un número de parámetros debe especificarse al crear un `Animation` objeto, incluidos los valores inicial y final de la propiedad que se está animando y una devolución de llamada que cambia el valor de la propiedad. Un `Animation` objeto también puede mantener una colección de las animaciones secundarias que se pueden ejecutar y sincronizado. Para obtener más información, consulte [animaciones secundarias](#child).

Ejecuta una animación creada con la [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) (clase), que puede o no incluir animaciones secundarias, se consigue mediante una llamada a la [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) método. Este método especifica la duración de la animación y, entre otros elementos, una devolución de llamada que controla si se deben repetir la animación.

## <a name="creating-an-animation"></a>Crear una animación

Al crear un [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) objeto, por lo general, un mínimo de tres parámetros son obligatorios, como se muestra en el ejemplo de código siguiente:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Este código define una animación de la [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedad de un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instancia de un valor de 1 a un valor de 2. El valor animado, que se deriva mediante Xamarin.Forms, se pasa a la devolución de llamada especificado como el primer argumento, que se utiliza para cambiar el valor de la `Scale` propiedad.

La animación se inicia con una llamada a la [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) método, como se muestra en el ejemplo de código siguiente:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Tenga en cuenta que la [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) método no devuelve un `Task` objeto. En su lugar, las notificaciones se proporcionan a través de métodos de devolución de llamada.

Los siguientes argumentos se especifican en el `Commit` método:

- El primer argumento (*propietario*) identifica al propietario de la animación. Esto puede ser el elemento visual que se aplica la animación, u otro elemento visual, como la página.
- El segundo argumento (*nombre*) identifica la animación con un nombre. El nombre se combina con el propietario para identificar de forma única la animación. Esta identificación única, a continuación, puede utilizarse para determinar si la animación se está ejecutando ([`AnimationIsRunning`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AnimationIsRunning/p/Xamarin.Forms.IAnimatable/System.String/)), o lo cancele ([`AbortAnimation`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/)).
- El tercer argumento (*velocidad*) indica el número de milisegundos entre cada llamada al método de devolución de llamada definido en el [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) constructor
- El cuarto argumento (*longitud*) para indicar la duración de la animación, en milisegundos.
- El quinto argumento (*aceleración*) define la función de aceleración que se usará en la animación. Como alternativa, se puede especificar la función de aceleración como un argumento a la [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) constructor. Para obtener más información acerca de funciones de aceleración, vea [funciones de aceleración](~/xamarin-forms/user-interface/animation/easing.md).
- El sexto argumento (*terminado*) es una devolución de llamada que se ejecutará cuando se haya completado la animación. Esta devolución de llamada toma dos argumentos, con el primer argumento que indica un valor final y el segundo argumento es una `bool` que se establece en `true` si se ha cancelado la animación. Como alternativa, el *terminado* devolución de llamada puede especificarse como un argumento a la [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) constructor. Sin embargo, con una sola animación si *terminado* las devoluciones de llamada se especifican tanto en el `Animation` constructor y el `Commit` método, solo la devolución de llamada especificado en el `Commit` se ejecutará el método.
- El séptimo argumento (*repita*) es una devolución de llamada que permite que la animación se repita. Se llama al final de la animación y devolver `true` indica que se debe repetir la animación.

El efecto general consiste en crear una animación que aumenta la [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedad de un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) de 1 a 2, más de 2 segundos (2000 milisegundos), con el [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) función de aceleración. Cada vez que se completa la animación, su `Scale` propiedad se restablece en 1 y se repite la animación.

> [!NOTE]
> **Tenga en cuenta**: animaciones simultáneas, que se ejecutan independientemente entre sí pueden crearse mediante la creación de un `Animation` objeto para cada animación y, a continuación, llamar a la `Commit` método en cada animación.

<a name="child" />

### <a name="child-animations"></a>Animaciones secundarias

El [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) clase también admite animaciones secundarias, que implica la creación de un `Animation` objeto qué Sí `Animation` se agregan objetos. Esto permite una serie de animaciones que se ejecutarán y se sincronizan. En el ejemplo de código siguiente se muestra cómo crear y ejecutar animaciones secundarias:

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

O bien, en el ejemplo de código se puede escribir más concisa, como se muestra en el ejemplo de código siguiente:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

En ambos ejemplos de código, un elemento primario [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) se crea el objeto, a la que más `Animation` , a continuación, se agregan objetos. Los dos primeros argumentos para la [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/) método especificar cuándo se debe empezar y finalizar la animación secundarios. Los valores de argumento deben estar comprendido entre 0 y 1 y representan el período relativo dentro de la animación primario que se activará la animación secundario especificado. Por lo tanto, en este ejemplo el `scaleUpAnimation` estarán activas para la primera mitad de la animación, la `scaleDownAnimation` estarán activas para la segunda mitad de la animación y la `rotateAnimation` estará activa durante toda la duración.

El efecto general es que la animación se produce más de 4 segundos (4000 milisegundos). El `scaleUpAnimation` anima la [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedad de 1 a 2, más de 2 segundos. El `scaleDownAnimation` , a continuación, se anima la `Scale` propiedad de 2 en 1, más de 2 segundos. Mientras se lleva a cabo dos animaciones de escala, el `rotateAnimation` anima la [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propiedad comprendido entre 0 y 360, más de 4 segundos. Tenga en cuenta que las animaciones de escalado usar funciones de aceleración. El [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) función de aceleración hace el [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) reducir inicialmente antes de obtener mayor y la [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) función de aceleración hace que la `Image` sea menor que su tamaño real hacia el final de la animación completa.

Hay una serie de diferencias entre una [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) objeto que usa animaciones secundarias y que no:

- Cuando se usa animaciones secundarias, el *terminado* devolución de llamada en una animación secundaria indica cuando se complete el elemento secundario y la *terminado* devolución de llamada se pasa a la `Commit` método indica cuándo se el se ha completado todo animación.
- Cuando se usa animaciones secundarias, devolver `true` desde el *repita* devolución de llamada en el `Commit` método no hará que la animación se repita, pero la animación continuarán ejecutándose sin nuevos valores.
- Al incluir una función de aceleración en el `Commit` método y la función de aceleración devuelve un valor mayor que 1, se terminará la animación. Si la función de aceleración devuelve un valor menor que 0, el valor se fija en 0. Para utilizar una función de aceleración que devuelve un valor menor que 0 o mayor que 1, se debe especificar en una de las animaciones secundarias, en lugar de en el `Commit` método.

El [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) también incluye la clase [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/) métodos que pueden utilizarse para agregar animaciones secundarias a un elemento primario `Animation` objeto. Sin embargo, sus *comenzar* y *finalizar* valores de argumento no están restringidos a 0 a 1, pero estará activa solamente esa parte de la animación de secundarios que se corresponde con un intervalo de 0 a 1. Por ejemplo, si un `WithConcurrent` llamada al método define una animación secundaria que tiene como destino un [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propiedad de 1 a 6, pero con *comenzar* y *finalizar* valores de -2 y 3, el *comenzar* valor-2 corresponde a un `Scale` valor de 1 y el *finalizar* valor 3 corresponde a un `Scale` valor de 6. Dado que los valores fuera del intervalo de 0 y 1 no desempeñan ninguna parte de una animación, la `Scale` propiedad solo se pueden animar de 3 a 6.

## <a name="canceling-an-animation"></a>Cancelar una animación

Una aplicación puede cancelar una animación con una llamada a la [ `AbortAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/) método de extensión, como se muestra en el ejemplo de código siguiente:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Tenga en cuenta que las animaciones se identificarán mediante una combinación del propietario de la animación y el nombre de la animación. Por lo tanto, el propietario y el nombre especifican al ejecutar la animación debe especificarse para cancelar la animación. Por lo tanto, el ejemplo de código cancelará inmediatamente la animación denominada `SimpleAnimation` que pertenece a la página.

## <a name="creating-a-custom-animation"></a>Crear una animación personalizada

Los ejemplos presentados hasta ahora han demostrado las animaciones que igualmente se pudieron lograr con los métodos de la [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase. Sin embargo, la ventaja de la [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) es de clase que tiene acceso para el método de devolución de llamada, que se ejecuta cuando cambia el valor animado. Esto permite que la devolución de llamada implementar cualquier animación deseado. Por ejemplo, en el ejemplo de código siguiente se anima la [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) propiedad de una página si se establece en [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) valores creados por la [ `Color.FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/)(método), con valores de matiz comprendido entre 0 y 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

La animación resultante proporciona la apariencia de avanzar el fondo de la página a través de los colores del arco iris.

Para obtener más ejemplos de creación de animaciones complejas, incluida una animación de curva de Bézier, vea [Chapter 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) de [crear aplicaciones móviles con Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="creating-a-custom-animation-extension-method"></a>Crear un método de extensión de animación personalizada

Los métodos de extensión en la [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase animar una propiedad de su valor actual en un valor especificado. Esto dificulta la creación, por ejemplo, un `ColorTo` método de animación que puede usarse para animar un color en un valor a otro, porque:

- El único [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) propiedad definida por el [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) clase es [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/), que no siempre es lo que se desea `Color` propiedad va a animar.
- A menudo el valor actual de un [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) propiedad es [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/), que no es un color real, y que no se puede usar en los cálculos de interpolación.

La solución a este problema es que no la `ColorTo` método destinados a un determinado [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) propiedad. En su lugar, es posible escribir un método de devolución de llamada que pasa la interpolación `Color` valor de vuelta al llamador. Además, el método tomará inicio y finalización `Color` argumentos.

El `ColorTo` método se puede implementar como un método de extensión que usa el [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/) método en el [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) clase para proporcionar su funcionalidad. Esto es porque el `Animate` método se puede utilizar para las propiedades de destino que no son de tipo `double`, como se muestra en el ejemplo de código siguiente:

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

El [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/) método requiere un *transformar* argumento, que es un método de devolución de llamada. La entrada para esta devolución de llamada es siempre un `double` comprendido entre 0 y 1. Por lo tanto, la `ColorTo` método define su propia transformación `Func` que acepta un `double` comprendida entre 0 y 1 y que devuelve un [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) valor correspondiente a ese valor. El `Color` valor se calcula mediante la interpolación el [ `R` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/), [ `G` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/), [ `B` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/), y [ `A` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/) valores de las dos proporcionada `Color` argumentos. El `Color` valor, a continuación, se pasa al método de devolución de llamada para la aplicación a una propiedad determinada.

Este enfoque permite la `ColorTo` método para animar cualquier [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) propiedad, como se muestra en el ejemplo de código siguiente:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

En este ejemplo de código, el `ColorTo` método anima la [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) y [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) propiedades de un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), el `BackgroundColor`propiedad de una página y el [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) propiedad de un [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/).

## <a name="summary"></a>Resumen

Este artículo muestra cómo utilizar el [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) clase para crear y cancelar las animaciones, sincronizar varias animaciones y crear animaciones personalizadas que animan propiedades que no están animando la animación existente métodos. La `Animation` clase es el bloque de creación de todas las animaciones de Xamarin.Forms.


## <a name="related-links"></a>Vínculos relacionados

- [Animaciones personalizadas (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)
- [Animación](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)
- [AnimationExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)
