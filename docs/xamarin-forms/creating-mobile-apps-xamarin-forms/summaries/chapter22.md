---
title: Resumen de capítulo 22. Animación
description: 'Creación de aplicaciones móviles con Xamarin.Forms: resumen de capítulo 22. Animación'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b25fed9a86b82e56cb3b2bf5e3276c8ff63f4e35
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241974"
---
# <a name="summary-of-chapter-22-animation"></a>Resumen de capítulo 22. Animación

Ha visto que puede crear sus propias animaciones utilizando el temporizador de Xamarin.Forms o `Task.Delay`, pero resulta generalmente más fácil usando los medios de animación proporcionados por Xamarin.Forms. Tres clases implementan estas animaciones:

- [`ViewExtensions`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/), el enfoque de alto nivel
- [`Animation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/), más versátil pero algo complicado
- [`AnimationExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/), un enfoque más versátil, nivel más bajo

Por lo general, las animaciones tener como destino propiedades que están respaldados por propiedades enlazables. Esto no es un requisito, pero estas son las únicas propiedades dinámicamente reaccionen ante los cambios.

No hay ninguna interfaz XAML para estas animaciones, pero se pueden integrar las animaciones en XAML mediante las técnicas descritas en [ **capítulo 23. Desencadenadores y los comportamientos**](chapter23.md).

## <a name="exploring-basic-animations"></a>Explorar las animaciones básicas

Las funciones de animación básicos son métodos de extensión que se encuentra en la [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) clase. Estos métodos se aplican a cualquier objeto que se deriva de `VisualElement`. Las animaciones más sencillas como destino las transformaciones propiedades se describen en [ `Chapter 21. Transforms` ](chapter21.md).

