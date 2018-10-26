---
title: Agregar un reconocedor de gestos de pasar el dedo
description: En este artículo se explica cómo reconocer un gesto de deslizar rápidamente que se producen en una vista.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 95e95d8849824cd2dc31c2019627cc5adbbefeec
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131925"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Agregar un reconocedor de gestos de pasar el dedo

_Un gesto de deslizar rápidamente se produce cuando un dedo se mueve a través de la pantalla en dirección horizontal o vertical y, a menudo se usa para iniciar la navegación a través de contenido. Los ejemplos de código en este artículo se toman de la [gesto de deslizar rápidamente](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/) ejemplo._

Para realizar un [ `View` ](xref:Xamarin.Forms.View) reconocer un gesto de deslizar rápidamente, crear un [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) de instancia, establezca el [ `Direction` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) propiedad a un [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) valor de enumeración (`Left`, `Right`, `Up`, o `Down`), establezca opcionalmente la [ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propiedad, el identificador de la [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) eventos, y agregue el reconocedor de gestos de nuevo a la [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) colección en la vista. El siguiente ejemplo de código muestra un `SwipeGestureRecognizer` adjunta a un [ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Éste es el equivalente C# código:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

El [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) clase también incluye un [ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propiedad, que opcionalmente se puede establecer en un `uint` valor que representa la distancia de pasar el dedo mínimo que se debe obtener para un Deslice el dedo para ser reconocido, en unidades independientes del dispositivo. El valor predeterminado de esta propiedad es 100, lo que significa que cualquier deslizamientos que son que menos de 100 unidades independientes del dispositivo se pasará por alto.

## <a name="recognizing-the-swipe-direction"></a>Reconocer la dirección de pasar el dedo

En los ejemplos anteriores, el [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) propiedad está establecida en un valor del único el [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) enumeración. Sin embargo, también es posible establecer esta propiedad en varios valores desde el `SwipeDirection` enumeración, por lo que el [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento se desencadena en respuesta al pasar el dedo en más de una dirección. Sin embargo, la restricción es que una sola [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) sólo puede reconocer los deslizamientos que se producen en el mismo eje. Por lo tanto, se pueden reconocer los deslizamientos que se producen en el eje horizontal estableciendo el `Direction` propiedad `Left` y `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

De forma similar, se pueden reconocer los deslizamientos que se producen en el eje vertical estableciendo el [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) propiedad `Up` y `Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Como alternativa, un [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) para cada dedo dirección puede crearse para que reconozca los deslizamientos en cada dirección:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Éste es el equivalente C# código:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> En los ejemplos anteriores, el mismo controlador de eventos responde a la [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) desencadenamiento de eventos. Sin embargo, cada [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) instancia puede usar un controlador de eventos diferente si es necesario.

## <a name="responding-to-the-swipe"></a>Responder a la misma

Un controlador de eventos para el [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento se muestra en el ejemplo siguiente:

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

El [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs) puede examinarse para determinar la dirección de la misma, con lógica personalizada que responde a la misma según sea necesario. Se puede obtener la dirección de la misma desde el [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) propiedad de los argumentos de evento, que se establecerá en uno de los valores de la [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) enumeración. Además, los argumentos de evento también tienen un [ `Parameter` ](xref:Xamarin.Forms.SwipedEventArgs.Parameter) propiedad que se establecerá en el valor de la [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propiedad, si ha definido.

## <a name="using-commands"></a>Uso de comandos

El [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) también incluye la clase [ `Command` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) y [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propiedades. Estas propiedades se utilizan normalmente en aplicaciones que usan el patrón Model-View-ViewModel (MVVM). El `Command` define la propiedad la `ICommand` que se invocará cuando se reconoce un gesto de deslizar rápidamente, con el `CommandParameter` propiedad que define un objeto que se pasarán a la `ICommand.` el ejemplo de código siguiente muestra cómo enlazar la `Command` propiedad a un `ICommand` definido en el modelo de vista cuya instancia se establece como la página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext):

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

El código XAML equivalente es:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` es una propiedad de tipo `ICommand` definidos en la instancia de modelo de vista que se establece como la página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Cuando se reconoce un gesto de deslizar rápidamente, el `Execute` método de la `SwipeCommand` objeto que se va a ejecutar. El argumento para el `Execute` método es el valor de la [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propiedad. Para obtener más información acerca de los comandos, consulte [la interfaz de comandos](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Creación de un contenedor de pasar el dedo

El `SwipeContainer` (clase), que se muestra en el siguiente ejemplo de código, es una clase de reconocimiento de pasar el dedo generalizado que se ajustó alrededor un [ `View` ](xref:Xamarin.Forms.View) para realizar el reconocimiento de gestos de pasar el dedo:

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

El `SwipeContainer` crea la clase [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) de objetos para todas las direcciones de pasar el dedo cuatro y adjunta `Swipe` controladores de eventos. Estos controladores de eventos invocan la `Swipe` evento definido por el `SwipeContainer`.

El ejemplo de código XAML siguiente muestra la `SwipeContainer` clase ajuste un [ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

El siguiente ejemplo de código muestra cómo el `SwipeContainer` ajusta un [ `BoxView` ](xref:Xamarin.Forms.BoxView) en un C# página:

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

Cuando el [ `BoxView` ](xref:Xamarin.Forms.BoxView) recibe un gesto de deslizar rápidamente el [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) eventos en el [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) se activa. Esto se controla mediante el `SwipeContainer` (clase), que se desencadena su propio `Swipe` eventos. Esto `Swipe` se controla el evento en la página. El [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs) , a continuación, se puede examinar para determinar la dirección de la misma, con lógica personalizada que responde a la misma según sea necesario.

## <a name="related-links"></a>Vínculos relacionados

- [Gesto de deslizar rápidamente (ejemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