El [ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) muestra cómo el `Clicked` controlador de eventos para un `Button` puede llamar a la [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método de extensión para poner en la botón en un círculo.

El `RotateTo` del método cambia la `Rotation` propiedad de la `Button` de 0 a 360 a lo largo de un cuarto de segundo (de forma predeterminada). Si el `Button` que se derivan de nuevo, sin embargo, no realiza ninguna acción porque la `Rotation` propiedad ya está 360 grados.

### <a name="setting-the-animation-duration"></a>Establecer la duración de animación

El segundo argumento `RotateTo` corresponde a una duración en milisegundos. Si establece en un valor grande, pulse en el `Button` durante una animación se inicia una nueva animación, comenzando en el ángulo actual.

### <a name="relative-animations"></a>Animaciones relativas

El `RelRotateTo` método realiza un giro relativo mediante la adición de un valor especificado para el valor existente. Este método permite la `Button` puntear varias veces y cada hora aumenta la `Rotation` propiedad 360 grados.

### <a name="awaiting-animations"></a>En espera de animaciones

Todos los métodos de animación de `ViewExtensions` devolver `Task<bool>` objetos. Esto significa que puede definir una serie de animaciones secuenciales con `ContinueWith` o `await`. El `bool` finalización valor devuelto es `false` si la animación finaliza sin interrupción o `true` si se canceló la [ `CancelAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) método, lo que cancela todas las animaciones iniciadas por el otro método en `ViewExtensions` que se establecen en el mismo elemento.

### <a name="composite-animations"></a>Animaciones compuestas

Se pueden mezclar las animaciones esperadas y no esperan para crear animaciones compuestas. Se trata de las animaciones en `ViewExtensions` que tienen como destino el `TranslatonX`, `TranslationY`, y `Scale` propiedades de transformación:

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`ScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.ScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)

Tenga en cuenta que `TranslateTo` afecta potencialmente tanto el `TranslationX` y `TranslationY` propiedades.

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll y Task.WhenAny

También es posible administrar las animaciones simultáneas con [ `Task.WhenAll` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAll%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/), que indica cuándo todos varias tareas se han finalizado, y [ `Task.WhenAny` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAny%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/), que indica cuándo el primero de varios ha concluido las tareas.

### <a name="rotation-and-anchors"></a>Rotación y delimitadores

Cuando se llama a la `ScaleTo`, `RelScaleTo`, `RotateTo`, y `RelRotateTo` métodos, puede establecer la [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) y [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propiedades para indicar el Centro de ajuste de escala y giro.

El [ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) demuestra esta técnica mediante la revolución de una `Button` alrededor del centro de la página.

### <a name="easing-functions"></a>Funciones de aceleración

Por lo general, las animaciones son lineales de un valor inicial a un valor final. Funciones de aceleración pueden hacer que las animaciones acelerar o ralentizar su transcurso. El último argumento opcional a los métodos de la animación es de tipo [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/), una clase que defina 11 campos estáticos de solo lectura de tipo `Easing`:

- [`Linear`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/), el valor predeterminado
- [`SinIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/), [ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/), y [`SinInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/)
- [`CubicIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/), [ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/), y [`CubicInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/)
- [`BounceIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) y [`BounceOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/)
- [`SpringIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) y [`SpringOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)

El `In` sufijo indica que el efecto está al principio de la animación, `Out` significa que al final, y `InOut` significa que se encuentra al principio y al final de la animación.

El [ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) ejemplo muestra el uso de funciones de aceleración.

### <a name="your-own-easing-functions"></a>Sus propias funciones de aceleración

También se puede definir funciones de aceleración propias pasando un [ `Func<double, double>` ](https://developer.xamarin.com/api/type/System.Func%3CT,TResult%3E/) a la [ `Easing` constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Easing.Easing/p/System.Func%7BSystem.Double,System.Double%7D/). `Easing` También define una conversión implícita de `Func<double, double>` a `Easing`. El argumento para la función de aceleración es siempre en el intervalo de 0 a 1 como la animación continúa linealmente de principio a fin. La función *normalmente* devuelve un valor en el intervalo de 0 a 1, pero podría ser brevemente negativo ni mayor que 1 (como sucede con la `SpringIn` y `SpringOut` funciones) o podría afectar a las reglas si sabe lo que está haciendo.

El [ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) ejemplo muestra una función de aceleración personalizada, y [ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) muestra otra.

El [ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) ejemplo también muestra una función de aceleración personalizada y también una técnica de cambiar la `AnchorX` y `AnchorY` propiedades dentro de una secuencia de animaciones de rotación.

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tiene un [ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) jiggle un botón cuando se hace clic en función de clase que utiliza una aceleración personalizada. El [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) ejemplo muestra.

### <a name="entrance-animations"></a>Animaciones de entrada

Un tipo conocido de animación se produce cuando aparece por primera vez una página. Se puede iniciar una animación de este tipo el [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) la invalidación de la página. Para estas animaciones, lo mejor posible para configurar el código XAML para definir cómo desea la página aparezca *después* la animación y, a continuación, inicializar y animar el diseño desde el código.

El [ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) ejemplo usa el [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método de extensión para intensificar el contenido de la página.

El [ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) ejemplo usa el [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) método de extensión para el contenido de la página se deslizan desde los lados.

El [ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) ejemplo usa el [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método de extensión para animar la `RotationY` propiedad. A [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método también está disponible.

### <a name="forever-animations"></a>Animaciones indefinidamente

En el otro extremo, "forever" animaciones ejecutan hasta que finaliza el programa. Por lo general, éstos están pensados para fines de demostración.

El [ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) ejemplo usa [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) animación de fundido de entrada y salida dos fragmentos de texto.

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) muestra palíndromo y, a continuación, secuencialmente gira las letras individuales 180 grados para que sean todas boca abajo. A continuación, la cadena completa se voltea 180 grados para leer el mismo que la cadena original.

El [ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) ejemplo gira una sencilla `BoxView` helicóptero al girar alrededor del centro de la pantalla.

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) gira `BoxView` radios alrededor del centro de la pantalla y, a continuación, gira cada radio para crear patrones interesantes:

[![Captura de pantalla triple de rotación de radios](images/ch22fg21-small.png "rotación radios")](images/ch22fg21-large.png#lightbox "rotación radios")

Sin embargo, se aumentar progresivamente la `Rotation` propiedad de un elemento podría no funcionar a largo plazo, como la [ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) muestra.

El [ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) ejemplo usa [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), y [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) para hacer que parezca como si se rotación de un mapa de bits en un espacio 3D.

### <a name="animating-the-bounds-property"></a>Animar la propiedad de límites

El único método de extensión en `ViewExtensions` aún no se ha mostrado es [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/), que anima eficazmente solo lectura [ `Bounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) propiedad mediante una llamada a la [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método. Normalmente se llama a este método `Layout` derivados tal y como se describen en [ **capítulo 26. CustomLayouts**](chapter26.md).

El `LayoutTo` método deben limitarse a fines especiales. El [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) programa usa para comprimir y expandir un `BoxView` tal y como lo rebota contra los lados de una página.

El [ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) ejemplo usa `LayoutTo` mover iconos en una implementación de la clásica rompecabezas de 15-16 que muestra una imagen codificada en lugar de iconos numerados:

[![Captura de pantalla triple de Xamarin Xuzzle](images/ch22fg26-small.png "Xuzzle Puzzle Game")](images/ch22fg26-large.png#lightbox "Xuzzle puzzles juego")

### <a name="your-own-awaitable-animations"></a>Sus propias animaciones esperables

El [ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) ejemplo crea una animación que admite await. La clase fundamental que puede devolver un `Task` objeto desde el método y la señal cuando se completa la animación es [ `TaskCompletionSource` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskCompletionSource%3CTResult%3E/).

## <a name="deeper-into-animations"></a>Profundiza en animaciones

El sistema de animación de Xamarin.Forms puede ser un poco confuso. Además el `Easing` (clase), el sistema de animación comprende la `ViewExtensions`, `Animation`, y `AnimationExtension` clases.

### <a name="viewextensions-class"></a>Clase ViewExtensions

Ya ha visto [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/). Define nueve métodos que devuelven `Task<bool>` y [ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/). Siete del destino nueve métodos propiedades de transformación. Las otras dos son [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), los destinos de la [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) propiedad, y [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/), que llama el [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método.

### <a name="animation-class"></a>Clase de animación

El [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) clase tiene un [constructor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Animation.Animation/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Action/) con cinco argumentos para definir la devolución de llamada y termine de métodos y parámetros de la animación.

Se pueden agregar animaciones secundarias con [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/), [ `Insert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/), [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/), y y sobrecarga de [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Double/System.Double/).

El objeto de animación, a continuación, se inicia con una llamada a la [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BSystem.Double,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) método.

### <a name="animationextensions-class"></a>Clase AnimationExtensions

El [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) clase contiene principalmente los métodos de extensión. Hay varias versiones de un `Animate` método y la clase genérica [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) método es tan versátil que es realmente la función de animación solo es necesario.

## <a name="working-with-the-animation-class"></a>Trabajar con la clase de animación

El [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) ejemplo muestra el [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) clase con varias animaciones diferentes.

### <a name="child-animations"></a>Animaciones secundarias

El [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) ejemplo también muestra secundarios animaciones, que hacen que el uso de la (muy similar) [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/) y [ `Insert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/) métodos.

### <a name="beyond-the-high-level-animation-methods"></a>Más allá de los métodos de alto nivel de animación

El [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) ejemplo también muestra cómo realizar animaciones que van más allá del propiedades de destino de la `ViewExtensions` métodos. En un ejemplo, una serie de períodos más largas; en otro ejemplo, un `BackgroundColor` propiedad está animada.

### <a name="more-of-your-own-awaitable-methods"></a>Varios de sus propios métodos que admite await y

El [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) método `ViewExtensions` no funciona con la [ `Easing.SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) (función). Se detiene cuando se obtiene el resultado de aceleración superiores a 1.

El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene un [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) clase con [ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) y [ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) métodos de extensión que no tienen este problema, así como [ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) y [ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) métodos para cancelarlos animaciones.

El [ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) muestra la `TranslateXTo` método.

El `MoreExtensions` clase también contiene un [ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) método de extensión que combina la traducción de X e Y, y un [ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) método.

### <a name="implementing-a-bezier-animation"></a>Implementar una animación de Bézier

También es posible desarrollar una animación que mueve un elemento a lo largo de la ruta de acceso de una curva spline de Bézier. El [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene un [ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) estructura que encapsula una curva spline de Bézier y una [ `BezierTangent` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) enumeración a la orientación del control.

El [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) clase contiene un [ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) método de extensión y un [ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) método.

El [ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) muestra un elemento a lo largo de una ruta de acceso de Beizer la animación.

## <a name="working-with-animationextensions"></a>Trabajar con AnimationExtensions

Un tipo de animación que faltan en la colección estándar es una animación de color. El problema es que no hay ninguna forma adecuada de interpolará entre dos `Color` valores. Es posible interpolar los valores RGB individuales, pero como válida es interpolar HSL (valores).

Por este motivo, el [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) clase en el [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contiene dos `Color` métodos de animación: [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)y [ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (También hay dos métodos de cancelación: [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) y [ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Ambos métodos convierten el uso de [ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), que realiza la animación mediante una llamada a una amplia genérico [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) método [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/).

El [ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) ejemplo muestra cómo utilizar estos dos tipos de animaciones de color.

## <a name="structuring-your-animations"></a>Estructurar las animaciones

A veces resulta útil expresar las animaciones en XAML y usarlos junto con MVVM. Este tema se trata en el próximo capítulo, [ **capítulo 23. Desencadenadores y los comportamientos**](chapter23.md).



## <a name="related-links"></a>Vínculos relacionados

- [Texto completo de capítulo 22 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Ejemplos de capítulo 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animación](~/xamarin-forms/user-interface/animation/index.md)
